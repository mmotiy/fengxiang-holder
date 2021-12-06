# debug namespance
[TOP]

## eth_debugTransaction

traceTransaction 调试方法将尝试以与在网络上执行的完全相同的方式运行事务。在最终尝试执行与给定散列对应的事务之前，它将重放在此事务之前可能已执行的任何事务。

除了交易的哈希值之外，您还可以给它一个次要的可选参数，用于指定此特定调用的选项。可能的选项是：
- disableStorage: BOOL. 将此设置为 true 将禁用存储捕获 (default = false).
- disableMemory: BOOL. 将此设置为 true 将禁用内存捕获 (default = false).
- disableStack: BOOL. 将此设置为 true 将禁用堆栈捕获 (default = false).
- tracer: STRING. 设置此项将启用基于 JavaScript 的事务跟踪，如下所述。如果设置，前四个参数将被忽略。
- timeout: STRING. 覆盖基于 JavaScript 的跟踪调用的默认超时 5 秒。 [此处](https://pkg.go.dev/time#ParseDuration) 描述了有效值。


#### 客户端方法调用

|版本| 方式|
|---|---|
|Go|`debug.TraceTransaction(txHash common.Hash, logger *vm.LogConfig) (*ExecutionResurt, error)`|
|Console|`debug.traceTransaction(txHash, [options])`|
|RPC|`{"method": "debug_traceTransaction", "params": [txHash, {}]}`|


#### JavaScript-based tracing
在第二个参数中指定 tracer 选项将启用基于 JavaScript 的跟踪。 在这种模式下，跟踪器被解释为一个 JavaScript 表达式，该表达式预期评估为必须公开结果和错误方法的对象。存在 3 种额外的方法，namely: step, enter and exit.您必须提供任一步骤，或输入 AND 退出。（即这两个必须一起暴露）。如果您选择这样做，您可以公开所有三个。

##### Step
step 是一个函数，它接受两个参数 log 和 db，并在 EVM 的每个步骤或发生错误时调用，用于跟踪指定的事务。

log 具有以下字段：

- op: Object，表示当前操作码的 OpCode 对象
- stack: array[big.Int]，EVM 执行栈
- memory: 对象，表示合约内存空间的结构
- contract: Object，代表执行当前操作的账户的对象

以及以下方法：:
- getPC() - 返回一个带有当前程序计数器的数字
- getGas() - 返回一个带有剩余气体量的数字
- getCost() - 以数字形式返回操作码的成本
- getDepth() - 以数字形式返回执行深度
- getRefund() - 以数字形式返回要退还的金额
- getError() - 如果发生错误，则返回有关错误的信息，否则返回 undefined
             如果 error 非空，则应忽略所有其他字段。

为提高效率，在每个执行步骤中重复使用相同的日志对象，并使用当前值进行更新；确保复制要在当前调用之外保留的值。例如，这个 step 函数将不起作用：

```javascript
function(log) {
  this.logs.append(log);
}
```

But this step function will:
```javascript
function(log) {
  this.logs.append({gas: log.getGas(), pc: log.getPC(), ...});
}
```

log.op 有以下方法:

- isPush() - 如果操作码是 PUSHn，则返回 true
- toString() - 返回操作码的字符串表示
- toNumber() - 返回操作码的编号

log.memory 有以下方法:

- slice(start, stop) - 将指定的内存段作为字节切片返回
- getUint(offset) - 返回给定偏移量处的 32 个字节

log.stack has the following methods:

- peek(idx) - 返回栈顶的第 idx 个元素（0 是最顶层的元素）作为 big.Int
- length() - 返回堆栈中元素的数量

log.contract has the following methods:

- getCaller() - 返回调用者的地址
- getAddress() - 返回当前合约的地址
- getValue() - 返回从调用者发送到合约的价值量作为 big.Int
- getInput() - 返回传递给合约的输入数据

db has the following methods:

- getBalance(address) - 返回一个带有指定账户余额的 big.Int
- getNonce(address) - 返回一个带有指定账户随机数的数字
- getCode(address) - 返回带有指定帐户代码的字节切片
- getState(address, hash) - 返回指定帐户和指定哈希的状态值
- exists(address) - 如果指定的地址存在，则返回 true

如果 step 函数在任何时候抛出异常或执行非法操作，则不会在任何进一步的 VM 步骤上调用它，并将错误返回给调用者。

Result
result 是一个函数，它接受两个参数 ctx 和 db，并期望返回一个 JSON 可序列化的值以返回给 RPC 调用者。

ctx 是事务执行的上下文，具有以下字段:

- type - 字符串，CALL 和 CREATE 两个值之一
- from - 地址，交易发送方
- to - 地址、交易目标
- input - 缓冲区，输入交易数据
- gas - 数量，交易的gas预算
- value - big.Int，要转入wei的金额
- block - 编号，块编号
- output - 缓冲区，从 EVM 返回的值
- gasUsed - 数量，执行交易时使用的 gas 数量（不包括 txdata 成本）
- time - 字符串，执行运行时

Fault
fault 是一个带有两个参数 log 和 db 的函数，就像 step 一样，当在 step 中未报告的操作码执行期间发生错误时调用它。方法 log.getError() 包含有关错误的信息。

Enter & Exit
enter 和 exit 分别在进入和退出内部调用时被调用。更具体地说，它们在 CALL 变体、CREATE 变体以及 SELFDESTRUCT 隐含的传输上被调用。

enter 将 callFrame 对象作为参数，它具有以下方法：

- getType() - 返回一个具有调用帧类型的字符串
- getFrom() - 返回调用帧发送者的地址
- getTo() - 返回调用帧目标的地址
- getInput() - 将输入作为缓冲区返回
- getGas() - 返回一个数字，其中包含为框架提供的气体量
- getValue() - 仅在可用时返回一个 big.Int，其中包含要转移的金额，否则未定义

exit 接受一个 frameResult 对象，该对象具有以下方法：

- getGasUsed() - 以数字形式返回整个框架中使用的气体量
- getOutput() - 将输出作为缓冲区返回 ` -getError() - 如果在执行期间发生错误则返回错误，否则返回 undefined`

Usage

请注意，有几个值是 Golang big.Int 对象，而不是 JavaScript 数字或 JS bigint。因此，它们具有与 godocs 中描述的相同的接口。它们默认的 JSON 序列化是一个 Javascript 数字；序列化大数字准确地调用 .String() 。为方便起见，提供了 big.NewInt(x)，并将 uint 转换为 Go BigInt。

用法示例，仅在每个 CALL 操作码处返回堆栈的顶部元素：
```javascript
debug.traceTransaction(txhash, {tracer: '{data: [], fault: function(log) {}, step: function(log) { if(log.op.toString() == "CALL") this.data.push(log.stack.peek(0)); }, result: function() { return this.data; }}'});
```
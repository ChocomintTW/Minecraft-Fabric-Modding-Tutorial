# 指令 (Command)

## Register 的基本認識
在 Fabric 中，呼叫建構指令時是用[[事件 (Event)]]中的 `Callback` 來建構的，寫在客戶端的初始化函數中：

```java=
public class Client implements ClientModInitializer {  
   @Override  
   public void onInitializeClient() {  
      CommandRegistrationCallback.EVENT.register(...);
   }  
}
```

`CommandRegistrationCallback` 是 Fabric API 幫我們寫好的 `Callback`
`register` 要放入的是如下的函數(或者說 lambda func.)：

```java=
public static void register(
		CommandDispatcher<ServerCommandSource> dispatcher,
		CommandRegistryAccess registryAccess,
		RegistrationEnvironment environment
);
```

其中，最重要的莫過於 `dispatcher`，其形態為 `CommandDispatcher<>`，泛型允許 `CommandSource`：如果是伺服端指令，可用 `ServerCommandSource`；若是客戶端指令，可用 `ClientCommandSource`。
*p.s. 對客戶伺服端有疑問者，詳見 [[Networking]]*

## 創建第一個指令
假設我現在想要一個功能— `/foo` 可在聊天室輸出一段訊息 `This is a test message.`

首先，我們簡化 `register`：
```java=
// TestCommand
public static void register(CommandDispatcher<ServerCommandSource> dispatcher) {
	dispatcher.register(...);
}
```

`Callback` 時要用 lambda function：
```java=
CommandRegistrationCallback.EVENT.register((dispatcher, registryAccess, environment) -> TestCommand.register(dispatcher));
```

回到 `dispatcher.register`。
這裡，你可以用 `CommandManager` 中的創建函數，範例如下：
```java=
dispatcher.register(
	CommandManager.literal("foo")
);
```

因為要讓這個指令執行東西，我們需要用 `execute`。其中的參數要放 lambda function：
```java=
dispatcher.register(
	CommandManager.literal("foo").executes(context -> {
	
		context.getSource().sendMessage(Text.of("This is a test message."));
		
		return 1;
	});
);
```
其中，`context` 的型態為 `ServerCommandSource` (若為客戶端指令則是 `ClientCommandSource`)；回傳值"通常" 1 指成功，0 指失敗 (當然回傳任意 int 皆可)
至於，`context.getSource()` 的成員函數很多，就留給大家自己探索。

## Requirements
如果說有一個指令你想要特定階級的人才能使用，你可以使用 `requires` 函數：
```java=
dispatcher.register(
	CommandManager.literal("foo")
		.requires(source -> source.hasPermissionLevel(4))
		.executes(...)
);
```

## Redirects

## Arguments

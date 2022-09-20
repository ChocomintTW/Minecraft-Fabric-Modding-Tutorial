# 物品 (Item)
*古人有言：「Minecraft之始，物品也」*，學習如何創建一個物品可說是我們進入製作模組的第一步，也是最重要的一步。

## 創建第一個物品
在 java 的模組路徑下新增資料夾 `item`，並加入檔案 `ModItems.java`

首先，我們需要寫一個可以註冊物品的函數：
```java=
private static Item registerItem(String name, Item item) {
  return Registry.register(Registry.ITEM, new Identifier(TestMod.MOD_ID, name), item);
}
```

接著我們就可以註冊物品：
```java=
public static final Item RUBY = registerItem("ruby",
	new Item(new FabricItemSettings().group(ItemGroup.MISC)));
```

最後，寫一個初始化函數並放到 ***Init Funciton*** 中即完成：
```java=
public static void registerModItems() {
    System.out.println("Registering Mod Items for " + More_Ores.MOD_ID);
}
```

## 加點材質給物品
resource 路徑的檔案樹狀圖：
```
resource
└─ assets
   └─ <ModId>
      ├─ lang
      │  └─ en_us.json
      ├─ models
      │  └─ item
      │     └─ <itemId>.json
      └─ textures
         └─ item
            └─ <itemId>.png
```

基本的物品 json 可以這樣寫：
```json=
{
  "parent": "item/generated",
  "textures": {
    "layer0": "<ModName>:item/<itemId>"
  }
}
```

翻譯檔 lang 則是：
```json=
"item.<ModName>.<Id>": "<Name>"
```
***
## Tooltip

先 Override Item 固有的函數 `appendTooltip`
```java=
@Override
public void appendTooltip(ItemStack stack, @Nullable World world, List<Text> tooltip, TooltipContext context) {}
```

tooltip 就是我們要拿來操作的東西。加入程式碼：
```java=
tooltip.add(new LiteralText("Test Tooltip").formatted(Formatting.GREEN));
```
若有很多行，可重複 `tooltip.add()`

### Text
常用的 `Text` 類別有 `LiteralText` 以及 `TranslatableText`，此二皆繼承 `BaseText`。
- `BaseText` implement `MutableText` extends `Text`

用法：
```java=
Text.of("Hello World!");
```

可以加入函數 `formatted()` 將文字新增色彩與樣式：
```java=
Text.of("Red and Bold").formatted(Formatting.RED, Formatting.BOLD);
```

若想在一串字內有不同樣式或者不同種類的 Text，可用函數 `append()`
```java=
Text.of("Apple: ").append(
        new TranslatableText("item.minecraft.apple").formatted(Formatting.YELLOW));
```

## Item Bar

在自訂物品 class 中 override 三個函數：

```java=
public boolean isItemBarVisible(ItemStack stack) {
    return true;
}

public int getItemBarStep(ItemStack stack) {
    return Math.round((0.1) * 12);
}

public int getItemBarColor(ItemStack stack) {
    return MathHelper.packRgb(R, G, B);
}
```

`isItemBarVisible` 是決定 ItemBar 的顯示與否
`getItemBarStep` 則是決定每次改變要改變多少的 pixel
`getItemBarColor` 則是 ItemBar 的顏色

※特別注意：
1. `getItemBarStep` 傳回整數型態，須以 round 做轉換
2. `packRgb` 中的 RGB 需填入 0~1 的浮點數 (float)

## Item v.s. ItemStack

Item 是那一個物品，所有同樣的 Item 都長得一樣；
但 ItemStack 是指那一坨東西，他可以擁有自己的 NBT、等等性質。
***
## 其他常用函數

### useOnBlock
```java=
public ActionResult useOnBlock(ItemUsageContext context) {
    return ActionResult.PASS;
}
```

#### ActionResult

![](https://i.imgur.com/tGcbDh9.png =600x)

- PASS 允許物品在副手


### useOnEntity
```java=
public ActionResult useOnEntity(ItemStack stack, PlayerEntity user, LivingEntity entity, Hand hand) {
    return ActionResult.PASS;
}
```

### use
```java=
public TypedActionResult<ItemStack> use(World world, PlayerEntity user, Hand hand) {}
```

| TypedActionResult |
| ---------------------- |
| success(T data)        |
| consume(T data)        |
| pass(T data)           |
| fail(T data)           |

### onStackClicked
```java=
public boolean onStackClicked(ItemStack stack, Slot slot, ClickType clickType, PlayerEntity player) {}
```

### postHit & postMine
```java=
public boolean postHit(ItemStack stack, LivingEntity target, LivingEntity attacker) {}

public boolean postMine(ItemStack stack, World world, BlockState state, BlockPos pos, LivingEntity miner) {}
```

### finishUsing
```java=
public ItemStack finishUsing(ItemStack stack, World world, LivingEntity user) {}
```

### onCraft
```java=
public void onCraft(ItemStack stack, World world, PlayerEntity player) {}
```

### getEnchantbility
```java=
public int getEnchantability() {}
```
物品能付到好的附魔的程度

| Wood | Stone | Iron | Diamond | Gold |
| ---- | ----- | ---- | ------- | ---- |
| 15   | 5     | 14   | 10      | 22   |

### canRepair
```java=
public boolean canRepair(ItemStack stack, ItemStack ingredient) {}
```

***
## ItemGroup Tab

創建 `ItemGroup`：
```java=
public static final ItemGroup RUBY = FabricItemGroupBuilder.build(new Identifier(TestMod.MOD_ID, "ruby"),
        () -> new ItemStack(ModItems.RUBY));
```
其中，`ItemStack` 中的物品是 Tab 的標題物品。
再使用 `ModItemGroup.RUBY` 來代替 `ItemGroup.MISC` 即可。
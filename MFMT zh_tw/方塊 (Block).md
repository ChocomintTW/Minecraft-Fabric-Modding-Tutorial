# 方塊 (Block)

## 創建第一個方塊
方塊較為特別的部分就是我們需要有方塊的「物品型態」，因此我們需要另外創建一個 function 來創建方塊的物品。

一樣在模組路徑下的 `block` 資料夾下加入 `ModBlocks.java`：
```java=
private static Block registerBlock(String name, Block block) {
    registerBlockItem(name, block);
    return Registry.register(Registry.BLOCK, new Identifier(TestMod.MOD_ID, name), block);
}

private static Item registerBlockItem(String name, Block block) {
    return Registry.register(Registry.ITEM, new Identifier(TestMod.MOD_ID, name),
        new BlockItem(block, new FabricItemSettings().group(ItemGroup.MISC)));
}
```

註冊方塊：
```java=
public static final Block RUBY_ORE = registerBlock("ruby_ore",
    new Block(FabricBlockSettings.of(Material.STONE).strength(4.0f)
        .breakByTool(FabricToolTags.PICKAXES, 2).requiresTool()));
```
其中，`breakByTool` 的第二個參數是工具等級，對照如下：
```
0 -> Wooden / Golden Pickaxe
1 -> Stone Pickaxe
2 -> Iron Pickaxe
3 -> Diamond Pickaxe
4 -> Netherite Pickaxe
```
常用 Settings：
```
of(Material.<material>)                     - 相當於<material>的材質
strength(<sec>f)                            - 挖掘時間
breakByTool(FabricToolTags.<tool>, <level>) - 挖掘工具
requiresTool()                              - 必須使用工具
```

## 加點材質給方塊

檔案架構：
```
resource
└─ assets
   └─ <ModId>
      ├─ blockstates
      │  └─ <blockId>.json
      ├─ lang
      │  └─ en_us.json
      ├─ models
      │  ├─ block
      │  │  └─ <blockId>.json
      │  └─ item
      │     └─ <blockId>.json
      └─ textures
         └─ block
            └─ <blockId>.png
```

```json=
// blockstates/<blockId>.json
{
  "variants": {
    "": { "model": "testmod:block/ruby_ore" }
  }
}
```

```json=
// models/block/<blockId>.json
{
  "parent": "block/cube_all",
  "textures": {
    "all": "testmod:block/ruby_ore"
  }
}
```

```json=
// models/item/<blockId>.json
{
  "parent": "testmod:block/ruby_ore"
}
```

`blockstates` 主要是在描述一個方塊的六個面要用哪些 `models`；而 `models` 的檔案則是描述要從 `png` 檔取哪一個部分。

[Block Models 教學](https://www.youtube.com/watch?v=3lECPqoSNeg&ab_channel=UncleJam)
[神的工具：Block Bench](https://www.blockbench.net/)
[進階方塊](https://hackmd.io/@Chocomint/FabricModding-Block)

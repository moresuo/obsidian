---
aliases:
  - 样式调整
---
[[obsidian]] [[外观]] [[样式]] [[菜有隐]] [[教程]]

# Obsidian 样式微调 - 中级
:rif_star: :rif_star: :rif_star: :obs_star: :obs_star: 
**本期是对采用的主题中部分样式进行微调** - 重点是**如何调整**，后续可以结合自己的喜好需求进行调整。

> 涉及模块：设置，主题设置，自定义 css
## CSS 设置示例
```css
body{
/*callout 标题大小*/
  --callout-title-size: 1em;
/*标题颜色，h1指一级标题，*/
  --h1-color: [[cace30]];
  --h2-color: [[a790e8]];
  --h3-color: [[29AF92]];
  --h4-color: [[8BD881]];
/*内外部链接颜色*/
  --link-color: [[D980A0]];
  --link-external-color: [[D980A0]];
  /* 去掉链接下划线 */
  --link-external-decoration: none;
/* 斜体粗体颜色*/
  --italic-color: [[CCC6F5]];
  --bold-color: [[D4995F]];
}
/* 内部链接字体加粗*/
.internal-link{
  font-weight: bold;
}
/* 外部链接字体加粗*/
.external-link{
  font-weight: bold;
}

```

## 附录
> [!info] 好用的配色工具
> [space 配色网站](https://mycolor.space/) - 根据给定的颜色，生成调色板
[adobe 配色网站](https://color.adobe.com/) - 工具集（协助工具里的对比检查器可以调整颜色对比度，对比度高则笔记越重点）
[colors](https://coolors.co/palettes/trending) - 流行的调色板，配色集 

> [!link]  授人以鱼不如授人以渔
> 
>
[黑曜石官方帮助文档](https://help.obsidian.md/) - 黑曜石的使用
[黑曜石官方开发者文档](https://docs.obsidian.md/Reference/CSS+variables/CSS+variables) - 面对开发者的文档
**插件的开发者文档** - 插件的使用（需要梯子）

> [!tip] 轻量级编辑器推荐，编辑 css 文件
> [ ATOM](https://atom-editor.cc/) | [Sublime](https://www.sublimetext.com/)

> [!hint] 截屏|取色|贴图小工具推荐
> [Snipaste](https://www.snipaste.com/)

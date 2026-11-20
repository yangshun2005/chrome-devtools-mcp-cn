# 无障碍调试代码片段

配合 `evaluate_script` 工具使用这些 JavaScript 代码片段。

## 1. 查找孤立表单输入

查找缺少关联标签的表单输入（无 `label[for]`、`aria-label`、`aria-labelledby` 或包裹的 `<label>`）。

```js
() =>
  Array.from(document.querySelectorAll('input, select, textarea'))
    .filter(i => {
      const hasId = i.id && document.querySelector(`label[for="${i.id}"]`);
      const hasAria =
        i.getAttribute('aria-label') || i.getAttribute('aria-labelledby');
      return !hasId && !hasAria && !i.closest('label');
    })
    .map(i => ({
      tag: i.tagName,
      id: i.id,
      name: i.name,
      placeholder: i.placeholder,
    }));
```

## 2. 测量点击目标尺寸

返回元素的边界框尺寸。将快照中元素的 `uid` 作为参数传给 `evaluate_script`。

```js
el => {
  const rect = el.getBoundingClientRect();
  return {width: rect.width, height: rect.height};
};
```

## 3. 检查颜色对比度

估算元素文字颜色与背景色之间的对比度。传入元素的 `uid`，按 WCAG AA 标准检测（普通文本 4.5:1，大号文本 3:1）。

**注意**：此处使用简化算法，可能无法处理透明度、渐变或背景图片。生产级审计建议注入 `axe-core`。

```js
el => {
  function getRGB(colorStr) {
    const match = colorStr.match(/rgba?\((\d+),\s*(\d+),\s*(\d+)/);
    return match
      ? [parseInt(match[1]), parseInt(match[2]), parseInt(match[3])]
      : [255, 255, 255];
  }
  function luminance(r, g, b) {
    const a = [r, g, b].map(function (v) {
      v /= 255;
      return v <= 0.03928 ? v / 12.92 : Math.pow((v + 0.055) / 1.055, 2.4);
    });
    return a[0] * 0.2126 + a[1] * 0.7152 + a[2] * 0.0722;
  }

  const style = window.getComputedStyle(el);
  const fg = getRGB(style.color);
  let bg = getRGB(style.backgroundColor);

  const l1 = luminance(fg[0], fg[1], fg[2]);
  const l2 = luminance(bg[0], bg[1], bg[2]);
  const ratio = (Math.max(l1, l2) + 0.05) / (Math.min(l1, l2) + 0.05);

  return {
    color: style.color,
    bg: style.backgroundColor,
    contrastRatio: ratio.toFixed(2),
  };
};
```

## 4. 全局页面检查

检查组件测试中常被忽略的文档级无障碍设置。

```js
() => ({
  lang:
    document.documentElement.lang ||
    'MISSING - Screen readers need this for pronunciation',
  title: document.title || 'MISSING - Required for context',
  viewport:
    document.querySelector('meta[name="viewport"]')?.content ||
    'MISSING - Check for user-scalable=no (bad practice)',
  reducedMotion: window.matchMedia('(prefers-reduced-motion: reduce)').matches
    ? 'Enabled'
    : 'Disabled',
});
```
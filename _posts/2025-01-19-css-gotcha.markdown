---
layout: post
title:  "CSS 的那些，切版時容易讓全端工程師踩進去的雷（持續更新）"
date:   2025-01-19 13:00:00 +0800
categories: 前端開發
---

所以身為全端，我會切版，但速度畢竟不如專業的前端工程師快。最近閒賦在家，好好來面對一下最頭疼的東西：CSS。這篇文章是我近期在「重修」CSS 的筆記（供自己回憶用的），紀錄著那些「隱晦」的... 可以說是踩雷點嗎？還有一些有助於未來在切版（或解決破版）時有用的概念。

其實，CSS 認真學，發現還滿有趣的，它的邏輯看似直觀，但實際切版、做效果時，會有點腦筋急轉彎的感覺，具體來說，工程師所掌握的是一堆「規則」，對於一些有挑戰性的切版，則要有足夠「直覺」去挑選哪些規格可以達到目的。

在我的感覺，寫 CSS 的思維方式，跟寫 code 是很不一樣的！

以下筆記開始，開始由淺入深，持續增加中。

### Background Color

- 使用 “background-color: green; opacity: 0.3” 會連元件的文字一起變透明。
- 使用 “background-color: rgba(0, 128, 0, 0.3) 則不會影響文字。
- https://www.w3schools.com/css/tryit.asp?filename=trycss_background_opacity2

### Border

- 沒有提供 border-style，任何的 border properties 設定都不會產生效果。
- https://www.w3schools.com/css/css_border.asp

### Margin

- Margin Collapse（邊距坍縮）
- 設兩垂直相鄰元件A與B，A在上，B在下，A 的 `margin-bottom: 50px`，B 的 `margin-bottom: 20px`，則兩個元件顯示時實際 margin 將會是兩者其中的最大值 50px。
- 邊距坍縮僅會發生在垂直相鄰的狀況，水平相鄰不會。
- 坍縮也會發生在父子元素，例如：當父元素沒有 border，子元素設定 margin-top 時，此 margin 將會超出父元素範圍，一但設定了 border，子元素的 margin-top 就會落在父元素內。
- https://www.w3schools.com/css/css_margin_collapse.asp

### Height and Width

- 此 Properties 不包含 padding, borders, margin 的大小喔！
- 它指的是 Box Model 最內層的那個區域，也就是 Content！
- width 設為 500px 時，當瀏覽器視窗寬度小於其時，會出現水平卷軸，這是因為此元件的寬度永遠固定是 500px 的緣故。
- 當 `max-width: 500px`，表示此元件最寬為 500px，當視窗小於此值時，寬度即會隨著視窗變窄。
- 同樣的，當 `min-width: 100px` 時，當視窗小於此值時，捲軸就會出現。
- https://www.w3schools.com/css/css_dimension.asp

### Box Model

- 元件的總寬度 = width + 左 padding + 右 padding + 左 border + 右 border
- 注意，Box Model 定義上的總寬度不包含 Margin，也因此才有所謂的 Margin Collapse 現象，Margin 寬度將以相鄰兩元件的 Margin 為最大者來決定！

### Shorthand Property

- 不管是 border-width, border-color 還是 margin… 都可以將四邊的設定寫在同一行，遵循順時鐘順序，由 12 點鐘方向開始，上、右、下、左的順序指定喔。
- 例：margin: 上, 右, 下, 左。

### Outline

- 包圍在 border 外面，像 border 一樣可以指定 style、width、color 甚至是 offset！但是，元件總寬度並不將它算在內！也就是說，outline 有可能會覆蓋到旁邊的元件！
- 與 border 一樣，如果沒有設定 style，所有的設定都不會生效喔！

### Font Size

- Absolute size
    - 直接指定 size。
    - 使用者的瀏覽器沒辦法修改大小，會造成無障礙使用性的問題。
    - 較適用於已知的螢幕物理大小。
- Relative size
    - 大小基於外覆物件的相對比例。
    - 允許使用者透過瀏覽器調整。

### Display

- Inline 元素雖然可以透過 `display: black;` 改變其顯示方式，但不會改變其為 inline 的性質，換句話說，它仍舊無法內含 bloack 元素。
- `display: none;` 與 `visibility: hidden;` 都可以隱藏元素，但使用 `visibility` 可讓隱藏元素仍舊佔據 layout 空間，使用 `none` 則否，且會影響畫面元素原本的位置排列。

### Overflow

- 白話來說，就是在內容「溢出」元素顯示的範圍時，該怎麼處理。
- overflow 只有在元素被指定高度時，設定才會有效。
- 使用 `<ul>` 內含 `li { float: left }` 來製作 Navigation 時，會發生 `<ul>` 沒有顯示出來，導致畫面不正常：
    - 在使用 `<ul>` 製作 Navigation 時，將其設定 `overflow: hidden;` 或其他類似的 `overflow` 屬性（如 `auto` 或 `scroll`），會觸發 **Block Formatting Context (BFC)。**
    - BFC 是 CSS 中的一種布局機制，啟用了 BFC 的元素會包含內部的**浮動元素**，從而讓父容器能正確計算高度。
    - 當你在 `<ul>` 上加上 `overflow: hidden;` 時：
        1. `<ul>` 形成一個 BFC。
        2. BFC 會自動擴展，包含它內部的所有浮動子元素（即 `<li>`）。
        3. 這樣 `<ul>` 的高度就能涵蓋所有的 `<li>`，不再是 0px，因此 `<ul>` 可以正常顯示背景色。

### Pseudo-classes

```css
p {
  display: none;
  background-color: yellow;
  padding: 20px;
}

/* 滑鼠 hover div 時，顯示 p */
div:hover p {
  display: block;
}
```

### 置中的幾種策略

https://www.w3schools.com/css/css_align.asp

- 情境：

```html
<div>
  <span class="big-text">大字</span>
  <span class="small-text">小字</span>
</div>
```

```css
.big-text {
  font-size: 2.0rem;
  vertical-align: middle;
 }

.small-text {
  font-size: 0.8rem;
  vertical-align: middle;
}
```

上面的狀況，由於兩個 `<span>` 的 font-size 不同，預設 vertical-align 是以父元件的文字**基線(baseline)**水平排列文字，所以視覺上看起來會無法水平置中，有時候會很怪。

此時設定 `vertical-align: middle` 可解決，或是 `<div style="display: flex; align-item: center;">` 也可以解決。

### 擬類別

- nth-of-child：不管是 `<p>` 或是 `<h1>`，純粹針對第幾個子元素。
- nth-of-type：針對同階層「同類型」的子元素。
- sample.nth-of-type：雖然不同類型但是相同 class，仍然針對「同類型」做挑選。

### line-height

行高可接受三種寫法：

- 數值＋單位：1px, 2rem, 3em
- 比例：**150%**，此寫法行高的計算，會以**從父元素設定的 font-size** 為基準計算。
- 數值：**1.5**，此寫法行高的計算，會以**元素自己的 font-size** 為基準計算。

```html
<div class="content">
  <h1>外觀。<br>外觀。</h1>
  <p>本文。<br>本文。</p>
</div>
```

```css
/* 數值＋單位 */
.content {
  font-size: 10px;
  line-height: 15px; /* 行高設定 15px */
}
h1 { font-size: 24px; } /* 實際行高為 15px */
p { font-size: 14px; } /* 實際行高為 15px; */

/* 比例 */
.content {
  font-size: 10px;
  line-height: 150%; /* 行高設定 10px X 150% = 15px */
}
h1 { font-size: 24px; } /* 實際行高 10px X 150% = 15px */
p { font-size: 14px; } /* 實際行高 10px X 150% = 15px */

/* 數值 */
.content {
  font-size: 10px;
  line-height: 1.5; /* 行高設定 10px X 1.5 = 15px */
}
h1 { font-size: 24px; } /* 實際行高 24px X 1.5 = 36px */
p { font-size: 14px; } /* 實際行高 14px X 1.5 = 21px */
```

### Inherit

- 所有的 CSS property 都可以設定其值為 inherit，表示「此值繼承自父元素」。

# Emotion

## Emotionの導入

```shell
yarn add @emotion/react
yarn add -D @emotion/babel-preset-css-prop
```

### ライブラリ説明

- @emotion/react
  - Reactで使用するemotionライブラリ
  - React, Vue, Angularなどライブラリに依存させない場合は、@emotion/cssを使用する
- @emotion/babel-preset-css-prop
  - React 17で登場した[JSXトランスフォーム](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html)を使用する際に、EmotionのCSS Propを有効にするためのBabel

### 設定

- .babelrcの設定

```json
{
  "presets": ["next/babel", "@emotion/babel-preset-css-prop"]
}
```

::: tip

- @emotion/babel-preset-css-propではなく、下記のライブラリを使用してもstyleが反映されるのを確認
- babelの設定をしていない場合は、styleが反映されない

```shell
yarn add -D @babel/preset-react
```

- babelrcの設定

```json
{
  "presets": [
    [
      "next/babel",
      {
        "preset-react": {
          "runtime": "automatic",
          "importSource": "@emotion/react"
        }
      }
    ]
  ]
}
```
:::

## Theme

Styled Component同様に`ThemeProvider`が使えるため、ThemeProviderを使用して`Theme`を流す

```tsx
import React from "react";
import { AppProps } from "next/app";
import { theme } from "./theme";

const App = ({Component, pageProps}: AppProps) => {
  return (
    <!-- themeを流す -->
    <ThemeProvider theme={theme}>
      {Component {...pageProps}}
    </ThemeProvider>
  )
}
```

### Themeの型付け

themeの型が効くようにします

```ts
import '@emotion/react';
import { theme } from '@/styles/designToken/theme';

type CustomTheme = typeof theme;
declare module '@emotion/react' {
  // eslint-disable-next-line
  export interface Theme extends CustomTheme {}
}
```

## CSS Props

オブジェクト形式でスタイルを書くことができます

```tsx
import React from "react";
import {css, Theme} from "@emotion/react";

const style = (theme: Theme) => css`
  color: ${theme.colors.red}
`;

const Text: React.FC = ({children}) => (
  /* themeが流れてくる */
  <p css={style}>{children}</p>
);
```

### style内で状態を切り替える

```tsx
import React from "react";
import {css, Theme} from "@emotion/react";

type Size = 'sm' | 'md' | 'lg';

// ベースのスタイル
const baseStyle = (theme: Theme) => css`
  display: inline-block;
  cursor: pointer;
  padding: 0.75rem 1.15rem;
  font-size: ${theme.fontSizes.md};
  margin: 0.15rem;
  background-color: ${theme.colors.white};
  border-radius: ${theme.borderRadius.sm};
  border: 1px solid ${theme.colors.lightGray};
  outline: none;
`;

const style = ({
  theme,
  size = 'md'
}: {
  theme: Theme,
  size: Size
}) => {
  const base = baseStyle(theme)
  // small sizeのスタイル
  if (size.sm) {
    return css`
      font-size: ${theme.fontSizes.sm};
      ${base};
    `
  }

  // large sizeのスタイル
  if (size.lg) {
    return css`
      font-size: ${theme.fontSizes.lg};
      ${base};
    `
  }

  // 標準(medium size)のスタイル
  return css`
    ${base};
  `
}

interface Props {
  size: Size,
  isDisabled?: boolean;
  isLoading?: boolean;
}

const Button: React.FC<Props> = ({children, size}) => (
  <button 
    disabled={isDisabled || isLoading}
    css={theme => style({theme, size})}
  >
    {children}
  </button>
)
```

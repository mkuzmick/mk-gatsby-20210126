### MDX LAYOUT WITH COMPONENTS

- let's also add custom components to mdx following the pattern found [here](https://theme-ui.com/mdx-components).

  ```
  /** @jsx jsx */
  import { jsx, ThemeProvider } from 'theme-ui'
  import theme from './theme'
  import components from './components'
  export default props => (
  <ThemeProvider theme={theme} components={components}>
      {props.children}
  </ThemeProvider>
  )
  ```

- then a test file in `pages` called `mdx.mdx` that will be [here](http://localhost:8000/mdx)

  ```
  ---
  title: "MDX with Components 001"
  date: 2020-01-16
  ---

  # MY MDX WITH COMPONENTS
  content goes here
  ![Gatsby](../../content/images/gatsby.jpg)


  ```

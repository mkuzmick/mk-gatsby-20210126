### ADD FONTS

- npm install @fontsource/prompt
  ```
  npm install @fontsource/crimson-pro @fontsource/lato
  ```

* then add the import statements to your theme
  ```
  import '@fontsource/crimson-pro/500.css';
  import '@fontsource/crimson-pro/400.css';
  import '@fontsource/lato/400.css';
  ```
* then add these fonts and reference them in your styles
  ```
  fonts: {
      body: "Crimson Pro, sans-serif",
      heading: "Crimson Pro, serif",
      monospace: "Menlo, monospace",
  },
  styles: {
      h1: {
      fontFamily: "heading",
      // etc.
      }
  }
  ```

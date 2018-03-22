# Руководство по написанию CSS-in-JavaScript кода от [Airbnb](https://github.com/airbnb/javascript/tree/master/css-in-javascript)

*Наиболее разумный подход к написанию CSS-in-JavaScript кода*

## Оглавление

1. [Именование](#naming)
1. [Порядок](#ordering)
1. [Вложенность](#nesting)
1. [Inline](#inline)
1. [Themes](#themes)

## Именование

  - Используйте верблюжийРегистр (`camelCase`) для ключей объекта (т.е. "селекторы").

    > Почему? Мы получаем доступ к этим ключам, как свойствам объекта `styles` в компоненте, поэтому удобнее использовать верблюжийРегистр.

    ```js
    // плохо
    {
      'bermuda-triangle': {
        display: 'none',
      },
    }

    // хорошо
    {
      bermudaTriangle: {
        display: 'none',
      },
    }
    ```

  - Используйте подчеркивание для модификаторов других стилей.

    > Почему? Подобно БЭМ, это соглашение дает понять, что стили предназначены для модификации элемента, которому предшествует символ подчеркивания. Подчеркивания не надо брать в кавычки, поэтому они предпочтительнее других символов, таких как тире.

    ```js
    // плохо
    {
      bruceBanner: {
        color: 'pink',
        transition: 'color 10s',
      },

      bruceBannerTheHulk: {
        color: 'green',
      },
    }

    // хорошо
    {
      bruceBanner: {
        color: 'pink',
        transition: 'color 10s',
      },

      bruceBanner_theHulk: {
        color: 'green',
      },
    }
    ```

  - Используйте `selectorName_fallback` для набора резервных стилей.

    > Почему? Подобно модификаторам, сохранение солгасованности имен помогает обнаружить взаимосвязь этих стилей со стилями, которые переопределяют их в подходящих браузерах.

    ```js
    // плохо
    {
      muscles: {
        display: 'flex',
      },

      muscles_sadBears: {
        width: '100%',
      },
    }

    // хорошо
    {
      muscles: {
        display: 'flex',
      },

      muscles_fallback: {
        width: '100%',
      },
    }
    ```

  - Используйте отдельный селектор для наборов резервных стилей.

    > Почему? Сохранение резервных стилей, содержащихся в отдельном объекте, разъясняет их цель, что улучшает читаемость.

    ```js
    // плохо
    {
      muscles: {
        display: 'flex',
      },

      left: {
        flexGrow: 1,
        display: 'inline-block',
      },

      right: {
        display: 'inline-block',
      },
    }

    // хорошо
    {
      muscles: {
        display: 'flex',
      },

      left: {
        flexGrow: 1,
      },

      left_fallback: {
        display: 'inline-block',
      },

      right_fallback: {
        display: 'inline-block',
      },
    }
    ```

  - Используйте абстрактные имена (такие как "small", "medium" и "large"), чтобы назвать контрольные точки медиа выражений.

    > Почему? Обычно используемые имена, такие как "phone", "tablet" и "desktop" не соответствуют характеристикам устройств в реальности. Использование этих имен задает неправильные ожидания.

    ```js
    // плохо
    const breakpoints = {
      mobile: '@media (max-width: 639px)',
      tablet: '@media (max-width: 1047px)',
      desktop: '@media (min-width: 1048px)',
    };

    // хорошо
    const breakpoints = {
      small: '@media (max-width: 639px)',
      medium: '@media (max-width: 1047px)',
      large: '@media (min-width: 1048px)',
    };
    ```

**[⬆ к оглавлению](#Оглавление)**

## Порядок

  - Определяйте стили после компонента.

    > Почему? Мы используем компонент высшего порядка для темизации наших стилей, которые естественно используются после определения компонента. Передача объекта стилей непосредственно в функции уменьшает косвенность.

    ```jsx
    // плохо
    const styles = {
      container: {
        display: 'inline-block',
      },
    };

    function MyComponent({ styles }) {
      return (
        <div {...css(styles.container)}>
          Never doubt that a small group of thoughtful, committed citizens can
          change the world. Indeed, it’s the only thing that ever has.
        </div>
      );
    }

    export default withStyles(() => styles)(MyComponent);

    // хорошо
    function MyComponent({ styles }) {
      return (
        <div {...css(styles.container)}>
          Never doubt that a small group of thoughtful, committed citizens can
          change the world. Indeed, it’s the only thing that ever has.
        </div>
      );
    }

    export default withStyles(() => ({
      container: {
        display: 'inline-block',
      },
    }))(MyComponent);
    ```
 
**[⬆ к оглавлению](#Оглавление)**

## Вложенность

  - Оставляйте пустую строку между соседними блоками на одном и том же уровне отступов.

    > Почему? Пробельное пространство улучшает читаемость и уменьшает вероятность конфликтов при слиянии.

    ```js
    // плохо
    {
      bigBang: {
        display: 'inline-block',
        '::before': {
          content: "''",
        },
      },
      universe: {
        border: 'none',
      },
    }

    // хорошо
    {
      bigBang: {
        display: 'inline-block',

        '::before': {
          content: "''",
        },
      },

      universe: {
        border: 'none',
      },
    }
    ```
 
**[⬆ к оглавлению](#Оглавление)**

## Inline

  - Use inline styles for styles that have a high cardinality (e.g. uses the value of a prop) and not for styles that have a low cardinality.

    > Why? Generating themed stylesheets can be expensive, so they are best for discrete sets of styles.

    ```jsx
    // bad
    export default function MyComponent({ spacing }) {
      return (
        <div style={{ display: 'table', margin: spacing }} />
      );
    }

    // good
    function MyComponent({ styles, spacing }) {
      return (
        <div {...css(styles.periodic, { margin: spacing })} />
      );
    }
    export default withStyles(() => ({
      periodic: {
        display: 'table',
      },
    }))(MyComponent);
    ```

## Themes

  - Use an abstraction layer such as [react-with-styles](https://github.com/airbnb/react-with-styles) that enables theming. *react-with-styles gives us things like `withStyles()`, `ThemedStyleSheet`, and `css()` which are used in some of the examples in this document.*

  > Why? It is useful to have a set of shared variables for styling your components. Using an abstraction layer makes this more convenient. Additionally, this can help prevent your components from being tightly coupled to any particular underlying implementation, which gives you more freedom.

  - Define colors only in themes.

    ```js
    // bad
    export default withStyles(() => ({
      chuckNorris: {
        color: '#bada55',
      },
    }))(MyComponent);

    // good
    export default withStyles(({ color }) => ({
      chuckNorris: {
        color: color.badass,
      },
    }))(MyComponent);
    ```

  - Define fonts only in themes.

    ```js
    // bad
    export default withStyles(() => ({
      towerOfPisa: {
        fontStyle: 'italic',
      },
    }))(MyComponent);

    // good
    export default withStyles(({ font }) => ({
      towerOfPisa: {
        fontStyle: font.italic,
      },
    }))(MyComponent);
    ```

  - Define fonts as sets of related styles.

    ```js
    // bad
    export default withStyles(() => ({
      towerOfPisa: {
        fontFamily: 'Italiana, "Times New Roman", serif',
        fontSize: '2em',
        fontStyle: 'italic',
        lineHeight: 1.5,
      },
    }))(MyComponent);

    // good
    export default withStyles(({ font }) => ({
      towerOfPisa: {
        ...font.italian,
      },
    }))(MyComponent);
    ```

  - Define base grid units in theme (either as a value or a function that takes a multiplier).

    ```js
    // bad
    export default withStyles(() => ({
      rip: {
        bottom: '-6912px', // 6 feet
      },
    }))(MyComponent);

    // good
    export default withStyles(({ units }) => ({
      rip: {
        bottom: units(864), // 6 feet, assuming our unit is 8px
      },
    }))(MyComponent);

    // good
    export default withStyles(({ unit }) => ({
      rip: {
        bottom: 864 * unit, // 6 feet, assuming our unit is 8px
      },
    }))(MyComponent);
    ```

  - Define media queries only in themes.

    ```js
    // bad
    export default withStyles(() => ({
      container: {
        width: '100%',

        '@media (max-width: 1047px)': {
          width: '50%',
        },
      },
    }))(MyComponent);

    // good
    export default withStyles(({ breakpoint }) => ({
      container: {
        width: '100%',

        [breakpoint.medium]: {
          width: '50%',
        },
      },
    }))(MyComponent);
    ```

  - Define tricky fallback properties in themes.

    > Why? Many CSS-in-JavaScript implementations merge style objects together which makes specifying fallbacks for the same property (e.g. `display`) a little tricky. To keep the approach unified, put these fallbacks in the theme.

    ```js
    // bad
    export default withStyles(() => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        'display ': 'table',
      },
    }))(MyComponent);

    // good
    export default withStyles(({ fallbacks }) => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        [fallbacks.display]: 'table',
      },
    }))(MyComponent);

    // good
    export default withStyles(({ fallback }) => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        [fallback('display')]: 'table',
      },
    }))(MyComponent);
    ```

  - Create as few custom themes as possible. Many applications may only have one theme.

  - Namespace custom theme settings under a nested object with a unique and descriptive key.

    ```js
    // bad
    ThemedStyleSheet.registerTheme('mySection', {
      mySectionPrimaryColor: 'green',
    });

    // good
    ThemedStyleSheet.registerTheme('mySection', {
      mySection: {
        primaryColor: 'green',
      },
    });
    ```

---

CSS puns adapted from [Saijo George](https://saijogeorge.com/css-puns/).

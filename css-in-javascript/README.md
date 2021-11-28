# Руководство по написанию CSS-in-JavaScript кода от [Airbnb](https://github.com/airbnb/javascript/tree/master/css-in-javascript)

*Наиболее разумный подход к написанию CSS-in-JavaScript кода*

## Оглавление

1. [Именование](#naming)
1. [Порядок](#ordering)
1. [Вложенность](#nesting)
1. [Inline](#inline)
1. [Темы](#themes)

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

  - Используйте подчёркивание для модификаторов других стилей.

    > Почему? Подобно БЭМ, это соглашение даёт понять, что стили предназначены для модификации элемента, которому предшествует символ подчёркивания. Подчёркивания не надо брать в кавычки, поэтому они предпочтительнее других символов, таких как тире.

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

    > Почему? Подобно модификаторам, сохранение согласованности имён помогает обнаружить взаимосвязь этих стилей со стилями, которые переопределяют их в подходящих браузерах.

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

  - Используйте абстрактные имена (такие как "small", "medium" и "large"), чтобы назвать контрольные точки медиавыражений.

    > Почему? Обычно используемые имена, такие как "phone", "tablet" и "desktop" не соответствуют характеристикам устройств в реальности. Использование этих имён задаёт неправильные ожидания.

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

  - Используйте inline-стили там, где стили изменяют свои значения (например, через свойства (props)).

    > Почему? Создание темизированных таблиц стилей является дорогостоящей операцией, поэтому они лучше всего подходят для дискретных наборов стилей.

    ```jsx
    // плохо
    export default function MyComponent({ spacing }) {
      return (
        <div style={{ display: 'table', margin: spacing }} />
      );
    }

    // хорошо
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

## Темы

  - Используйте абстрактный слой, такой как [react-with-styles](https://github.com/airbnb/react-with-styles), чтобы включить темизацию. *react-with-styles предоставляет такие инструменты, как `withStyles()`, `ThemedStyleSheet` и `css()`, которые используются в некоторых примерах в этой документации.*

  > Почему? Полезно иметь набор общих переменных для стилизации компонентов. Использование слоя абстракции делает это более удобным. К тому же, это может помочь предотвратить тесную связь компонентов с какой-либо конкретной базовой реализацией, что даёт вам больше свободы.

  - Определяйте цвета только в темах.

    ```js
    // плохо
    export default withStyles(() => ({
      chuckNorris: {
        color: '#bada55',
      },
    }))(MyComponent);

    // хорошо
    export default withStyles(({ color }) => ({
      chuckNorris: {
        color: color.badass,
      },
    }))(MyComponent);
    ```

  - Определяйте шрифты только в темах.

    ```js
    // плохо
    export default withStyles(() => ({
      towerOfPisa: {
        fontStyle: 'italic',
      },
    }))(MyComponent);

    // хорошо
    export default withStyles(({ font }) => ({
      towerOfPisa: {
        fontStyle: font.italic,
      },
    }))(MyComponent);
    ```

  - Определяйте шрифты как наборы связанных стилей.

    ```js
    // плохо
    export default withStyles(() => ({
      towerOfPisa: {
        fontFamily: 'Italiana, "Times New Roman", serif',
        fontSize: '2em',
        fontStyle: 'italic',
        lineHeight: 1.5,
      },
    }))(MyComponent);

    // хорошо
    export default withStyles(({ font }) => ({
      towerOfPisa: {
        ...font.italian,
      },
    }))(MyComponent);
    ```

  - Определяйте базовые единицы сетки в теме (либо как значение, либо как функцию, которая принимает множитель).

    ```js
    // плохо
    export default withStyles(() => ({
      rip: {
        bottom: '-6912px', // 6 футов
      },
    }))(MyComponent);

    // хорошо
    export default withStyles(({ units }) => ({
      rip: {
        bottom: units(864), // 6 футов, предполагая, что наш блок 8px
      },
    }))(MyComponent);

    // хорошо
    export default withStyles(({ unit }) => ({
      rip: {
        bottom: 864 * unit, // 6 футов, предполагая, что наш блок 8px
      },
    }))(MyComponent);
    ```

  - Определяйте медиазапросы (media queries) только в темах.

    ```js
    // плохо
    export default withStyles(() => ({
      container: {
        width: '100%',

        '@media (max-width: 1047px)': {
          width: '50%',
        },
      },
    }))(MyComponent);

    // хорошо
    export default withStyles(({ breakpoint }) => ({
      container: {
        width: '100%',

        [breakpoint.medium]: {
          width: '50%',
        },
      },
    }))(MyComponent);
    ```

  - Определяйте сложные фолбэки в темах.

    > Почему? Многие реализации CSS-in-JavaScript объединяют стили вместе, что делает определение фолбэка для одного и того же свойства (например, `display`) немного сложным. Чтобы сохранить подход единым, поместите эти фолбэки в тему.

    ```js
    // плохо
    export default withStyles(() => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        'display ': 'table',
      },
    }))(MyComponent);

    // хорошо
    export default withStyles(({ fallbacks }) => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        [fallbacks.display]: 'table',
      },
    }))(MyComponent);

    // хорошо
    export default withStyles(({ fallback }) => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        [fallback('display')]: 'table',
      },
    }))(MyComponent);
    ```

  - Создайте как можно меньше пользовательских тем. Многие приложения могут иметь только одну тему.

  - Пространство имён темы во вложенном объекте должно содержать уникальный и наглядный ключ.

    ```js
    // плохо
    ThemedStyleSheet.registerTheme('mySection', {
      mySectionPrimaryColor: 'green',
    });

    // хорошо
    ThemedStyleSheet.registerTheme('mySection', {
      mySection: {
        primaryColor: 'green',
      },
    });
    ```

---

CSS каламбуры взяты из [Saijo George](https://saijogeorge.com/css-puns/).

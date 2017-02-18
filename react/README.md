# Руководство по написанию React/JSX кода от [Airbnb](https://github.com/airbnb/javascript/react)

*В основном разумный подход к написанию React и JSX*

## Оглавление

  1. [Основные правила](#basic-rules)
  1. [Class vs `React.createClass` vs stateless](#class-vs-reactcreateclass-vs-stateless)
  1. [Именование](#naming)
  1. [Declaration](#declaration)
  1. [Выравнивание](#alignment)
  1. [Кавычки](#quotes)
  1. [Пробелы](#spacing)
  1. [Props](#props)
  1. [Refs](#refs)
  1. [Parentheses](#parentheses)
  1. [Tags](#tags)
  1. [Methods](#methods)
  1. [Ordering](#ordering)
  1. [`isMounted`](#ismounted)

## <a name="basic-rules">Основные правила</a>

  - Включайте только один React компонент в файл.
    - Однако, разрешается несколько [Без состояний (чистых) компонентов](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions) в файле. eslint: [`react/no-multi-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless).
  - Всегда используйте JSX синтаксис.
  - Не используйте `React.createElement`, если вы только не инициализируете программу из файла, который не является JSX.

## Class против `React.createClass` против компонента без состояния (stateless)

  - Если у вас есть внутреннее состояние (`state`) и/или ссылки (`refs`), отдавайте предпочтение `class extends React.Component` вместо `React.createClass`, если у вас нет веских причин использовать миксины. eslint: [`react/prefer-es6-class`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md) [`react/prefer-stateless-function`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)

    ```jsx
    // плохо
    const Listing = React.createClass({
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    });

    // хорошо
    class Listing extends React.Component {
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    }
    ```

    И если у вас нет состояния (`state`) или ссылок (`refs`), отдавайте предпочтение нормальным функциям (не стрелочным функциям), а не классам:

    ```jsx
    // плохо
    class Listing extends React.Component {
      render() {
        return <div>{this.props.hello}</div>;
      }
    }

    // плохо (не рекомендуется делать выводы, опираясь на название функции)
    const Listing = ({ hello }) => (
      <div>{hello}</div>
    );

    // хорошо
    function Listing({ hello }) {
      return <div>{hello}</div>;
    }
    ```

## <a name="naming">Именование</a>

  - **Расширения**: Используйте расширение `.jsx` для компонентов React.
  - **Имя файла**: Используйте `PascalCase` для названий файлов, например, `ReservationCard.jsx`.
  - **Именование переменной**: Используйте `PascalCase` для компонентов React и `camelCase` для их экземпляров. eslint: [`react/jsx-pascal-case`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-pascal-case.md)

    ```jsx
    // плохо
    import reservationCard from './ReservationCard';

    // хорошо
    import ReservationCard from './ReservationCard';

    // плохо
    const ReservationItem = <ReservationCard />;

    // хорошо
    const reservationItem = <ReservationCard />;
    ```

  - **Именование компонента**: Называйте файлы также как и компоненты. Например, `ReservationCard.jsx` должен содержать внутри компонент `ReservationCard`. Однако корневые компоненты в директории должны лежать в файле `index.jsx`, в таком случае название папки должно быть таким же, как название компонента:

    ```jsx
    // плохо
    import Footer from './Footer/Footer';

    // плохо
    import Footer from './Footer/index';

    // хорошо
    import Footer from './Footer';
    ```
  - **Именование компонента высшего порядка**: Используйте сочетание имени компонента высшего порядка и имени переданного в него компонента как свойство `displayName` сгенерированного компонента. Например, из компонента высшего порядка `withFoo()`, которому передан компонент `Bar`, должен получаться компонент с `displayName` равным `withFoo(Bar)`.

  > Почему? Свойство `displayName` может быть использовано в инструментах разработчика или сообщениях об ошибках, и если оно ясно выражает связь между компонентами, это помогает людям понять, что происходит.

    ```jsx
    // плохо
    export default function withFoo(WrappedComponent) {
      return function WithFoo(props) {
        return <WrappedComponent {...props} foo />;
      }
    }

    // хорошо
    export default function withFoo(WrappedComponent) {
      function WithFoo(props) {
        return <WrappedComponent {...props} foo />;
      }

      const wrappedComponentName = WrappedComponent.displayName
        || WrappedComponent.name
        || 'Component';

      WithFoo.displayName = `withFoo(${wrappedComponentName})`;
      return WithFoo;
    }
    ```

  - **Названия свойств**: Избегайте использования названий свойств DOM-компонента для других целей.

  > Почему? Люди ожидают, что такие свойства как `style` и `className` имеют одно определенное значение. Изменение этого API в вашем приложении ухудшает читабельность и поддержку кода, что может приводить к ошибкам.

    ```jsx
    // плохо
    <MyComponent style="fancy" />

    // хорошо
    <MyComponent variant="fancy" />
    ```

## Declaration

  - Do not use `displayName` for naming components. Instead, name the component by reference.

    ```jsx
    // плохо
    export default React.createClass({
      displayName: 'ReservationCard',
      // stuff goes here
    });

    // хорошо
    export default class ReservationCard extends React.Component {
    }
    ```

## <a name="alignment">Выравнивание</a>

  - Следуйте приведенным ниже стилям для JSX-синтаксиса. eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

    ```jsx
    // плохо
    <Foo superLongParam="bar"
         anotherSuperLongParam="baz" />

    // хорошо
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    />

    // если свойства помещаются на одну строку, оставляйте их на одной строке
    <Foo bar="bar" />

    // отступ у дочерних элементов задается как обычно
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    >
      <Quux />
    </Foo>
    ```

## <a name="quotes">Кавычки</a>

  - Всегда используйте двойные кавычки (`"`) для JSX-атрибутов, но одинарные кавычки (`'`) для всего остального JS. eslint: [`jsx-quotes`](http://eslint.org/docs/rules/jsx-quotes)

  > Почему? Для стандартных HTML-атрибутов обычно используются двойные кавычки, а не одинарные, и JSX-атрибуты тоже следуют этому соглашению.

    ```jsx
    // плохо
    <Foo bar='bar' />

    // хорошо
    <Foo bar="bar" />

    // плохо
    <Foo style={{ left: "20px" }} />

    // хорошо
    <Foo style={{ left: '20px' }} />
    ```

## <a name="spacing">Пробелы</a>

  - Всегда вставляйте один пробел в ваш самозакрывающийся тег. eslint: [`no-multi-spaces`](http://eslint.org/docs/rules/no-multi-spaces), [`react/jsx-space-before-closing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-space-before-closing.md)

    ```jsx
    // плохо
    <Foo/>

    // very bad
    <Foo                 />

    // плохо
    <Foo
     />

    // хорошо
    <Foo />
    ```

  - Не отделяйте фигурные скобки пробелами в JSX. eslint: [`react/jsx-curly-spacing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)

    ```jsx
    // плохо
    <Foo bar={ baz } />

    // хорошо
    <Foo bar={baz} />
    ```

## Props

  - Always use camelCase for prop names.

    ```jsx
    // плохо
    <Foo
      UserName="hello"
      phone_number={12345678}
    />

    // хорошо
    <Foo
      userName="hello"
      phoneNumber={12345678}
    />
    ```

  - Omit the value of the prop when it is explicitly `true`. eslint: [`react/jsx-boolean-value`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

    ```jsx
    // плохо
    <Foo
      hidden={true}
    />

    // хорошо
    <Foo
      hidden
    />
    ```

  - Always include an `alt` prop on `<img>` tags. If the image is presentational, `alt` can be an empty string or the `<img>` must have `role="presentation"`. eslint: [`jsx-a11y/img-has-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-has-alt.md)

    ```jsx
    // плохо
    <img src="hello.jpg" />

    // хорошо
    <img src="hello.jpg" alt="Me waving hello" />

    // хорошо
    <img src="hello.jpg" alt="" />

    // хорошо
    <img src="hello.jpg" role="presentation" />
    ```

  - Do not use words like "image", "photo", or "picture" in `<img>` `alt` props. eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

  > Why? Screenreaders already announce `img` elements as images, so there is no need to include this information in the alt text.

    ```jsx
    // плохо
    <img src="hello.jpg" alt="Picture of me waving hello" />

    // хорошо
    <img src="hello.jpg" alt="Me waving hello" />
    ```

  - Use only valid, non-abstract [ARIA roles](https://www.w3.org/TR/wai-aria/roles#role_definitions). eslint: [`jsx-a11y/aria-role`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

    ```jsx
    // плохо - not an ARIA role
    <div role="datepicker" />

    // плохо - abstract ARIA role
    <div role="range" />

    // хорошо
    <div role="button" />
    ```

  - Do not use `accessKey` on elements. eslint: [`jsx-a11y/no-access-key`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

  > Why? Inconsistencies between keyboard shortcuts and keyboard commands used by people using screenreaders and keyboards complicate accessibility.

  ```jsx
  // плохо
  <div accessKey="h" />

  // хорошо
  <div />
  ```

  - Avoid using an array index as `key` prop, prefer a unique ID. ([why?](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318))

  ```jsx
  // плохо
  {todos.map((todo, index) =>
    <Todo
      {...todo}
      key={index}
    />
  )}

  // хорошо
  {todos.map(todo => (
    <Todo
      {...todo}
      key={todo.id}
    />
  ))}
  ```

  - Always define explicit defaultProps for all non-required props.

  > Why? propTypes are a form of documentation, and providing defaultProps means the reader of your code doesn’t have to assume as much. In addition, it can mean that your code can omit certain type checks.

  ```jsx
  // плохо
  function SFC({ foo, bar, children }) {
    return <div>{foo}{bar}{children}</div>;
  }
  SFC.propTypes = {
    foo: PropTypes.number.isRequired,
    bar: PropTypes.string,
    children: PropTypes.node,
  };

  // хорошо
  function SFC({ foo, bar }) {
    return <div>{foo}{bar}</div>;
  }
  SFC.propTypes = {
    foo: PropTypes.number.isRequired,
    bar: PropTypes.string,
    children: PropTypes.node,
  };
  SFC.defaultProps = {
    bar: '',
    children: null,
  };
  ```

## Refs

  - Always use ref callbacks. eslint: [`react/no-string-refs`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-string-refs.md)

    ```jsx
    // плохо
    <Foo
      ref="myRef"
    />

    // хорошо
    <Foo
      ref={(ref) => { this.myRef = ref; }}
    />
    ```

## Parentheses

  - Wrap JSX tags in parentheses when they span more than one line. eslint: [`react/wrap-multilines`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/wrap-multilines.md)

    ```jsx
    // плохо
    render() {
      return <MyComponent className="long body" foo="bar">
               <MyChild />
             </MyComponent>;
    }

    // хорошо
    render() {
      return (
        <MyComponent className="long body" foo="bar">
          <MyChild />
        </MyComponent>
      );
    }

    // хорошо, when single line
    render() {
      const body = <div>hello</div>;
      return <MyComponent>{body}</MyComponent>;
    }
    ```

## Tags

  - Always self-close tags that have no children. eslint: [`react/self-closing-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)

    ```jsx
    // плохо
    <Foo className="stuff"></Foo>

    // хорошо
    <Foo className="stuff" />
    ```

  - If your component has multi-line properties, close its tag on a new line. eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

    ```jsx
    // плохо
    <Foo
      bar="bar"
      baz="baz" />

    // хорошо
    <Foo
      bar="bar"
      baz="baz"
    />
    ```

## Methods

  - Use arrow functions to close over local variables.

    ```jsx
    function ItemList(props) {
      return (
        <ul>
          {props.items.map((item, index) => (
            <Item
              key={item.key}
              onClick={() => doSomethingWith(item.name, index)}
            />
          ))}
        </ul>
      );
    }
    ```

  - Bind event handlers for the render method in the constructor. eslint: [`react/jsx-no-bind`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

  > Why? A bind call in the render path creates a brand new function on every single render.

    ```jsx
    // плохо
    class extends React.Component {
      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv.bind(this)} />
      }
    }

    // хорошо
    class extends React.Component {
      constructor(props) {
        super(props);

        this.onClickDiv = this.onClickDiv.bind(this);
      }

      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv} />
      }
    }
    ```

  - Do not use underscore prefix for internal methods of a React component.
  > Why? Underscore prefixes are sometimes used as a convention in other languages to denote privacy. But, unlike those languages, there is no native support for privacy in JavaScript, everything is public. Regardless of your intentions, adding underscore prefixes to your properties does not actually make them private, and any property (underscore-prefixed or not) should be treated as being public. See issues [#1024](https://github.com/airbnb/javascript/issues/1024), and [#490](https://github.com/airbnb/javascript/issues/490) for a more in-depth discussion.

    ```jsx
    // плохо
    React.createClass({
      _onClickSubmit() {
        // do stuff
      },

      // other stuff
    });

    // хорошо
    class extends React.Component {
      onClickSubmit() {
        // do stuff
      }

      // other stuff
    }
    ```

  - Be sure to return a value in your `render` methods. eslint: [`react/require-render-return`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/require-render-return.md)

    ```jsx
    // плохо
    render() {
      (<div />);
    }

    // хорошо
    render() {
      return (<div />);
    }
    ```

## Ordering

  - Ordering for `class extends React.Component`:

  1. optional `static` methods
  1. `constructor`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *clickHandlers or eventHandlers* like `onClickSubmit()` or `onChangeDescription()`
  1. *getter methods for `render`* like `getSelectReason()` or `getFooterContent()`
  1. *optional render methods* like `renderNavigation()` or `renderProfilePicture()`
  1. `render`

  - How to define `propTypes`, `defaultProps`, `contextTypes`, etc...

    ```jsx
    import React, { PropTypes } from 'react';

    const propTypes = {
      id: PropTypes.number.isRequired,
      url: PropTypes.string.isRequired,
      text: PropTypes.string,
    };

    const defaultProps = {
      text: 'Hello World',
    };

    class Link extends React.Component {
      static methodsAreOk() {
        return true;
      }

      render() {
        return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>
      }
    }

    Link.propTypes = propTypes;
    Link.defaultProps = defaultProps;

    export default Link;
    ```

  - Ordering for `React.createClass`: eslint: [`react/sort-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

  1. `displayName`
  1. `propTypes`
  1. `contextTypes`
  1. `childContextTypes`
  1. `mixins`
  1. `statics`
  1. `defaultProps`
  1. `getDefaultProps`
  1. `getInitialState`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *clickHandlers or eventHandlers* like `onClickSubmit()` or `onChangeDescription()`
  1. *getter methods for `render`* like `getSelectReason()` or `getFooterContent()`
  1. *optional render methods* like `renderNavigation()` or `renderProfilePicture()`
  1. `render`

## `isMounted`

  - Do not use `isMounted`. eslint: [`react/no-is-mounted`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

  > Why? [`isMounted` is an anti-pattern][anti-pattern], is not available when using ES6 classes, and is on its way to being officially deprecated.

  [anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html

## Translation

  This JSX/React style guide is also available in other languages:

  - ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese (Simplified)**: [JasonBoy/javascript](https://github.com/JasonBoy/javascript/tree/master/react)
  - ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [pietraszekl/javascript](https://github.com/pietraszekl/javascript/tree/master/react)
  - ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [apple77y/javascript](https://github.com/apple77y/javascript/tree/master/react)
  - ![Br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Portuguese**: [ronal2do/javascript](https://github.com/ronal2do/airbnb-react-styleguide)
  - ![jp](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/javascript-style-guide](https://github.com/mitsuruog/javascript-style-guide/tree/master/react)
  - ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Español**: [agrcrobles/javascript](https://github.com/agrcrobles/javascript/tree/master/react)
  - ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [ivanzusko/javascript](https://github.com/ivanzusko/javascript/tree/master/react)

**[⬆ к оглавлению](#Оглавление)**

# How To Use [React-Intl](https://github.com/yahoo/react-intl): internationalize your React app
> This short tutorial assumes you know about ES6 syntax.

* [Get started](#get-started)
* [Create a messages file](#create-a-messages-file)
* [Import messages](#import-messages)
* [Import languages](#import-languages)
* [Wrap this up](#wrap-this-up)
* [Insert messages in your app](#insert-messages-in-your-app)
* [What if I'm using Redux](#what-if-im-using-redux)

## Get started
`yarn add react-intl` or `npm i --save react-intl`.

## Create a messages file
This file should contain every message for your app.  
It has an **object** of **(locale, messages)** couples:
```javascript
export default {
  'en-GB': {
    // We're using dots for nesting and camelCase for each name
    'homepage.welcome': 'Welcome!',
    'homepage.subscribeHeader': 'Subscribe Now',
    // You can use placeholders using curly braces ({variable})
    'user.hello': 'Hi {username}, how are you?',
    // ...and also HTML
    'user.signIn': 'Please, <em>sign in</em>'
  },
  'fr-FR': {
    'homepage.welcome': 'Bienvenue !',
    'homepage.subscribeHeader': 'Abonnez-vous',
    'user.hello': 'Bonjour {username}, comment ça va ?',
    'user.signIn': 'Veuillez <em>vous connecter</em> s\'il vous plaît'
  }
};
```
Each message should be associated to a unique ID that will be required later (e.g. `homepage.welcome`).  
*We'll now assume that these messages are stored in a `messages.js` file.*

## Import messages
At the **root of your app**, import your messages file:  
```javascript
import messages from './messages';
```

## Import languages
For **each language you're using in your application**, you'll need one additional line on your same **app index file**:
```javascript
// If we're using English and French ('en', 'fr')
import en from 'react-intl/locale-data/en';
import fr from 'react-intl/locale-data/fr';

// But if you need Spanish for example, you'd then need to import
import es from 'react-intl/locale-data/es';
```
And then tell `react-intl` to use them (we'll import `addLocaleData` right after):
```javascript
addLocaleData([...en, ...fr]);
```

## Wrap this up
It's now time to provide the messages to all our components.  
Here, we'll need both `IntlProvider` and `addLocaleData` (from the previous step):
```javascript
// Paste this before the previous imports
import { addLocaleData, IntlProvider } from 'react-intl';
```
Look up for the user's locale:
```javascript
const locale = (navigator.languages && navigator.languages[0])
               || navigator.language
               || navigator.userLanguage
               || 'en-US';
```
And add the provider to your app:
```jsx
ReactDOM.render((
  <IntlProvider locale={locale} messages={messages[locale]}>
    {/* Previous code... */}
  </IntlProvider>
, document.getElementById('root'));
```
*As you can see, we're telling `react-intl` what locale and messages should be used, through its props.*

## Insert messages in your app
You can now use every message you put into your `messages.js` file, inside your app.
- [`FormattedMessage`](https://github.com/yahoo/react-intl/wiki/Components#string-formatting-components): for normal messages:
```jsx
import { FormattedMessage } from 'react-intl';

class HomePage extends React.Component {
  render() {
    return (
      <div>
        <h1>
          <FormattedMessage id="homepage.welcome" />
          <FormattedMessage id="user.hello" values={{ username: 'Arnaud' }} />
        </h1>
      </div>
    );
  }
}
```
- [`FormatedHTMLMessage`](https://github.com/yahoo/react-intl/wiki/Components#string-formatting-components): for every message containing HTML syntax:
```jsx
<FormattedHTMLMessage id="user.signIn" />
```
- [`FormattedDate` & `FormattedTime`](https://github.com/yahoo/react-intl/wiki/Components#date-formatting-components): yup, for every date/time related message (they both need a Unix timestamp in ms):
```jsx
<FormattedDate
  value={new Date(1501257521)}
  year="numeric"
  month="long"
  day="numeric"
/>

<FormattedTime
  value={new Date(1501257521)}
  year="2-digit"
  month="2-digit"
  day="2-digit"
/>
```
- [`FormattedRelative`](https://github.com/yahoo/react-intl/wiki/Components#date-formatting-components): 10 days ago..., and so on: 
```jsx
<FormattedRelative value={new Date(1501257521)} />
```
- [`FormattedNumber`](https://github.com/yahoo/react-intl/wiki/Components#number-formatting-components): prices, random numbers, ...:
```jsx
<FormattedNumber
  value={64.42}
  style="currency"
  currencyDisplay="symbol"
  currency="USD"
/>
```
- otherwise, you're probably looking for a non-component message, **a simple string** you want to be returned:
```jsx
import { injectIntl } from 'react-intl';

// 'intl' will be available among the component's props, after the injection
function HelloWorld({ intl, ...props }) {
  const placeholder = intl.formatMessage({
    id: 'random.placeholder', // This ID could be anything else as well
  });
  
  return <textarea placeholder={placeholder}></textarea>;
}

export default injectIntl(HelloWorld);
```

## What if I'm using Redux
Check the [`react-intl-redux` package](https://www.npmjs.com/package/react-intl-redux).  
Easy to add to your app, their documentation/example should be enough.  

Hope this helped you.  
Most information comes from [@damonbauer's course](https://egghead.io/courses/add-internationalization-i18n-to-a-react-app-using-react-intl).  
[Feel free to share your thoughts with me on twitter.](https://twitter.com/eveningkid)

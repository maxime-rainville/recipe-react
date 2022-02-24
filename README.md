# Silverstripe CMS react recipe

The purpose of this module is to allow Silverstripe CMS developers to quickly get started building React components to customise the CMS UI.

## Before you get started

This guides assumes you have the following:
- a working Silverstripe CMS project
- the `composer` executable installed in your path
- the `yarn` executable installed in your path

## Setting up your projects

### Step 1 - Add the recipe to your project

```bash
composer require maxime-rainville/recipe-react
```

Installing the recipe will:
- Add a `app/cms-client` folder to your project. This is where you will put your React JS components.
- Add a `app/_config/recipe-config.yml` file to your project. This will get your compiled JS/CSS code loaded in the Silverstripe CMS admin.

### Step 2 - Re-install `silverstripe/admin` from source

To compile `app/cms-client`, you need to install `silverstripe/admin` from source and download the `silverstripe/admin` JS dependencies.

`maxime-rainville/recipe-react` comes with a small bash script to simplyfie this process:
- Running `vendor/bin/from-source check` will validate if `silverstripe/admin` has been installed properly to do CMS JS development.
- Running `vendor/bin/from-source reinstall` will attempt to reinstall `silverstripe/admin` to allow you to compile `app/cms-client`.

Alternatively, you can run this sequence of command:
```bash
# Start in your project root. This will reinstall admin from source
rm -rf vendor/silverstripe/admin
composer install --prefer-source

# This line will download all silverstripe/admin JS dependencies
(cd vendor/silverstripe/admin && yarn install)
```

### Step 3 - Initial build of your CMS client

You should now be able to compile your CMS client:

```bash
cd app/cms-client
yarn install
yarn dev
```

This should create a `app/cms-client/dist` directory.

### Step 4 - Expose your CMS client

The `app/cms-client/dist` folder needs to be accessible to your browser. To do this you need to "expose" it.

Update your project `composer.json` file. You need to add an `expose` key under `extra` and give it an array of paths to expose. Your composer file should look something like this when you are done.

```json
{
    "name": "your/project",
    "require": {
        "php": "^7.1 || ^8",
        "silverstripe/recipe-plugin": "^1.2",
        "silverstripe/recipe-cms": "4.x-dev",
        "silverstripe-themes/simple": "~3.2.0",
        "maxime-rainville/recipe-react": "dev-master"
    },
    "extra": {
        "resources-dir": "_resources",
        "expose": [
            "app/cms-client/dist"
        ]
    },
}
```

You now need to run this command to expose your cms-client:
```bash
composer vendor-expose
```

You should now have a folder `public/_resources/app/cms-client/dist`.

### Step 5 - Test your set up

You can take these optional steps to validate that everything is working correctly.

1. Edit `app/cms-client/src/bundles/bundle.js` and add `alert('My set up is done');` at the end.
2. Run `yarn dev` from `app/cms-client` to rebuild your library.
3. Rebuild your Silverstripe CMS project with `vendor/bin/sake dev/build`
4. Access your test site in your browser. You may need to flush the Silverstripe cache with by adding `?flush=1` to your URL.
5. Access the CMS administration area.

You should see a JS alert with "My set up is done".

## Building and using your first react component for Silverstripe CMS

In this step we will create a simple On/Off form field.

### Step 1 - Create the PHP form field class

In your PHP code base, create a `OnOffButton.php` file with this content. This will be the object you will be adding to your PHP form.

```php
<?php

use MaximeRainville\SilverstripeReact\ReactFormField;
use SilverStripe\Forms\FormField;

class OnOffButton extends ReactFormField
{

    /**
     * This should match the value type that our field will manage
     */
    protected $schemaDataType = FormField::SCHEMA_DATA_TYPE_STRING;

    /**
     * If your component renders an HTML form field, you can omit this line
     */
    protected $renderInput = true;

    /**
     * This will be the name of the React component that will be use to
     * render the field.
     */
    public function getComponent(): string
    {
        return 'OnOffButton';
    }

    /**
     * You can customise the props that will be pass to your React component
     * by overriding the `ReactFormField::getProps` method
     */
    public function getProps(): array
    {
        return parent::getProps();
    }

    /**
     * Our field will default to `off`
     */
    public function Value()
    {
        return parent::Value() ?: 'off';
    }
}

```

### Step 2 - Create a React Component

This is the react component will render our form field. Create a `app/cms-client/src/components/OnOffButton.js` file with the following content.

```jsx
/* eslint-disable import/no-extraneous-dependencies */
import React from 'react';
import Button from 'components/Button/Button';

export default function ({ value, extraClass, id, onChange }) {
  const props = {
    color: 'info',
    className: extraClass,
    outline: value === 'off',
    // This callback will tell our parent when our form value changes
    onClick: (event) => {
      onChange(event, { id, value: value === 'off' ? 'on' : 'off' });
    }
  };

  return <Button {...props}>{value}</Button>;
}
```

### Step 3 - Register your new component

We need to register our new component our form field can be bootstrapped properly.

Update `app/cms-client/src/components/index.js` with this content.

```js
import OnOffButton from './OnOffButton';

export {
  OnOffButton
};
```

We need to rebuild our CMS client with `yarn dev`. 

### Step 4 - Seeing our new component in action

You can add your new form field to any CMS form with this code snippet.

```php
$fields->addFieldToTab(
    'Root.Main',
    OnOffButton::create('Toggle')
);
```

Simply rebuild your project with `vendor/bin/sake dev/build` and access your CMS form to view the result.

## Next steps

Here's some next step you might want to consider.

- [Building a CMS area entirely in react](docs/en/Building-react-cms-area.md)



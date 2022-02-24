# Building a CMS area entirely in react

In this article you will add brand new area to Silverstripe CMS and build the UI entirely in react.

## Pre-requisite

You have followed the steps outline in the [README](../../README.md) and have a functional cms-client JS build configured.

## Step 1 - Create a new CMS section

1. Create a new class in your project and call it `ReactCMSSection`. The class should extends `MaximeRainville\SilverstripeReact\ReactAdmin`.
2. Define `$url_segment` and `$menu_title` as private static variables.
```php
<?php

use MaximeRainville\SilverstripeReact\ReactAdmin;

class ReactCMSSection extends ReactAdmin
{
    private static $url_segment = 'react-cms-section';
    private static $menu_title = 'React CMS Section';
}
```

If you run a `dev/build` and flush your Silverstripe CMS cache, you will immediately be able view your new _React CMS Section_ section in the administration area. Initially, it will display a generic component.

## Step 2 - Create a react component to render into your new _React CMS Section_

1. Create a new file under `app/client-cms/src/components/ReactCMSSection.js`
2. Add the following code to your file:
```jsx
import React from 'react';
import { loadComponent } from 'lib/Injector';

// This command will load a generic component that can be used to create
// UI that matches the look and field of the CMS.
const LeftAndMain = loadComponent('LeftAndMain');

const ReactCMSSection = () => {
  return (
    <LeftAndMain>
      My first React Section inside Silverstripe CMS
    </LeftAndMain>
  );
};

export default ReactCMSSection;
```
3. Update `app/client-cms/src/components/index.js` to reference your new component.
```js
import ReactCMSSection from './ReactCMSSection';

export {
  ReactCMSSection
};
```
4. Rebuild your CMS client library by running `yarn dev` from the `app/client-cms` folder.

## Step 3 - Tell your ReactAdmin class to use your new component

1. Update `app/client-cms/src/components/ReactCMSSection.js` and add a `getComponent()` function.
```php
    public function getComponent(): string
    {
        // This string must match the name that was registered
        // in `app/client-cms/src/components/index.js`
        return 'ReactCMSSection';
    }
```

If you access your _React CMS Section_ in the Silverstripe CMS, it will now be using your now component.



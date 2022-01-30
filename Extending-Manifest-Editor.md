## Widget - technical overview

When a widget is loaded it will be provided with some standard properties, and some custom configuration - specific to that widget.

```js
// This may not be the actual API, just demonstrative.
const widget = new Widget(vault);
const instance = widget.create({
  locator,
  config
});
```

The first is the `vault` instance. This is provided automatically by the [[Shell]] and keeps state across the application in sync. The aim of any extension to Manifest Editor is to display or mutate something inside the vault. The "something" in this case, is a locator. 

The `locator` is the slice of the vault that the extension intends to edit. For example, if I created an widget to edit the `label` of the top level manifest, the locator may just be the ref:
```json
{
  "id": "htts://example.org/manifest",
  "type": "Manifest"
}
```

However, the same editor could be used to edit the `label` of a canvas, simply by changing the locator.
```json
{
  "id": "https://example.org/canvas-1",
  "type": "Canvas"
}
```

This allows for widgets to be created and reused. The locator is different from a ref however, as it may also specify one or more fields on the resource. My widget could now be used to edit any language map field.

```json
{
  "id": "https://example.org/canvas-1",
  "type": "Canvas",
  "fields": ["label"]
}
```

### Supports

A widget can optionally tell the Shell if it supports a particular locator. This can help validation of configuration, if widgets are being configured incorrectly. 

This would also allow for the Shell to suggest the extension in contextual menus. For example, `r-click` on a canvas in a sidebar could check all extensions, and populate an "open in..." menu. 

```js
widget.supports(locator) // bool
```

If there is a slot in the existing application for the extension, then it could go there - or simply replace the current app in the shell. 

### Locks

To avoid conflicts between widgets - an app may choose to "lock" a resource while the user is focused on their UI (like a form, for example). This is similar to how in Miro a sticky-note becomes inaccessible when someone is typing.

```js
vaultExtensions.lock('my-app-id', locator); // returns function to unlock.
``` 

The extension may also decide to lock the entire locator used to instantiate it. By default, the entire locator will be locked. This will be important for data integrity.


#### React example (locks)

Here is an example of how this might look to a React developer creating an extension for the Manifest Editor. 

```jsx
import { useLock } from 'manifest-editor';

function MyLabelEditor(props) {
  // Use a hook to lock, unlock and get the lock status of a locator.
  const { isLocked, lock, unlock } = useLock(props.locator);

  // When we focus the form, lock the resource.
  const onFocus = () => {
    lock();
  };

  // When we blur the form, unlock the resource.
  const onBlur = () => {
    unlock();
  };


  // When we submit the resource, update the vault.
  const onSubmit = (e) => {
    vault.updateFieldValue(...);
    // .. etc
  };


  return (
    <form onFocus={onFocus} onSubmit={onSubmit} onBlur={onBlur}>
      {/* We can use a fieldset to disable the whole form if it was locked (not by us) */}
      <fieldset disabled={isLocked}>
         <input type="text" [...] />

          [ ... other UI ... ]
 
        <button type="submit" />
      </fieldset>
    </form>
  );
}
```

### Iterative design

The model of `locators` will allow us to quantify how much of a Manifest our Manifest Editor can edit and plug gaps. 

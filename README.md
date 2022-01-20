# Planning your UpzeloJs Integration

Our integration options are designed to provide ease and flexibility while ensuring appropriate security levels are in place for handling cancellation flow actions.

## 1. Simple
To launch a cancellation flow without automated cancellation processing you’ll need to implement the Upzelo JavaScript tag into the <head> of your website or application. For this scenario, follow Step 1 only.

**Example HTML**: [simple.html](simple.html)

**Demo**: [Matts Cakes](https://exit-popup-84fb14.netlify.app/examples/cakes.html)

## 2. Custom
If you would like to customise UpzeloJs further, such as launching from custom selectors on your page, implement step 2.

**Example HTML**: [custom.html](custom.html)

**Demo**: [Boots 4 U](https://exit-popup-84fb14.netlify.app/examples/boots.html)

## 3. Secure
To enable automated processing with your subscription platform you’ll need to implement additional steps to secure your integration with a ‘signed payload’ from your server.

**Example HTML**: [secure.html](secure.html)

**Demo**: [Razors](https://exit-popup-84fb14.netlify.app/examples/razors.html).


## Where to find your App ID

Inside your Upzelo account you’ll find your App Id in the “Setup Upzelo” area: [https://upzelo.com/app/setup/complete-setup](https://upzelo.com/app/setup/complete-setup).

## Live or Test mode

By default, the UpzeloJs script will connect in Live Mode. If you are creating a Test workflow for your staging website or application we recommend creating content in the Upzelo “Test Mode” account, which can be access via the sub-menu.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/45277d10-7337-4174-81dc-0cbf4a458cd1/Untitled.png)

Next, in order to sync with the Test mode account, you should supply the ‘mode’ attribute in the single script attribute or inside window.upzelo.init().

```js
window.upzelo({
	mode: "test",
});
```

## Where do you want to launch a cancellation flow?

In most cases, your website or application will have a cancellation page with a `<button>` or link with an ID attribute of ‘cancel’. In this case, simply apply the Single script tag below into the `<head>` of your website or application.

If you have buttons or links that have an ID attribute of 'cancel' that you don’t want to launch a cancellation flow, then you need to supply a custom selector. This will override the default and prevent loading a cancellation flow on those elements.

If you have multiple cancel elements on your website or application, you can supply multiple CSS selectors to the ‘selector’ attribute in **Step 2**.

# Implementation Steps

## 1. Single script tag

The simplest method to integrate an Upzelo cancellation flow. Should be installed across your website or application in the `<head>` for best results.

- Launches a cancellation flow on any button or link with `id=’cancel’`
- Enables manual billing alerts. For automated billing processing continue to step 3.

```html
<script id="upzpdl" src="//upzelo.io/pdl.js" appId="[App id from your Account]" customerId="[Customer Id from your subscription platform]"></script>
```

Available attributes:

- `appId` - The Id of your Upzelo app account. e.g. `upz_123456789`
- `customerId` - The customer's Id, from your subscription platform. e.g. `cus_123456abc` | `rec_123456abc`
- `mode` `test` | `live` optional - *Which account mode to process can flows in.* defaults to `live`
- `selector` optional - Override the default selector with your own 
- `debug` optional - Outputs logs to the console. Internal use only

**NOTE:** `hash` and `type` are not parameters - in order to implement the "Complete Setup" with automated billing actions, refer to `window.upzelo.init()` configuration options below.

## 2. Script in head and in page

Are you customising how UpzeloJs loads on your website or application? Implement a `window.upzelo.init()` function with configuration options.

```html
<script id="upzpdl" src="upzelo.com/the_vue_app.js" appId="upz_app_b9c70b638d48"></script>
```

```js
window.upzelo.init({	
	customerId: '1233',
	...config
});
```

Configuration options

- `appId`
- `customerId`
- `type` 'minimal` | `full` defaults to `minimal`. `hash` is required when set to `full`
- `mode` 'test' | 'live' defaults to 'live'
- `selector` optional
- `hash` optional
- `data` internal use only

## Custom events

Either of the above methods allows you to implement your own events to open the modal

```js
element.addEventListener('click', () => {
	window.upzelo.open();
})
```

## 3. Signed Payload for Automated Processing

To verify the request is authorized by your website, Upzelo requires a hash that’s been generated on your server.

This can be generated in many different backend languages using your API key and the customer ID.

Here’s an example in PHP:

```php
<?php
echo hash_hmac("sha256", $customerId, $upzeloApiKey);
```

Then you need to pass Upzelo the hash when a cancel flow is started:

```js
window.upzelo({
    hash: "***", // The hash generated previously
});
```

Your API key should not be exposed in the browser. If you believe it has been, you can reset it in Upzelo.
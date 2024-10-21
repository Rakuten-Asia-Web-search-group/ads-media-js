# Content Delivery - JS SDK

Content Delivery JS SDK is a javascript SDK for serving content (ads, themes) from Rakuten Content Delivery.

Table of Contents

- [Installations](#installations)
- [Basic Usage](#basic-usage)
- [Ads Providers Supported](#ads-provider-supported)
- [Ads Providers Default UI Template](./UI.md)
- [Ads Parameters](#ads-parameters)
- [Extra Settings](#extra-settings)
- [CHANGELOG](./CHANGELOG)

<br />

# `Installations`

## `Import file via script tag`

To install via script, import our JS SDK file by pasting the following `<script>` tag inside the `<header>` tag.

Source file: `https://websearch.r10s.jp/ads-media/sdk-static/sdk/{VERSION}/content-delivery-sdk.js`<br />

Latest (Beta) v0.0.1: https://websearch.r10s.jp/ads-media/sdk-static/sdk/0.0.1/content-delivery-sdk.js

```html
<header>
	// ... put this before the end of header tag
	<script
		type="text/javascript"
		src="https://websearch.r10s.jp/ads-media/sdk-static/sdk/0.0.1/content-delivery-sdk.js"></script>
</header>
```

## `Initialize SDK`

After pasting the script, Content Delivery SDK JS will be available and can be accessed in the `window` object, through the `ContentDeliverySDK` variable.

```html
<script>
	const contentDeliverySDK = window.ContentDeliverySDK || {};
	contentDeliverySDK.events = contentDeliverySDK.events || [];
</script>
```

<br />

# Basic Usage

We provide several ways to display the ads from Content Delivery, and you can choose which approach suits your needs.

## `1. Using HTML div element`

You can easily display Content Delivery Ads by providing some div elements with the following guidelines:

1. Create a div element
2. Add `content-delivery-sdk-container` class to the div element
3. Pass the required parameters as the div element's `data-[param]=value`, for example: `data-ad-slot=abcde`, or `data-source-id="rakuten_app_iphone"`.
4. Define the div element's height and width if needed

Here's the example of the div element to display ads:

```html
<div class="content-delivery-sdk-container" data-ad-slot="rakuten-app-top"></div>
```

\*note: data parameters in the html use `kebab-case`, and will be converted to `camelCase` queries:

- `data-channel=abcde`: `{ "channel": "abcde" }`
- `data-ad-slot=abcde-1234`: `{ "adSlot": "abcde-1234" }`

In the above example, we're displaying Ads using these parameters:

```json
{
	"adSlot": "rakuten-app-top",
	"query": "iphone",
	"pageNumber": 1,
	"channel": "aw_web_newclient_20131007",
	"client": "mobile"
}
```

## `2. Using JS API`

You can trigger the ads display by calling Content Delivery API:

1. `displayAds`: to display single ads
2. `displayMultipleAds`: to display multiple ads

Format:

```javascript
contentDeliverySDK.displayAds({ ELEMENT_ID }, { ADSLOT_DATA }, { CALLBACK });
contentDeliverySDK.displayMultipleAds([
	[{ ELEMENT_ID }, { ADSLOT_DATA }, { CALLBACK }],
	[{ ELEMENT_ID }, { ADSLOT_DATA }, { CALLBACK }],
]);
```

1. `ELEMENT_ID`: div element's id where the ads will be displayed.
2. `ADSLOT_DATA`: adslot parameters used to display the ads.
3. `CALLBACK? (Optional)`: callback function triggered after events occured. Details: (Callback)

Example:

```javascript
const contentDeliverySDK: MainSDK = window.ContentDeliverySDK || {};
contentDeliverySDK.events = contentDeliverySDK.events || [];

contentDeliverySDK.events.push(() => {
	contentDeliverySDK.displayAds("ad-mediator-iphone-container", {
		adSlot: "rakuten-app-top",
		query: "iphone",
		page: 1,
		channel: "web_newclient_20131007",
		sourceId: "rakuten_app_iphone",
	});
});
```

\*Please wrap your Content Delivery SDK JS codes inside the `ContentDeliverySDK.events` stack to avoid undefined `ContentDeliverySDK` due to browser is still loading the SDK. After Content Delivery SDK is loaded, it will automatically execute codes inside `ContentDeliverySDK.events`.

# Ads Providers Supported

As of now, we provide support to deliver the ads from several ads providers. And for each provider, you may need to send some required parameters to deliver a specific ads.

| No. | Ads Provider | Description                  |
| --- | ------------ | ---------------------------- |
| 1.  | RPP BTA      | Display RPP BTA Ads          |
| 2.  | RPP SWAD     | Display RPP SWAD             |
| 3.  | RUNA         | Display Ads from RUNA        |
| 4.  | Google AdEx  | Display Ads from Google AdEx |
| 5.  | Yahoo Ads    | Display Yahoo Ads            |

# Ads Providers Default UI Template

You can refer to this documentation [UI.md](./UI.md) of some default templates provided for Content Delivery JS SDK. If you have any requests for new UI, please kindly contact us.

# Ads Parameters

When calling Content Delivery JS SDK, you may need to provide some mandatory or optional parameters for better ads delivery and trackings:

| Parameter  | Type    | Mandatory | Mandatory for         | Description                                     | Example                    |
| ---------- | ------- | --------- | --------------------- | ----------------------------------------------- | -------------------------- |
| adSlot     | String  | Yes       | For all ads providers | Content Delivery Ad Slot Name as Ads Identifier | rwsweb-test-adslot         |
| query      | String  | Yes       | RPP SWAD, Yahoo       | Search query                                    | iphone                     |
| adCount    | Integer | No        | -                     | Number of ads to request                        | 1                          |
| pageNumber | Integer | No        | -                     | Page number of pagination                       | 1                          |
| domain     | String  | No        | -                     | Domain of the website                           | https://rakuten.co.jp      |
| sourceId   | String  | No        | -                     | Source identifier                               | toolbar_top                |
| client     | String  | No        | -                     | Source identifier                               | mobile-rakuten             |
| channel    | String  | No        | -                     | Channel identifier                              | web_client                 |
| referrer   | String  | No        | -                     | Referrer URL                                    | https://rakuten.co.jp/test |

# Extra Settings

## Callback

You can pass a callback function to get the ads display status, and we will call your callback function with some data.
Data passed to your callback status `event`: `PAGE_LOADED` | `ADS_EMPTY` | `ADS_DISPLAYED` | `SET_SIZE`.

Example:

```javascript
  const contentDeliverySDK: MainSDK = window.ContentDeliverySDK || {};
  contentDeliverySDK.events = contentDeliverySDK.events || [];

  contentDeliverySDK.events.push(() => {
    const handleCallback = (callbackData) = {
      if (callbackData.event === 'ADS_DISPLAYED') {
        console.log('ads displayed');
      }
      else if (callbackData.event === 'ADS_EMPTY') {
        console.log('ads empty');
      }
    }

    contentDeliverySDK.displayAds(
      'ad-mediator-iphone-container',
      {
        adSlot: 'rakuten-app-top',
        query: 'iphone',
        page: 1,
        channel: 'web_newclient_20131007',
        sourceId: 'rakuten_app_iphone',
      },
      handleCallback
    );
  });
```

## Callback Data Format

Callback value will have this format:

| Key    | Nullable | Type                                                    | Description                                                               |
| ------ | -------- | ------------------------------------------------------- | ------------------------------------------------------------------------- |
| name   | N        | `CONTENT_DELIVERY_SDK`                                  | Event of the name                                                         |
| event  | N        | `PAGE_LOADED`, `ADS_EMPTY`, `ADS_DISPLAYED`, `SET_SIZE` | Status of the ads                                                         |
| adSlot | N        | String                                                  | Ad Slot ID                                                                |
| adName | Y        | `RPP-BTA`, `ADEX`, `RPP-SWAD`, `YAHOO`                  | Displayed Ads provider. Will be filled when the event === `ADS_DISPLAYED` |
| height | Y        | number                                                  | The height size of the ads                                                |
| width  | Y        | number                                                  | The width size of the ads                                                 |

## Callback Status

| Status          | Description                                                     |
| --------------- | --------------------------------------------------------------- |
| `PAGE_LOADED`   | Triggered when the ads is being loaded in an iframe             |
| `ADS_DISPLAYED` | Triggered when the ads is finally displayed in an iframe        |
| `ADS_EMPTY`     | Triggered when there's no ads returned and displayed            |
| `SET_SIZE`      | Triggered after the Ads size changed. Please note that the size |

\*Note: Content Delivery Ads server has an ads-priority & waterfall-like mechanism to check if there's any ads returned from several ads providers. So, the SDK will trigger the `PAGE_LOADED` event when the first ads is rendered, and will wait until all the mechanism done before sending a `ADS_DISPLAYED` or `ADS_EMPTY` event.

## Responsive Ads Size

By default, the Content Delivery JS SDK will display the ads in an iframe size based on the actual ads size, so you don't need to define the size (width and height) of the ads.
For example, if you prepare this div element for the ads without defining the size:

```html
<div class="content-delivery-sdk-container" data-ad-slot="rakuten-app-top"></div>
```

The ads will be displayed as an iframe inside the div element, and the iframe size will be calculated responsively according to the actual ads size. And the DOM will become this:

```html
<div class="content-delivery-sdk-container" data-ad-slot="rakuten-app-top">
	<iframe src="" width="320px" height="50px"></iframe>
</div>
```

But, you can also define the div element to have a specific width & height by specifying the height & width to the element, for example:

```html
<div
	class="content-delivery-sdk-container"
	data-ad-slot="rakuten-app-top"
	style="width: 400px; height: 200px"></div>
```

In this scenario, the ads will be displayed inside the div element but will still have their own width and height, and will be centered horizontally. And the DOM will become this:

```html
<div
	class="content-delivery-sdk-container"
	data-ad-slot="rakuten-app-top"
	style="width: 400px; height: 200px">
	<iframe src="" width="320px" height="50px"></iframe>
</div>
```

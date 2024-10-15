# Content Delivery - JS SDK

Content Delivery JS SDK is a javascript SDK for serving content (ads, themes) from Content Delivery.

Table of Contents

- [Installations](#installations)
- [Basic Usage](#basic-usage)
- [Extra Settings](#extra-settings)
- [CHANGELOG](./CHANGELOG)
- [UI.md](./UI.md)

<br />

# `Installations`

## `Import file via script tag`

To install via script, import our JS SDK file by pasting the following `<script>` tag inside the `<header>` tag.

Source file: `https://websearch.r10s.jp/ads-media/sdk-static/sdk/{VERSION}/content-delivery-sdk.js`<br />

Latest (Beta) version: `https://websearch.r10s.jp/ads-media/sdk-static/sdk/0.0.1/content-delivery-sdk.js`

```html
<header>
	// ... put this before the end of header tag
	<script
		type="text/javascript"
		src="https://websearch.r10s.jp/ads-media/sdk-static/sdk/0.0.1/content-delivery-sdk.js"></script>
</header>
```

## `Initialize SDK`

After pasting the script, Mission SDK JS will be available and can be accessed in the `window` object, through the `ContentDeliverySDK` variable.

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
<div
	class="content-delivery-sdk-container"
	data-ad-slot="rakuten-app-top"
	data-query="iphone"
	data-page-number="1"
	data-channel="aw_web_newclient_20131007"
	data-client="mobile"
	data-source-id="rakuten_app_iphone"
	style="height: 700px; width: 360px"></div>
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

# Ads Provider Supported

As of now, we provide support to deliver the ads from several ads providers. And for each provider, you may need to send some required parameters to deliver a specific ads.

| No. | Ads Provider | Description                                            | Required Parameters | Example                                           |
| --- | ------------ | ------------------------------------------------------ | ------------------- | ------------------------------------------------- |
| 1.  | RPP BTA      | Display RPP BTA Ads                                    | `adSlot`            | `{ adSlot: 'rakuten-app-top' }`                   |
| 2.  | RPP SWAD     | Display RPP SWAD                                       | `adSlot`, `query`   | `{ adSlot: 'rakuten-app-top', query: 'samsung' }` |
| 3.  | RUNA         | Display Ads from RUNA, can be RUNA Pure Ads or RPP Ads | `adSlot`            | `{ adSlot: 'rakuten-app-top' }`                   |
| 4.  | Google AdEx  | Display Ads from Google AdEx                           | `adSlot`            | `{ adSlot: 'rakuten-app-top' }`                   |
| 5.  | Yahoo Ads    | Display Yahoo Ads                                      | `adSlot`, `query`   | `{ adSlot: 'rakuten-app-top', query: 'samsung' }` |

# Extra Settings

## `Set Device IFA/AdID`

You can use `.setDeviceAdId(value)` to specify a IFA of a mobile device to send to Ad Server.

```javascript
const contentDeliverySDK: MainSDK = window.ContentDeliverySDK || {};
contentDeliverySDK.events = contentDeliverySDK.events || [];

contentDeliverySDK.events.push(() => {
	contentDeliverySDK.setDeviceAdId("00000000-0000-0000-0000-000000000000");
	contentDeliverySDK.displayAds("ad-mediator-iphone-container", {
		adSlot: "rakuten-app-top",
		query: "iphone",
		page: 1,
		channel: "web_newclient_20131007",
		sourceId: "rakuten_app_iphone",
	});
});
```

## `Set Rz & Rp Cookie`

You can use `.setRzCookie(value)` & `.setRpCookie(value)` to specify user's Rz Cookie & Rp cookie to send to Ad Server.

```javascript
const contentDeliverySDK: MainSDK = window.ContentDeliverySDK || {};
contentDeliverySDK.events = contentDeliverySDK.events || [];

contentDeliverySDK.events.push(() => {
	contentDeliverySDK.setRzCookie("abcde");
	contentDeliverySDK.setRpCookie("12345");

	contentDeliverySDK.displayAds("ad-mediator-iphone-container", {
		adSlot: "rakuten-app-top",
		query: "iphone",
		page: 1,
		channel: "web_newclient_20131007",
		sourceId: "rakuten_app_iphone",
	});
});
```

## Callback

You can pass a callback function to get the ads display status, and we will call your callback function with some data.

Data passed to your callback status `event`: `PAGE_LOADED` | `ADS_EMPTY` | `ADS_DISPLAYED`.

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

### Callback Data Format

Callback value will have this format:

| Key    | Nullable | Type                                        | Description                                                               |
| ------ | -------- | ------------------------------------------- | ------------------------------------------------------------------------- |
| name   | N        | `CONTENT_DELIVERY_SDK`                      | Event of the name                                                         |
| event  | N        | `PAGE_LOADED`, `ADS_EMPTY`, `ADS_DISPLAYED` | Status of the ads                                                         |
| adSlot | N        | String                                      | Ad Slot ID                                                                |
| adName | Y        | `RPP-BTA`, `ADEX`, `RPP-SWAD`, `YAHOO`      | Displayed Ads provider. Will be filled when the event === `ADS_DISPLAYED` |

### Callback Status

| Status          | Description                                              |
| --------------- | -------------------------------------------------------- |
| `PAGE_LOADED`   | Triggered when the ads is being loaded in an iframe      |
| `ADS_DISPLAYED` | Triggered when the ads is finally displayed in an iframe |
| `ADS_EMPTY`     | Triggered when there's no ads returned and displayed     |

\*Note: Content Delivery Ads server has an ads-priority & waterfall-like mechanism to check if there's any ads returned from several ads providers. So, the SDK will trigger the `PAGE_LOADED` event when the first ads is rendered, and will wait until all the mechanism done before sending a `ADS_DISPLAYED` or `ADS_EMPTY` event.

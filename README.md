# Adpacker-Video integration guide.



This guide for integrating with Adpacker-Video using OpenRTB 2.3, VAST 3.0.  
(Should there be missing fields or requirements, please default requirements to the spec itself.)

Adpacker-Video uses JSON and the HTTP POST method.

If you have any question about this document, please reach out to adpacker@nasmedia.co.kr.  
<br>

**Note**
- Required fields must be included.
- Optional fields have positive impact on operations.
<br>

# 1. Bid Request Specification
## Object model

Object | Support | Extension field | Description
:--- | :---: | :---: | :---
BidRequest | O | X | Top-level object.
Imp | O | X | Container for the description of a specific impression; at least 1 per request.
Banner | X | X | Details for a banner impression (incl. in-banner video) or video companion ad.
Video | O | X | Details for a video impression or the video asset of a native impression.
Native | X | X | Container for a native impression conforming to the Native Ad Spec.
Site | O | X | Details of the website calling for the impression.
App | O | X | Details of the application calling for the impression.
Publisher | X | X | Entity that controls the content of and distributes the site or app.
Content | O | X | Details about the published content itself, within which the ad will be shown.
Producer | X | X | Producer of the content; not necessarily the publisher (e.g., syndication).
Device | O | X | Details of the device on which the content and impressions are displayed.
Geo | X | X | Location of the device or user’s home base depending on the parent object.
User | O | X | Human user of the device; audience for advertising.
Data | X | X | Collection of additional user targeting data from a specific data source.
Segment | X | X | Specific data point about a user from a specific data source.
Regs | X | X | Regulatory conditions in effect for all impressions in this bid request.
PMP | X | X | Collection of private marketplace (PMP) deals applicable to this impression.
Deal | X | X | Deal terms pertaining to this impression between a seller and buyer.
<br>

## Object Specifications

- Object : BidRequest

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
id | O | string; required | Unique ID of the bid request, provided by the exchange.
imp | O | object array; required | Array of `Imp objects` representing the impressions offered. At least 1 Imp object is required.
site | O | object; required(site or app) | Details via a `Site object` about the publisher’s website. Only applicable and recommended for websites.
app | O | object; required(site or app) | Details via an `App object` about the publisher’s app (i.e., non-browser applications). Only applicable and recommended for apps.
device | O | object; required | Details via a `Device object` about the user’s device to which the impression will be delivered.
user | O | object; optional | Details via a 'User object' about the human user of the device; the advertising audience.
test | X | integer | Indicator of test mode in which auctions are not billable, where 0 = live mode, 1 = test mode.
at | X | integer | Auction type, where 1 = First Price, 2 = Second Price Plus. Exchange-specific auction types can be defined using values greater than 500. 
tmax | X | integer| Maximum time in milliseconds to submit a bid to avoid timeout. This value is commonly communicated offline.
wseat | X | string array | Whitelist of buyer seats allowed to bid on this impression. Seat IDs must be communicated between bidders and the exchange a priori. Omission implies no seat restrictions.
allimps | X | integer | Flag to indicate if Exchange can verify that the impressions offered represent all of the impressions available in context (e.g., all on the web page, all video spots such as pre/mid/post roll) to support road-blocking. 0 = no or unknown, 1 = yes, the impressions offered represent all that are available.
cur | X | string array | Array of allowed currencies for bids on this bid request using ISO-4217 alpha codes. Recommended only if the exchange accepts multiple currencies.
bcat | O | string array; optional | Blocked advertiser categories using the IAB content categories.
badv | X | string array | Block list of advertisers by their domains (e.g., “ford.com”).
regs | X | object | A Regs object that specifies any industry, legal, or governmental regulations in force for this request.
ext | X | object | Placeholder for exchange-specific extensions to OpenRTB.
<br>

- Object : Imp

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
id | O | string; required | A unique identifier for this impression within the context of the bid request (typically, starts with 1 and increments.
banner | X | object | A `Banner object` required if this impression is offered as a banner ad opportunity.
video | O | object; required | A Video object required if this impression is offered as a video ad opportunity.
native | X | object | A Native object required if this impression is offered as a native ad opportunity.
displaymanager | X | string | Name of ad mediation partner, SDK technology, or player responsible for rendering ad (typically video or mobile). Used by some ad servers to customize ad code by partner. Recommended for video and/or apps.
displaymanagerver | X | string | Version of ad mediation partner, SDK technology, or player responsible for rendering ad (typically video or mobile). Used by some ad servers to customize ad code by partner. Recommended for video and/or apps.
instl | X | integer | 1 = the ad is interstitial or full screen, 0 = not interstitial.
tagid | X | string | Identifier for specific ad placement or ad tag that was used to initiate the auction. This can be useful for debugging of any issues, or for optimization by the buyer.
bidfloor | O | float; required | Minimum bid for this impression expressed in CPM.
bidfloorcur | O (USD/KRW only) | string; required | Currency specified using ISO-4217 alpha codes. This may be different from bid currency returned by bidder if this is allowed by the exchange.
secure | O | integer; optional | Flag to indicate if the impression requires secure HTTPS URL creative assets and markup, where 0 = non-secure, 1 = secure. If omitted, the secure state is unknown, but non-secure HTTP support can be assumed.
iframebuster | X | string array | Array of exchange-specific names of supported iframe busters.
pmp | X | object | A Pmp object containing any private marketplace deals in effect for this impression.
ext | X | object | Placeholder for exchange-specific extensions to OpenRTB.
<br>

- Object : Video

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
mimes | O | string array; required | Content MIME types supported. Popular MIME types may include “video/x-ms-wmv” for Windows Media and “video/x-flv” for Flash Video.
minduration | O | integer; optional | Minimum video ad duration in seconds.
maxduration | O | integer; optional | Maximum video ad duration in seconds.
protocol | X | integer; DEPRECATED | NOTE: Use of protocols instead is highly recommended. Supported video bid response protocol. At least one supported protocol must be specified in either the protocol or protocols attribute.
protocols | O | integer array; optional | Array of supported video bid response protocols. At least one supported protocol must be specified in either the protocol or protocols attribute.
w | O | integer; optional | Width of the video player in pixels.
h | O | integer; optional | Height of the video player in pixels.
startdelay | X | integer | Indicates the start delay in seconds for pre-roll, mid-roll, or post-roll ad placements.
linearity | X | integer | Indicates if the impression must be linear, nonlinear, etc. If none specified, assume all are allowed.
sequence | X | integer | If multiple ad impressions are offered in the same bid request, the sequence number will allow for the coordinated delivery of multiple creatives.
battr | X | integer array | Blocked creative attributes.
maxextended | X | integer | Maximum extended video ad duration if extension is allowed. If blank or 0, extension is not allowed. If -1, extension is allowed, and there is no time limit imposed. If greater than 0, then the value represents the number of seconds of extended play supported beyond the maxduration value.
minbitrate | O | integer; optional | Minimum bit rate in Kbps. Exchange may set this dynamically or universally across their set of publishers.
maxbitrate | O | integer; optional | Maximum bit rate in Kbps. Exchange may set this dynamically or universally across their set of publishers.
boxingallowed | X | integer; default 1 | Indicates if letter-boxing of 4:3 content into a 16:9 window is allowed, where 0 = no, 1 = yes. 
playbackmethod | X | integer array | Allowed playback methods. If none specified, assume all are allowed.
delivery | X | integer array | Supported delivery methods (e.g., streaming, progressive). If none specified, assume all are supported.
pos | X | integer | Ad position on screen.
companionad | X | object array | Array of Banner objects if companion ads are available.
api | X | integer array | List of supported API frameworks for this impression. If an API is not explicitly listed, it is assumed not to be supported.
companiontype | X | integer array | Supported VAST companion ad types.Recommended if companion Banner objects are included via the companionad array.
ext | X | object | Placeholder for exchange-specific extensions to OpenRTB.

<br>

- Object : Site

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
id | O | string; required | Exchange-specific site ID.
name | O | string; required | Site name (may be aliased at the publisher’s request).
domain | O | string; required | Domain of the site (e.g., “mysite.foo.com”).
cat | O | string array; required | Array of IAB content categories of the site.
sectioncat | X | string array | Array of IAB content categories that describe the current section of the site.
pagecat | X | string array | Array of IAB content categories that describe the current page or view of the site.
page | X | string | URL of the page where the impression will be shown.
ref | X | string | Referrer URL that caused navigation to the current page.
search | X | string | Search string that caused navigation to the current page.
mobile | X | integer | Mobile-optimized signal, where 0 = no, 1 = yes.
privacypolicy | X | integer | Indicates if the site has a privacy policy, where 0 = no, 1 = yes.
publisher | X | object | Details about the Publisher of the site.
content | O | object; optional | Details about the Content within the site.
keywords | X | string | Comma separated list of keywords about the site.
ext | X | object | Placeholder for exchange-specific extensions to OpenRTB.
<br>

- Object : App

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
id | O | string; required | Exchange-specific app ID.
name | O | string; required | App name (may be aliased at the publisher’s request).
bundle | O | string; required | Application bundle or package name (e.g., com.foo.mygame); intended to be a unique ID across exchanges.
domain | O | string; optional | Domain of the app (e.g., “mygame.foo.com”).
storeurl | O | string; optional | App store URL for an installed app; for QAG 1.5 compliance.
cat | O | string array; required | Array of IAB content categories of the app.
sectioncat | X | string array | Array of IAB content categories that describe the current section of the app.
pagecat | X | string array | Array of IAB content categories that describe the current page or view of the app.
ver | X | string | Application version.
privacypolicy | X | integer | Indicates if the app has a privacy policy, where 0 = no, 1 = yes.
paid | X | integer | 0 = app is free, 1 = the app is a paid version.
publisher | X | object | Details about the Publisher of the app.
content | O | object; optional | Details about the Content within the app.
keywords | X | string | Comma separated list of keywords about the app.
ext | X | object | Placeholder for exchange-specific extensions to OpenRTB.
<br>

- Object : Content

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
id | X | string | ID uniquely identifying the content.
episode | X | integer | Episode number (typically applies to video content).
title | X | string | Content title. Video Examples: “Search Committee” (television), “A New Hope” (movie), or “Endgame” (made for web). Non-Video Example: “Why an Antarctic Glacier Is Melting So Quickly” (Time magazine article).
series | X | string | Content series. Video Examples: “The Office” (television), “Star Wars” (movie), or “Arby ‘N’ The Chief” (made for web). Non-Video Example: “Ecocentric” (Time Magazine blog).
season | X | string | Content season; typically for video content (e.g., “Season 3”).
producer | X | object | Details about the content Producer.
url | X | string | URL of the content, for buy-side contextualization or review.
cat | O | string array | Array of IAB content categories that describe the content producer.
videoquality | X | integer | Video quality per IAB’s classification.
context | X | integer | Type of content (game, video, text, etc.).
contentrating | X | string | Content rating (e.g., MPAA).
userrating | X | string | User rating of the content (e.g., number of stars, likes, etc.).
qagmediarating | X | integer | Media rating per QAG guidelines.
keywords | X | string | Comma separated list of keywords describing the content.
livestream | X | integer | 0 = not live, 1 = content is live (e.g., stream, live blog).
sourcerelationship | X | integer | 0 = indirect, 1 = direct.
len | X | integer | Length of content in seconds; appropriate for video or audio.
language | X | string | Content language using ISO-639-1-alpha-2.
embeddable | X | integer | Indicator of whether or not the content is embeddable (e.g., an embeddable video player), where 0 = no, 1 = yes.
ext | X | object | Placeholder for exchange-specific extensions to OpenRTB.
<br>

- Object : Device

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
ua | O | string; optional | Browser user agent string.
geo | X | object | Location of the device assumed to be the user’s current location defined by a Geo object
dnt | O | integer; optional | Standard “Do Not Track” flag as set in the header by the browser, where 0 = tracking is unrestricted, 1 = do not track.
lmt | X | integer | “Limit Ad Tracking” signal commercially endorsed (e.g., iOS, Android), where 0 = tracking is unrestricted, 1 = tracking must be limited per commercial guidelines.
ip | O | string; required | IPv4 address closest to device.
ipv6 | X | string | IP address closest to device as IPv6.
devicetype | O | integer; optional | The general type of device.
make | X | string | Device make (e.g., “Apple”).
model | O | string; required | Device model (e.g., “iPhone”).
os | O | string; required | Device operating system (e.g., “iOS”).
osv | O | string; required | Device operating system version (e.g., “3.1.2”).
hwv | X | string | Hardware version of the device (e.g., “5S” for iPhone 5S).
h | X | integer | Physical height of the screen in pixels.
w | X | integer | Physical width of the screen in pixels.
ppi | X | integer | Screen size as pixels per linear inch.
pxratio | X | float | The ratio of physical pixels to device independent pixels.
js | X | integer | Support for JavaScript, where 0 = no, 1 = yes.
flashver | X | string | Version of Flash supported by the browser.
language | O | string; optional | Browser language using ISO-639-1-alpha-2.
carrier | O | string; required | Carrier or ISP (e.g., “VERIZON”). “WIFI” is often used in mobile to indicate high bandwidth (e.g., video friendly vs. cellular).
connectiontype | O | integer; optional | Network connection type.
ifa | O | string; required | ID sanctioned for advertiser use in the clear (i.e., not hashed).
didsha1 | X | string | Hardware device ID (e.g., IMEI); hashed via SHA1.
didmd5 | X | string | Hardware device ID (e.g., IMEI); hashed via MD5.
dpidsha1 | X | string | Platform device ID (e.g., Android ID); hashed via SHA1.
dpidmd5 | X | string | Platform device ID (e.g., Android ID); hashed via MD5.
macsha1 | X | string | MAC address of the device; hashed via SHA1.
macmd5 | X | string | MAC address of the device; hashed via MD5.
ext | X | object | Placeholder for exchange-specific extensions to OpenRTB.
<br>

- Object : User

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
id | O | string; optional | Exchange-specific ID for the user. At least one of id or buyerid is recommended.
buyerid | O | string; optional | Buyer-specific ID for the user as mapped by the exchange for the buyer. At least one of buyerid or id is recommended.
yob | O | integer; optional | Year of birth as a 4-digit integer.
gender | O | string; optional | Gender, where “M” = male, “F” = female, “O” = known to be other (i.e., omitted is unknown).
keywords | X | string | Comma separated list of keywords, interests, or intent.
customdata | X | string | Optional feature to pass bidder data that was set in the exchange’s cookie. The string must be in base85 cookie safe characters and be in any format. Proper JSON encoding must be used to include “escaped” quotation marks.
geo | X | object | Location of the user’s home base defined by a Geo object. This is not necessarily their current location.
data | X | object array | Additional user data. Each Data object represents a different data source.
ext | X | object | Placeholder for exchange-specific extensions to OpenRTB.
<br>

## Request sample
	{
		"id": "adpvideo31kjh39ah548adkjf93035",
		"bcat": [ "IAB25" ],
		"imp": [ {
			"id": "1",
			"bidfloor": 3.125,
			"bidfloorcur": "USD",
			"secure": 0,
			"video": {
				"mimes": [ "video\/mp4" ],
				"minduration": 5,
				"maxduration": 360,
				"protocols": [ 2, 3 ],
				"w": 640,
				"h": 360
			}
		 } ],
		"app": {
			"id": "adpvideo_1",
			"content": {
				"cat": [ "IAB24" ]
			},
			"name": "com.foobar.example",
			"bundle": "com.foobar.example",
			"storeurl": "https://play.google.com/store/apps/details?id=com.foobar.example&hl=ko",
			"cat": [ "IAB1" ]
		},
		"device": {
			"os": "iOS",
			"osv": "13.3.1",
			"devicetype": 4,
			"model": "iPhone11",
			"ua": "Mozilla/5.0 (iPhone; CPU iPhone OS 13_3_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148",
			"ip": "123.145.167.11",
			"carrier": "KT",
			"connectiontype": 2,
			"dnt": 0,
			"ifa": "1234abcd-1a2b-3c4d-5e6f-1ab7a1536772"
		},
		"user": {
			"id": ""
		}
	}
<br>

# 2. Bid Response Specification
## Object model

Object | Support | Extension field | Description
:--- | :---: | :---: | :---
BidResponse | O | X | Top-level object.
SeatBid | O | X | Collection of bids made by the bidder on behalf of a specific seat.
Bid | O | X | An offer to buy a specific impression under certain business terms.
<br>

## Object Specifications

- Object : BidResponse

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
id | O | string | ID of the bid request to which this is a response.
seatbid | O | object array | Array of seatbid objects; 1+ required if a bid is to be made.
bidid | X | string | Bidder generated response ID to assist with logging/tracking.
cur | O (USD/KRW only) | string | Bid currency using ISO-4217 alpha codes.
customdata | X | string | Optional feature to allow a bidder to set data in the exchange’s cookie. The string must be in base85 cookie safe characters and be in any format. Proper JSON encoding must be used to include “escaped” quotation marks.
nbr | X | integer | Reason for not bidding.
ext | X | object | Placeholder for bidder-specific extensions to OpenRTB.
<br>

- Object : SeatBid

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
bid | O | object array | Array of 1+ Bid objects each related to an impression. Multiple bids can relate to the same impression.
seat | O | string | ID of the bidder seat on whose behalf this bid is made.
group | X | integer | 0 = impressions can be won individually; 1 = impressions must be won or lost as a group.
ext | X | object | Placeholder for bidder-specific extensions to OpenRTB.
<br>

- Object : Bid

Attribute | Support | Type | Description
:--- | :---: | :---: | :---
id | O | string | Bidder generated bid ID to assist with logging/tracking.
impid | O | string | ID of the Imp object in the related bid request.
price | O | float | Bid price expressed as CPM although the actual transaction is for a unit impression only. Note that while the type indicates float, integer math is highly recommended when handling currencies (e.g., BigDecimal in Java).
adid | X | string | ID of a preloaded ad to be served if the bid wins.
nurl | X | string | Win notice URL called by the exchange if the bid wins; optional means of serving ad markup.
adm | O (VAST v3.0) | string | Optional means of conveying ad markup in case the bid wins; supersedes the win notice if markup is included in both.
adomain | O | string array | Advertiser domain for block list checking (e.g., “ford.com”). This can be an array of for the case of rotating creatives. Exchanges can mandate that only one domain is allowed.
bundle | O | string | Bundle or package name (e.g., com.foo.mygame) of the app being advertised, if applicable; intended to be a unique ID across exchanges.
iurl | O | string | URL without cache-busting to an image that is representative of the content of the campaign for ad quality/safety checking.
cid | O | string | Campaign ID to assist with ad quality checking; the collection of creatives for which iurl should be representative.
crid | O | string | Creative ID to assist with ad quality checking.
cat | O | string array | IAB content categories of the creative.
attr | X | integer array | Set of attributes describing the creative.
dealid | X | string | Reference to the deal.id from the bid request if this bid pertains to a private marketplace direct deal.
h | O | integer | Height of the creative in pixels.
w | O | integer | Width of the creative in pixels.
ext | X | object | Placeholder for bidder-specific extensions to OpenRTB.
<br>

![#f03c15](https://placehold.it/15/f03c15/000000?text=+) `See the link below for the vast 3.0 specification`  
https://www.iab.com/wp-content/uploads/2015/06/VASTv3_0.pdf

## Response sample
	{
	"id": "adpvideo31kjh39ah548adkjf93035",
	"cur": "USD",
	"seatbid": [ {
			"seat": "adpvideo",
			"bid": [ {
				"id": "adpv_20200101113200525565046535",
				"impid": "2",
				"price": 3.063600,
				"w": 1280,
				"h": 720,
				"adomain": [ "www.adpvideo.co.kr" ],
				"bundle": "com.adpacker.video",
				"iurl": "http://www.adpvideo.com/nasmedia/0912c76003cba4781/720",
				"cid": "50",
				"crid": "1077",
				"cat": [ "IAB7", "IAB7-1", "IAB7-25", "IAB7-31", "IAB7-32", "IAB7-38", "IAB7-44", "IAB7-45" ],
				"adm": "<?xml version=\"1.0\" encoding=\"UTF-8\"?><VAST version=\"3.0\" xmlns:xsi=\"http:\/\/www.w3.org\/2001\/XMLSchema-instance\" xsi:noNamespaceSchemaLocation=\"vast.xsd\"><Ad id=\"249\"><InLine><AdSystem version=\"1.0.14\">Adpacker_Video<\/AdSystem><AdTitle>TEST0101<\/AdTitle><Impression><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/beacon?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/Impression><Error><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/error?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&errcode=[ERRORCODE]&ord=[CACHEBUSTING]]]><\/Error><Creatives><Creative id=\"1077\" sequence=\"1\"><Linear skipoffset=\"00:00:15\"><Duration>00:00:15<\/Duration><TrackingEvents><Tracking event=\"start\"><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/start?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bpri=${AUCTION_PRICE}&spri=3.063600&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/Tracking><Tracking event=\"firstQuartile\"><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/firstq?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/Tracking><Tracking event=\"midpoint\"><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/midpoint?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/Tracking><Tracking event=\"thirdQuartile\"><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/thirdq?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/Tracking><Tracking event=\"progress\" offset=\"00:00:15\"><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/view?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/Tracking><Tracking event=\"complete\"><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/complete?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/Tracking><Tracking event=\"skip\"><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/skip?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/Tracking><\/TrackingEvents><MediaFiles><MediaFile delivery=\"progressive\" type=\"video\/mp4\" width=\"1280\" height=\"720\"><![CDATA[http:\/\/www.adpvideo.com\/nasmedia\/0912c76003cba4781\/720]]><\/MediaFile><\/MediaFiles><VideoClicks><ClickThrough><![CDATA[https:\/\/youtu.be\/test]]><\/ClickThrough><ClickTracking><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/click?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/ClickTracking><\/VideoClicks><Icons><Icon program=\"ad\" width=\"192\" height=\"62\" xPosition=\"left\" yPosition=\"bottom\" offset=\"00:00:00\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/click\/CLICK_25px.png]]><\/StaticResource><IconClicks><IconClickThrough><![CDATA[https:\/\/youtu.be\/test]]><\/IconClickThrough><IconClickTracking><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/clickicon?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/IconClickTracking><\/IconClicks><\/Icon><Icon program=\"skip_des_15\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:00\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_15_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_14\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:01\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_14_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_13\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:02\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_13_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_12\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:03\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_12_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_11\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:04\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_11_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_10\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:05\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_10_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_9\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:06\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_9_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_8\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:07\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_8_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_7\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:08\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_7_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_6\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:09\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_6_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_5\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:10\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_5_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_4\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:11\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_4_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_3\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:12\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_3_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_2\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:13\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_2_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip_des_1\" width=\"192\" height=\"66\" xPosition=\"right\" yPosition=\"bottom\" duration=\"00:00:01\" offset=\"00:00:14\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip_count\/25px\/skip_1_25px.png]]><\/StaticResource><\/Icon><Icon program=\"skip\" width=\"128\" height=\"95\" xPosition=\"right\" yPosition=\"bottom\" offset=\"00:00:15\"><StaticResource creativeType=\"image\/png\"><![CDATA[http:\/\/cdnet.nasmob.com\/adpvideo\/icon\/skip\/skip_25px.png]]><\/StaticResource><IconClicks><IconClickTracking><![CDATA[http:\/\/adpv1.admixer.co.kr:8080\/skip?ads_id=249&app_key=adpv_1&os=iOS&mime=video\/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]><\/IconClickTracking><\/IconClicks><\/Icon><\/Icons><\/Linear><\/Creative><\/Creatives><\/InLine><\/Ad><\/VAST>"
			} ]
		} ] 
	}
<br>

## ADM sample
	<?xml version="1.0" encoding="UTF-8"?>
	<VAST xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="3.0" xsi:noNamespaceSchemaLocation="vast.xsd">
		<Ad id="249">
		<InLine>
		<AdSystem version="1.0.14">Adpacker_Video</AdSystem>
		<AdTitle>TEST0101</AdTitle>
		<Impression><![CDATA[http://adpv1.admixer.co.kr:8080/beacon?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></Impression>
		<Error><![CDATA[http://adpv1.admixer.co.kr:8080/error?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&errcode=[ERRORCODE]&ord=[CACHEBUSTING]]]></Error>
		<Creatives>
			<Creative id="1077" sequence="1">
			<Linear skipoffset="00:00:15">
			<Duration>00:00:15</Duration>
			<TrackingEvents>
				<Tracking event="start"><![CDATA[http://adpv1.admixer.co.kr:8080/start?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bpri=${AUCTION_PRICE}&spri=3.063600&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></Tracking>
				<Tracking event="firstQuartile"><![CDATA[http://adpv1.admixer.co.kr:8080/firstq?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></Tracking>
				<Tracking event="midpoint"><![CDATA[http://adpv1.admixer.co.kr:8080/midpoint?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></Tracking>
				<Tracking event="thirdQuartile"><![CDATA[http://adpv1.admixer.co.kr:8080/thirdq?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></Tracking>
				<Tracking event="progress" offset="00:00:15"><![CDATA[http://adpv1.admixer.co.kr:8080/view?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></Tracking>
				<Tracking event="complete"><![CDATA[http://adpv1.admixer.co.kr:8080/complete?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></Tracking>
				<Tracking event="skip"><![CDATA[http://adpv1.admixer.co.kr:8080/skip?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></Tracking>
			</TrackingEvents>
			<MediaFiles>
				<MediaFile delivery="progressive" type="video/mp4" width="1280" height="720"><![CDATA[http://www.adpvideo.com/nasmedia/0912c76003cba4781/720]]></MediaFile>
			</MediaFiles>
			<VideoClicks>
				<ClickThrough><![CDATA[https://youtu.be/test]]></ClickThrough>
				<ClickTracking><![CDATA[http://adpv1.admixer.co.kr:8080/click?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></ClickTracking>
			</VideoClicks>
			<Icons>
				<Icon program="ad" width="192" height="62" xPosition="left" yPosition="bottom" offset="00:00:00">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/click/CLICK_25px.png]]></StaticResource>
					<IconClicks>
						<IconClickThrough><![CDATA[https://youtu.be/test]]></IconClickThrough>
						<IconClickTracking><![CDATA[http://adpv1.admixer.co.kr:8080/clickicon?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></IconClickTracking>
					</IconClicks>
				</Icon>
				<Icon program="skip_des_15" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:00">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_15_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_14" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:01">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_14_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_13" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:02">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_13_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_12" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:03">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_12_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_11" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:04">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_11_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_10" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:05">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_10_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_9" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:06">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_9_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_8" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:07">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_8_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_7" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:08">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_7_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_6" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:09">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_6_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_5" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:10">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_5_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_4" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:11">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_4_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_3" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:12">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_3_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_2" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:13">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_2_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip_des_1" width="192" height="66" xPosition="right" yPosition="bottom" duration="00:00:01" offset="00:00:14">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip_count/25px/skip_1_25px.png]]></StaticResource>
				</Icon>
				<Icon program="skip" width="128" height="95" xPosition="right" yPosition="bottom" offset="00:00:15">
					<StaticResource creativeType="image/png"><![CDATA[http://cdnet.nasmob.com/adpvideo/icon/skip/skip_25px.png]]></StaticResource>
					<IconClicks>
						<IconClickTracking><![CDATA[http://adpv1.admixer.co.kr:8080/skip?ads_id=249&app_key=adpv_1&os=iOS&mime=video/mp4&resolution=1280x720&bitrate=1979280&duration=15&model=null&screen=2&udid=null&udid_use=1&ifa_type=2&dev_ip=123.145.167.11&bidfloor=3.063600&rtb_type=0&billtype=3&imp_id=20200311113200_4_249_123.145.167.11_null_1790881387&bid_id=1790881387&report=1&offset=[CONTENTPLAYHEAD]&ord=[CACHEBUSTING]]]></IconClickTracking>
					</IconClicks>
				</Icon>
			</Icons>
			</Linear>
			</Creative>
		</Creatives>
		</InLine>
		</Ad>
	</VAST>
<br>

# 3. Substitution Macros

## Notifications
Macro | Description
:---: | :---
`${AUCTION_PRICE}` | Settlement price using the same currency and units as the bid. BidResponse(Bid.adm) includes a macro that must be replaced by the partner with the price it will be charged.
[ERRORCODE] | Replaced with one of the error codes listed in `VAST v3.0-Doc section2.4.2.3.` When the associated error occurs; reserved for error tracking URIs.
[CONTENTPLAYHEAD] | Replaced with the current time offset “HH:MM:SS.mmm” of the video content.
[CACHEBUSTING] | Replaced with a random 8-digit number.
<br>



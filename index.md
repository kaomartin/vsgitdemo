## Young Living - 3PL API Quick Start Guide 

![](vertopal_a8551728a94b4564bd4e4a4d25defcc3/media/image1.jpg){width="5.213333333333333in"
height="1.8533333333333333in"}

> Young Living
>
> 3PL API's
>
> Quick Start Guide
>
> Fulfillment and Inventory
>
> ![](vertopal_a8551728a94b4564bd4e4a4d25defcc3/media/image2.jpg){width="1.925in"
> height="1.8583333333333334in"}

This document is proprietary information and is the property of Young
Living Essential Oils

# 3PL API Overview 

Young Living Essential Oils (YLEO) provides APIs for Third Party
Logistics (3PL) partners to enable accurate and timely inventory control
and order fulfillment.

This Document provides details of the APIs used for all APIs integrated
with YLEO's Distributed Order Management System (DOMS) and the YLEO ERP
system which is JD Edwards (JDE). These APIs can be broken down into 2
main areas:

-   Inventory Control

## See Swagger documentation for our 3PL Inventory API

-   here:
    <https://tpl-inventory-api-stage.awsvodev.youngliving.com/swagger/index.html>

-   Order/Shipment Fulfillment

The following diagram depicts the integrations and general flow of data.

![Diagram Description automatically
generated](vertopal_a8551728a94b4564bd4e4a4d25defcc3/media/image3.png){width="6.533333333333333in"
height="3.6527777777777777in"}

This setup provides a high-level overview for required API installation
and connectivity to manage your operations and provide the necessary
two-way communication between your 3PL and Young Living.

Refer to the following table to see required APIs necessary to set up
for an initial launch.

+----------------------+----------------------+----------------------+
| API Name             | > Type of Call       | > Description        |
+======================+======================+======================+
| DOMS (Distributed    | Fulfillment Requests | > Includes shipments |
| Order Management     |                      | > from YL (Young     |
| System)              |                      | > Living)            |
|                      |                      | > Distributed Order  |
| > Fulfillment and    |                      | > Management system  |
| > Shipments          |                      | > that are ready to  |
|                      |                      | > ship. Can be       |
|                      |                      | > pushed to 3PL or   |
|                      |                      | > retrieved via a    |
|                      |                      | > GET FR request     |
+----------------------+----------------------+----------------------+
|                      | > POST Shipment      | > 3PL sends shipping |
|                      | > Confirmation       | > and tracking info  |
|                      |                      | > at the line-item   |
|                      |                      | > level.             |
+----------------------+----------------------+----------------------+
|                      | > GET SKU Master     | > Daily call to YL   |
|                      | > Synch (SMS)        | > to get all items   |
|                      |                      | > or just new or     |
|                      |                      | > changed items.     |
+----------------------+----------------------+----------------------+
|                      | > PUT Acknowledge    | > 3PL posts daily    |
|                      | > SMS                | > inventory levels   |
|                      |                      | > by LOT number.     |
+----------------------+----------------------+----------------------+
| SSR (Stock Status    | POST SSR             | 3PL posts daily      |
| Reporting)           |                      | inventory levels by  |
|                      |                      | LOT number.          |
+----------------------+----------------------+----------------------+
|                      | > GET                | > Contains the BoM   |
|                      |                      | > (Bill of           |
|                      | BoM                  | > Materials) for all |
|                      |                      | > our virtual kits   |
|                      |                      | > for a 3PL.         |
|                      |                      | >                    |
|                      |                      | > (Future state:     |
|                      |                      | > this can be        |
|                      |                      | > included in the    |
|                      |                      | > SMS response       |
|                      |                      | > payload)           |
+----------------------+----------------------+----------------------+
| > HSCodes            | > Get HS Codes       | > Includes all HS    |
|                      |                      | > (Harmonized        |
|                      |                      | > System) Codes for  |
|                      |                      | > all the items in   |
|                      |                      | > the SMS payload.   |
|                      |                      | > (Future state:     |
|                      |                      | > this can be        |
|                      |                      | > included in the    |
|                      |                      | > SMS response       |
|                      |                      | > payload)           |
+----------------------+----------------------+----------------------+
|                      | > POST Inventory     | > Used for 3PL to    |
|                      | > Adjustment         | > make inventory     |
|                      |                      | > adjustments        |
+----------------------+----------------------+----------------------+
|                      | > GET Advanced       | > Notifies 3PL of    |
|                      | >                    | > incoming shipments |
|                      | > Shipment           |                      |
|                      | >                    |                      |
|                      | > Notification       |                      |
|                      | >                    |                      |
|                      | > (ASN)              |                      |
+----------------------+----------------------+----------------------+
|                      | > PUT Acknowledge    | > 3PL acknowledges   |
|                      | > ASN                | > receipt of ASN     |
|                      |                      | > information.       |
|                      |                      | > Include YL         |
|                      |                      | > TransactionID and  |
|                      |                      | > 3PL unique         |
|                      |                      | > identifier         |
+----------------------+----------------------+----------------------+
|                      | > POST PO            | > 3PL reports        |
|                      | >                    | > receipt of         |
|                      | > Receipt            | > inventory to YL    |
+----------------------+----------------------+----------------------+
|                      | > GET                | > Includes list of   |
|                      | >                    | > all FRs still      |
|                      | > Pending FRs        | > pending in the YL  |
|                      |                      | > System             |
+----------------------+----------------------+----------------------+

# \*Green lines are minimum APIs required for go-live.

# Security Protocol and Verification 

NOTE: For further details on the Security Protocol, refer to the
Security Protocol and Verification Section in the YLEO-ERP-JDE-API 3PL
Technical Documentation.

# Inventory Control APIs 

It is critical that YL and its 3PL partners are in synch when it comes
to inventory items and levels. The 3PL Warehouse Management System (WMS)
utilizes the following YLEO APIs for inventory purposes:

NOTE: To review code examples for any of the APIs listed below, refer to
the JDE 3PL Technical Documentation, and search for the API example and
click the embedded pdf file.

### SKU Master Sync: 

Kit SKUs will be at the beginning of the file followed by stockable
items. This provides the inventory items for all shippable YLEO items.
The Receiver value will be the stock location number assigned to the
3PL.

Kits and Component can be linked using the FR. Additionally the 3PL can
call the Get BoM to identify Kits and Components.

#### SMS -- HTTP GET Request: 

The following is a GET call to our API.

This request downloads the Young Living SKU Master and Updates the 3PL
systems.

The recipient will use the ACCEPT header and specify the format desired.

This value will either be "application/xml" or "application/json".

The Sender will always be 1001 in the GET call

The Receiver value will be the number assigned to the 3PL

#### SMS -- HTTP PUT Request 

The following is a PUT call to our API. Acknowledgment:

After receiving the JSON body for the SkuMaster, you will need to send
Young Living Essential

Oils (YLEO) a PUT request with the \"TransactionReferenceNumber\" and
your own generated Unique ID for each transaction to confirm you
received the data successfully when you ran the GET Call.

This acknowledges that the 3PL received the data for the SKU\'s through
the GET Call.

### Stock Status Reporting (SSR) 

This is a POST submitted daily at the same time to YLEO with inventory
levels of each item at the LOT level.

#### Stock Status Report Post Call 

Create an HTTP REQUEST with the "Authentication" and "Timestamp" headers
referenced in the Security section.

Include accounts at the lot level for all items

LOT numbers can be either the YL Lot number or the Vendor Lot number but
not both.

#### API Post Call Response 

The sender will receive a response like the following upon successful
transfer (xml sample)

\<StockStatusResponse
xmlns:i=\"[http://www.w3.org/2001/XMLSchema-instance]{.ul}\"
xmlns=\"[http://schemas.datacontract.org/2004/07/EssentialApplication.ViewModels]{.ul}\"\>

\<BatchId\>T20160223195225\</BatchId\>

\<Message\>Ok\</Message\>

\</StockStatusResponse\>

The BatchId returned in the response is for the senders' records in
order to reference this specific transaction call if necessary.

## Advanced Shipment Notification (ASN) 

This API is used by the 3PL to get notifications of inbound items and
allows the 3PL to receive these items into inventory so YLEO inventory
levels for the 3PL are updated without the need for manual input.

#### ASN GET Call Initiation 

Create an HTTP REQUEST with the "Authentication" and "Timestamp" headers
mentioned in the Security section.

This is a GET call to our API.

The data recipient will have the option to receive the Purchase Order
information in either XML or JSON format.

The recipient will use the ACCEPT header and specify the format desired.

This value will either be "application/xml" or "application/json".

The Sender will always be 1001 in the GET call

The Receiver value will be the number assigned to the 3PL.

## POST -- Purchase Order Receipt 

The POST - Purchase Order Receipt call is to be used by the 3PL to send
a confirmation to Young Living that they have received in inventory the
open Purchase Order item(s).

Create an HTTP REQUEST with the "Authentication" and "Timestamp" headers
following your Security protocol.

The sender will need to ensure that a "Content-type" header is included
with the format of the POST BODY chosen to send the actual PO receipt
information.

This value will either be "application/xml" or "application/json".

The POST BODY will contain an xml or json representation of the PO
receipt information being sent.

The Sender value will be the stock location (branch plant) number
assigned to the 3PL.

The Receiver value will always be 1001 in the POST call

## Inventory Adjustment Processing (IAP) 

This API is used to adjust inventory levels when items are lost,
damaged, or a count correction is required.

#### POST Call: 

The following is a POST call to the YLEO IAP API.

The sender will need to ensure that a "Content-type" header is included
with the format of the POST BODY chosen to send the actual stock status
information.

This value will either be "application/xml" or "application/json".

The POST BODY will contain an xml or json representation of the stock
status information being sent.

The Sender value will be the stock location number assigned to the 3PL.

The Receiver value will always be 1001 in the POST call.

LOT numbers can be either the YL Lot number or the Vendor Lot number but
not both.

Create an HTTP REQUEST with the "Authentication" and "Timestamp" headers
mentioned in the Security section.

This is a POST call to our API.

The sender will need to ensure that a "Content-type" header is included
with the format of the POST BODY chosen for use to send the actual PO
receipt information.

This value will either be "application/xml" or "application/json".

The POST BODY will contain an xml or json representation of the PO
receipt information being sent.

The Sender value will be the stock location number assigned to the 3PL
The Receiver value will always be 1001 in the POST call.

#### API POST Call Response 

The sender will receive a response like the following upon successful
transfer:

> PurchaseOrderReceiptResponse
> xmlns:i=\"[http://www.w3.org/2001/XMLSchema-instance]{.ul}\"
> xmlns=\"[http://schemas.datacontract.org/2004/07/EssentialApplication.ViewModels]{.ul}\"\>
>
> \<BatchId\>R20160224224628\</BatchId\>
>
> \<Message\>Ok\</Message\>
>
> \</PurchaseOrderReceiptResponse\>
>
> The BatchId returned in the response is for the senders' records to
> refer to this specific transaction call if necessary.

# Order/Shipment Fulfillment APIs 

When an order is paid in full in the YLEO system and there is inventory
to fulfill that order, the YLEO Distributed Order Management system
(DOMS) will generate a Fulfillment Request (FR)

All Shipments in this status are then ready to be retrieved by the 3PL
for fulfillment or can be pushed to the 3PL's endpoint.

After the 3PL ships the order, they send the shipment Confirmation to
YLEO. When this happens, the DOMS updates the shipment information and
the ERP system. Inventory at the 3PL is then decremented for the items
in the shipment and the inventory reservation in the DOMS is marked as
"consumed."

It is important for the 3PL to send the Lot numbers for lot controlled
items.

The tracking number, carrier and shipping method must also be sent for
fulfilled shipments.

The 3PL can also reject an FR for various reasons. If an FR is rejected,
the Lines must each include a reason code for the rejection.

## **Get Pending Fulfillment Requests**

If an FR is missing in the 3PL WMS, the FR can either be retrieved via
an API or it can be re-pushed from the DOMS via a script.

There is an API on the DOMS that can be called to get all pending FRs
for a site

Ask for more information about this API


<!--- ## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/kaomartin/vsgitdemo/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/kaomartin/vsgitdemo/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
--->
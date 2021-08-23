---
title: Get a price sheet
description: Obtain a price sheet for a given market and view. Supports filters to get history by month.
ms.date: 01/24/2020
ms.service: partner-dashboard
ms.subservice: partnercenter-sdk
ms.localizationpriority: medium
---

# Get a price sheet

Applies to:

- Partner API

This topic explains how to get a price sheet for a given market and view. This method supports filters to get history by month.

## Prerequisites

- Credentials as described in [Partner API authentication](api-authentication.md). This scenario only supports application user authentication. Application-ony is not yet supported.

## Details

- Current returns data only for Azure plan consumption and reservation products.
- Current [pricing](pricing.md) includes all meters and products available during the current month to the date the API is called. Previous months include all meters and products available for the given month.
- Consumption meter prices are only in USD, partners are to use the foreign exchange rates API to calculate local currency costs.
- Consumption meter prices are estimated retail prices. Partner discounts are available via [partner earned credit](https://docs.microsoft.com/partner-center/partner-earned-credit-explanation).
- Reservations meter prices include the CSP partner discounts. Estimated retail prices for reservations can be found in the reservations shared services downloadable from the Partner Center "Pricing and offers" page.
- More information about Azure plan pricing can be found in the [Azure plan pricing documentation](https://docs.microsoft.com/partner-center/azure-plan-price-list).
- Partner pricing and foreign exchange rate APIs are not part of the [Partner Center SDK](https://docs.microsoft.com/partner-center/develop/get-started).
- This method returns the price list as a file stream. File stream is either a .csv file or a zip compressed version of the .csv. Details about how to request compressed files are included below.

## REST request

### Request syntax

| Method   | Request URI                                                                                                 |
|----------|-------------------------------------------------------------------------------------------------------------|
| **GET** | https://api.partner.microsoft.com/v1.0/sales/pricesheets(Market='{market}',PricesheetView='{view}')/$value                                     |

### URI required parameters

Use the following path parameters to request which market and type of price sheet you want.

| Name                   | Type     | Required | Description                                                     |
|------------------------|----------|----------|-----------------------------------------------------------------|
|Market                      | string   | Yes       | Two letter country code for the market being requested       |
|PricesheetView	| string   | Yes       | The type of price sheet being requested, this can be azure_consumption or azure_reservations       |

### URI filter parameters

Use the following filter parameters.

| Name                   | Type     | Required | Description                                                     |
|------------------------|----------|----------|-----------------------------------------------------------------|
|Timeline| string   | No| Defaults to current if not passed. Possible values are history, current and future.       |
|Month| string   | No| Only required if history is requested, must adhere to YYYYMM for the price sheet being requested.       |

### Request headers

- See [Partner REST headers](headers.md) for more information.

In addition to the above headers, pricing files can be retrieved as compressed reducing bandwidth and download times. By default the files are not compressed. To get compressed versions of the files you can include the below header value. Realize that compressed sheets are only available from April 2020 onward, all sheets prior to April 2020 are only available as not compressed.

| Header                   | Value Type     | Value | Description                                                     |
|------------------------|----------|----------|-----------------------------------------------------------------|
|Accept-Encoding| string   | deflate| Optional. If omitted file stream is not compressed.       |

### Request example

```http
GET https://api.partner.microsoft.com/v1.0/sales/pricesheets(Market='ad',PricesheetView='azure_consumption')/$value?timeline=history&month=201909 HTTP/1.1
Authorization: Bearer
Host: api.partner.microsoft.com

```

## REST response

If successful, this method returns the price list as a file stream. File stream is either a .csv file or a zip compressed version of the .csv.

### Response example

``` http
HTTP/1.1 200 OK
Cache-Control: private
Content-Length: 42180180
Content-Type: application/octet-stream
Content-Disposition: attachment; filename=pricesheet
Request-ID: 9f8bed52-e4df-4d0c-9ca6-929a187b0731
Date: Wed, 02 Oct 2019 03:41:20 GMT

"ProductTitle","ProductId","SkuId","SkuTitle","Publisher","SkuDescription","UnitOfMeasure","TermDuration","Market","Currency","UnitPrice","PricingTierRangeMin","PricingTierRangeMax","EffectiveStartDate","EffectiveEndDate","MeterIds","MeterType","Tags“
"Advanced Data Security - SQL Database","DZH318Z0C16V","001J","Advanced Data Security - SQL Database - Standard - US East 2","Microsoft","Advanced Data Security - SQL Database - Standard - US East 2","1 Node/Month","payG-1","US","USD","15","","","3/1/2018 12:00:00 AM","11/30/9999 11:59:59 PM","cb0969aa-aaaa-4d6c-ab4b-7e182fa06aff","1 Node/Month","Azure“
======= Truncated ==============

```

### Response success and error codes

Each response comes with an HTTP status code that indicates success or failure and additional debugging information. Use a network trace tool to read this code, error type, and additional parameters. For the full list, see [Error Codes](error-codes.md).

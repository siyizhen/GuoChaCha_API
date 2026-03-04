# GuoChaCha API

## 1. Introduction
GuoChaCha API is a service platform focused on **Apple & Android device serial number / IMEI lookup** and **ID-photo receipt services**.

- **Official site**: `https://api.51gcc.com`

## 2. Unified Request Specification

- **Gateway URL**: `https://open-api.51gcc.com`
- **HTTP Method**: POST (`application/x-www-form-urlencoded`)
- **Common parameters**:
  - `api_id`: API ID
  - `api_key`: API key from the user center
  - `api_test`: Whether to return demo data (`1=yes; 0=no`)
  - `timestamp`: Millisecond timestamp (used for signature)
  - `sign`: Signature (used for verification)

## 3. Call Examples

### PHP
```php
<?php
    $url = 'https://open-api.51gcc.com';
    $params = ['sn' => 'YOUR_VALUE', 'api_id' => '1', 'api_test' => 'YOUR_VALUE', 'api_key' => 'YOUR_VALUE'];
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($params));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = curl_exec($ch);
    curl_close($ch);
    echo $response;
```

### Python
```python
import requests

url = "https://open-api.51gcc.com"
params = {
    'sn': 'YOUR_VALUE',
    'api_id': '1',
    'api_test': 'YOUR_VALUE',
    'api_key': 'YOUR_VALUE',
}
response = requests.post(url, data=params)
print(response.text)
```

### Java
```java
import java.io.*;
import java.net.*;
import java.util.*;

public class ApiClient {
    public static void main(String[] args) throws Exception {
        String url = "https://open-api.51gcc.com";
        Map<String, String> params = new HashMap<>();
        params.put("sn", "YOUR_VALUE");
        params.put("api_id", "1");
        params.put("api_test", "YOUR_VALUE");
        params.put("api_key", "YOUR_VALUE");

        URL obj = new URL(url);
        HttpURLConnection con = (HttpURLConnection) obj.openConnection();
        con.setRequestMethod("POST");
        con.setDoOutput(true);

        StringBuilder postData = new StringBuilder();
        for (Map.Entry<String, String> param : params.entrySet()) {
            if (postData.length() != 0) postData.append('&');
            postData.append(URLEncoder.encode(param.getKey(), "UTF-8"));
            postData.append('=');
            postData.append(URLEncoder.encode(param.getValue(), "UTF-8"));
        }

        try (DataOutputStream wr = new DataOutputStream(con.getOutputStream())) {
            wr.writeBytes(postData.toString());
        }

        int responseCode = con.getResponseCode();
        BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));
        String inputLine;
        StringBuffer response = new StringBuffer();
        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }
        in.close();
        System.out.println(response.toString());
    }
}
```

## 4. Example: General Query · General IMEI Lookup (api_id=1)

**Request parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Device IMEI |
| api_id | Yes | `1` |
| api_test | Yes | `1=yes; 0=no` |
| api_key | Yes | API key from user center |
| timestamp | Yes | Millisecond timestamp |
| sign | Yes | Signature |

**Success example**
```json
{
  "code": 200,
  "msg": "ok",
  "data": {
    "image": "https://snapi.51gcc.com/storage/site/no_name.png",
    "result": [
      { "key": "imei", "text": "imei", "value": 356698882008924, "notice": "" },
      { "key": "model", "text": "型号", "value": "iPhone 16 Pro", "notice": "" }
    ],
    "other_result": []
  }
}
```

**Failure example**
```json
{
  "code": 403,
  "msg": "签名错误",
  "time": 1770703778,
  "data": []
}
```

## 5. Status Codes

| Code | Meaning |
|---:|---|
| 200 | Request succeeded |
| 210 | Parameter error |
| 220 | System function error |
| 230 | Data fetch failed |
| 403 | Request refused (signature / key / permission, etc.) |
| 404 | Resource not found |
| 429 | Too many requests (rate limit) |
| 500 | Internal server error |
| 600 | Insufficient balance |

<!-- API_LIST_START -->

## 6. API List (by category)

Gateway example: `https://open-api.51gcc.com`

Common parameters (shared by all APIs): `api_id`, `api_key`, `api_test`, `timestamp`, `sign`.

### 1. IMEI / Serial APIs

**Response field description**

| Field | Type | Description | Example |
|---|---|---|---|
| image | string | Device image URL | https://snapi.51gcc.com/storage/site/no_name.png |
| key | string | Field key | model |
| text | string | Field label | 型号 |
| value | string\|array | Field value | iPhone XR |

#### (1) General Query
Supports all devices with IMEI.

##### (1.1) api_id=1 · General IMEI Query
This service can query basic info (model, brand, manufacturer, etc.) for any device that has an IMEI.
- **Billing**: billable (0.03 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | IMEI |

##### (1.2) api_id=2 · General Color / Model / Capacity Query
This service can query model, color and capacity for Apple, Xiaomi/Redmi, Huawei/Honor, Samsung, OPPO/realme/OnePlus, VIVO/IQOO and other devices.
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial / IMEI / MEID or MAC |

#### (2) Apple
Provides a series of APIs for Apple serial / IMEI lookup, activation date and warranty status check, GSX verification reports, etc. It can precisely detect refurbished units, swap units, supervised / corporate devices, demo units, and deeply inspect carrier lock, Apple ID activation lock, supervision (MDM) lock and black/white list status based on official data.

##### (2.1) api_id=3 · Warranty & Activation Date
> (no color / capacity)
This API returns model, valid purchase date, activation status, estimated purchase date, registration status, warranty status, warranty expiration, phone support, AppleCare, etc. It helps users determine whether iPad, iPod and iPhone are within warranty. Note: MacBook will not return warranty/activation info; instead it returns info about swap device / supervised (lease) device / refurbished device.
- **Billing**: billable (0.13 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial number |

##### (2.2) api_id=4 · Warranty & Activation Pro
> (with color / model / capacity)
This Pro version additionally returns color and capacity, and the same warranty / activation information as api_id=3.
- **Billing**: billable (0.31 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.3) api_id=5 · Color / Capacity / Model
Query Apple device model, color and capacity.
- **Billing**: billable (0.08 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial number |

##### (2.4) api_id=6 · Repair Status
> (whether the device is currently under official repair)
Check whether the device is currently in Apple’s official repair process (**not** whether it has ever been repaired).
- **Billing**: billable (0.31 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.5) api_id=7 · Color / Capacity / Model / ID Lock
> (strongly recommended for second-hand)
This API only supports Apple devices. It returns color, model, capacity and ID lock status. For iPhone/iPad it shows Activation Lock. Note: MacBook does not return capacity / color, only Activation Lock status. If the result is different from your expectation due to product limitations, this cannot be used as a refund reason. It checks whether “Find My iPhone” is enabled and warns about second-hand risks when the lock is on.
- **Billing**: billable (0.24 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial number |

##### (2.6) api_id=8 · ID Activation Lock
> (iPhone / iPad, etc.)
Checks whether the official “Find My iPhone” / Activation Lock is enabled on iPhone/iPad. Buyers should ensure Find My iPhone is **off** before purchasing a used device. For Mac devices, use **Mac Activation Lock** (api_id=9) instead.
- **Billing**: billable (0.13 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.7) api_id=9 · Mac Activation Lock
> (iMac / MacBook, etc.)
Use cases: Mac authenticity check, second-hand trading, device recycling, ownership transfer, pre‑repair check. It precisely checks Mac Activation Lock / iCloud lock status. Before transferring ownership you should turn off Find My Mac and the lock to ensure the new owner can activate the device.
- **Billing**: billable (2.04 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.8) api_id=10 · SIM Lock
> (carrier network lock)
When “SIM Lock: Locked” is returned, the device is carrier‑locked and can only use specific network providers (e.g. T‑Mobile, Sprint, MVNO). Unlock usually requires finishing contract and clearing debt via the carrier. Locked devices are less valuable in the second‑hand market.
- **Billing**: billable (0.24 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.9) api_id=11 · ID Black / White List
> (lost / stolen mode for iPhone / iPad)
Some serial numbers are not supported. For Mac devices, use **Mac ID Black/White List** (api_id=12).
- **Billing**: billable (0.24 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.10) api_id=12 · Mac ID Black / White List
> (lost / stolen mode for iMac / MacBook)
White list: iCloud status is **Clean** and “Find My Mac” is off — no activation lock, ownership can be transferred freely. Black list: Marked as lost/stolen by owner — cannot be activated until the original Apple ID clears it. This API is used to identify such states for Mac devices.
- **Billing**: billable (2.87 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.11) api_id=13 · Apple ID Hint
> (Apple ID hint for iPhone / iPad)
Returns only **obfuscated Apple ID hint**, not the full account (e.g. `j*****@gmail.com`).
- **Billing**: billable (3.82 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.12) api_id=14 · GSX Device Report
> (must‑check for new / used devices)
Official GSX‑backed inspection report including:
- Activation lock status
- Warranty status and AppleCare+
- Carrier & network lock
- Repair history
- Original purchase info
- Full tech specs (color, storage, etc.)

- **Billing**: billable (2.00 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.13) api_id=15 · Supervision (MDM) Lock
> (strongly recommended for second‑hand)
Checks if the device is supervised / corporate / rental (MDM‑locked). Such devices may restrict settings, app installs, and may still legally belong to a company. When MDM is detected, you usually need the original organization to remove it before safe reuse.
- **Billing**: billable (2.40 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.14) api_id=16 · Official Replacement Device
Checks whether the device is an official Apple replacement (swap) unit.
- **Billing**: billable (0.13 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.15) api_id=17 · Replaced Device (Black Device)
Checks whether the device has ever been replaced during warranty (black device check).
- **Billing**: billable (0.13 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.16) api_id=18 · Special Channel Devices
> (identify demo / swap / supervised / special‑supply devices)
Distinguishes demo units, swap devices, supervised / rental units, official replacement devices, official refurbished devices, etc., based on GSX data and Apple’s internal labels.
- **Billing**: billable (0.55 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.17) api_id=19 · Basic Query
> (mainland / US / HK models, etc.)
Focuses on base device info: iCloud status, activation status, warranty period, and official black/white list status. It does **not** include detailed carrier contract / policy info.
- **Billing**: billable (0.44 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.18) api_id=20 · Carrier Query A
> (carrier + SIM lock)
Returns locked carrier and SIM‑lock status.
- **Billing**: billable (0.66 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.19) api_id=21 · Carrier Query B
> (carrier + SIM lock + warranty + phone support)
Adds detailed warranty info and phone support coverage on top of Carrier Query A.
- **Billing**: billable (0.87 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.20) api_id=22 · Carrier Query C
> (carrier + warranty info + GSMA black/white list)
Provides global‑level GSMA black/white list info together with carrier & warranty details.
- **Billing**: billable (1.42 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.21) api_id=23 · Demo Device Info
Identifies Apple demo units and returns detailed info including model, color, capacity, activation and warranty info.
- **Billing**: billable (1.43 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.22) api_id=24 · Advanced Query A
> (without carrier / SIM lock)
Detailed Apple device info, including basic GSMA blacklist status but **no** carrier/SIM‑lock info.
- **Billing**: billable (0.77 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.23) api_id=25 · Advanced Query B
> (with carrier / blacklist status)
Focuses on carrier binding, lock status and basic blacklist checks; suitable for most background checks.
- **Billing**: billable (1.09 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.24) api_id=26 · Advanced Query Pro
> (with carrier / blacklist history)
Adds full blacklist history tracking and more lifecycle signals on top of Advanced Query B; designed for high‑risk device screening.
- **Billing**: billable (1.31 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (2.25) api_id=27 · GSMA Blacklist History
> (black history)
Only the **first IMEI** is supported; using IMEI2 will always appear “white‑list normal”. Returns full GSMA blacklist history (insert/remove records) and is brand‑agnostic (Apple, Samsung, Xiaomi, etc.).
- **Billing**: billable (0.88 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | IMEI |

##### (2.26) api_id=28 · Sales Info & GSX Verification
> (includes supervision lock and advanced info)
Combines sales channel and GSX data to generate a full lifecycle report: production, sales, activation, warranty, repairs, lock states, etc. Suitable for high‑value or high‑risk devices.
- **Billing**: billable (14.37 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

#### (3) Huawei / Honor
Provides APIs for Huawei / Honor devices: model, capacity, color, activation date, warranty info, etc.

##### (3.1) api_id=29 · Huawei Model / Capacity / Color
- **Billing**: billable (0.06 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (3.2) api_id=30 · Huawei Warranty & Activation
Returns warranty, activation status, model, and dates for Huawei devices.
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial / IMEI / MEID or MAC |

##### (3.3) api_id=31 · Honor Model / Capacity / Color
- **Billing**: billable (0.06 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (3.4) api_id=32 · Honor Model / Capacity / Color (Alt)
- **Billing**: billable (0.06 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (3.5) api_id=33 · Honor Warranty & Activation
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

#### (4) Xiaomi / Redmi
Provides APIs for Xiaomi / Redmi devices: model, capacity, color, activation date, warranty, activation lock, etc.

##### (4.1) api_id=34 · Model / Capacity / Color
- **Billing**: billable (0.06 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (4.2) api_id=35 · Warranty & Activation
- **Billing**: billable (0.31 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (4.3) api_id=36 · Activation Lock
Checks Xiaomi / Redmi activation / ID lock status.
- **Billing**: billable (0.06 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | IMEI |

#### (5) OPPO / realme / OnePlus
Model / capacity / color, activation date, warranty info, etc.

##### (5.1) api_id=37 · Model / Capacity / Color
- **Billing**: billable (0.06 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (5.2) api_id=38 · OPPO / OnePlus Warranty & Activation
- **Billing**: billable (0.31 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (5.3) api_id=39 · realme Warranty & Activation
- **Billing**: billable (0.31 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

#### (6) VIVO / IQOO
Model / capacity / color, activation date, warranty info, etc.

##### (6.1) api_id=40 · Model / Capacity / Color
- **Billing**: billable (0.06 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (6.2) api_id=41 · Warranty & Activation
- **Billing**: billable (0.31 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

#### (7) Samsung

##### (7.1) api_id=42 · Model / Capacity / Color
- **Billing**: billable (0.06 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

##### (7.2) api_id=43 · Warranty & Activation
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

#### (8) ZTE

##### (8.1) api_id=44 · Warranty & Activation
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | IMEI |

#### (9) DJI

##### (9.1) api_id=45 · Warranty & Activation
Returns DJI device warranty / activation / model / date and related DJI Care entitlements.
- **Billing**: billable (0.31 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial number |

#### (10) ASUS

##### (10.1) api_id=46 · Warranty & Activation
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial or IMEI |

#### (11) Meizu

##### (11.1) api_id=47 · Warranty & Activation
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial number |

#### (12) Dell

##### (12.1) api_id=48 · Warranty & Config
> (including Alienware)
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial number |

#### (13) HP

##### (13.1) api_id=49 · Warranty Details
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial number |

#### (14) Acer

##### (14.1) api_id=50 · Warranty Details
- **Billing**: billable (0.12 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | Serial number |

#### (15) Motorola

##### (15.1) api_id=51 · Warranty & Activation
- **Billing**: billable (0.31 CNY / call)

**Business parameters**

| Name | Required | Description |
|---|---|---|
| sn | Yes | IMEI |

### 2. ID‑Photo Receipt APIs

<!-- API_LIST_END -->


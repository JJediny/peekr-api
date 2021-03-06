# Peekr-API
Peekr API allows running vulnerability scanning of Docker images through the free Peekr service.

## Basic Usage
### Step 1
Log in to Peekr [https://peekr.aquasec.com](https://peekr.aquasec.com) and Get user name & API Key from UI
![Peekr Api](img/peekr_api.png?raw=true "Peek API")
### Step 2
Generate `Athorization` header through Base64(user:API Key). This header should be included with all Peekr REST API calls.
```
    Authorization: Basic dXNlcm5hbWU6YXBpdG9rZW4=
```
### Step 3
#### [POST /apiscan](#scan)
Scan your image by providing repository name, image tag and registry name. Don't forget to send the Authorization header as part of the request.
Note: By default Peekr comes with 2 registries:  
`Docker Hub`  
`Quay.io`  
 You can add your own private registries through the Peekr UI.
```json
    POST https://peekr.aquasec.com/apiscan
    
    {
        "registry_name":"Docker Hub",
        "image":"mongo",
        "tag":"3.3.5"
    }
```
The response is a JSON that contains the scan_id.

### Step 4
Get scan results for the image using `scan_id` received from the [/apiscan](#apiscan) API
```
    GET https://peekr.aquasec.com/apiscan/<scan_id>
```
If scan is finished The response is a JSON contains scan results.
If still under scan you will get `"state": "PENDING"`

## API
- [Scan](#scan)
    - [POST](#post-apiscan-1)
- [Scan Results](#scan-results)
    - [GET](#get-apiscanscan_id)    
- [User Scans](#user-scans)
    - [GET](#get-user_scans)

## Scan
#### POST /apiscan

###### Description
Returns all repository tags.

###### Request (example)
```
    Request URL:https://peekr.aquasec.com/apiscan
    Request Method:POST
    
    Authorization: Basic dXNlcm5hbWU6YXBpdG9rZW4=
    Content-Type: application/json
    
    {
        "registry_name":"Docker Hub",
        "image":"mongo",
        "tag":"latest"
    }
```

###### Response (example)
```json
{
  "scan_id": "4KLuSmKc3IAafUHWLR1e",
  "state": "FINISHED"
}

```

## Scan Results
#### GET /apiscan/:scan_id

###### Description
Will return `scan` and `scan_results`. `scan_results` include:  
`INSPECT`: inspect data of the image (docker inspect)  
`OS_INFO`: basic OS image info  
`SCAN_RESULTS`: all vulnerabilities found nested by file/package they affect   
###### Request (example)
```
    Request URL:https://peekr.aquasec.com/apiscan/4KLuSmKc3IAafUHWLR1e
    Request Method:GET
    
    Authorization: Basic dXNlcm5hbWU6YXBpdG9rZW4=
```
###### Response (example)
```
{
  "scan":{"scan_id": "4KLuSmKc3IAafUHWLR1e", "image_id": "sha256:2af0a84bf165c291e2033327584a67158b5befe2fa2d739e13f6c8c71998b634", "image": "mongo",…},
  "scan_results": [
    {
        "scan_id": "4KLuSmKc3IAafUHWLR1e",
        "type": "INSPECT",
        "result":{"Id": "cf57aeefb867a91f97821d20ff490b63a83266bf5b9a331c1f04c3d71d581b74", "Args":["mongod" ], "Name": "/P9NZ9ZBsYi89",…}
    },
    {
        "scan_id": "4KLuSmKc3IAafUHWLR1e",
        "type": "OS_INFO",
        "result": "Debian GNU/Linux 7 (wheezy)"
    },
    {
        "scan_id": "4KLuSmKc3IAafUHWLR1e",
        "type": "SCAN_RESULTS",
        "result":[{"sha1": "", "filename": "libc6_2.13-38+deb7u10_amd64.deb", "vulnerabilities":[{"name": "CVE-2015-0235",…]
    },
    {
        "scan_id": "4KLuSmKc3IAafUHWLR1e",
        "type": "PROFILE_RESULTS",
        "result":{"Name": "P9NZ9ZBsYi89", "Stat":{"cpu": "0.800000", "nthr": "16",…}
    }
  ]
}
```

## User Scans
#### GET /user_scans

###### Description
Returns all user performed scans. 
###### Request (example)
```
    Request URL:https://peekr.aquasec.com/user_scans
    Request Method:GET
```
###### Response (example)
```json 
[
  {
    "scan_id": "4KLuSmKc3IAafUHWLR1e",
    "image_id": "",
    "image": "mongo",
    "tag": "latest",
    "registry": "https://index.docker.io/",
    "date": 1462351404,
    "date_original": 0,
    "vulns_critical": 0,
    "vulns_high": 22,
    "vulns_medium": 63,
    "vulns_low": 14,
    "archived": false,
    "state": "FINISHED",
    "error_msg": "",
    "reused_scan": false
  },
  {
    "scan_id": "GfMy130vyTU14OvpmEem",
    "image_id": "",
    "image": "centos",
    "tag": "latest",
    "registry": "https://index.docker.io/",
    "date": 1462273244,
    "date_original": 0,
    "vulns_critical": 0,
    "vulns_high": 15,
    "vulns_medium": 38,
    "vulns_low": 9,
    "archived": false,
    "state": "FINISHED",
    "error_msg": "",
    "reused_scan": false
  },
]

```

# Peekr-API
Peekr API allows running vulnerability scanning of Docker images through the free Peekr service.

## Basic Usage
### Step 1
Log in to Peekr [https://peekr.scalock.com](https://peekr.scalock.com) and Get user name & password from UI
![Peekr Api](img/peekr_api.png?raw=true "Peek API")
### Step 2
Set `Athorization` header Basic Auth using user name and password 
```
    Authorization: Basic dXNlcm5hbWU6YXBpdG9rZW4=
```

### Step 3 
#### [GET /registries](#registries)
Get the registry id you want to use
```
    GET https://peekr.scalock.com/registries
```

### Step 4
#### [POST /scan](#scan)
Scan your image if you know its repository and tag, if not use [`/repositories`](#repositories) and [`/repositories/:image_name/tags`](#repository-tags) for finding image for scanning
```json
    POST https://peekr.scalock.com/scan
    
    {
        "registry_id":"LHFxNZWfTbDAJtXMH8Am",
        "image":"mongo",
        "tag":"latest"
    }
```
### Step 5
Get detailed scan results for the image using `scan_id` received from scanning the image
```
    GET https://peekr.scalock.com/user_scans/<scanned_image_id>
```


## API
- [Registries](#registries)
    - [GET](#get-registries-1)
- [Repositories](#repositories)
    - [POST](#post-repos)
- [Repository tags](#repository-tags)
    - [POST](#post-reposimage_nametags)
- [Scan](#scan)
    - [POST](#post-scan-1)
- [Scan Results](#scan-results)
    - [GET](##get-user_scansscan_id)    
- [User Scans](#user-scans)
    - [GET](#get-user_scans)

## Registries
#### Get /registries 

###### Description

Registries GET request return all saved registries for current user. 
By default Docker Hub and Quay.io.

###### Request (example)
```
    Request URL:https://peekr.scalock.com/registries
    Request Method:GET
    
    Authorization: Basic dXNlcm5hbWU6YXBpdG9rZW4=
```

###### Response (example)
```json
[
  {
    "registry_id": "LHFxNZWfTbDAJtXMH8Am",
    "user_id": "rDtgYHjk75UHF7el7pzP",
    "user_name": "",
    "registry_url": "https://index.docker.io/",
    "registry_name": "Docker Hub"
  },
  {
    "registry_id": "ANYNqESuFgoqqodRy1WT",
    "user_id": "rDtgYHjk75UHF7el7pzP",
    "user_name": "",
    "registry_url": "https://quay.io/",
    "registry_name": "Quay.io"
  }
]

```

## Repositories
#### POST /repos 

###### Description
Will return search results for the `search` field. Repository POST will return 100 of the closest matches.

###### Request (example)
```json
    Request URL:https://peekr.scalock.com/repos
    Request Method:POST
      
    Authorization: Basic dXNlcm5hbWU6YXBpdG9rZW4=
    Content-Type: application/json
    
    {
        "registry_id": "LHFxNZWfTbDAJtXMH8Am", 
        "search": "mongo"
    }
```
###### Response (example)
```json
{
  "num_pages": 26,
  "num_results": 2567,
  "page": "1",
  "page_size": 100,
  "query": "mongo",
  "results": [
    {
      "name": "mongo",
      "description": "MongoDB document databases provide high availability and easy scalability.",
      "is_official": true,
      "is_trusted": false,
      "is_public": false
    },   
    {
      "name": "mongo-express",
      "description": "Web-based MongoDB admin interface, written with Node.js and express",
      "is_official": true,
      "is_trusted": false,
      "is_public": false
    },
  ]
}

```

## Repository Tags
#### POST /repos/:image_name/tags 

###### Description
Returns all repository tags.

###### Request (example)
```json
    Request URL:https://peekr.scalock.com/repos/mongo/tags
    Request Method:POST
      
    Authorization: Basic dXNlcm5hbWU6YXBpdG9rZW4=
    Content-Type: application/json
    
    {
        "registry_id": "LHFxNZWfTbDAJtXMH8Am"
    }
```

###### Response (example)
```
[
  "2.2.7",
  ...
  ...
  "3",
  "latest"
]
```

## Scan
#### POST /scan

###### Description
Returns all repository tags.

###### Request (example)
```
    Request URL:https://peekr.scalock.com/scan
    Request Method:POST
    
    Authorization: Basic dXNlcm5hbWU6YXBpdG9rZW4=
    Content-Type: application/json
    
    {
        "registry_id":"LHFxNZWfTbDAJtXMH8Am",
        "image":"mongo",
        "tag":"latest"
    }
```

###### Response (example)
```json
{
  "scan_id": "4KLuSmKc3IAafUHWLR1e",
  "image_id": "sha256:2af0a84bf165c291e2033327584a67158b5befe2fa2d739e13f6c8c71998b634",
  "image": "mongo",
  "tag": "latest",
  "registry": "https://index.docker.io/",
  "date": 1462351681,
  "date_original": 1462351407,
  "vulns_critical": 0,
  "vulns_high": 22,
  "vulns_medium": 63,
  "vulns_low": 14,
  "archived": false,
  "state": "FINISHED",
  "error_msg": "",
  "reused_scan": false
}

```

## Scan Results
#### GET /user_scans/:scan_id

###### Description
Will return `scan` result and `scan_results`. `scan_results` include:
`INSPECT`: inspect data of the image (docker inspect)
`OS_INFO`: basic OS image info
`SCAN_RESULTS`: all vulnerabilities found nested by file/package they affect 
`PROFILE_RESULTS`: image profiling at run time results. including cpu usage, network connection attempts, executable files ran and etc.
###### Request (example)
```
    Request URL:https://peekr.scalock.com/user_scans/4KLuSmKc3IAafUHWLR1e
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
    Request URL:https://peekr.scalock.com/user_scans
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
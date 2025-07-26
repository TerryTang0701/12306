# Component Reference Guide

## Core Classes and Modules

### 1. Main Application (`run.py`)

#### Functions
- `parser_arguments(argv)` - Command line argument parser

### 2. Ticket Selection Engine (`init/select_ticket_info.py`)

#### Class: `select`
**Purpose**: Main booking engine orchestrating the entire ticket booking process

**Attributes:**
- `cdn_list` (list): Available CDN endpoints
- `httpClint` (HTTPClient): HTTP client instance
- `login` (GoLogin): Login manager
- `cookies` (str): Session cookies
- `urls` (dict): URL configurations
- `passengerTicketStrList` (str): Passenger ticket information
- `oldPassengerStr` (str): Existing passenger data
- `set_type` (str): Selected seat type
- `flag` (bool): Control flag for ticket display

**Methods:**
- `__init__()` - Initialize booking system
- `get_ticket_info()` - Display configuration info (static)
- `station_table(from_station, to_station)` - Convert station names to codes
- `call_login(auth=False)` - Handle authentication
- `main()` - Main booking execution loop

### 3. Login Management (`init/login.py`)

#### Class: `GoLogin`
**Purpose**: Handle user authentication and session management

**Methods:**
- `__init__(session, is_auto_code, auto_code_type)` - Initialize login system
- `go_login()` - Execute complete login process
- `auth()` - Perform session authentication
- `get_login_code()` - Retrieve login CAPTCHA
- `verify_login_code()` - Verify CAPTCHA solution

### 4. Query System (`inter/Query.py`)

#### Class: `query`
**Purpose**: Real-time ticket availability checking

**Attributes:**
- `session` - Main selection object
- `httpClint` (HTTPClient): HTTP client for queries
- `from_station` (str): Origin station code
- `to_station` (str): Destination station code
- `station_trains` (list): Target train numbers
- `_station_seat` (list): Preferred seat types
- `station_dates` (list): Travel dates
- `ticke_peoples_num` (int): Number of passengers

**Methods:**
- `__init__(...)` - Initialize query system
- `sendQuery()` - Execute ticket availability query
- `station_seat(index)` - Get seat type mapping
- `check_is_need_train(ticket_info)` - Validate train selection

### 5. HTTP Client (`myUrllib/httpUtils.py`)

#### Class: `HTTPClient`
**Purpose**: Advanced HTTP client with 12306-specific optimizations

**Attributes:**
- `_s` (requests.Session): Underlying session object
- `_cdn` (str): Current CDN endpoint
- `cdnList` (list): Available CDN endpoints
- `_proxies` (dict): Proxy configuration

**Methods:**
- `__init__(is_proxy, cdnList=None)` - Initialize HTTP client
- `send(urls, data=None, **kwargs)` - Send HTTP request
- `set_cookies(kwargs)` - Set session cookies
- `get_cookies()` - Retrieve current cookies
- `del_cookies()` - Clear all cookies
- `setHeaders(headers)` - Update request headers
- `resetHeaders()` - Reset to default headers
- `setHeadersHost(host)` - Set Host header
- `setHeadersReferer(referer)` - Set Referer header

### 6. Order Submission (`inter/SubmitOrderRequest.py`)

#### Class: `submitOrderRequest`
**Purpose**: Handle standard ticket order submission

**Attributes:**
- `session` - Main selection object
- `secretStr` (str): Encrypted booking string
- `train_no` (str): Train number
- `set_type` (str): Seat type
- `passengerTicketStrList` (str): Passenger information
- `train_date` (str): Travel date

**Methods:**
- `__init__(...)` - Initialize order submission
- `sendSubmitOrderRequest()` - Submit ticket order
- `data_apr()` - Prepare order data

#### Class: `submitOrderRequestByAfterNate`
**Purpose**: Handle standby order submission

**Methods:**
- `__init__(session, secretList, tickerNo)` - Initialize standby order
- `sendSubmitOrderRequest()` - Submit standby order
- `data_apr()` - Prepare standby order data

### 7. Advanced Order Submission (`inter/AutoSubmitOrderRequest.py`)

#### Class: `autoSubmitOrderRequest`
**Purpose**: Advanced automatic order submission with validation

**Methods:**
- `__init__(...)` - Initialize advanced order system
- `sendAutoSubmitOrderRequest()` - Execute complete order workflow
- `data_apr()` - Prepare order data
- `check_face()` - Perform face verification if required

### 8. CAPTCHA Verification (`verify/localVerifyCode.py`)

#### Class: `Verify`
**Purpose**: Local machine learning-based CAPTCHA solving

**Attributes:**
- `textModel` - Text recognition model
- `imgModel` - Image classification model

**Methods:**
- `__init__()` - Initialize verification system
- `verify(fn)` - Perform CAPTCHA verification
- `loadTextModel()` - Load text recognition model
- `loadImgModel()` - Load image classification model

**Helper Functions:**
- `get_text(img, offset=0)` - Extract text from CAPTCHA
- `base64_to_image(base64_code)` - Convert base64 to image

### 9. Queue Management (`inter/GetQueueCount.py`)

#### Class: `getQueueCount`
**Purpose**: Manage ticket queue and availability

**Methods:**
- `__init__(...)` - Initialize queue manager
- `sendGetQueueCount()` - Check queue status
- `data_apr()` - Prepare queue query data

#### Class: `queryQueueByAfterNate`
**Purpose**: Handle standby queue queries

**Methods:**
- `__init__(...)` - Initialize standby queue
- `sendQueryQueueByAfterNate()` - Query standby status

### 10. Passenger Management (`inter/GetPassengerDTOs.py`)

#### Class: `getPassengerDTOs`
**Purpose**: Manage passenger information and ticket allocation

**Attributes:**
- `selectObj` - Main selection object
- `ticket_peoples` (list): Passenger list
- `set_type` (str): Seat type
- `is_more_ticket_num` (int): Ticket quantity adjustment

**Methods:**
- `__init__(...)` - Initialize passenger manager
- `sendGetPassengerDTOs()` - Retrieve passenger information
- `getPassengerTicketStrListAndOldPassengerStr()` - Format passenger data
- `filterPassengerByNum()` - Filter passengers by ticket availability

### 11. CDN Management (`agency/cdn_utils.py`)

#### Class: `CDNProxy`
**Purpose**: Multi-threaded CDN testing and optimization

**Attributes:**
- `cdns` (list): CDN endpoints to test
- `results` (list): Test results

**Methods:**
- `__init__(cdns)` - Initialize CDN proxy
- `run()` - Execute CDN testing

**Functions:**
- `open_cdn_file(cdnFile)` - Load CDN list from file
- `sortCdn()` - Sort CDNs by performance
- `filterCdn()` - Filter and test CDN endpoints

### 12. Proxy Management (`agency/agency_tools.py`)

#### Class: `proxy`
**Purpose**: Handle proxy configuration and rotation

**Attributes:**
- `proxy_list` (list): Available proxy servers

**Methods:**
- `__init__()` - Initialize proxy manager
- `get_proxy()` - Get proxy list
- `filter_proxy()` - Test proxy availability
- `setProxy()` - Configure proxy for HTTP client

### 13. Notification Systems

#### Email (`config/emailConf.py`)
**Function: `sendEmail(msg)`**
- Send email notifications for booking results
- Supports SMTP with SSL/TLS
- Configurable via `EMAIL_CONF` in TickerConfig

#### WeChat (`config/serverchanConf.py`)
**Function: `sendServerChan(msg)`**
- Send WeChat notifications via Server酱
- Requires Server酱 secret key
- Configurable via `SERVER_CHAN_CONF`

### 14. Configuration Management (`config/`)

#### URL Configuration (`config/urlConf.py`)
- `urls` (dict): Complete mapping of 12306 API endpoints
- Each endpoint includes retry logic, timing, and headers

#### Common Configuration (`config/configCommon.py`)
**Functions:**
- `checkSleepTime(session)` - Enforce sleep periods
- `checkDate(station_dates)` - Validate travel dates
- `seat_conf` (dict): Seat type mappings
- `seat_conf_2` (dict): Reverse seat type mappings

#### Cookie Management (`config/getCookie.py`)
**Functions:**
- `getDrvicesID(session)` - Get device ID for session
- `request_device_id(session)` - Request new device ID
- `request_alg_id(session)` - Get algorithm ID

#### Logging (`config/logger.py`)
**Functions:**
- `log(msg, func="info")` - Write timestamped log entries
- `getLogFile()` - Get current log file path
- `isAnotherDay(s)` - Check if log rotation needed

### 15. Exception Classes (`myException/`)

#### Core Exceptions
- `UserPasswordException` - Authentication failures
- `PassengerUserException` - Passenger information errors
- `ticketIsExitsException` - Ticket availability issues
- `ticketConfigException` - Configuration problems
- `ticketNumOutException` - Quantity limit exceeded
- `balanceException` - Insufficient account balance

### 16. Order Confirmation (`inter/CheckOrderInfo.py`)

#### Class: `checkOrderInfo`
**Purpose**: Validate and confirm order details

**Methods:**
- `__init__(...)` - Initialize order checker
- `sendCheckOrderInfo()` - Validate order information
- `data_apr()` - Prepare validation data

### 17. Queue Confirmation (`inter/ConfirmSingleForQueue.py`)

#### Class: `confirmSingleForQueue`
**Purpose**: Confirm single ticket in queue

**Methods:**
- `__init__(...)` - Initialize queue confirmation
- `sendConfirmSingleForQueue()` - Execute queue confirmation
- `data_apr()` - Prepare confirmation data

### 18. Face Verification (`inter/ChechFace.py`)

#### Class: `chechFace`
**Purpose**: Handle face verification for standby orders

**Methods:**
- `__init__(...)` - Initialize face verification
- `sendChechFace()` - Execute face verification process

### 19. User Validation (`inter/CheckUser.py`)

#### Class: `checkUser`
**Purpose**: Continuous user session validation

**Methods:**
- `__init__(session)` - Initialize user checker
- `sendCheckUser()` - Validate user session

### 20. Ticket Initialization (`inter/LiftTicketInit.py`)

#### Class: `liftTicketInit`
**Purpose**: Initialize ticket booking system

**Methods:**
- `__init__(session)` - Initialize ticket system
- `reqLiftTicketInit()` - Request system initialization

## Data Structures

### Ticket Information Format
```python
ticket_info = {
    "status": bool,           # Query success
    "secretStr": str,         # Booking secret
    "train_no": str,          # Train number
    "stationTrainCode": str,  # Station train code
    "train_date": str,        # Travel date
    "seat": int,              # Seat type code
    "leftTicket": str,        # Remaining tickets
    "is_more_ticket_num": int # Adjusted passenger count
}
```

### URL Configuration Format
```python
url_config = {
    "req_url": str,      # API endpoint
    "req_type": str,     # HTTP method
    "Referer": str,      # Referer header
    "Host": str,         # Target host
    "re_try": int,       # Retry attempts
    "re_time": float,    # Retry delay
    "s_time": float,     # Request delay
    "is_logger": bool,   # Enable logging
    "is_json": bool,     # JSON response
    "is_cdn": bool       # Use CDN
}
```

### Passenger Data Format
```python
passenger_data = {
    "passengerTicketStrList": str,  # Ticket string
    "oldPassengerStr": str,         # Existing passenger data
    "set_type": str                 # Seat type
}
```

## Integration Points

### Main Workflow Integration
1. `select.main()` → `GoLogin.go_login()` → Authentication
2. `select.main()` → `query.sendQuery()` → Ticket search
3. `query.sendQuery()` → `submitOrderRequest.sendSubmitOrderRequest()` → Order
4. Order confirmation → Notification system → User alert

### Error Handling Integration
- All components raise specific exceptions
- Main loop catches and handles appropriately
- Logging system records all significant events
- Notification system alerts on critical failures

### Configuration Integration
- `TickerConfig.py` provides centralized settings
- All components reference configuration
- Runtime configuration validation
- Environment-specific overrides supported

This reference guide provides detailed information about all components, their relationships, and integration points within the 12306 ticket booking system.
# Function Reference Guide

## Core Application Functions

### Entry Point (`run.py`)

#### `parser_arguments(argv)`
```python
def parser_arguments(argv):
    """
    Parse command line arguments for the application
    
    Parameters:
        argv (list): Command line arguments
        
    Returns:
        argparse.Namespace: Parsed arguments with 'operate' attribute
        
    Example:
        args = parser_arguments(['r'])
        print(args.operate)  # 'r'
    """
```

## Main Booking Engine (`init/select_ticket_info.py`)

### Class: `select`

#### `__init__()`
```python
def __init__(self):
    """
    Initialize the booking system
    
    Sets up:
        - CDN list for load balancing
        - HTTP client with proxy support
        - Login manager
        - URL configurations
        - Seat type mappings
    """
```

#### `get_ticket_info()` (static)
```python
@staticmethod
def get_ticket_info():
    """
    Display current configuration and system information
    
    Prints:
        - Software version
        - Python version check
        - Configuration summary (stations, dates, seats, passengers)
        - QQ group information
        
    Raises:
        Exception: If Python version < 3.6.0
    """
```

#### `station_table(from_station, to_station)`
```python
def station_table(self, from_station, to_station):
    """
    Convert human-readable station names to system codes
    
    Parameters:
        from_station (str): Departure station name (e.g., "北京")
        to_station (str): Arrival station name (e.g., "上海")
        
    Returns:
        tuple: (from_station_code, to_station_code)
        
    Example:
        codes = select.station_table("北京", "上海")
        # Returns: ('BJP', 'SHH')
    """
```

#### `call_login(auth=False)`
```python
def call_login(self, auth=False):
    """
    Initiate login process with sleep time checking
    
    Parameters:
        auth (bool): If True, only perform authentication
                    If False, perform full login
        
    Returns:
        bool: Login success status
    """
```

#### `main()`
```python
def main(self):
    """
    Main booking execution loop
    
    Process:
        1. Initialize ticket system
        2. Get device ID
        3. Authenticate user
        4. Start user validation thread
        5. Get passenger information
        6. Enter booking loop with timing control
        7. Query tickets and submit orders
        
    Handles all exceptions and continues execution
    """
```

## Query System (`inter/Query.py`)

### Class: `query`

#### `__init__(...)`
```python
def __init__(self, selectObj, from_station, to_station, from_station_h, 
             to_station_h, _station_seat, station_trains, ticke_peoples_num, 
             station_dates=None):
    """
    Initialize query system
    
    Parameters:
        selectObj: Main selection object
        from_station (str): Origin station code
        to_station (str): Destination station code
        from_station_h (str): Origin station name (human readable)
        to_station_h (str): Destination station name (human readable)
        _station_seat (list): Preferred seat type codes
        station_trains (list): Target train numbers
        ticke_peoples_num (int): Number of passengers
        station_dates (list, optional): Travel dates
    """
```

#### `sendQuery()`
```python
def sendQuery(self):
    """
    Execute ticket availability query
    
    Returns:
        dict: Query result with keys:
            - status (bool): Query success status
            - secretStr (str): Booking secret string
            - train_no (str): Train number
            - stationTrainCode (str): Station train code
            - train_date (str): Travel date
            - seat (int): Available seat type code
            - leftTicket (str): Remaining ticket count
            - is_more_ticket_num (int): Adjusted passenger count
            - cdn (str): CDN endpoint used
            
    Example:
        result = query_instance.sendQuery()
        if result.get("status"):
            print(f"Found tickets on {result['train_no']}")
    """
```

#### `station_seat(index)`
```python
def station_seat(self, index):
    """
    Get seat type code mapping
    
    Parameters:
        index (str): Seat type name (e.g., "二等座")
        
    Returns:
        int: Seat type code (e.g., 30 for "二等座")
    """
```

#### `check_is_need_train(ticket_info)`
```python
def check_is_need_train(self, ticket_info):
    """
    Check if train matches selection criteria
    
    Parameters:
        ticket_info (list): Train information from query
        
    Returns:
        bool: True if train matches criteria
    """
```

## HTTP Client (`myUrllib/httpUtils.py`)

### Helper Functions

#### `_set_header_default()`
```python
def _set_header_default():
    """
    Generate default HTTP headers for 12306 requests
    
    Returns:
        OrderedDict: Default headers including User-Agent, Accept-Encoding, etc.
    """
```

#### `_set_user_agent()`
```python
def _set_user_agent():
    """
    Generate user agent string
    
    Returns:
        str: User agent string for browser simulation
    """
```

### Class: `HTTPClient`

#### `__init__(is_proxy, cdnList=None)`
```python
def __init__(self, is_proxy, cdnList=None):
    """
    Initialize HTTP client with advanced features
    
    Parameters:
        is_proxy (int): Proxy usage flag (0=disabled, 1=enabled)
        cdnList (list, optional): List of CDN endpoints
    """
```

#### `send(urls, data=None, **kwargs)`
```python
def send(self, urls, data=None, **kwargs):
    """
    Send HTTP request with retry logic and error handling
    
    Parameters:
        urls (dict): URL configuration with keys:
            - req_url (str): Request URL path
            - Host (str): Target host
            - Referer (str): Referer header
            - is_json (bool): Expect JSON response
            - re_try (int): Retry attempts
            - s_time (float): Sleep time between requests
        data (str, optional): POST data payload
        **kwargs: Additional requests parameters
        
    Returns:
        dict|str|bytes: Response data based on configuration
        
    Example:
        config = {
            "req_url": "/otn/leftTicket/query",
            "Host": "kyfw.12306.cn",
            "Referer": "https://kyfw.12306.cn/",
            "is_json": True,
            "re_try": 3,
            "s_time": 0.1
        }
        response = client.send(config)
    """
```

#### `set_cookies(kwargs)`
```python
def set_cookies(self, kwargs):
    """
    Set session cookies from dictionary list
    
    Parameters:
        kwargs (list): List of cookie dictionaries
        
    Example:
        client.set_cookies([{"JSESSIONID": "abc123"}])
    """
```

#### `setHeaders(headers)`
```python
def setHeaders(self, headers):
    """
    Update request headers
    
    Parameters:
        headers (dict): Header key-value pairs
        
    Returns:
        HTTPClient: Self for method chaining
        
    Example:
        client.setHeaders({"Content-Type": "application/json"})
    """
```

## Order Submission (`inter/SubmitOrderRequest.py`)

### Class: `submitOrderRequest`

#### `__init__(...)`
```python
def __init__(self, selectObj, secretStr, from_station, to_station, 
             train_no, set_type, passengerTicketStrList, oldPassengerStr, 
             train_date, ticke_peoples):
    """
    Initialize order submission
    
    Parameters:
        selectObj: Main selection object
        secretStr (str): Encrypted booking string from query
        from_station (str): Origin station code
        to_station (str): Destination station code
        train_no (str): Train number
        set_type (str): Seat type
        passengerTicketStrList (str): Formatted passenger information
        oldPassengerStr (str): Existing passenger data
        train_date (str): Travel date (YYYY-MM-DD)
        ticke_peoples (list): List of passenger names
    """
```

#### `sendSubmitOrderRequest()`
```python
def sendSubmitOrderRequest(self):
    """
    Submit ticket order to 12306 system
    
    Process:
        1. Prepare order data
        2. Submit to booking API
        3. Handle response validation
        4. Proceed to order confirmation if successful
        
    Raises:
        ticketIsExitsException: If order submission fails
        
    Example:
        order = submitOrderRequest(...)
        order.sendSubmitOrderRequest()
    """
```

#### `data_apr()`
```python
def data_apr(self):
    """
    Prepare order submission data
    
    Returns:
        list: Tuple list of form data for order submission
    """
```

## CAPTCHA Verification (`verify/localVerifyCode.py`)

### Helper Functions

#### `get_text(img, offset=0)`
```python
def get_text(img, offset=0):
    """
    Extract text region from CAPTCHA image
    
    Parameters:
        img (numpy.ndarray): Input image
        offset (int): Horizontal offset for text extraction
        
    Returns:
        numpy.ndarray: Processed text region for model input
    """
```

#### `base64_to_image(base64_code)`
```python
def base64_to_image(base64_code):
    """
    Convert base64 encoded image to OpenCV format
    
    Parameters:
        base64_code (str): Base64 encoded image data
        
    Returns:
        numpy.ndarray: OpenCV image array
        
    Example:
        img = base64_to_image("iVBORw0KGgoAAAANSUhEUgAA...")
    """
```

### Class: `Verify`

#### `__init__()`
```python
def __init__(self):
    """
    Initialize CAPTCHA verification system
    
    Loads machine learning models:
        - Text recognition model (model.v2.0.h5)
        - Image classification model (12306.image.model.h5)
    """
```

#### `verify(fn)`
```python
def verify(self, fn):
    """
    Perform complete CAPTCHA verification
    
    Parameters:
        fn (str): Base64 encoded CAPTCHA image
        
    Returns:
        list: List of selected image positions as strings
        
    Example:
        verifier = Verify()
        positions = verifier.verify(base64_image)
        # Returns: ['1', '3', '7'] for clicking positions 1, 3, and 7
    """
```

#### `loadTextModel()`
```python
def loadTextModel(self):
    """
    Load text recognition model if not already loaded
    
    Loads model from 'model.v2.0.h5' file
    """
```

#### `loadImgModel()`
```python
def loadImgModel(self):
    """
    Load image classification model if not already loaded
    
    Loads model from '12306.image.model.h5' file
    """
```

## Notification System

### Email Notifications (`config/emailConf.py`)

#### `sendEmail(msg)`
```python
def sendEmail(msg):
    """
    Send email notification for booking results
    
    Parameters:
        msg (str): Email message content
        
    Configuration:
        Uses EMAIL_CONF from TickerConfig.py
        
    Example:
        sendEmail("订票成功！订单号：EB52743573")
        
    Raises:
        Exception: If email configuration is invalid
    """
```

### WeChat Notifications (`config/serverchanConf.py`)

#### `sendServerChan(msg)`
```python
def sendServerChan(msg):
    """
    Send WeChat notification via Server酱 service
    
    Parameters:
        msg (str): Notification message
        
    Configuration:
        Uses SERVER_CHAN_CONF from TickerConfig.py
        
    Example:
        sendServerChan("抢票成功通知")
        
    Returns:
        bool: True if notification sent successfully
    """
```

## CDN Management (`agency/cdn_utils.py`)

#### `open_cdn_file(cdnFile)`
```python
def open_cdn_file(cdnFile):
    """
    Load CDN list from file
    
    Parameters:
        cdnFile (str): CDN file name
        
    Returns:
        list: List of CDN endpoints
        
    Example:
        cdns = open_cdn_file("filter_cdn_list")
    """
```

#### `sortCdn()`
```python
def sortCdn():
    """
    Sort CDN endpoints by performance
    
    Reads from 'tmp/cdn_result' and sorts by response time
    """
```

#### `filterCdn()`
```python
def filterCdn():
    """
    Filter and test CDN endpoints for optimal performance
    
    Process:
        1. Load CDN list from 'cdn_list' file
        2. Test each CDN endpoint concurrently
        3. Measure response times
        4. Save working CDNs to 'filter_cdn_list'
        
    Usage:
        Called via: python run.py c
    """
```

### Class: `CDNProxy`

#### `__init__(cdns)`
```python
def __init__(self, cdns):
    """
    Initialize CDN testing thread
    
    Parameters:
        cdns (list): List of CDN endpoints to test
    """
```

#### `run()`
```python
def run(self):
    """
    Execute CDN testing in separate thread
    
    Tests each CDN endpoint and records performance metrics
    """
```

## Configuration Functions (`config/configCommon.py`)

#### `checkSleepTime(session)`
```python
def checkSleepTime(session):
    """
    Check if current time is within sleep period (23:00-06:00)
    
    Parameters:
        session: Main session object
        
    Blocks execution during sleep hours to avoid server load
    """
```

#### `checkDate(station_dates)`
```python
def checkDate(station_dates):
    """
    Validate travel dates
    
    Parameters:
        station_dates (list): List of travel dates
        
    Returns:
        bool: True if dates are valid
    """
```

#### `getNowTimestamp()`
```python
def getNowTimestamp():
    """
    Get current timestamp in milliseconds
    
    Returns:
        int: Current timestamp
    """
```

## Logging Functions (`config/logger.py`)

#### `log(msg, func="info")`
```python
def log(msg, func="info"):
    """
    Write log message to file with timestamp
    
    Parameters:
        msg (str): Log message content
        func (str): Log level ("info", "error", "debug")
        
    Example:
        log("开始查询车票", "info")
        log("登录失败", "error")
    """
```

#### `getLogFile()`
```python
def getLogFile():
    """
    Get current log file path with date rotation
    
    Returns:
        str: Path to current log file
    """
```

## Cookie Management (`config/getCookie.py`)

#### `getDrvicesID(session)`
```python
def getDrvicesID(session):
    """
    Get device ID for session based on configuration
    
    Parameters:
        session: Main session object
        
    Uses COOKIE_TYPE setting:
        1: Selenium-based device ID
        2: Web API device ID
        3: Manual device ID from config
    """
```

#### `request_device_id(session)`
```python
def request_device_id(session):
    """
    Request new device ID from 12306 API
    
    Parameters:
        session: Main session object
        
    Returns:
        str: Device ID string
    """
```

## Passenger Management (`inter/GetPassengerDTOs.py`)

### Class: `getPassengerDTOs`

#### `sendGetPassengerDTOs()`
```python
def sendGetPassengerDTOs(self):
    """
    Retrieve passenger information from 12306 system
    
    Returns:
        dict: Passenger information and contact details
        
    Example:
        passenger_mgr = getPassengerDTOs(...)
        info = passenger_mgr.sendGetPassengerDTOs()
    """
```

#### `getPassengerTicketStrListAndOldPassengerStr(secretStr, secretList)`
```python
def getPassengerTicketStrListAndOldPassengerStr(self, secretStr, secretList):
    """
    Format passenger data for order submission
    
    Parameters:
        secretStr (str): Booking secret string
        secretList (str): Standby booking secret
        
    Returns:
        dict: Formatted passenger data with keys:
            - status (bool): Success status
            - passengerTicketStrList (str): Ticket string
            - oldPassengerStr (str): Passenger string
            - set_type (str): Seat type
    """
```

## Queue Management (`inter/GetQueueCount.py`)

### Class: `getQueueCount`

#### `sendGetQueueCount()`
```python
def sendGetQueueCount(self):
    """
    Check current queue status and ticket availability
    
    Returns:
        dict: Queue information with keys:
            - ticket (str): Available ticket count
            - countT (str): Total queue count
            - op_2 (str): Queue operation status
            
    Example:
        queue_mgr = getQueueCount(...)
        status = queue_mgr.sendGetQueueCount()
    """
```

## Proxy Management (`agency/agency_tools.py`)

### Class: `proxy`

#### `setProxy()`
```python
def setProxy(self):
    """
    Configure proxy settings for HTTP client
    
    Returns:
        dict: Proxy configuration for requests library
        
    Format:
        {
            'http': 'http://proxy_ip:port',
            'https': 'https://proxy_ip:port'
        }
    """
```

#### `get_proxy()`
```python
def get_proxy(self):
    """
    Load proxy list from file
    
    Returns:
        list: List of proxy server addresses
    """
```

#### `filter_proxy()`
```python
def filter_proxy(self):
    """
    Test proxy servers for availability
    
    Returns:
        list: List of working proxy servers
    """
```

## Utility Functions

### Time Functions (`config/AutoSynchroTime.py`)

#### `autoSynchroTime()`
```python
def autoSynchroTime():
    """
    Synchronize system time with network time server
    
    Attempts to sync with multiple NTP servers for accurate timing
    """
```

### File Operations (`config/configCommon.py`)

#### `getWorkDir()`
```python
def getWorkDir():
    """
    Get application working directory
    
    Returns:
        str: Path to working directory
    """
```

#### `getTmpDir()`
```python
def getTmpDir():
    """
    Get temporary files directory
    
    Returns:
        str: Path to temp directory
    """
```

#### `getVCodeDir()`
```python
def getVCodeDir():
    """
    Get verification code images directory
    
    Returns:
        str: Path to CAPTCHA images directory
    """
```

This function reference provides detailed information about all public functions, their parameters, return values, and usage examples. Use this guide alongside the API documentation for complete understanding of the system's capabilities.
# 12306 Ticket Booking Assistant - API Documentation

## Table of Contents

- [Overview](#overview)
- [Core Components](#core-components)
- [Configuration](#configuration)
- [Main APIs](#main-apis)
- [Network Layer](#network-layer)
- [Verification System](#verification-system)
- [Order Management](#order-management)
- [Notification System](#notification-system)
- [Exception Handling](#exception-handling)
- [Utilities](#utilities)
- [Usage Examples](#usage-examples)

## Overview

This is an automated ticket booking assistant for China Railway's 12306 system. The application supports automatic CAPTCHA solving, login management, ticket querying, order submission, and notification services.

### Key Features
- Automatic CAPTCHA recognition (local/cloud)
- Smart ticket booking with fallback options
- Email and WeChat notifications
- CDN proxy support for improved performance
- Comprehensive error handling and logging

## Core Components

### 1. Main Application Entry Point

#### `run.py`

**Function: `parser_arguments(argv)`**
```python
def parser_arguments(argv):
    """
    Parse command line arguments for the application
    
    Args:
        argv (list): Command line arguments
        
    Returns:
        argparse.Namespace: Parsed arguments
        
    Usage:
        python run.py r  # Run ticket booking
        python run.py c  # Filter CDN list
        python run.py t  # Test email and server notifications
    """
```

### 2. Ticket Selection Engine

#### `init/select_ticket_info.py`

**Class: `select`**
```python
class select:
    """
    Main ticket booking engine that handles the complete booking workflow
    
    Attributes:
        cdn_list (list): List of CDN endpoints for load balancing
        httpClint (HTTPClient): HTTP client for network requests
        login (GoLogin): Login manager instance
        cookies (str): Session cookies
        
    Methods:
        get_ticket_info(): Display configuration information
        station_table(): Convert station names to station codes
        call_login(): Handle user authentication
        main(): Main booking loop
    """
```

**Methods:**

```python
def get_ticket_info():
    """
    Display current configuration and system information
    
    Prints:
        - Current version
        - Python version check
        - Departure/arrival stations
        - Train numbers and dates
        - Seat preferences
        - Passenger information
    """

def station_table(from_station, to_station):
    """
    Convert human-readable station names to system codes
    
    Args:
        from_station (str): Departure station name
        to_station (str): Arrival station name
        
    Returns:
        tuple: (from_station_code, to_station_code)
        
    Example:
        from_code, to_code = select.station_table("北京", "上海")
        # Returns: ('BJP', 'SHH')
    """

def call_login(auth=False):
    """
    Initiate login process
    
    Args:
        auth (bool): Whether to perform authentication only
        
    Returns:
        bool: Login success status
    """

def main():
    """
    Main booking execution loop
    
    Process:
        1. Initialize ticket system
        2. Authenticate user
        3. Get passenger information
        4. Enter booking loop with timing control
        5. Query available tickets
        6. Submit orders when tickets found
    """
```

## Configuration

### `TickerConfig.py`

Core configuration parameters for the booking system:

```python
# Booking mode configuration
TICKET_TYPE = 1  # 1=booking only, 2=booking+standby

# Travel details
STATION_DATES = ["2020-01-18"]  # Departure dates
STATION_TRAINS = []  # Specific train numbers (empty = all trains)
FROM_STATION = "广州南"  # Departure station
TO_STATION = "隆回"  # Arrival station
SET_TYPE = ["二等座"]  # Seat types

# User credentials
USER = ""  # 12306 username
PWD = ""   # 12306 password

# CAPTCHA configuration
IS_AUTO_CODE = True  # Enable automatic CAPTCHA solving
AUTO_CODE_TYPE = 3   # 2=local, 3=cloud service

# Notification settings
EMAIL_CONF = {
    "IS_MAIL": True,
    "email": "",
    "notice_email_list": "",
    "username": "",
    "password": "",
    "host": "smtp.qq.com",
}

# Performance settings
IS_CDN = 1  # Enable CDN acceleration
ORDER_TYPE = 2  # Order submission method
ORDER_MODEL = 1  # 1=pre-sale, 2=ticket hunting
```

## Main APIs

### 1. Query System

#### `inter/Query.py`

**Class: `query`**
```python
class query:
    """
    Ticket availability query system
    
    Handles real-time ticket availability checking with support for
    multiple dates, trains, and seat types.
    """

def __init__(self, selectObj, from_station, to_station, from_station_h, 
             to_station_h, _station_seat, station_trains, ticke_peoples_num, 
             station_dates=None):
    """
    Initialize query system
    
    Args:
        selectObj: Main selection object
        from_station (str): Origin station code
        to_station (str): Destination station code
        from_station_h (str): Origin station name (human readable)
        to_station_h (str): Destination station name (human readable)
        _station_seat (list): Preferred seat types
        station_trains (list): Target train numbers
        ticke_peoples_num (int): Number of passengers
        station_dates (list): Travel dates
    """

def sendQuery(self):
    """
    Execute ticket availability query
    
    Returns:
        dict: Query result containing:
            - status (bool): Query success status
            - secretStr (str): Booking secret string
            - train_no (str): Train number
            - seat (int): Available seat type
            - leftTicket (str): Remaining ticket count
            - cdn (str): CDN endpoint used
            
    Example:
        result = query_instance.sendQuery()
        if result.get("status"):
            print(f"Found tickets on train {result['train_no']}")
    """
```

### 2. Login Management

#### `init/login.py`

**Class: `GoLogin`**
```python
class GoLogin:
    """
    Handles user authentication and session management
    
    Features:
        - Automatic CAPTCHA solving
        - Session persistence
        - Login retry logic
        - Cookie management
    """

def go_login(self):
    """
    Execute complete login process
    
    Process:
        1. Get login CAPTCHA
        2. Solve CAPTCHA (automatic/manual)
        3. Submit login credentials
        4. Validate session
        5. Store authentication cookies
        
    Returns:
        bool: Login success status
    """

def auth(self):
    """
    Perform session authentication
    
    Returns:
        bool: Authentication success status
    """
```

### 3. Order Submission

#### `inter/SubmitOrderRequest.py`

**Class: `submitOrderRequest`**
```python
class submitOrderRequest:
    """
    Handle ticket order submission
    
    Manages the complete order submission workflow including
    validation, passenger information, and payment preparation.
    """

def __init__(self, selectObj, secretStr, from_station, to_station, 
             train_no, set_type, passengerTicketStrList, oldPassengerStr, 
             train_date, ticke_peoples):
    """
    Initialize order submission
    
    Args:
        selectObj: Main selection object
        secretStr (str): Encrypted booking string
        from_station (str): Origin station code
        to_station (str): Destination station code
        train_no (str): Train number
        set_type (str): Seat type
        passengerTicketStrList (str): Passenger ticket information
        oldPassengerStr (str): Existing passenger data
        train_date (str): Travel date
        ticke_peoples (list): Passenger list
    """

def sendSubmitOrderRequest(self):
    """
    Submit ticket order to 12306 system
    
    Process:
        1. Prepare order data
        2. Submit to booking API
        3. Handle response validation
        4. Proceed to order confirmation
        
    Raises:
        ticketIsExitsException: If order submission fails
    """
```

## Network Layer

### `myUrllib/httpUtils.py`

**Class: `HTTPClient`**
```python
class HTTPClient:
    """
    HTTP client with advanced features for 12306 API communication
    
    Features:
        - Session management
        - Cookie handling
        - Proxy support
        - CDN load balancing
        - Request retry logic
        - Header management
    """

def __init__(self, is_proxy, cdnList=None):
    """
    Initialize HTTP client
    
    Args:
        is_proxy (int): Proxy usage flag (0=disabled, 1=enabled)
        cdnList (list): List of CDN endpoints
    """

def send(self, urls, data=None, **kwargs):
    """
    Send HTTP request with advanced error handling
    
    Args:
        urls (dict): URL configuration containing:
            - req_url (str): Request URL
            - Host (str): Target host
            - Referer (str): Referer header
            - is_json (bool): JSON response expected
            - re_try (int): Retry attempts
            - s_time (float): Sleep time between requests
        data (str): POST data payload
        
    Returns:
        dict|str: Response data (JSON dict or string)
        
    Example:
        url_config = {
            "req_url": "/otn/leftTicket/query",
            "Host": "kyfw.12306.cn",
            "Referer": "https://kyfw.12306.cn/otn/leftTicket/init",
            "is_json": True,
            "re_try": 3,
            "s_time": 0.1
        }
        response = client.send(url_config, data="query_params")
    """

def set_cookies(self, kwargs):
    """
    Set session cookies
    
    Args:
        kwargs (list): List of cookie dictionaries
    """

def setHeaders(self, headers):
    """
    Update request headers
    
    Args:
        headers (dict): Header key-value pairs
        
    Returns:
        HTTPClient: Self for method chaining
    """
```

## Verification System

### `verify/localVerifyCode.py`

**Class: `Verify`**
```python
class Verify:
    """
    Local CAPTCHA verification system using machine learning models
    
    Uses TensorFlow/Keras models for:
        - Text recognition in CAPTCHAs
        - Image classification
        - Multi-step verification process
    """

def __init__(self):
    """
    Initialize verification system and load ML models
    
    Loads:
        - Text recognition model (model.v2.0.h5)
        - Image classification model (12306.image.model.h5)
    """

def verify(self, fn):
    """
    Perform CAPTCHA verification
    
    Args:
        fn (str): Base64 encoded CAPTCHA image
        
    Returns:
        list: List of selected image positions (1-8)
        
    Example:
        verifier = Verify()
        result = verifier.verify(base64_image)
        # Returns: ['1', '3', '7'] for positions to click
    """

def loadTextModel(self):
    """Load text recognition model"""

def loadImgModel(self):
    """Load image classification model"""
```

### Remote Verification

For cloud-based CAPTCHA solving, the system supports external services:

```python
# Configuration for cloud CAPTCHA service
AUTO_CODE_TYPE = 3
HOST = "120.77.154.140:8000"
REQ_URL = "/verify/base64/"
HTTP_TYPE = "http"
```

## Order Management

### Advanced Order Submission

#### `inter/AutoSubmitOrderRequest.py`

**Class: `autoSubmitOrderRequest`**
```python
class autoSubmitOrderRequest:
    """
    Advanced automatic order submission with comprehensive validation
    
    Features:
        - Multi-step order validation
        - Queue management
        - Payment preparation
        - Error recovery
    """

def sendAutoSubmitOrderRequest(self):
    """
    Execute automatic order submission workflow
    
    Process:
        1. Submit initial order
        2. Get submission token
        3. Validate passenger information
        4. Check queue status
        5. Confirm order
        6. Prepare for payment
        
    Returns:
        bool: Submission success status
    """
```

### Queue Management

#### `inter/GetQueueCount.py`

**Class: `getQueueCount`**
```python
class getQueueCount:
    """
    Manage ticket queue and availability checking
    
    Handles real-time queue status and ticket allocation
    """

def sendGetQueueCount(self):
    """
    Check current queue status and ticket availability
    
    Returns:
        dict: Queue information containing:
            - ticket (str): Available ticket count
            - countT (str): Total queue count
            - op_2 (str): Queue operation status
    """
```

## Notification System

### Email Notifications

#### `config/emailConf.py`

```python
def sendEmail(msg):
    """
    Send email notification for booking results
    
    Args:
        msg (str): Email message content
        
    Configuration required in TickerConfig.py:
        EMAIL_CONF = {
            "IS_MAIL": True,
            "email": "sender@example.com",
            "notice_email_list": "recipient@example.com",
            "username": "email_username",
            "password": "email_password",
            "host": "smtp.qq.com"
        }
        
    Example:
        sendEmail("订票成功！订单号：EB52743573")
    """
```

### WeChat Notifications (Server酱)

#### `config/serverchanConf.py`

```python
def sendServerChan(msg):
    """
    Send WeChat notification via Server酱 service
    
    Args:
        msg (str): Notification message
        
    Configuration required:
        SERVER_CHAN_CONF = {
            "is_server_chan": True,
            "secret": "your_server_chan_secret"
        }
        
    Example:
        sendServerChan("抢票成功通知")
    """
```

## Exception Handling

The system includes comprehensive exception handling:

### Custom Exceptions

```python
# User authentication errors
class UserPasswordException(Exception):
    """Raised when login credentials are invalid"""

# Passenger information errors  
class PassengerUserException(Exception):
    """Raised when passenger information is invalid"""

# Ticket availability errors
class ticketIsExitsException(Exception):
    """Raised when requested tickets are not available"""

# Configuration errors
class ticketConfigException(Exception):
    """Raised when ticket configuration is invalid"""

# Quota errors
class ticketNumOutException(Exception):
    """Raised when ticket quantity exceeds available seats"""

# Balance errors
class balanceException(Exception):
    """Raised when account balance is insufficient"""
```

## Utilities

### CDN Management

#### `agency/cdn_utils.py`

```python
def filterCdn():
    """
    Filter and test CDN endpoints for optimal performance
    
    Process:
        1. Load CDN list from cdn_list file
        2. Test each CDN endpoint
        3. Measure response time
        4. Save working CDNs to filter_cdn_list
        
    Usage:
        python run.py c  # Run CDN filtering
    """

class CDNProxy(threading.Thread):
    """
    Multi-threaded CDN testing and filtering
    
    Tests CDN endpoints concurrently for better performance
    """
```

### Proxy Support

#### `agency/agency_tools.py`

```python
class proxy:
    """
    Proxy management for network requests
    
    Features:
        - Proxy rotation
        - Health checking
        - Load balancing
    """

def setProxy(self):
    """
    Configure proxy settings for HTTP client
    
    Returns:
        dict: Proxy configuration for requests library
    """
```

### Logging

#### `config/logger.py`

```python
def log(msg, func="info"):
    """
    Write log messages to file with timestamp
    
    Args:
        msg (str): Log message
        func (str): Log level (info, error, debug)
        
    Example:
        log("开始查询车票", "info")
        log("登录失败", "error")
    """
```

## Usage Examples

### Basic Ticket Booking

```python
# 1. Configure settings in TickerConfig.py
STATION_DATES = ["2024-02-01"]
FROM_STATION = "北京"
TO_STATION = "上海" 
SET_TYPE = ["二等座"]
USER = "your_username"
PWD = "your_password"

# 2. Run the application
python run.py r
```

### Testing Configuration

```python
# Test email and notification settings
python run.py t

# Filter CDN endpoints for better performance
python run.py c
```

### Advanced Configuration

```python
# Multiple dates and train types
STATION_DATES = ["2024-02-01", "2024-02-02"]
STATION_TRAINS = ["G1", "G3", "G7"]  # Specific trains
SET_TYPE = ["一等座", "二等座"]  # Multiple seat types

# Enable standby booking
TICKET_TYPE = 2  # Booking + standby

# Configure notifications
EMAIL_CONF = {
    "IS_MAIL": True,
    "email": "your_email@qq.com",
    "notice_email_list": "notify@qq.com",
    "username": "your_email",
    "password": "your_auth_code",
    "host": "smtp.qq.com"
}

SERVER_CHAN_CONF = {
    "is_server_chan": True,
    "secret": "your_server_chan_secret"
}
```

### Docker Deployment

```bash
# Build and run with Docker Compose
docker-compose up --build -d

# View logs
docker logs --follow ticket

# Stop the service
docker-compose down
```

### Programmatic Usage

```python
from init.select_ticket_info import select
from config import TickerConfig

# Initialize booking system
booking_system = select()

# Configure travel details
TickerConfig.FROM_STATION = "北京"
TickerConfig.TO_STATION = "上海"
TickerConfig.STATION_DATES = ["2024-02-01"]

# Start booking process
booking_system.main()
```

### Custom Query Implementation

```python
from inter.Query import query
from myUrllib.httpUtils import HTTPClient

# Initialize HTTP client
client = HTTPClient(is_proxy=0)

# Create query instance
q = query(
    selectObj=booking_system,
    from_station="BJP",  # Beijing
    to_station="SHH",   # Shanghai
    from_station_h="北京",
    to_station_h="上海",
    _station_seat=[30],  # Second class
    station_trains=[],   # All trains
    ticke_peoples_num=1,
    station_dates=["2024-02-01"]
)

# Execute query
result = q.sendQuery()
if result.get("status"):
    print(f"Found tickets: {result}")
```

## Error Handling Examples

```python
try:
    booking_system.main()
except UserPasswordException:
    print("Login failed - check username/password")
except ticketIsExitsException as e:
    print(f"Ticket booking failed: {e}")
except PassengerUserException:
    print("Passenger information error")
except Exception as e:
    print(f"Unexpected error: {e}")
```

## Performance Optimization

### CDN Configuration
```python
# Enable CDN for better performance
IS_CDN = 1

# Filter CDNs before booking
python run.py c
```

### Request Timing
```python
# Adjust request intervals to avoid rate limiting
MAX_TIME = 3  # Maximum interval
MIN_TIME = 1  # Minimum interval

# Pre-sale timing
OPEN_TIME = "12:59:57"  # Start time for pre-sale
```

### Proxy Usage
```python
# Enable proxy for IP rotation
IS_PROXY = 1

# Configure proxy list in agency/proxy_list
```

This documentation covers all major components, APIs, and usage patterns of the 12306 ticket booking assistant. Each component is designed to work together to provide a reliable and efficient ticket booking experience.
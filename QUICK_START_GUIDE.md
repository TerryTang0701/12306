# Quick Start Guide

## Prerequisites

### System Requirements
- Python 3.6 or higher
- Linux/Windows/macOS
- Internet connection
- 12306 account credentials

### Dependencies Installation

```bash
# Install required packages
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple -r requirements.txt

# For local CAPTCHA recognition (optional)
pip3 install tensorflow keras opencv-python
```

## Basic Setup

### 1. Download Models (For Local CAPTCHA)

If using local CAPTCHA recognition (`AUTO_CODE_TYPE = 2`):

```bash
# Download models to project root directory
# Option 1: From Baidu Cloud
# URL: https://pan.baidu.com/s/1rS155VjweWVWIJogakechA
# Password: bmlm

# Option 2: From Git repository
git clone https://github.com/testerSunshine/12306model.git
cp 12306model/*.h5 ./
```

### 2. Configure Settings

Edit `TickerConfig.py`:

```python
# Basic travel information
STATION_DATES = ["2024-02-01"]        # Travel dates
FROM_STATION = "北京"                  # Departure city
TO_STATION = "上海"                    # Destination city
SET_TYPE = ["二等座"]                  # Seat preferences

# Login credentials
USER = "your_12306_username"           # Your 12306 username
PWD = "your_12306_password"            # Your 12306 password

# Passenger information
TICKET_PEOPLES = ["张三", "李四"]       # Passenger names (as registered)

# CAPTCHA settings
IS_AUTO_CODE = True                    # Enable auto CAPTCHA solving
AUTO_CODE_TYPE = 3                     # 2=local, 3=cloud service

# Notification (optional)
EMAIL_CONF = {
    "IS_MAIL": True,
    "email": "your_email@qq.com",
    "notice_email_list": "notify@qq.com",
    "username": "your_email",
    "password": "your_email_password",
    "host": "smtp.qq.com"
}
```

### 3. Test Configuration

```bash
# Test email and notification settings
python3 run.py t
```

### 4. Optimize Performance

```bash
# Filter CDN endpoints for better performance
python3 run.py c
```

### 5. Start Booking

```bash
# Start the ticket booking process
python3 run.py r
```

## Docker Setup (Recommended)

### Prerequisites
- Docker 18.09+
- Docker Compose 1.23.2+

### Configuration for Docker

Edit `TickerConfig.py` for Docker deployment:

```python
# Important: Use local CAPTCHA service in Docker
AUTO_CODE_TYPE = 3
HOST = "captcha:80"  # Docker service name
```

### Run with Docker

```bash
# Start booking service
docker-compose up --build -d

# View logs
docker logs --follow ticket

# Stop service
docker-compose down
```

## Configuration Options

### Booking Modes

```python
# Mode 1: Regular booking only
TICKET_TYPE = 1

# Mode 2: Booking + standby tickets
TICKET_TYPE = 2
```

### Order Types

```python
# Type 1: Fast order submission (less stable)
ORDER_TYPE = 1

# Type 2: Standard order submission (recommended)
ORDER_TYPE = 2
```

### Timing Modes

```python
# Mode 1: Pre-sale mode (for exact release time)
ORDER_MODEL = 1
OPEN_TIME = "12:59:57"  # Release time

# Mode 2: Ticket hunting mode (continuous search)
ORDER_MODEL = 2
```

### Seat Types

```python
SET_TYPE = [
    "商务座",    # Business class
    "一等座",    # First class
    "二等座",    # Second class
    "特等座",    # Premium class
    "软卧",      # Soft sleeper
    "硬卧",      # Hard sleeper
    "硬座",      # Hard seat
    "无座",      # Standing
    "动卧"       # Dynamic sleeper
]
```

## Common Use Cases

### 1. Spring Festival Rush Booking

```python
# Configuration for high-demand periods
STATION_DATES = ["2024-02-01", "2024-02-02", "2024-02-03"]
STATION_TRAINS = []  # All available trains
SET_TYPE = ["二等座", "一等座", "无座"]  # Multiple seat options
ORDER_MODEL = 1  # Pre-sale mode
OPEN_TIME = "12:59:57"  # Exact release time
IS_MORE_TICKET = True  # Accept partial bookings
```

### 2. Business Travel Booking

```python
# Configuration for business travel
STATION_TRAINS = ["G1", "G3", "G7"]  # High-speed trains only
SET_TYPE = ["商务座", "一等座"]  # Premium seats
ORDER_MODEL = 2  # Continuous hunting
TICKET_TYPE = 2  # Include standby options
```

### 3. Budget Travel Booking

```python
# Configuration for budget travel
SET_TYPE = ["硬座", "无座", "硬卧"]  # Economical options
ORDER_MODEL = 2  # Ticket hunting mode
IS_MORE_TICKET = True  # Accept any available quantity
```

## Troubleshooting

### Common Issues

#### 1. Login Failures
```bash
# Check credentials in TickerConfig.py
USER = "correct_username"
PWD = "correct_password"

# Test login
python3 run.py t
```

#### 2. CAPTCHA Problems
```bash
# Switch to cloud CAPTCHA service
AUTO_CODE_TYPE = 3

# Or disable auto CAPTCHA
IS_AUTO_CODE = False
```

#### 3. Network Issues
```bash
# Enable CDN acceleration
IS_CDN = 1

# Filter CDNs first
python3 run.py c

# Enable proxy if needed
IS_PROXY = 1
```

#### 4. No Tickets Found
```python
# Expand search criteria
STATION_TRAINS = []  # All trains
SET_TYPE = ["二等座", "一等座", "无座"]  # Multiple seat types
STATION_DATES = ["2024-02-01", "2024-02-02"]  # Multiple dates
```

### Error Messages

#### "12306接口无响应"
- Network connectivity issue
- Try filtering CDNs: `python3 run.py c`
- Check firewall settings

#### "余票数小于乘车人数"
- Set `IS_MORE_TICKET = True` to accept partial bookings
- Reduce passenger count

#### "验证码识别失败"
- Switch CAPTCHA method: `AUTO_CODE_TYPE = 3`
- Check model files if using local recognition

## Performance Optimization

### 1. CDN Optimization
```bash
# Regularly filter CDNs for best performance
python3 run.py c
```

### 2. Request Timing
```python
# Adjust request intervals
MAX_TIME = 2  # Faster requests (higher risk)
MIN_TIME = 0.5

# Or slower, safer requests
MAX_TIME = 5
MIN_TIME = 2
```

### 3. Proxy Configuration
```python
# Enable proxy rotation
IS_PROXY = 1

# Add proxies to agency/proxy_list
```

## Monitoring and Logging

### View Logs
```bash
# Real-time log monitoring
tail -f logs/ticker.log

# Docker logs
docker logs --follow ticket
```

### Log Levels
- INFO: Normal operations
- ERROR: Critical failures
- DEBUG: Detailed debugging info

## Security Considerations

### Account Safety
- Use dedicated 12306 account
- Enable two-factor authentication
- Monitor account activity

### Network Security
- Use trusted proxy servers only
- Avoid public Wi-Fi for booking
- Keep software updated

## Advanced Configuration

### Multiple Passenger Management
```python
TICKET_PEOPLES = ["张三", "李四", "王五"]
IS_MORE_TICKET = True  # Allow partial bookings
```

### Notification Setup
```python
# Email notifications
EMAIL_CONF = {
    "IS_MAIL": True,
    "email": "sender@qq.com",
    "notice_email_list": "recipient1@qq.com,recipient2@qq.com",
    "username": "sender",
    "password": "email_auth_code",
    "host": "smtp.qq.com"
}

# WeChat notifications via Server酱
SERVER_CHAN_CONF = {
    "is_server_chan": True,
    "secret": "your_server_chan_secret"
}
```

### Custom Station Codes
```python
# If automatic station detection fails
# Check station_name.txt for correct codes
FROM_STATION = "北京南"  # Use exact station name
TO_STATION = "上海虹桥"
```

## Support and Community

### Getting Help
- Check existing issues on GitHub
- Join QQ groups for community support
- Read update logs for latest changes

### Contributing
- Report bugs with detailed logs
- Suggest improvements
- Share successful configurations

This quick start guide should get you up and running with the 12306 ticket booking assistant. For detailed API documentation, refer to the API_DOCUMENTATION.md file.
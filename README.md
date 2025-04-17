# Secure Squid Proxy Docker Setup

A lightweight, dockerized Squid proxy server with password authentication for secure network traffic routing in restricted environments. Deploy in seconds with Docker Compose and bypass network limitations while maintaining security.

## Features

- ✅ Password protection for secure access
- ✅ Easy deployment with Docker Compose
- ✅ Customizable configuration
- ✅ Persistent cache for better performance

## Quick Start

### Prerequisites

- Docker and Docker Compose installed on your server
- Basic knowledge of terminal commands

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/erfnemati/docker-squid-proxy.git
   cd docker-squid-proxy
   ```

2. Create password file:
   ```bash
   sudo apt install apache2-utils # Debian/Ubuntu
   sudo yum install httpd-tools   # CentOS/RHE
   mkdir -p auth
   htpasswd -c auth/passwd proxyuser
   # Enter your desired password when prompted
   ```
   * Remove my user

3. Start the proxy:
   ```bash
   docker-compose up -d
   ```

4. Your proxy is now running at `your-server-ip:8443`

## Usage

### Terminal/Command Line

```bash
# Using curl with the proxy
curl --proxy http://proxyuser:yourpassword@your-server-ip:8443 https://ifconfig.me

# Setting environment variables
export http_proxy=http://proxyuser:yourpassword@your-server-ip:8443
export https_proxy=http://proxyuser:yourpassword@your-server-ip:8443
curl https://ifconfig.me
```

### Browser Configuration

1. Go to your browser's network settings
2. Set the proxy server to your server IP and port 8443
3. If prompted, enter your username and password

### Application Configuration

Most applications support proxy configuration through:
- Direct proxy settings in the application
- Environment variables: `http_proxy` and `https_proxy`

## Configuration Files

### docker-compose.yml

```yaml
version: '3'

services:
  squid-proxy:
    image: ubuntu/squid:latest
    container_name: squid-proxy
    ports:
      - "8443:8443"
    volumes:
      - ./squid.conf:/etc/squid/squid.conf
      - ./auth:/etc/squid/auth
      - squid_cache:/var/spool/squid
    restart: always
    networks:
      - proxy-network

networks:
  proxy-network:
    driver: bridge

volumes:
  squid_cache:
    driver: local
```

### squid.conf

The configuration file contains security settings and caching parameters. See the file for details.

## Management

```bash
# Check logs
docker logs squid-proxy

# Restart the proxy
docker-compose restart squid-proxy

# Stop the proxy
docker-compose down

# Add another user
htpasswd auth/passwd newuser
docker-compose restart squid-proxy
```

## Customization

- **Change port**: Edit the first number in the `ports` section of docker-compose.yml
- **Add users**: Use `htpasswd auth/passwd username` to add more users
- **Advanced config**: Edit squid.conf for more customization options

## Security Considerations

- Always use strong passwords
- Regularly update your Docker images
- Consider using HTTPS if deploying in production
- Restrict access in squid.conf based on your needs

## License

MIT License

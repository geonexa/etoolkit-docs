# SSL Configuration

Guide for configuring SSL/HTTPS for EO-Toolkit.

## Overview

SSL/TLS encryption is essential for production deployments to secure data transmission.

## Let's Encrypt (Recommended)

### Installation

```bash
sudo apt update
sudo apt install certbot python3-certbot-apache
```

### Obtain Certificate

```bash
sudo certbot --apache -d etoolkit.terrawatch.net -d www.etoolkit.terrawatch.net
```

Follow prompts:
- Enter email address
- Agree to terms
- Choose redirect HTTP to HTTPS

### Auto-Renewal

Certbot sets up auto-renewal automatically. Test renewal:

```bash
sudo certbot renew --dry-run
```

### Manual Renewal

```bash
sudo certbot renew
```

## Apache SSL Configuration

### SSL Module

```bash
sudo a2enmod ssl
sudo systemctl restart apache2
```

### Virtual Host Configuration

Update Apache configuration:

```apache
<VirtualHost *:443>
    ServerName etoolkit.terrawatch.net
    
    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/etoolkit.terrawatch.net/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/etoolkit.terrawatch.net/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf
    SSLCertificateChainFile /etc/letsencrypt/options-ssl-apache.conf
    
    # ... rest of configuration
</VirtualHost>
```

### HTTP to HTTPS Redirect

```apache
<VirtualHost *:80>
    ServerName etoolkit.terrawatch.net
    Redirect permanent / https://etoolkit.terrawatch.net/
</VirtualHost>
```

## Manual SSL Certificate

### Obtain Certificate

1. Purchase from CA or use self-signed
2. Obtain certificate files
3. Place in secure location

### Configure Apache

```apache
SSLEngine on
SSLCertificateFile /path/to/certificate.crt
SSLCertificateKeyFile /path/to/private.key
SSLCertificateChainFile /path/to/chain.crt
```

## Security Headers

### HSTS

```apache
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
```

### Other Headers

```apache
Header always set X-Content-Type-Options "nosniff"
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-XSS-Protection "1; mode=block"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
```

## Certificate Renewal

### Let's Encrypt

Certificates expire every 90 days. Auto-renewal handles this.

### Manual Renewal

1. Obtain new certificate
2. Update Apache configuration
3. Reload Apache
4. Verify certificate

## Troubleshooting

### Certificate Not Working

- Verify certificate paths
- Check certificate validity
- Verify Apache SSL module enabled
- Check certificate permissions

### Mixed Content Warnings

- Ensure all resources use HTTPS
- Update hardcoded HTTP URLs
- Check Django settings for HTTPS

### Certificate Expired

- Renew certificate
- Update configuration
- Reload Apache

## Testing SSL

### SSL Labs

Test SSL configuration:
https://www.ssllabs.com/ssltest/

### Command Line

```bash
openssl s_client -connect etoolkit.terrawatch.net:443
```

## Best Practices

1. **Use Let's Encrypt**: Free and automated
2. **Auto-Renewal**: Ensure auto-renewal works
3. **Strong Ciphers**: Use modern cipher suites
4. **HSTS**: Enable HTTP Strict Transport Security
5. **Monitor Expiration**: Set up expiration alerts
6. **Backup Certificates**: Keep certificate backups

---

Return to: [Deployment Overview](overview.md)

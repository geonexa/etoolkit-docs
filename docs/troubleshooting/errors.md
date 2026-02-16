# Error Messages Reference

Common error messages in EO-Toolkit and their meanings.

## Authentication Errors

### "Invalid username or password"

**Cause**: Incorrect credentials or account not verified

**Solution**: 
- Verify username and password
- Check account is verified
- Try password reset if needed

### "Account is inactive"

**Cause**: Email not verified

**Solution**: Complete email verification process

### "CSRF verification failed"

**Cause**: Missing or invalid CSRF token

**Solution**: 
- Refresh the page
- Clear browser cookies
- Ensure CSRF token included in requests

## Database Errors

### "relation does not exist"

**Cause**: Database migrations not applied

**Solution**: 
```bash
python manage.py migrate
```

### "connection refused"

**Cause**: PostgreSQL not running or wrong connection details

**Solution**: 
- Check PostgreSQL status
- Verify connection settings
- Check firewall rules

## File Upload Errors

### "Unsupported geometry types"

**Cause**: GeoJSON contains unsupported geometry

**Solution**: Use only Polygon or MultiPolygon geometries

### "File too large"

**Cause**: Upload exceeds size limit

**Solution**: Reduce file size or increase limit

### "Invalid GeoJSON"

**Cause**: Malformed GeoJSON file

**Solution**: Validate GeoJSON format before upload

## Analysis Errors

### "Area outside data coverage"

**Cause**: Selected area not covered by dataset

**Solution**: Select area within dataset coverage

### "Analysis timeout"

**Cause**: Analysis taking too long

**Solution**: 
- Reduce area size
- Limit temporal range
- Try again later

### "GEE quota exceeded"

**Cause**: Google Earth Engine quota limit reached

**Solution**: 
- Wait for quota reset
- Reduce request frequency
- Contact support

## GeoServer Errors

### "Layer not found"

**Cause**: Layer not published in GeoServer

**Solution**: 
- Check layer exists
- Verify layer is published
- Check workspace name

### "WMS request failed"

**Cause**: GeoServer connection issue

**Solution**: 
- Verify GeoServer running
- Check WMS service enabled
- Test GeoServer connection

## Task Errors

### "Task failed"

**Cause**: Error during task execution

**Solution**: 
- Check task logs
- Review error details
- Try again
- Contact support if persistent

### "Task timeout"

**Cause**: Task exceeded time limit

**Solution**: 
- Task may be too complex
- Check server resources
- Contact support

## API Errors

### "400 Bad Request"

**Cause**: Invalid request parameters

**Solution**: 
- Verify required parameters
- Check parameter formats
- Review API documentation

### "401 Unauthorized"

**Cause**: Not authenticated

**Solution**: Log in and retry

### "404 Not Found"

**Cause**: Resource doesn't exist

**Solution**: Verify resource ID or URL

### "500 Internal Server Error"

**Cause**: Server error

**Solution**: 
- Check server logs
- Contact support
- Try again later

## General Errors

### "Permission denied"

**Cause**: Insufficient permissions

**Solution**: 
- Check file permissions
- Verify user permissions
- Contact administrator

### "Service unavailable"

**Cause**: Service not running

**Solution**: 
- Check service status
- Restart service if needed
- Contact administrator

---


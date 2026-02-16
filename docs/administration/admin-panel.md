# Admin Panel Guide

The Django Admin Panel provides administrative control over EO-Toolkit.

## Accessing Admin Panel

1. Navigate to `/admin/`
2. Log in with superuser credentials
3. Access admin dashboard

## Main Sections

### Users

Manage user accounts:

- **Custom Users**: View and manage users
- **Groups**: User groups and permissions
- **Permissions**: User permissions

**Actions**:
- Create/edit users
- Activate/deactivate accounts
- Reset passwords
- Manage permissions

### Areas

Manage user areas:

- **Areas**: View all user-created areas
- **Filter**: By user, country, name
- **Actions**: View, edit, delete areas

### Geo Data Uploads

Manage GeoServer data:

- **Geo Data Uploads**: Upload and manage spatial data
- **Actions**: Add, edit, delete layers



### Custom Apps

Manage custom applications:

- **Custom Apps**: Add external applications
- **Actions**: Add, edit, delete apps



### Task History

View task history:

- **Task History**: View all analysis tasks
- **Filter**: By user, area, date
- **Actions**: View details, delete tasks

### Celery Results

Monitor Celery tasks:

- **Task Results**: View task execution results
- **Status**: Pending, Success, Failure
- **Details**: Task information and results

## Common Tasks

### Adding GeoServer Data

1. Go to **Geo Data Uploads**
2. Click **Add Geo Data Upload**
3. Fill in required fields
4. Upload data file
5. Upload thumbnail
6. Add keywords
7. Upload SLD (optional)
8. Click **Save**


### Managing Users

1. Go to **Custom Users**
2. Filter/search for user
3. Click user to edit
4. Modify fields as needed
5. Save changes

### Viewing Task Status

1. Go to **Task History** or **Task Results**
2. Filter by user, area, or status
3. Click task to view details
4. Check status and results


## Permissions

### Superuser

Full access to all features.

### Staff Users

Can access admin panel but may have limited permissions.

### Regular Users

No admin panel access.

## Troubleshooting

### Cannot Access Admin

- Verify superuser status
- Check credentials
- Verify admin URL
- Check Django settings

### Upload Fails

- Check file format
- Verify permissions
- Check GeoServer connection
- Review error messages

### Tasks Not Processing

- Check Celery status
- Verify Redis connection
- Review task logs
- Check worker status

---


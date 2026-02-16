# Authentication Guide

This guide covers user authentication, account management, and security features in EO-Toolkit.

## Registration

### Creating an Account

1. Navigate to the registration page (`/accounts/register/`)
2. Fill in the registration form:
   - **Username**: Choose a unique username (required)
   - **Email**: Your email address (required, used for verification)
   - **Password**: Create a strong password (required)
   - **Password Confirmation**: Re-enter your password
3. Click **Register**

!!! tip "Password Requirements"
    Use a strong password with:
    - At least 8 characters
    - Mix of uppercase and lowercase letters
    - Numbers and special characters

### Email Verification

After registration:

1. You'll be redirected to the email verification page
2. Your account is created but **inactive** until verified
3. Click **Send OTP** to receive a verification code
4. Check your email inbox (and spam folder) for the OTP
5. Enter the 6-digit code in the verification form
6. Click **Verify**

!!! warning "Account Status"
    You cannot log in until your email is verified. If you don't receive the OTP:
    - Check your spam folder
    - Click "Send OTP" again
    - Verify your email address is correct

## Login

### Signing In

1. Navigate to the login page (`/accounts/login/`)
2. Enter your **Username** and **Password**
3. Click **Sign In**

### Remember Me

- The login form includes a "Remember me" option
- This keeps you logged in across browser sessions
- Use only on trusted devices

### Forgot Password

If you forget your password:

1. Click **Forgot Password** on the login page
2. Enter your email address
3. Check your email for password reset instructions
4. Follow the link to reset your password
5. Create a new password

## Password Management

### Changing Your Password

1. Log in to your account
2. Navigate to **Account Settings** → **Change Password**
3. Enter your current password
4. Enter your new password
5. Confirm the new password
6. Click **Change Password**

!!! tip "Security Best Practices"
    - Change your password regularly
    - Use unique passwords for different services
    - Never share your password
    - Log out when using shared computers

## Account Security

### Email Verification

- Email verification is required for account activation
- OTP codes expire after a set time period
- You can request a new OTP if needed

### Session Management

- Sessions expire after a period of inactivity
- You'll be logged out automatically for security
- Log in again to continue using the application

### Logout

To log out:

1. Click on your username in the top menu
2. Select **Logout**
3. Or navigate to `/accounts/logout/`

!!! warning "Public Computers"
    Always log out when using public or shared computers to protect your account.

## Account Settings

### Profile Information

- Username: Cannot be changed after registration
- Email: Contact support to change your email address
- Password: Can be changed anytime through settings

### Privacy

- Your account information is kept private
- Areas and analyses are private to your account
- Reports are only accessible by you

## Troubleshooting

### Cannot Log In

**Problem**: "Invalid username or password"

**Solutions**:
- Verify your username is correct
- Check if Caps Lock is on
- Try resetting your password
- Ensure your account is verified

### Email Not Received

**Problem**: OTP email not arriving

**Solutions**:
- Check spam/junk folder
- Verify email address is correct
- Wait a few minutes and try again
- Contact support if issue persists

### Account Locked

**Problem**: Account appears locked or inactive

**Solutions**:
- Complete email verification
- Check if account was deactivated
- Contact support for assistance

### Password Reset Not Working

**Problem**: Password reset link not working

**Solutions**:
- Check if link has expired (usually valid for 24 hours)
- Request a new password reset
- Clear browser cache and cookies
- Try a different browser

## Support

If you continue to experience authentication issues:

1. Check the [Troubleshooting Guide](../troubleshooting/common-issues.md)
2. Contact support through the Contact Us page
3. Provide details about the issue you're experiencing

---

Next: [Data Catalog Guide](data-catalog.md)

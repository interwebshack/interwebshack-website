# Cloudflare API Token Setup for R2 + DNS + Rules Automation

## 1. Create a Custom API Token

When asked for the **Token Name**, enter something clear and descriptive so you remember its purpose later.  
Recommended name:

**`GitHub Deployment — R2 + DNS + Rules`**

This makes it obvious the token is used for automation triggered by GitHub Actions.

## 2. Set Permissions

Add the following permissions to the token:

| Permission Scope      | Permission Category                          | Permission Level | Why Needed                                             |
|-----------------------|----------------------------------------------|------------------|--------------------------------------------------------|
| **Account**           | **Workers R2 Storage**                       | Edit             | Create/manage R2 buckets and uploads                   |
| **Account**           | **Transform Rules**                          | Edit             | Manage zone rules needed for routing and automation    |
| **Zone**              | **DNS**                                      | Edit             | Create and update DNS records automatically            |
| **Zone**              | **Origin Rules**                             | Edit             | Create/modify Origin Rules for R2 routing              |
| **Zone**              | **Transform Rules**                          | Edit             | Create/modify Transform Rules for R2 ting              |
| **Zone**              | **Zone**                                     | Read             | Required for looking up zone details during deployment |

## 3. Assign Account Resources

Under **Account Resources**, choose:

**Account:** `Include → All accounts`

> ✅ This gives the token access to your Cloudflare account so it can manage R2, transform rules, and other account-level resources.

> 💡 If you have multiple Cloudflare accounts and only want this token to work for one, choose **Include → Specific account** instead and select the correct account.

## 4. Assign Zone Resources

Under **Zone Resources**, choose:

**Zone:** `Include → All zones`

This gives the token permission to access DNS and zone settings for your domains.

> ✅ Required for DNS record creation during deployments  
> 🔐 If you only want the token to work for a specific domain, choose **Specific zone** and select it.

## 5. Client IP Address Filtering (Optional)

Cloudflare allows restricting where this token can be used from by whitelisting IP addresses.

For most GitHub-based deployments, leave this **not configured** because GitHub Actions runners use dynamic IPs.

> ✅ Recommended: **Do not restrict IPs** when using GitHub Actions  
> 🔐 If you self-host runners with static IPs, you may add them here for extra security

## 6. Token TTL (Optional)

Token TTL lets you automatically expire the token after a set time.

For most CI/CD workflows (like GitHub Actions), this is optional.

- **Recommended:** Leave TTL unset (no expiration)
- **Only set a TTL** if you want the token to auto-expire for security reasons

> ⚠️ If you set a TTL and forget to rotate the token, your deployments will fail once it expires.
> ✅ Best practice: If you set a TTL, store the expiration reminder somewhere (GitHub Secrets note, calendar, etc.)

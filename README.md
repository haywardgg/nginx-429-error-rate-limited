# Rate Limit Page for Nginx 🚦

A friendly, dark-themed 429 page for Nginx that automatically retries your user's request with exponential backoff. It replaces the default error page with a calmer experience that shows progress, tracks attempts, and encourages patience. 🧘

## ✨ Features
- 🔄 **Automatic Retry** with exponential backoff (5s → 10s → 20s)
- ⏳ **Animated progress bar** to show the countdown until retry
- 🧮 **Attempt tracking** (3 tries) with a 5-minute reset window
- 🖱️ **Manual retry** option after the automatic attempts finish
- 📱 **Responsive UI** that looks great on any screen
- 🧠 **Clean, modern design** with a dark theme
- 🔒 **URL validation** to prevent open redirects

## 🚀 Installation
1. Place `rate-limit.html` in your web root, e.g. `/var/www/html/rate-limit.html`.
2. Add the rate limiting config to your Nginx server block:

```nginx
# Rate limiting zone (add this in http or server context)
limit_req_zone $binary_remote_addr zone=php_limit:10m rate=10r/s;

server {
    # ... other configuration

    # Rate limiting rules
    limit_req zone=php_limit burst=10 nodelay;
    limit_req_status 429;

    # Custom error page for rate limiting
    error_page 429 /rate-limit.html;

    # Ensure the rate limit page itself isn't rate limited
    location = /rate-limit.html {
        internal;
    }
}
```

## 🛠️ Configuration
Customize the behavior by editing the JavaScript constants near the top of `rate-limit.html`:

```javascript
const CONFIG = {
    BASE_DELAY: 5,              // Base delay in seconds
    BACKOFF_FACTOR: 2,          // Exponential backoff factor (5s, 10s, 20s)
    MAX_ATTEMPTS: 3,            // Maximum automatic retry attempts
    RESET_AFTER_MINUTES: 5,     // Reset counter after X minutes
};
```

## 🔍 How it works
1. Nginx serves this page whenever it returns a `429`.
2. The script stores retry attempts in `localStorage`.
3. Each retry uses exponential backoff (5s, 10s, 20s).
4. After 3 failed attempts, users must wait 5 minutes or use the manual retry button.
5. The script redirects back to the original URL as soon as the delay finishes.

## 🎨 Customization
- **Colors & theme:** Tweak the CSS variables in the `<style>` block to match your brand.
- **Content:** Update the title, body text, and footer (including contact email) directly in the HTML.
- **Icons:** Swap or edit the inline SVGs if you prefer different visuals.
- **Analytics:** Drop your tracking snippet into the `<script>` section to monitor rate-limit hits.

## 🧭 Browser support
Optimized for modern browsers that support:
- `localStorage`
- ES6 JavaScript
- CSS Flexbox
- CSS Custom Properties

## 🔐 Security notes
- The script validates retry URLs to ensure they are same-origin.
- Uses `localStorage` with expiration to avoid indefinite state storage.
- No external dependencies or third-party tracking by default.

## 🛟 Troubleshooting
- **Page doesn't retry:** Make sure `localStorage` is enabled and check the browser console for errors.
- **State not persisting:** Some browsers limit or block `localStorage` in private/incognito mode.
- **Nginx not serving the page:** Verify file permissions on `/rate-limit.html`, check Nginx error logs, and confirm the `internal;` directive is present.

## 📄 License
Free to use and modify. Attribution appreciated but not required.

## 📬 Contact
Questions or issues? Reach out at **hayward@protonmail.com**.

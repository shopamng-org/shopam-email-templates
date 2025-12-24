# ShopAm Email Templates

This repository contains all HTML email templates for the ShopAm application. These templates are production-ready and optimized for email clients.

## 📁 Folder Structure

```
templates/
├── buyer/          # Email templates for buyers
├── seller/         # Email templates for sellers
├── shared/         # Email templates for both buyers and sellers
└── index.html      # Preview page for all templates
```

## 📧 Email Templates List

### Buyer Templates (9 templates)
1. **purchase-successful.html** - Payment confirmation with order details
2. **order-verified.html** - Order verification notification
3. **order-delivered.html** - Delivery confirmation
4. **payment-to-wallet.html** - Payment credited to wallet notification
5. **product-saved.html** - Saved product notification
6. **price-reduced.html** - Price drop alert for saved items
7. **phone-added.html** - Phone number verification
8. **otp-verification.html** - OTP code for verification
9. **refund-processed.html** - Refund confirmation

### Seller Templates (10 templates)
1. **item-sold.html** - Item sold notification with shipping instructions
2. **order-completed.html** - Order completion confirmation
3. **withdrawal-processed.html** - Bank withdrawal confirmation
4. **verified-seller.html** - Seller verification welcome email
5. **order-issues.html** - Order complaint notification
6. **refund-approved.html** - Refund approval notification
7. **phone-added.html** - Phone number verification
8. **otp-verification.html** - OTP code for verification

### Shared Templates (2 templates)
1. **order-ready.html** - Order arrived at hub notification
2. **password-changed.html** - Password change confirmation

## 🎨 Design Specifications

### Colors
- **Primary Green (Header):** `#167A3C`
- **Primary Orange (Buttons):** `#E67E22`
- **Background:** `#f5f5f5`
- **Text Primary:** `#000000`
- **Text Secondary:** `#333333`
- **Text Muted:** `#666666`

### Layout
- **Container Width:** 375px (mobile-optimized)
- **Border Radius:** 12px
- **Header Padding:** 10px 24px
- **Content Padding:** 24px
- **Button Padding:** 12px 40px

## 🔧 Dynamic Variables

All templates use placeholder variables wrapped in double curly braces `{{variable_name}}` that need to be replaced with actual data from your backend.

### Common Variables

#### User Information
```
{{buyer_name}}          - Buyer's first name or full name
{{seller_name}}         - Seller's first name or full name
{{user_email}}          - User's email address
{{user_phone}}          - User's phone number
```

#### Order Information
```
{{order_id}}            - Unique order identifier
{{transaction_id}}      - Payment transaction ID
{{product_name}}        - Name of the product
{{product_image}}       - URL to product image
{{tracking_id}}         - Shipment tracking number
```

#### Financial Information
```
{{amount_paid}}         - Total amount paid (e.g., "30,000")
{{item_price}}          - Item price (e.g., "28,000")
{{delivery_fee}}        - Delivery fee (e.g., "2000")
{{protection_fee}}      - Buyer protection fee (e.g., "Free")
{{commission}}          - ShopAm commission (e.g., "1200")
{{transfer_amount}}     - Amount transferred to seller (e.g., "15,800")
{{old_price}}           - Original price before discount
{{new_price}}           - New discounted price
```

#### Date & Time
```
{{payment_date}}        - Payment date (e.g., "20 - 12 - 2025")
{{delivery_date}}       - Expected delivery date
{{withdraw_date}}       - Withdrawal date (e.g., "11 - 11 - 2025")
{{due_date}}            - Payment due date (e.g., "13 - 11 - 2025")
{{current_date}}        - Current date
```

#### Bank Information
```
{{account_name}}        - Bank account holder name
{{bank_name}}           - Bank name (e.g., "GTB")
{{account_number}}      - Bank account number
{{amount}}              - Withdrawal amount (e.g., "500,000")
```

#### Links
```
{{order_details_link}}      - Link to view order details
{{order_tracking_link}}     - Link to track order
{{contact_support_link}}    - Link to contact support
{{view_product_link}}       - Link to view product
{{reset_password_link}}     - Password reset link
{{contact_us_link}}         - Contact page link
{{view_details_link}}       - Generic view details link
```

#### Social Media Links
```
{{twitter_link}}        - Twitter/X profile link
{{facebook_link}}       - Facebook page link
{{instagram_link}}      - Instagram profile link
{{linkedin_link}}       - LinkedIn page link
{{tiktok_link}}         - TikTok profile link
```

#### Footer Links
```
{{privacy_policy_link}} - Privacy policy page
{{terms_link}}          - Terms of service page
```

#### OTP & Verification
```
{{otp_code}}            - One-time password code (e.g., "123456")
```

## 🚀 Integration Guide

### Step 1: Choose Your Email Service
These templates work with any email service:
- Nodemailer (Node.js)
- SendGrid
- AWS SES
- Mailgun
- Postmark
- Any SMTP service

### Step 2: Template Processing

#### Option A: String Replacement (Simple)
```javascript
const fs = require('fs');

function sendEmail(templateName, data) {
  // Read template
  let template = fs.readFileSync(`./templates/buyer/${templateName}.html`, 'utf8');
  
  // Replace all variables
  Object.keys(data).forEach(key => {
    const regex = new RegExp(`{{${key}}}`, 'g');
    template = template.replace(regex, data[key]);
  });
  
  // Send email
  // ... your email sending logic
}

// Usage
sendEmail('purchase-successful', {
  buyer_name: 'John Doe',
  seller_name: 'Jane Smith',
  product_name: 'iPhone 13 Pro Max',
  amount_paid: '30,000',
  transaction_id: '90b3Jdf4f878783'
});
```

#### Option B: Template Engine (Recommended)
```javascript
const fs = require('fs');
const Handlebars = require('handlebars');

function sendEmail(templateName, data) {
  // Read template
  const source = fs.readFileSync(`./templates/buyer/${templateName}.html`, 'utf8');
  
  // Compile template (Handlebars supports {{variable}} syntax)
  const template = Handlebars.compile(source);
  
  // Generate HTML
  const html = template(data);
  
  // Send email
  // ... your email sending logic
}
```

### Step 3: Example Implementation with Nodemailer

```javascript
const nodemailer = require('nodemailer');
const fs = require('fs');

// Configure transporter
const transporter = nodemailer.createTransport({
  host: 'smtp.gmail.com',
  port: 587,
  secure: false,
  auth: {
    user: 'your-email@gmail.com',
    pass: 'your-app-password'
  }
});

// Function to send email
async function sendShopAmEmail(to, subject, templatePath, data) {
  // Read template
  let html = fs.readFileSync(templatePath, 'utf8');
  
  // Replace variables
  Object.keys(data).forEach(key => {
    const regex = new RegExp(`{{${key}}}`, 'g');
    html = html.replace(regex, data[key] || '');
  });
  
  // Send email
  const info = await transporter.sendMail({
    from: '"ShopAm" <noreply@shopam.com>',
    to: to,
    subject: subject,
    html: html
  });
  
  console.log('Email sent:', info.messageId);
}

// Example usage
sendShopAmEmail(
  'buyer@example.com',
  'Your Purchase is Successful',
  './templates/buyer/purchase-successful.html',
  {
    buyer_name: 'John Doe',
    seller_name: 'Akin Ajiboye',
    product_name: 'Red Zara Gown',
    amount_paid: '30,000',
    item_price: '28,000',
    delivery_fee: '2000',
    protection_fee: 'Free',
    payment_date: '20 - 12 - 2025',
    transaction_id: '90b3Jdf4f878783',
    order_details_link: 'https://shopam.com/orders/123456',
    twitter_link: 'https://twitter.com/shopam',
    facebook_link: 'https://facebook.com/shopam',
    instagram_link: 'https://instagram.com/shopam',
    linkedin_link: 'https://linkedin.com/company/shopam',
    tiktok_link: 'https://tiktok.com/@shopam',
    privacy_policy_link: 'https://shopam.com/privacy',
    terms_link: 'https://shopam.com/terms'
  }
);
```

## ✅ Testing Checklist

Before deploying, test each template:

- [ ] All variables are replaced correctly
- [ ] No `{{variable}}` placeholders remain in sent emails
- [ ] Images display correctly (if using product images)
- [ ] Links work and point to correct URLs
- [ ] Email renders correctly in:
  - [ ] Gmail (Desktop & Mobile)
  - [ ] Outlook (Desktop & Web)
  - [ ] Apple Mail (iOS & macOS)
  - [ ] Yahoo Mail
  - [ ] Thunderbird
- [ ] Social media icons link to correct profiles
- [ ] CTA buttons are clickable and styled correctly
- [ ] Text is readable on all backgrounds
- [ ] Layout is responsive on mobile devices

## 🧪 Testing Tools

### Online Email Testing Services
- **Litmus** - https://litmus.com
- **Email on Acid** - https://www.emailonacid.com
- **Mail Tester** - https://www.mail-tester.com

### Free Testing
- Send test emails to your own accounts on different providers
- Use browser DevTools to test responsive behavior
- Check spam score using Mail Tester

## 📱 Preview Templates

Open `index.html` in your browser to preview all templates with navigation buttons.

```bash
# If you have a local server
npx http-server .

# Or simply open index.html in your browser
open index.html  # macOS
start index.html # Windows
xdg-open index.html # Linux
```

## 🔒 Security Notes

1. **Never include sensitive data in templates** - Always pass them as variables
2. **Sanitize user input** - Prevent XSS attacks by escaping HTML in user-generated content
3. **Use environment variables** - Store API keys and credentials securely
4. **Validate email addresses** - Before sending emails
5. **Rate limiting** - Implement rate limiting to prevent abuse

## 🐛 Troubleshooting

### Problem: Variables not being replaced
**Solution:** Ensure you're using the correct syntax `{{variable_name}}` and that the variable exists in your data object.

### Problem: Email looks broken in Outlook
**Solution:** Our templates use table-based layouts which work in Outlook. If issues persist, check for:
- Missing `cellpadding="0" cellspacing="0"` attributes
- Invalid CSS properties
- Missing inline styles

### Problem: Images not displaying
**Solution:** 
- Use absolute URLs for all images
- Ensure images are hosted on a reliable CDN
- Check image file sizes (keep under 1MB)
- Provide alt text for all images

### Problem: Links not working
**Solution:**
- Use complete URLs with `https://`
- Test all links before deployment
- Ensure variables contain valid URLs

## 📞 Support

For questions or issues:
- **Email:** support@shopam.com
- **GitHub Issues:** [Create an issue](https://github.com/shopam/email-templates/issues)

## 📄 License

© 2025 ShopAm. All rights reserved.

---

**Last Updated:** December 2025  
**Version:** 1.0.0
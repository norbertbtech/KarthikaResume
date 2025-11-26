# EmailJS Integration Guide for Contact Form

## Overview
This guide explains how to add email functionality to your portfolio website so that the contact form sends actual emails to your inbox when visitors submit the form.

## Why EmailJS?
- **Free**: 200 emails/month on the free tier
- **No Backend Required**: Perfect for static websites
- **Easy Setup**: Just JavaScript configuration
- **Secure**: API keys and email templates managed in EmailJS dashboard

## Step-by-Step Setup

### Step 1: Create EmailJS Account
1. Go to [https://www.emailjs.com/](https://www.emailjs.com/)
2. Click "Sign Up" and create a free account
3. Verify your email address

### Step 2: Add Email Service
1. Login to EmailJS dashboard
2. Go to **Email Services**
3. Click **Add New Service**
4. Choose your email provider (Gmail, Outlook, etc.)
5. Follow the connection instructions
6. Save the **Service ID** (you'll need this later)

### Step 3: Create Email Template
1. Go to **Email Templates**
2. Click **Create New Template**
3. Use this template structure:

**Subject**: New Contact Form Submission from {{from_name}}

**Content**:
```
Hello,

You have received a new message from your portfolio website:

Name: {{from_name}}
Email: {{from_email}}
Subject: {{subject}}

Message:
{{message}}

---
This email was sent from your portfolio contact form.
```

4. Save the template and note the **Template ID**

### Step 5: Get Your Public Key
1. Go to **Account** → **General**
2. Find your **Public Key**
3. Copy it for later use

### Step 6: Update Your Website Files

#### 6.1: Fix and Update index.html

You'll need to fix the corrupted `index.html` file. The header section got corrupted during editing. Here's what the correct `<head>` section should look like:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Karthika Manickavasagam - Software Engineer with 6 years of experience in Java, Python, DevOps, and automation. Available for freelance opportunities.">
    <meta name="keywords" content="Software Engineer, Java Developer, Python Developer, DevOps, Freelancer, Automation, Toronto">
    <meta name="author" content="Karthika Manickavasagam">
    <title>Karthika Manickavasagam - Software Engineer | FreelanceDeveloper</title>
    <link rel="stylesheet" href="styles.css">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=Space+Grotesk:wght@500;700&display=swap" rel="stylesheet">
    <!-- EmailJS SDK -->
    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js"></script>
</head>
<body>
    <!-- Navigation -->
    <nav class="navbar" id="navbar">
        <!-- rest of your content... -->
```

#### 6.2: Update script.js

Replace the Contact Form Handling section (lines 148-194) with this enhanced version:

```javascript
// ===========================
// EmailJS Initialization
// ===========================

// Initialize EmailJS with your public key
(function() {
    emailjs.init("YOUR_PUBLIC_KEY_HERE"); // Replace with your actual public key
})();

// ===========================
// Contact Form Handling with EmailJS
// ===========================

const contactForm = document.getElementById('contactForm');

contactForm.addEventListener('submit', (e) => {
    e.preventDefault();
    
    const submitBtn = contactForm.querySelector('.btn-submit');
    const originalText = submitBtn.textContent;
    
    // Show loading state
    submitBtn.textContent = 'Sending...';
    submitBtn.disabled = true;
    
    // Get form data
    const templateParams = {
        from_name: document.getElementById('name').value,
        from_email: document.getElementById('email').value,
        subject: document.getElementById('subject').value,
        message: document.getElementById('message').value
    };
    
    // Send email using EmailJS
    emailjs.send(
        'YOUR_SERVICE_ID',      // Replace with your Service ID
        'YOUR_TEMPLATE_ID',     // Replace with your Template ID
        templateParams
    )
    .then(function(response) {
        // Success
        console.log('SUCCESS!', response.status, response.text);
        submitBtn.textContent = 'Message Sent! ✓';
        submitBtn.style.background = 'linear-gradient(135deg, #22d3ee 0%, #10b981 100%)';
        
        // Reset form
        contactForm.reset();
        
        // Reset button after 3 seconds
        setTimeout(() => {
            submitBtn.textContent = originalText;
            submitBtn.style.background = '';
            submitBtn.disabled = false;
        }, 3000);
    }, function(error) {
        // Error
        console.log('FAILED...', error);
       submitBtn.textContent = 'Failed to Send ✗';
        submitBtn.style.background = 'linear-gradient(135deg, #ef4444 0%, #dc2626 100%)';
        submitBtn.disabled = false;
        
        // Show error message to user
        alert('Sorry, there was an error sending your message. Please try again or contact me directly at mnkarthika718@gmail.com');
        
        // Reset button after 3 seconds
        setTimeout(() => {
            submitBtn.textContent = originalText;
            submitBtn.style.background = '';
        }, 3000);
    });
});
```

## Configuration Checklist

Before deploying, make sure to replace these placeholders in `script.js`:

- [ ] **YOUR_PUBLIC_KEY_HERE** → Your EmailJS Public Key
- [ ] **YOUR_SERVICE_ID** → Your Email Service ID
- [ ] **YOUR_TEMPLATE_ID** → Your Email Template ID

## Testing

1. Start your local server: `python -m http.server 8000`
2. Open `http://localhost:8000`
3. Fill out and submit the contact form
4. Check your email inbox for the message
5. Check browser console for any errors

## Troubleshooting

**Problem**: Emails not sending
- Check that all IDs (Public Key, Service ID, Template ID) are correct
- Verify EmailJS service is connected
- Check browser console for errors
- Ensure you haven't exceeded free tier limit (200/month)

**Problem**: CORS errors
- EmailJS handles CORS automatically, but make sure you're using HTTPS when deployed
- Local testing should work fine with HTTP

**Problem**: Wrong email template
- Verify template variable names match: `from_name`, `from_email`, `subject`, `message`
- Check template is set to "Auto-reply" if you want confirmation emails

## Next Steps

Once email is working:
1. Consider adding a visual success/error notification instead of alerts
2. Add Google reCAPTCHA to prevent spam
3. Set up email notifications for yourself
4. Consider upgrading EmailJS plan if needed

## Alternative: Simple mailto: Link

If you prefer a simpler solution without EmailJS, you can change the contact button to a mailto link:

```html
<a href="mailto:mnkarthika718@gmail.com?subject=Project%20Inquiry" class="btn btn-primary">
    Email Me Directly
</a>
```

This opens the user's default email client, but doesn't provide the same professional form experience.

# Customer's Canvas Integration Guide

## Your Account Credentials

**Login URL:** https://customerscanvas.com/account/login  
**Email:** mitch@supermassiveventuresinc.com  
**Password:** Ech0pl3x!!

---

## Step 1: Get API Credentials (15 minutes)

1. **Login to Customer's Canvas:**
   - Go to https://customerscanvas.com/account/login
   - Use credentials above

2. **Navigate to API Settings:**
   - Click "Account" or "Settings" in top-right
   - Find "API Keys" or "Developer Settings"
   - Generate new API key

3. **Get Your Tenant ID:**
   - Should be visible in account dashboard
   - Format: `tenantId` or similar
   - Copy this value

4. **Get Your Editor URL:**
   - Look for "Design Editor URL" or "Editor Instance URL"
   - Format: `https://[your-tenant].api.customerscanvas.com`
   - Copy this value

---

## Step 2: Add to Vercel Environment Variables

After deploying to Vercel:

1. Go to your Vercel project dashboard
2. Click **Settings** → **Environment Variables**
3. Add these three:

```env
NEXT_PUBLIC_CUSTOMERS_CANVAS_URL=https://[your-tenant].api.customerscanvas.com
CUSTOMERS_CANVAS_API_KEY=[your-api-key]
CUSTOMERS_CANVAS_TENANT_ID=[your-tenant-id]
```

4. Click **Save**
5. **Redeploy** the project (Vercel → Deployments → Redeploy)

---

## Step 3: Create Templates in Customer's Canvas Backend

### Template 1: Classic Vineyard
- **Name:** `classic-vineyard`
- **Size:** 5" x 3.5" (standard gift card)
- **Background:** Vineyard landscape image
- **Editable Zones:**
  - Text: Recipient name (center, large font)
  - Text: Amount (bottom right, bold)
  - Text: Personal message (center bottom, italic)
- **Colors:** Use Wine Shipping palette (#2c1810, #d4a574)

### Template 2: Modern Bottle
- **Name:** `modern-bottle`
- **Background:** Minimalist wine bottle silhouette
- **Editable Zones:** (same as above)

### Template 3: Celebration
- **Name:** `celebration`
- **Background:** Festive champagne/grapes
- **Editable Zones:** (same as above)

### Template 4: Rustic Barrel
- **Name:** `rustic-barrel`
- **Background:** Wood barrel texture
- **Editable Zones:** (same as above)

### Template 5: Elegant Gold
- **Name:** `elegant-gold`
- **Background:** Deep burgundy with gold foil accents
- **Editable Zones:** (same as above)

### Template 6: Birthday Toast
- **Name:** `birthday-toast`
- **Background:** Birthday-themed wine celebration
- **Editable Zones:** (same as above)

---

## Step 4: Update Code with API Credentials

Once you have your credentials, update these files:

### A. Create `.env.local`

```bash
cd wineshipping-gift-cards
nano .env.local
```

Add:
```env
NEXT_PUBLIC_CUSTOMERS_CANVAS_URL=https://[your-tenant].api.customerscanvas.com
CUSTOMERS_CANVAS_API_KEY=[your-api-key]
CUSTOMERS_CANVAS_TENANT_ID=[your-tenant-id]
```

### B. Update `components/DesignEditor.tsx`

Replace the mock editor section (lines 15-25) with:

```typescript
useEffect(() => {
  // Load Customer's Canvas IFrame API
  const script = document.createElement('script');
  script.id = 'CcIframeApiScript';
  script.src = `${process.env.NEXT_PUBLIC_CUSTOMERS_CANVAS_URL}/Resources/Generated/IframeApi.js`;
  script.onload = () => {
    // Initialize the editor
    if (iframeRef.current && window.CustomersCanvas) {
      window.CustomersCanvas.IframeApi.loadEditor(iframeRef.current, {
        productDefinition: {
          templateId: templateId,
          surfaces: [{
            width: 500,
            height: 350
          }]
        },
        userId: 'customer',
        tenantId: process.env.CUSTOMERS_CANVAS_TENANT_ID
      }).then((editor) => {
        setEditorReady(true);
        setIsLoading(false);
      });
    }
  };
  document.head.appendChild(script);

  return () => {
    document.head.removeChild(script);
  };
}, [templateId]);
```

### C. Add TypeScript Declarations

Create `types/customerscanvas.d.ts`:

```typescript
interface Window {
  CustomersCanvas: {
    IframeApi: {
      loadEditor: (iframe: HTMLIFrameElement, config: any) => Promise<any>;
    };
  };
}
```

---

## Step 5: Test the Integration

1. **Local Test:**
   ```bash
   npm run dev
   ```
   - Open http://localhost:3000
   - Select a template
   - Verify editor loads with drag-and-drop

2. **Production Test:**
   - Push changes to GitHub
   - Vercel auto-deploys
   - Test live URL

---

## Step 6: Configure Print Output

In Customer's Canvas backend:

1. Go to **Print Settings**
2. Set output format: **PDF** (high-resolution)
3. Enable **Print-ready export**
4. Configure bleed/trim settings (standard gift card specs)

---

## Troubleshooting

### Editor Not Loading
- Check browser console for errors
- Verify API URL is correct
- Confirm tenant ID matches your account

### Templates Not Found
- Ensure template IDs match exactly
- Check spelling (case-sensitive)
- Verify templates are published (not draft)

### CORS Errors
- Contact Customer's Canvas support
- Whitelist your Vercel domain
- Add `*.vercel.app` to allowed origins

---

## Support

**Customer's Canvas Support:**
- Email: info@aurigma.com
- Support Portal: https://customerscanvas.com/account/cases
- Documentation: https://customerscanvas.com/dev/

**Need Help?**
- Check browser console for errors
- Review Customer's Canvas quick start guide
- Contact their support team (they're responsive)

---

## Next Steps After Integration

1. ✅ Templates created
2. ✅ Editor loading with drag-and-drop
3. ✅ Design saving/exporting
4. ➡️ Add payment processing
5. ➡️ Implement gift card code generation
6. ➡️ Set up email delivery
7. ➡️ Configure custom domain

---

**Current Status:** Ready for Customer's Canvas setup. Should take 1-2 hours to get fully integrated.

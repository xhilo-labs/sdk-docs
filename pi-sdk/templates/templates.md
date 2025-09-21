---
layout: default
title: Templates
---

# Templates

The Pi SDK includes a ready-to-use Next.js template to help you get started quickly.

## 🚀 Quick Start

Create a new Next.js app with the Pi SDK template:

```bash
npm install @xhilo/pi-sdk --template -nextjs
```

This creates a complete Next.js application with:
- ✅ Pi SDK integration
- ✅ Authentication system
- ✅ Payment flows
- ✅ Ad integration
- ✅ Debug panel
- ✅ Responsive design

## 📁 Template Structure

```
pi-sdk-nextjs-app/
├── app/
│   ├── layout.tsx          # Root layout with Pi SDK script
│   ├── page.tsx           # Main page with tab navigation
│   ├── globals.css        # Global styles
│   └── api/               # API routes for payment processing
│       └── payments/
│           ├── approve/
│           │   └── route.ts
│           └── complete/
│               └── route.ts
├── components/
│   ├── ClientProvider.tsx # Client wrapper for XhiloPiProvider
│   ├── AuthSection.tsx    # Authentication testing
│   ├── PaymentFlows.tsx   # Payment flow selector
│   ├── AdSection.tsx      # Ad testing functionality
│   ├── DebugPanel.tsx     # Debug and logging panel
│   └── payment/           # Payment flow components
│       ├── SimplePaymentFlow.tsx
│       ├── AdvancedPaymentFlow.tsx
│       └── CustomPaymentFlow.tsx
├── package.json           # Dependencies and scripts
├── next.config.js         # Next.js configuration
├── tailwind.config.js     # Tailwind CSS configuration
├── tsconfig.json          # TypeScript configuration
├── .gitignore            # Git ignore file
└── README.md             # Template documentation
```

## 🎯 Features Included

### Authentication Tab
- Initialize Pi SDK
- Authenticate with Pi Network
- View user information
- Logout functionality

### Payment Flows Tab
- **Simple Payment**: Test basic payments without backend
- **Advanced Payment**: Test payments with backend integration
- **Custom Payment**: Test full control implementation

### Ads & Rewards Tab
- Check ad eligibility
- Verify ad readiness
- Request and show ads
- Test rewarded ad functionality

### Debug Panel Tab
- Monitor SDK status in real-time
- View and filter console logs
- Export logs for analysis
- Check user information and environment

## ⚙️ Setup

### 1. Install Dependencies

```bash
cd pi-sdk-nextjs-app
npm install
```

### 2. Environment Variables

Create a `.env.local` file:

```env
# Required
PI_API_KEY=your_pi_api_key_here
PI_WALLET_PRIVATE_SEED=your_wallet_private_seed_here
NEXT_PUBLIC_APP_URL=http://localhost:3000

# Optional
NEXT_PUBLIC_PI_SANDBOX=true
```

### 3. Start Development Server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

## 🔧 Customization

### Styling

The template uses Tailwind CSS. Customize styles in:
- `app/globals.css` - Global styles
- Component files - Individual component styles

### Components

Modify components in the `components/` directory:
- `AuthSection.tsx` - Authentication UI
- `PaymentFlows.tsx` - Payment flow selector
- `AdSection.tsx` - Ad functionality
- `DebugPanel.tsx` - Debug interface

### API Routes

Customize payment processing in `app/api/payments/`:
- `approve/route.ts` - Payment approval
- `complete/route.ts` - Payment completion

### Navigation

Update the main page (`app/page.tsx`) to modify:
- Tab navigation
- Page layout
- Feature organization

## 📱 Responsive Design

The template is fully responsive and includes:
- Mobile-first design
- Tablet optimization
- Desktop layouts
- Touch-friendly interfaces

## 🎨 Theme Customization

### Colors

The template uses a Pi Network-inspired color scheme:
- Primary: Pi orange (#F7931A)
- Secondary: Dark blue (#1A1A1A)
- Accent: Light blue (#4A90E2)

### Typography

- Headers: Inter font family
- Body: System font stack
- Code: Monaco, Consolas monospace

## 🔌 API Integration

### Payment Processing

The template includes example API routes for:
- Payment approval
- Payment completion
- Error handling
- Status updates

### Webhook Support

Add webhook endpoints to handle:
- Payment status updates
- User authentication events
- Ad completion notifications

## 🧪 Testing

### Development Testing

1. **Start the dev server**: `npm run dev`
2. **Open in Pi Browser**: Navigate to your app URL
3. **Test features**: Use the debug panel to monitor
4. **Check logs**: View real-time SDK logs

### Production Testing

1. **Build the app**: `npm run build`
2. **Start production server**: `npm start`
3. **Test in Pi Browser**: Verify all functionality
4. **Monitor performance**: Use browser dev tools

## 📦 Deployment

### Vercel

1. Push to GitHub
2. Connect to Vercel
3. Set environment variables
4. Deploy

### Netlify

1. Build the project
2. Deploy to Netlify
3. Configure environment variables
4. Set up redirects

### Other Platforms

The template works with any platform that supports Next.js:
- AWS Amplify
- Railway
- Render
- DigitalOcean App Platform

## 🐛 Troubleshooting

### Common Issues

1. **"use client" errors**: All components have proper directives
2. **Pi SDK not loading**: Script is included in layout
3. **Environment variables**: Check `.env.local` file
4. **Build errors**: Ensure all dependencies are installed

### Debug Tips

- Use the Debug Panel to monitor SDK status
- Check browser console for errors
- Verify environment variables
- Test in Pi Browser for full functionality

## 📚 Next Steps

After using the template:

1. **Customize the UI** - Match your brand
2. **Add your features** - Extend functionality
3. **Configure backend** - Set up your API
4. **Deploy** - Go live with your app

## 🔗 Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [Pi Network Developer Docs](https://developers.minepi.com/)
- [SDK API Reference](/api-reference)

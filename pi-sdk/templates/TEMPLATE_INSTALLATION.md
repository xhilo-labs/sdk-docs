# Template Installation Guide

This guide explains how users can install the Next.js template from your Pi SDK package.

## 🚀 Installation Command

Users can install the template using:

```bash
npm install @xhilo/pi-sdk --template -nextjs
```

## 📋 What This Does

When users run the above command, npm will:

1. **Install the Pi SDK package** (`@xhilo/pi-sdk`)
2. **Extract the Next.js template** from the `templates/nextjs` directory
3. **Create a new project** with all the template files
4. **Set up dependencies** automatically

## 📁 Template Structure

The template includes:

```
pi-sdk-nextjs-app/
├── app/
│   ├── layout.tsx          # Root layout with ClientProvider
│   ├── page.tsx           # Main page with tab navigation
│   └── api/               # API routes for payment processing
├── components/
│   ├── ClientProvider.tsx # Client wrapper for XhiloPiProvider
│   ├── AuthSection.tsx    # Authentication testing
│   ├── PaymentFlows.tsx   # Payment flow selector
│   ├── AdSection.tsx      # Ad testing functionality
│   ├── DebugPanel.tsx     # Debug and logging panel
│   └── payment/           # Payment flow components
├── package.json           # Dependencies and scripts
├── next.config.js         # Next.js configuration
├── tailwind.config.js     # Tailwind CSS configuration
├── .gitignore            # Git ignore file (excludes node_modules)
└── README.md             # Template documentation
```

## ✅ Features Included

- ✅ **Next.js App Router Compatible** - All components properly configured with "use client" directives
- 🔐 **Authentication** - Pi Network user authentication and management
- 💳 **Payment Flows** - Simple, Advanced, and Custom payment implementations
- 📺 **Ad Integration** - Pi Network ads and rewards functionality
- 🐛 **Debug Panel** - Real-time SDK monitoring and logging
- 🎨 **Responsive Design** - Built with Tailwind CSS

## 🛠️ After Installation

Once users install the template, they need to:

1. **Navigate to the project directory:**
   ```bash
   cd pi-sdk-nextjs-app
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Set up environment variables:**
   ```bash
   cp env.example .env.local
   ```

4. **Configure environment variables in `.env.local`:**
   ```env
   PI_API_KEY=your_pi_api_key_here
   PI_WALLET_PRIVATE_SEED=your_wallet_private_seed_here
   NEXT_PUBLIC_PI_SANDBOX=true
   NEXT_PUBLIC_APP_URL=http://localhost:3000
   ```

5. **Start the development server:**
   ```bash
   npm run dev
   ```

6. **Open [http://localhost:3000](http://localhost:3000) in their browser**

## 📦 Package.json Configuration

The template's `package.json` is configured to:

- Use the published `@xhilo/pi-sdk` package (version ^1.0.0)
- Include all necessary Next.js dependencies
- Set up proper scripts for development, building, and production
- Include TypeScript and Tailwind CSS configurations

## 🔧 Template Files

The following files are included in the template:

- **App Router files** - `app/layout.tsx`, `app/page.tsx`, `app/globals.css`
- **API routes** - `app/api/payments/approve/route.ts`, `app/api/payments/complete/route.ts`
- **React components** - All components with proper "use client" directives
- **Configuration files** - `next.config.js`, `tailwind.config.js`, `tsconfig.json`
- **Documentation** - `README.md` with comprehensive setup instructions
- **Git configuration** - `.gitignore` to exclude `node_modules`

## 🎯 Benefits

This template approach provides:

1. **Easy setup** - One command to get started
2. **Best practices** - Proper Next.js App Router configuration
3. **Complete example** - Working Pi SDK integration
4. **No node_modules** - Clean template without dependencies
5. **Documentation** - Comprehensive setup and usage guide

## 📚 User Documentation

Users will find detailed documentation in the template's `README.md` file, including:

- Setup instructions
- Environment variable configuration
- Feature explanations
- Troubleshooting guide
- Customization options

## 🔄 Updates

When you update the template:

1. Make changes to the `templates/nextjs/` directory
2. Update the version in `package.json`
3. Publish the updated package
4. Users can install the updated template with the same command

This ensures users always get the latest template with your improvements and fixes.

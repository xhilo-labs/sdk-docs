# Documentation Organization

This document explains the organization structure of the Xhilo Labs SDK Documentation repository.

## 📁 Repository Structure

```
sdk-docs/
├── README.md                    # Main repository overview
├── ORGANIZATION.md              # This file
└── pi-sdk/                      # Pi SDK documentation
    ├── README.md                # Pi SDK overview
    ├── getting-started/         # Getting started guides
    │   ├── README.md
    │   └── getting-started.md
    ├── api-reference/           # API documentation
    │   ├── README.md
    │   └── api-reference.md
    ├── react/                   # React SDK documentation
    │   ├── README.md
    │   ├── components.md
    │   ├── PaymentButton.md
    │   ├── PaymentProcessDisplay.md
    │   ├── provider-hooks.md
    │   ├── SimplePaymentButton.md
    │   ├── usePiPayments.md
    │   ├── usePiSimplePayments.md
    │   ├── useXhiloPi.md
    │   ├── useXhiloPiLogs.md
    │   ├── useXhiloPiNetwork.md
    │   ├── useXhiloPiReady.md
    │   └── useXhiloPiUser.md
    ├── backend/                 # Backend SDK documentation
    │   ├── README.md
    │   └── README.md (from backend/)
    ├── examples/                # Usage examples
    │   ├── README.md
    │   └── examples.md
    ├── troubleshooting/         # Troubleshooting guides
    │   ├── README.md
    │   └── troubleshooting.md
    └── templates/               # Project templates
        ├── README.md
        └── templates.md
```

## 🎯 Documentation Principles

### 1. **Modular Organization**
- Each SDK has its own section
- Related documentation is grouped together
- Clear separation between frontend and backend

### 2. **Consistent Structure**
- Every section has a README.md index
- Navigation links between related sections
- Consistent formatting and styling

### 3. **User-Friendly Navigation**
- Clear section headers
- Quick links to related documentation
- Breadcrumb-style navigation

### 4. **Comprehensive Coverage**
- Getting started guides
- Complete API references
- Real-world examples
- Troubleshooting guides
- Project templates

## 🔗 Link Structure

All internal links follow this pattern:
- `./filename.md` - Same directory
- `../section/filename.md` - Different section
- `https://github.com/xhilo-labs/pi-sdk` - External repository links

## 📚 Adding New SDKs

When adding new SDKs to this repository:

1. Create a new directory under the root (e.g., `new-sdk/`)
2. Follow the same structure as `pi-sdk/`
3. Update the main `README.md` to include the new SDK
4. Ensure all internal links are properly formatted

## 🚀 Maintenance

- Keep documentation up-to-date with code changes
- Update links when repository URLs change
- Add new examples and use cases regularly
- Monitor for broken links and fix them promptly

---

*This documentation structure was created to provide a comprehensive, organized, and user-friendly experience for developers using Xhilo Labs SDKs.*

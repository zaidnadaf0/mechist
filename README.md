# MECHIST ⚙️

A full-stack platform connecting vehicle owners with certified mechanics for diagnostics, maintenance scheduling, and repair services.

## 🏗️ Architecture

- **Frontend:** React + Vite + Tailwind CSS
- **Backend:** Firebase (Auth, Firestore, Cloud Functions)
- **ML Integration:** Basic diagnostic recommendation engine
- **Storage:** Firebase Storage for images and documents

## ✨ Features

- User authentication with role-based access (owner / mechanic / admin)
- Vehicle profile management
- Service booking and scheduling system
- Mechanic rating and review system
- Diagnostic report generation
- ML-powered preliminary fault recommendations
- Admin dashboard for platform management

## 🚀 Getting Started

### Prerequisites

- Node.js ≥ 18
- Firebase project with Auth, Firestore, Cloud Functions, and Storage enabled

### Installation

```bash
git clone https://github.com/zaidnadaf0/mechist.git
cd mechist
npm install
```

### Environment Setup

Create a `.env` file in the root directory:

```env
VITE_FIREBASE_API_KEY=your_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_auth_domain
VITE_FIREBASE_PROJECT_ID=your_project_id
```

### Run Locally

```bash
npm run dev
```

## 📁 Project Structure

```
mechist/
├── src/
│   ├── components/     # Reusable UI components
│   ├── pages/          # Page-level components
│   ├── services/       # Firebase & API services
│   ├── ml/             # ML diagnostic models
│   ├── hooks/          # Custom React hooks
│   └── utils/          # Utility functions
├── functions/          # Firebase Cloud Functions
├── public/             # Static assets
└── tests/              # Unit & integration tests
```

## 🧪 Testing

```bash
npm test
```

## 📄 License

This project is licensed under the [Apache License 2.0](LICENSE).

## 🙋 Author

**Mohammed Zaid Nadaf** — CS Engineering Student · Aspiring Cloud Engineer

> Built as a technical project demonstrating full-stack development, role-based access control, and basic ML integration.

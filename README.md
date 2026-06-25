echo "# Secured-id-datatavualt" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:crishaternal/Secured-id-datatavualt.git
git push -u origin main
import { useState } from "react";

const files = [
  {
    name: "package.json",
    lang: "json",
    desc: "Project dependencies & scripts",
    content: `{
  "name": "secureid-site",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.2.0",
    "vite": "^4.4.0"
  },
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}`
  },
  {
    name: "vite.config.js",
    lang: "js",
    desc: "Vite bundler config",
    content: `import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()]
})`
  },
  {
    name: "vercel.json",
    lang: "json",
    desc: "Vercel routing — connects /api/* to serverless functions",
    content: `{
  "rewrites": [
    { "source": "/api/(.*)", "destination": "/api/$1" }
  ]
}`
  },
  {
    name: "index.html",
    lang: "html",
    desc: "HTML entry point",
    content: `<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>SecureID — Personal Data Vault</title>
    <meta name="description" content="The world's most secure personal contact details service." />
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>`
  },
  {
    name: "src/main.jsx",
    lang: "jsx",
    desc: "React entry point",
    content: `import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)`
  },
  {
    name: "api/chat.js",
    lang: "js",
    desc: "🔑 Secure serverless proxy — hides your API key from the browser",
    content: `export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' })
  }

  const { messages, system } = req.body

  if (!messages || !Array.isArray(messages)) {
    return res.status(400).json({ error: 'Invalid request body' })
  }

  try {
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'x-api-key': process.env.ANTHROPIC_API_KEY,
        'anthropic-version': '2023-06-01'
      },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 1000,
        system: system || '',
        messages
      })
    })

    if (!response.ok) {
      const err = await response.json()
      return res.status(response.status).json({ error: err })
    }

    const data = await response.json()
    return res.status(200).json(data)

  } catch (error) {
    console.error('Proxy error:', error)
    return res.status(500).json({ error: 'Internal server error' })
  }
}`
  },
  {
    name: ".env.example",
    lang: "bash",
    desc: "Environment variable template — rename to .env locally",
    content: `# Get your key at https://console.anthropic.com
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxxxxxxxxxxxxxxx`
  },
  {
    name: "src/App.jsx",
    lang: "jsx",
    desc: "Main app — copy from the SecureID artifact, apply ONE change below",
    content: `// 1. Copy the full code from the "SecureID" artifact in your Claude chat.
//
// 2. Find this block inside the AIAdvisor component (~send function):
//
//   const res = await fetch("https://api.anthropic.com/v1/messages", {
//     method: "POST",
//     headers: { "Content-Type": "application/json" },
//     body: JSON.stringify({ model: "claude-sonnet-4-20250514", ... })
//   });
//
// 3. Replace it with this (routes through your secure proxy):
//
//   const res = await fetch("/api/chat", {
//     method: "POST",
//     headers: { "Content-Type": "application/json" },
//     body: JSON.stringify({ messages: history, system: SYSTEM })
//   });
//
// That's the only change needed. Everything else stays the same.`
  }
];

const steps = [
  {
    num: "01", title: "Install Prerequisites",
    cmd: "node --version   # needs v18+\nnpm --version    # needs v9+",
    note: "Download Node.js from nodejs.org if not installed."
  },
  {
    num: "02", title: "Scaffold the Project",
    cmd: "mkdir secureid-site && cd secureid-site\nnpm create vite@latest . -- --template react\nnpm install",
    note: "Creates a fresh React + Vite project in the current folder."
  },
  {
    num: "03", title: "Add Your Files",
    cmd: "# Copy each file from the tabs above into your project.\n# Create the api/ folder for the serverless proxy:\nmkdir api",
    note: "File structure: index.html · src/App.jsx · src/main.jsx · api/chat.js · vercel.json"
  },
  {
    num: "04", title: "Test Locally",
    cmd: "npm run dev\n# Opens at http://localhost:5173",
    note: "Create a .env file with your ANTHROPIC_API_KEY to test the AI chat locally."
  },
  {
    num: "05", title: "Deploy to Vercel",
    cmd: "npx vercel\n# Follow the prompts — choose your project name\n# Your site goes live at https://your-name.vercel.app",
    note: "First time? It'll ask you to log in or create a free Vercel account."
  },
  {
    num: "06", title: "Add Your API Key in Vercel",
    cmd: "# In Vercel dashboard:\n# Project → Settings → Environment Variables\n# Name:  ANTHROPIC_API_KEY\n# Value: sk-ant-xxxxxxxxxxxx",
    note: "After adding, redeploy once: npx vercel --prod — then ARIA is live for everyone."
  }
];

const palette = {
  bg: "#060c1a", bg2: "#0a1428", bg3: "#0e1e3a",
  gold: "#c9a84c", goldD: "#7a5c1a", goldL: "#f0d080",
  cyan: "#00d4ff", muted: "#4a6080", white: "#f0f4ff",
  green: "#00e676"
};

const mono = "'Share Tech Mono', 'Courier New', monospace";
const bebas = "Impact, sans-serif";

export default function DeployKit() {
  const [activeFile, setActiveFile] = useState(0);
  const [copied, setCopied] = useState({});
  const [copiedStep, setCopiedStep] = useState({});

  const copy = (text, key, setter) => {
    navigator.clipboard.writeText(text).then(() => {
      setter(s => ({ ...s, [key]: true }));
      setTimeout(() => setter(s => ({ ...s, [key]: false })), 1800);
    });
  };

  const s = {
    wrap: { background: palette.bg, minHeight: "100vh", fontFamily: "sans-serif", color: palette.white, paddingBottom: 60 },
    header: { background: `linear-gradient(135deg, ${palette.bg3}, ${palette.bg2})`, borderBottom: `1px solid ${palette.goldD}`, padding: "28px 32px 24px" },
    logo: { fontFamily: bebas, fontSize: 26, letterSpacing: 4, color: palette.gold, marginBottom: 4 },
    subtitle: { fontFamily: mono, fontSize: 11, color: palette.muted, letterSpacing: 2 },
    badge: { display: "inline-flex", alignItems: "center", gap: 6, background: "rgba(0,230,118,0.08)", border: `1px solid rgba(0,230,118,0.25)`, borderRadius: 4, padding: "4px 12px", marginTop: 12 },
    badgeDot: { width: 6, height: 6, borderRadius: "50%", background: palette.green },
    badgeText: { fontFamily: mono, fontSize: 10, color: palette.green, letterSpacing: 1 },

    section: { padding: "32px 32px 0" },
    sectionLabel: { fontFamily: mono, fontSize: 10, color: palette.cyan, letterSpacing: 3, textTransform: "uppercase", marginBottom: 16 },

    stepsGrid: { display: "flex", flexDirection: "column", gap: 14 },
    stepCard: { background: palette.bg2, border: `1px solid #1a3050`, borderRadius: 8, overflow: "hidden" },
    stepHeader: { display: "flex", alignItems: "center", gap: 14, padding: "14px 18px", background: `linear-gradient(90deg, ${palette.bg3}, ${palette.bg2})` },
    stepNum: { fontFamily: bebas, fontSize: 22, color: palette.goldD, letterSpacing: 2, minWidth: 32 },
    stepTitle: { fontFamily: bebas, fontSize: 18, letterSpacing: 2, color: palette.goldL, flex: 1 },
    stepNote: { fontFamily: mono, fontSize: 10, color: palette.muted, letterSpacing: 1 },
    codeBlock: { background: "rgba(0,0,0,0.35)", borderTop: `1px solid #1a3050`, padding: "14px 18px", position: "relative" },
    codeText: { fontFamily: mono, fontSize: 12, color: "#a0c8e0", whiteSpace: "pre", overflowX: "auto", lineHeight: 1.7, margin: 0 },
    copyBtn: (active) => ({
      position: "absolute", top: 10, right: 12,
      background: active ? "rgba(0,230,118,0.15)" : "rgba(201,168,76,0.1)",
      border: `1px solid ${active ? "rgba(0,230,118,0.4)" : palette.goldD}`,
      color: active ? palette.green : palette.gold,
      fontFamily: mono, fontSize: 10, letterSpacing: 1,
      padding: "4px 10px", borderRadius: 4, cursor: "pointer", transition: "all 0.2s"
    }),

    fileTabs: { display: "flex", flexWrap: "wrap", gap: 6, marginBottom: 0 },
    tab: (active) => ({
      fontFamily: mono, fontSize: 10, letterSpacing: 1,
      padding: "7px 14px", borderRadius: "6px 6px 0 0",
      border: `1px solid ${active ? palette.goldD : "#1a3050"}`,
      borderBottom: active ? `1px solid ${palette.bg2}` : `1px solid ${palette.goldD}`,
      background: active ? palette.bg2 : "transparent",
      color: active ? palette.goldL : palette.muted,
      cursor: "pointer", transition: "all 0.2s"
    }),
    filePanel: {
      background: palette.bg2, border: `1px solid ${palette.goldD}`,
      borderRadius: "0 8px 8px 8px", overflow: "hidden"
    },
    filePanelHead: {
      display: "flex", alignItems: "center", justifyContent: "space-between",
      padding: "10px 16px", background: `linear-gradient(90deg, ${palette.bg3}, ${palette.bg2})`,
      borderBottom: `1px solid #1a3050`
    },
    filePanelName: { fontFamily: mono, fontSize: 12, color: palette.gold, letterSpacing: 1 },
    filePanelDesc: { fontFamily: mono, fontSize: 10, color: palette.muted },
    fileContent: { padding: "18px", overflowX: "auto" },
    fileCode: { fontFamily: mono, fontSize: 12, color: "#90b8d8", whiteSpace: "pre", lineHeight: 1.75, margin: 0 },

    deployBand: {
      margin: "32px 32px 0",
      background: `linear-gradient(135deg, #0a1e3c, #0e2850)`,
      border: `1px solid ${palette.goldD}`, borderRadius: 10,
      padding: "28px 32px", textAlign: "center"
    },
    deployTitle: { fontFamily: bebas, fontSize: 30, letterSpacing: 4, color: palette.gold, marginBottom: 8 },
    deploySub: { fontFamily: mono, fontSize: 11, color: palette.muted, letterSpacing: 1, marginBottom: 20 },
    deployBtns: { display: "flex", gap: 12, justifyContent: "center", flexWrap: "wrap" },
    deployBtn: {
      background: `linear-gradient(135deg, ${palette.goldD}, ${palette.gold})`,
      color: palette.bg, fontFamily: bebas, fontSize: 16, letterSpacing: 3,
      padding: "12px 28px", border: "none", borderRadius: 6,
      cursor: "pointer", textDecoration: "none", display: "inline-block"
    },
    outlineBtn: {
      background: "transparent", color: palette.cyan,
      border: `1px solid #007a9a`, fontFamily: bebas, fontSize: 16, letterSpacing: 3,
      padding: "12px 28px", borderRadius: 6, cursor: "pointer", textDecoration: "none", display: "inline-block"
    },
    checklist: { display: "flex", flexWrap: "wrap", gap: 10, marginTop: 16, justifyContent: "center" },
    checkItem: { display: "flex", alignItems: "center", gap: 6, fontFamily: mono, fontSize: 10, color: palette.muted, letterSpacing: 1 },
    checkIcon: { color: palette.gold }
  };

  return (
    <div style={s.wrap}>
      <div style={s.header}>
        <div style={s.logo}>🛡️ SECUREID — DEPLOYMENT KIT</div>
        <div style={s.subtitle}>// VERCEL + VITE + REACT + ANTHROPIC API</div>
        <div style={s.badge}>
          <div style={s.badgeDot} />
          <span style={s.badgeText}>READY TO DEPLOY — 6 STEPS — ~5 MINUTES</span>
        </div>
      </div>

      <div style={s.section}>
        <div style={s.sectionLabel}>// STEP-BY-STEP DEPLOY GUIDE</div>
        <div style={s.stepsGrid}>
          {steps.map((step, i) => (
            <div key={i} style={s.stepCard}>
              <div style={s.stepHeader}>
                <div style={s.stepNum}>{step.num}</div>
                <div style={{ flex: 1 }}>
                  <div style={s.stepTitle}>{step.title}</div>
                  <div style={s.stepNote}>{step.note}</div>
                </div>
              </div>
              <div style={s.codeBlock}>
                <pre style={s.codeText}>{step.cmd}</pre>
                <button style={s.copyBtn(copiedStep[i])} onClick={() => copy(step.cmd, i, setCopiedStep)}>
                  {copiedStep[i] ? "✓ COPIED" : "COPY"}
                </button>
              </div>
            </div>
          ))}
        </div>
      </div>

      <div style={{ ...s.section, paddingTop: 36 }}>
        <div style={s.sectionLabel}>// PROJECT FILES — CLICK TO VIEW & COPY</div>
        <div style={s.fileTabs}>
          {files.map((f, i) => (
            <div key={i} style={s.tab(activeFile === i)} onClick={() => setActiveFile(i)}>
              {f.name}
            </div>
          ))}
        </div>
        <div style={s.filePanel}>
          <div style={s.filePanelHead}>
            <div>
              <div style={s.filePanelName}>{files[activeFile].name}</div>
              <div style={s.filePanelDesc}>{files[activeFile].desc}</div>
            </div>
            <button style={s.copyBtn(copied[activeFile])} onClick={() => copy(files[activeFile].content, activeFile, setCopied)}>
              {copied[activeFile] ? "✓ COPIED" : "COPY FILE"}
            </button>
          </div>
          <div style={s.fileContent}>
            <pre style={s.fileCode}>{files[activeFile].content}</pre>
          </div>
        </div>
      </div>

      <div style={s.deployBand}>
        <div style={s.deployTitle}>⚡ DEPLOY IN ONE CLICK</div>
        <div style={s.deploySub}>PUSH TO GITHUB THEN IMPORT TO VERCEL — OR USE THE CLI ABOVE</div>
        <div style={s.deployBtns}>
          <a href="https://vercel.com/new" target="_blank" rel="noopener noreferrer" style={s.deployBtn}>
            🚀 OPEN VERCEL DASHBOARD
          </a>
          <a href="https://console.anthropic.com" target="_blank" rel="noopener noreferrer" style={s.outlineBtn}>
            🔑 GET API KEY
          </a>
        </div>
        <div style={s.checklist}>
          {["FREE HOSTING TIER", "CUSTOM DOMAIN SUPPORT", "AUTO HTTPS/SSL", "SERVERLESS API PROXY", "GLOBAL CDN", "CI/CD FROM GIT"].map((t, i) => (
            <div key={i} style={s.checkItem}><span style={s.checkIcon}>✓</span>{t}</div>
          ))}
        </div>
      </div>
    </div>
  );
}

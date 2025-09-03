# proto-v2max
{
  "name": "proto-v2max",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "next": "14.2.5",
    "react": "18.2.0",
    "react-dom": "18.2.0",
    "yaml": "2.5.0",
    "zod": "3.23.8"
  },
  "devDependencies": {
    "@types/node": "20.11.30",
    "@types/react": "18.2.66",
    "typescript": "5.4.5"
  }
}
/** @type {import('next').NextConfig} */
const nextConfig = { reactStrictMode: true };
module.exports = nextConfig;

{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "es2022"],
    "allowJs": false,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "baseUrl": ".",
    "paths": { "@/*": ["./*"] }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}

/// <reference types="next" />
/// <reference types="next/image-types/global" />

// NOTE: This file should not be edited

export default function Home() {
  return (
    <main style={{ padding: 24 }}>
      <h1>Proto v2</h1>
      <p>このトップが表示されれば 404 は解消です。</p>
      <ul>
        <li><a href="/api/health">/api/health</a></li>
        <li><a href="/shipping/labels">/shipping/labels</a></li>
        <li><a href="/api/risk">/api/risk</a></li>
      </ul>
    </main>
  );
}

import { NextResponse } from 'next/server';

export async function GET() {
  return NextResponse.json({ ok: true, ts: new Date().toISOString() });
}

import { NextResponse } from 'next/server';

export async function GET() {
  return NextResponse.json({
    provider: "mock",
    label_png_url: "/favicon.ico",
    label_pdf_url: "https://example.com/mock.pdf"
  });
}

export async function POST(req: Request) {
  const body = await req.json().catch(() => ({}));
  return NextResponse.json({
    provider: "mock",
    request: body,
    label_png_url: "/favicon.ico",
    created_at: new Date().toISOString()
  });
}

import fs from 'node:fs';
import path from 'node:path';
import { NextResponse } from 'next/server';
import YAML from 'yaml';

export async function GET() {
  const p = path.join(process.cwd(), 'ops', 'risk_rules.yaml');
  const src = fs.readFileSync(p, 'utf8');
  const doc = YAML.parse(src);
  return NextResponse.json({ ok: true, rules: doc });
}

version: 1
chat:
  rate_limits:
    messages_per_minute: 20
    burst: 10
  pii_blocklist:
    - /[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}/i
    - /\b0\d{1,3}-\d{2,4}-\d{4}\b/
    - /\bhttps?:\/\/[^\s]+/i
listings:
  new_account_guard:
    min_account_age_days: 3
    max_active_listings: 5
keywords:
  suspicious:
    - "即振込"
    - "前払い"
    - "格安コピー"


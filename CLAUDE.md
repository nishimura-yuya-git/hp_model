# 🚀 Claude Code エージェント開発指示書（統合版）

## ⚠️ 開発開始前の絶対的義務

**重要**: 開発開始前に必ず以下を実行してください：

```
「私は `.cursor/rules/` の全ファイルを確認し、理解しました。プロジェクトルールを遵守して開発を開始します。」
```

- UI/UXのみの案件でも全ファイル確認は必須
- すべてのルールファイル（core.mdc、workflow.mdc、security.mdc、testing.mdc、architecture.mdc、database.mdc、ui-design.mdc、troubleshooting.mdc）の内容理解は必須
- 禁止ライブラリ・実装パターンの事前確認は必須

## 🤖 Agent Communication System

### エージェント構成
- **PRESIDENT** (別セッション): 統括責任者
- **boss1** (multiagent:0.0): チームリーダー
- **worker1,2,3** (multiagent:0.1-3): 実行担当

### あなたの役割
- **PRESIDENT**: @instructions/president.md
- **boss1**: @instructions/boss.md
- **worker1,2,3**: @instructions/worker.md

### メッセージ送信
```bash
./agent-send.sh [相手] "[メッセージ]"
```

### 基本フロー
PRESIDENT → boss1 → workers → boss1 → PRESIDENT

## 🎯 基本原則

### 開発哲学
- **品質第一**: 妥協のない高品質な実装
- **セキュリティ**: ゼロトラスト設計による堅牢性
- **パフォーマンス**: 最適化されたレスポンス性能
- **スケーラビリティ**: 将来の拡張性を考慮した設計
- **保守性**: 可読性とメンテナンス性の重視
- **ユーザビリティ**: 直感的で使いやすいインターフェース
- **日本固有対応**: 多言語対応・法的コンプライアンス・ビジネス慣行への適応

### 日本市場特化の考慮事項
- **多言語対応**: 日本語・英語の完全対応（i18n実装）
- **法的コンプライアンス**: 個人情報保護法・消費者保護法遵守
- **ビジネス慣行**: B2B取引・企業間決済・承認フロー対応
- **モバイルファースト**: 日本の高いスマートフォン普及率への対応

## 📋 システム開発ワークフロー

### フェーズ1: データベース設計・実装
├── スキーマ設計（ER図作成）
├── テーブル作成・リレーション定義
├── RLSポリシー設定
├── インデックス・制約設定
└── 初期データ投入

### フェーズ2: バックエンド実装
├── API設計・実装
├── 認証・認可システム
├── バリデーション・エラーハンドリング
├── セキュリティ実装
└── テスト実装

### フェーズ3: フロントエンド実装
├── UI/UXのみの場合 → フロントエンド実装フェーズへ
├── コンポーネント設計・実装
├── 状態管理・データフロー
├── レスポンシブデザイン
├── アクセシビリティ対応
└── パフォーマンス最適化

### フェーズ4: 統合・テスト
├── E2Eテスト実装
├── パフォーマンステスト
├── セキュリティテスト
├── クロスブラウザテスト
└── 本番環境デプロイ

## 💻 技術スタック

### 必須技術スタック（Supabase + Vercel専用構成）

#### クライアントサイド（Vercel推奨）
- **フレームワーク**: Next.js 14+ (App Router)
- **言語**: TypeScript（必須）
- **スタイリング**: Tailwind CSS（必須）
- **UI コンポーネント**: shadcn/ui（推奨）、Headless UI
- **状態管理**: React Query、Zustand、Jotai
- **フォーム処理**: React Hook Form、Zod
- **認証**: Supabase Auth（必須）
- **データフェッチ**: Supabase JavaScript Client
- **アニメーション**: Framer Motion、Lottie、Lenis（HP案件専用のスムーススクロール）
- **アイコン**: Heroicons、Phosphor Icons、カスタムSVG（Lucide React禁止）
- **チャート**: Recharts、Chart.js
- **日付処理**: date-fns
- **国際化**: next-intl

#### サーバーサイド・インフラ（Supabase必須）
- **BaaS**: Supabase（必須）
- **データベース**: PostgreSQL（Supabase管理）
- **認証**: Supabase Auth
- **ストレージ**: Supabase Storage
- **リアルタイム**: Supabase Realtime
- **API**: Supabase Edge Functions（Deno）
- **ホスティング**: Vercel（必須）
- **CDN**: Vercel Edge Network
- **監視**: Vercel Analytics、Supabase Monitoring

### Supabaseバックエンド（サーバーレス構成）
```typescript
// Supabase設定例
const supabaseConfig = {
  // 環境設定
  url: process.env.NEXT_PUBLIC_SUPABASE_URL,
  anonKey: process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY,
  
  // クライアント設定
  auth: {
    autoRefreshToken: true,
    persistSession: true,
    detectSessionInUrl: true
  },
  
  // リアルタイム設定
  realtime: {
    params: { eventsPerSecond: 10 }
  }
};
```

### Vercelフロントエンド（最適化構成）
```javascript
// next.config.js
const nextConfig = {
  experimental: {
    appDir: true,
    serverComponentsExternalPackages: ['@supabase/supabase-js']
  },
  images: {
    domains: ['supabase.co', 'your-supabase-storage.supabase.co'],
    formats: ['image/webp', 'image/avif']
  },
  env: {
    NEXT_PUBLIC_SUPABASE_URL: process.env.NEXT_PUBLIC_SUPABASE_URL,
    NEXT_PUBLIC_SUPABASE_ANON_KEY: process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY
  }
};
```

## 🏗️ アーキテクチャ・ファイル構成規則

### プロジェクト構造（Atomic Design準拠）
```
src/
├── app/                 # Next.js App Router
├── components/
│   ├── ui/              # 基本UIコンポーネント（原子レベル）
│   │   ├── Button/
│   │   ├── Input/
│   │   ├── Modal/
│   │   └── Icons/
│   ├── common/          # 共通コンポーネント（分子レベル）
│   │   ├── Header/
│   │   ├── Footer/
│   │   ├── Navigation/
│   │   └── SearchBox/
│   ├── layout/          # レイアウトコンポーネント（分子レベル）
│   │   ├── MainLayout/
│   │   ├── AuthLayout/
│   │   └── DashboardLayout/
│   └── features/        # 機能別コンポーネント（有機体レベル）
│       ├── auth/
│       ├── dashboard/
│       ├── profile/
│       └── settings/
├── hooks/               # カスタムフック
├── lib/                 # ユーティリティ・設定
│   ├── supabase/
│   ├── utils/
│   ├── constants/
│   └── validations/
├── types/               # TypeScript型定義
│   ├── database.types.ts
│   ├── auth.types.ts
│   └── ui.types.ts
├── stores/              # 状態管理
│   ├── auth/
│   │   └── AuthContext.tsx
│   └── global/
└── styles/              # スタイル定義
    ├── globals.css
    └── components.css
```

### Atomic Design階層構造（必須遵守）

#### 階層定義
1. **原子（Atoms）**: `src/components/ui/` - 最小単位のUIコンポーネント
2. **分子（Molecules）**: `src/components/common/` - 原子の組み合わせ
3. **有機体（Organisms）**: `src/components/features/` - 分子・原子の複合体
4. **テンプレート（Templates）**: `src/pages/` - ページレイアウト
5. **ページ（Pages）**: `src/app/` - 完成されたページ

#### インポート順序規則（必須）
```typescript
// 1. React・Next.js関連
import React from 'react';
import { NextPage } from 'next';

// 2. 外部ライブラリ
import { Button } from '@/components/ui/Button';
import { Card } from '@/components/ui/Card';

// 3. 内部モジュール
import { useAuth } from '@/hooks/useAuth';
import { supabase } from '@/lib/supabase';

// 4. 型定義
import type { User } from '@/types/auth.types';

// 5. 相対インポート
import './ComponentName.css';
```

## 🎨 UI/UXデザインガイドライン

### 🚨 高級感重視のデザインシステム

#### デザイン原則
- **余白重視**: 臨場感のある豊富な余白（最低24px以上）
- **魅力的なボタン**: 思わず押したくなる高級感のあるボタン設計
- **カードデザイン禁止**: 自然で流れるようなレイアウト採用
- **チープな素材禁止**: 低品質な画像・アイコン・フォントは一切使用禁止
- **Lucideアイコン使用絶対禁止**: Heroicons、Phosphor Icons、カスタムSVGを使用

#### 🚨 チープなアイコンデザインの絶対禁止ルール

##### 絶対禁止：角丸四角形アイコン
- `w-12 h-12 bg-gradient-to-br rounded-xl` 等の角丸四角形アイコン
- 色付き背景（グラデーション・単色問わず）の四角形アイコン
- 中央に文字・記号を配置した四角形デザイン

##### 推奨代替案：エレガントなテキスト統合デザイン
```tsx
// ✅ 推奨：エレガントなテキスト統合デザイン
<div className="inline-flex items-baseline space-x-2 mb-3">
  <span className="text-3xl font-bold text-amber-600 group-hover:text-amber-700 transition-colors">2020</span>
  <span className="text-sm text-amber-500 font-medium">年</span>
</div>

// ✅ 推奨：SVGアイコンと文章の統合
<div className="flex items-center space-x-3 text-gray-700">
  <svg className="w-5 h-5 text-amber-600" fill="currentColor" viewBox="0 0 20 20">
    <path d="M..." />
  </svg>
  <span className="font-medium">厳選豆使用</span>
</div>
```

### 🏷️ HP専用セクション見出しルール

#### セクション見出しの英語化原則
**主要セクションの見出しは英語で表記し、高級感・国際的な印象を演出する**

#### 必須英語見出し一覧
```tsx
const SECTION_HEADINGS = {
  // カフェHP用
  about: "ABOUT US",           // ❌ 「私たちについて」
  menu: "OUR MENU",           // ❌ 「メニュー」
  location: "LOCATION",       // ❌ 「店舗情報」
  contact: "CONTACT",         // ❌ 「お問い合わせ」
  news: "NEWS",               // ❌ 「お知らせ」
  gallery: "GALLERY",         // ❌ 「ギャラリー」
  concept: "OUR CONCEPT",     // ❌ 「コンセプト」
  access: "ACCESS",           // ❌ 「アクセス」
  
  // ジムHP用
  programs: "PROGRAMS",       // ❌ 「プログラム」
  trainers: "TRAINERS",       // ❌ 「トレーナー」
  facilities: "FACILITIES",   // ❌ 「施設」
  membership: "MEMBERSHIP",   // ❌ 「料金プラン」
} as const;
```

#### 見出しデザインテンプレート
```tsx
// ✅ 推奨：高級感のあるセクション見出しテンプレート
export const SectionHeading: React.FC<{ title: string }> = ({ title }) => (
  <div className="text-center mb-16 animate-fade-in">
    <div className="relative inline-flex flex-col items-center mb-8">
      {/* 装飾ライン（上） */}
      <div className="w-16 h-px bg-gradient-to-r from-transparent via-amber-300 to-transparent mb-4"></div>
      
      {/* メイン見出し */}
      <div className="relative">
        <h3 className="text-sm font-semibold tracking-[0.2em] text-amber-800 uppercase mb-2 relative z-10">
          {title}
        </h3>
        {/* 背景装飾 */}
        <div className="absolute inset-0 bg-gradient-to-r from-transparent via-amber-50 to-transparent blur-sm opacity-70 scale-110"></div>
      </div>
      
      {/* 装飾ライン（下） */}
      <div className="w-24 h-px bg-gradient-to-r from-transparent via-amber-400 to-transparent mt-2"></div>
      
      {/* 装飾ドット */}
      <div className="flex space-x-1 mt-3">
        <div className="w-1 h-1 bg-amber-400 rounded-full"></div>
        <div className="w-1.5 h-1.5 bg-amber-500 rounded-full"></div>
        <div className="w-1 h-1 bg-amber-400 rounded-full"></div>
      </div>
    </div>
  </div>
);
```

### 🖼️ FV（ファーストビュー）設計ルール

#### FVの基本原則
- **画面領域の完全活用**: ビューポートの100vh（100%の高さ）を必ず使用
- **FV画像の絶対配置**: 関連する高品質画像を100vh全体に必ず配置
- **文字配置**: 見出し・テキストは中央ではなく中央より左側に配置（左寄せまたは左中央）
- **シンプル・ミニマル**: 不要な装飾要素・色付きアイコンは一切排除
- **インパクト重視**: 訪問者の注意を即座に引く強力なビジュアル
- **メッセージの明確性**: 3秒以内にサイトの目的が理解できる構成
- **高級感の演出**: プレミアムブランドにふさわしい洗練されたデザイン
- **アクション誘導**: 明確なCTAボタンの配置

#### 🚨 FV画像配置ルール（絶対遵守）

##### 100vh画像配置の絶対実装ルール
**FVには必ず高品質画像を100vh全体に配置すること**
- 画像なしのFVは絶対禁止
- 画像が100vh未満の配置は絶対禁止
- 背景グラデーションのみは絶対禁止

##### 完璧なFVテンプレート
```tsx
export const HeroSection: React.FC = () => {
  return (
    <section 
      id="home" 
      className="
        w-screen h-screen 
        min-h-screen
        relative overflow-hidden
        supports-[height:100dvh]:h-[100dvh]
        supports-[height:100dvh]:min-h-[100dvh]
      "
    >
      {/* 🚨 必須：背景画像を100vh全体に絶対配置 */}
      <div className="absolute inset-0 z-0">
        <picture>
          <source 
            media="(min-width: 1920px)" 
            srcSet="https://images.unsplash.com/photo-1554118811-1e0d58224f24?ixlib=rb-4.0.3&auto=format&fit=crop&w=2560&q=80" 
          />
          <img 
            src="https://images.unsplash.com/photo-1554118811-1e0d58224f24?ixlib=rb-4.0.3&auto=format&fit=crop&w=768&q=80"
            alt="プレミアム体験 - 心地よい空間"
            className="w-full h-full object-cover"
            loading="eager"
            decoding="async"
          />
        </picture>
        
        {/* オーバーレイ（読みやすさ確保） */}
        <div className="absolute inset-0 bg-gradient-to-b from-black/60 via-black/40 to-black/70" />
      </div>
      
      {/* 🚨 FV文字配置：中央より左側（左中央配置） */}
      <div className="relative z-10 w-full h-full">
        <div className="h-full flex items-center justify-start pl-8 md:pl-16 lg:pl-24">
          <div className="text-left max-w-4xl space-y-8">
            {/* メインタイトル */}
            <h1 className="text-5xl md:text-7xl lg:text-8xl font-serif font-bold text-white leading-tight animate-fade-in animation-delay-100">
              <span className="block mb-4">心地よい空間で</span>
              <span className="bg-gradient-to-r from-amber-300 via-orange-300 to-yellow-300 bg-clip-text text-transparent">
                特別なひととき
              </span>
            </h1>
            
            {/* サブタイトル */}
            <p className="text-xl md:text-2xl lg:text-3xl font-serif text-white/90 max-w-3xl animate-fade-in animation-delay-200 leading-relaxed">
              厳選された豆から淹れる最高の一杯と、<br />
              温かみのある空間があなたをお待ちしています
            </p>
            
            {/* CTAボタン */}
            <div className="flex flex-col sm:flex-row gap-6 animate-fade-in animation-delay-300">
              <Button variant="luxury" size="xl" className="min-w-[200px] bg-gradient-to-r from-amber-600 to-orange-600 hover:from-amber-700 hover:to-orange-700">
                メニューを見る
              </Button>
              <Button variant="outline" size="xl" className="min-w-[200px] text-white border-white hover:bg-white hover:text-gray-900">
                店舗情報
              </Button>
            </div>
          </div>
        </div>
      </div>
    </section>
  );
};
```

### 🚨 最低ページ数・セクション構成要件

#### HPの最低必須セクション構成
**全てのWebサイトで以下のセクションは必須実装**

##### 必須セクション（8セクション）
1. **Hero Section (FV)** - ファーストビュー：100vh画像付き
2. **About Section** - 会社・店舗紹介
3. **Services/Menu Section** - サービス・商品紹介
4. **Features Section** - 特徴・強み
5. **Gallery Section** - 実績・ギャラリー
6. **Contact Section** - お問い合わせ
7. **Access/Location Section** - アクセス・店舗情報  
8. **Footer Section** - フッター情報

##### 業界別追加推奨セクション

###### カフェ・レストラン
- **News Section** - 最新情報・イベント
- **Concept Section** - コンセプト・こだわり
- **Staff Section** - スタッフ紹介

###### ジム・フィットネス
- **Programs Section** - プログラム・コース
- **Trainers Section** - トレーナー紹介
- **Membership Section** - 料金プラン

###### コーポレート
- **News Section** - ニュース・プレスリリース
- **Recruit Section** - 採用情報
- **Company Section** - 会社概要

#### セクション実装チェックリスト
```
🚨 必須セクションの確認項目：
□ Hero Section（FV）が100vh画像付きで実装されているか
□ About Sectionが適切な内容で実装されているか
□ Services/Menu Sectionが商品・サービス情報と共に実装されているか
□ Features Sectionが3-6個の特徴を紹介しているか
□ Gallery Sectionが実績・写真を表示しているか
□ Contact Sectionがフォーム付きで実装されているか
□ Access/Location Sectionが地図・アクセス情報と共に実装されているか
□ Footer Sectionが必要な情報・リンクと共に実装されているか
□ 各セクション見出しが英語表記になっているか
□ フローティングアクションボタンが実装されているか
```

### 📱 フローティングバナー実装ルール

#### フローティングバナーの基本原則
**画面右端固定のアクションバナーで重要な行動誘導を効果的に実現**

#### 推奨アクション構成
```tsx
const FLOATING_ACTIONS = [
  {
    type: 'line',
    label: 'LINE公式',
    icon: 'MessageCircle',
    gradient: 'from-green-500 to-green-600',
    textColor: 'text-green-50',
    href: 'https://line.me/...'
  },
  {
    type: 'booking',
    label: '体験予約',
    icon: 'Calendar',
    gradient: 'from-red-500 to-red-600',
    textColor: 'text-red-50',
    href: '/booking'
  },
  {
    type: 'contact',
    label: 'お問い合わせ',
    icon: 'Mail',
    gradient: 'from-gray-500 to-gray-600',
    textColor: 'text-gray-50',
    href: '/contact'
  }
] as const;
```

#### フローティングバナー実装テンプレート
```tsx
export const FloatingActions: React.FC = () => {
  return (
    <div className="fixed bottom-4 right-0 z-50 space-y-1">
      {FLOATING_ACTIONS.map((action, index) => (
        <a
          key={action.type}
          href={action.href}
          className={`
            flex flex-col items-center py-3 px-4 
            bg-gradient-to-b ${action.gradient}
            ${action.textColor} 
            rounded-l-xl shadow-lg hover:shadow-xl
            transition-all duration-300 transform 
            hover:scale-105 hover:-translate-x-1
            min-w-[80px]
          `}
        >
          {/* アイコン（上） */}
          <div className="mb-1">
            <svg className="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
              {/* カスタムSVGアイコン */}
            </svg>
          </div>
          
          {/* テキスト（下） */}
          <span className="text-xs font-medium text-center leading-tight">
            {action.label}
          </span>
        </a>
      ))}
    </div>
  );
};
```

## 🎬 HP専用：Lenisスムーススクロールライブラリ（必須）

### 🚨 HP案件でのLenis必須導入ルール

#### 適用範囲の明確な区別
**Lenisスムーススクロールライブラリはホームページ（HP）案件でのみ必須導入**
- **HP案件（必須導入）**: カフェ、ジム、企業サイト、ポートフォリオサイト等
- **システム案件（導入禁止）**: 管理画面、ダッシュボード、CRUD操作システム等
- 案件タイプによる明確な使い分けでプレミアムな体験を提供

### Lenisライブラリ実装手順

#### 1. インストール
```bash
# HP案件では必須
npm install @studio-freight/lenis
# または
yarn add @studio-freight/lenis
```

#### 2. カスタムフック実装
```typescript
// src/hooks/useLenis.ts
'use client'

import { useEffect } from 'react'
import Lenis from '@studio-freight/lenis'

export const useLenis = () => {
  useEffect(() => {
    // Lenisインスタンスの作成
    const lenis = new Lenis({
      duration: 1.2,           // スクロール時間（1.2秒）
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)), // カスタムイージング
      direction: 'vertical',   // 縦スクロール
      gestureDirection: 'vertical',
      smooth: true,           // スムーススクロール有効
      smoothTouch: false,     // タッチデバイスでは無効（パフォーマンス重視）
      touchMultiplier: 2,     // タッチ感度
    })

    // フレーム更新関数
    function raf(time: number) {
      lenis.raf(time)
      requestAnimationFrame(raf)
    }

    requestAnimationFrame(raf)

    // グローバルアクセス用（ナビゲーション用）
    ;(window as any).lenis = lenis

    // クリーンアップ
    return () => {
      lenis.destroy()
      ;(window as any).lenis = null
    }
  }, [])
}
```

#### 3. レイアウトでの適用
```typescript
// src/app/layout.tsx
'use client'

import { useLenis } from '@/hooks/useLenis'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  // HP案件では必須のLenisスムーススクロール
  useLenis()

  return (
    <html lang="ja">
      <body className="antialiased">
        {children}
      </body>
    </html>
  )
}
```

#### 4. スムーススクロールナビゲーション
```typescript
// src/components/common/SmoothScrollNav.tsx
'use client'

import { useEffect, useState } from 'react'

export const SmoothScrollNav: React.FC = () => {
  const [lenis, setLenis] = useState<any>(null)

  useEffect(() => {
    // Lenisインスタンスの取得
    const lenisInstance = (window as any).lenis
    setLenis(lenisInstance)
  }, [])

  const scrollTo = (target: string) => {
    if (lenis) {
      lenis.scrollTo(target, {
        duration: 1.5,
        easing: (t: number) => Math.min(1, 1.001 - Math.pow(2, -10 * t))
      })
    }
  }

  return (
    <nav className="fixed top-0 left-0 w-full z-50 bg-white/90 backdrop-blur-md border-b border-gray-200">
      <div className="container mx-auto px-6 py-4">
        <div className="flex items-center justify-between">
          <div className="font-serif text-2xl font-bold text-gray-900">
            Premium Site
          </div>
          
          <ul className="hidden md:flex space-x-8">
            {[
              { href: '#about', label: 'ABOUT US' },
              { href: '#services', label: 'SERVICES' },
              { href: '#gallery', label: 'GALLERY' },
              { href: '#contact', label: 'CONTACT' }
            ].map((item) => (
              <li key={item.href}>
                <button 
                  onClick={() => scrollTo(item.href)}
                  className="font-semibold text-gray-700 hover:text-amber-600 transition-colors duration-300"
                >
                  {item.label}
                </button>
              </li>
            ))}
          </ul>
        </div>
      </div>
    </nav>
  )
}
```

#### 5. スムーススクロール対応セクション
```typescript
// src/components/layout/Section.tsx
interface SectionProps {
  id: string
  children: React.ReactNode
  className?: string
}

export const Section: React.FC<SectionProps> = ({ 
  id, 
  children, 
  className = '' 
}) => {
  return (
    <section 
      id={id}
      className={`min-h-screen py-20 ${className}`}
    >
      {children}
    </section>
  )
}

// 使用例
<Section id="about" className="bg-gray-50">
  <AboutContent />
</Section>
```

### Lenisライブラリ設定オプション

#### 推奨設定パラメータ
```typescript
const LENIS_CONFIG = {
  // 基本設定
  duration: 1.2,              // スクロール時間（秒）
  smooth: true,               // スムーススクロール有効
  direction: 'vertical',      // スクロール方向
  gestureDirection: 'vertical',
  
  // パフォーマンス設定
  smoothTouch: false,         // タッチデバイスでは無効化
  touchMultiplier: 2,         // タッチ感度
  infinite: false,            // 無限スクロール無効
  
  // イージング関数（プレミアム感のある滑らかな動き）
  easing: (t: number) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
  
  // その他
  autoResize: true,           // 自動リサイズ対応
  syncTouch: false,           // タッチとマウスの同期無効
} as const
```

### 実装チェックリスト

#### HP案件でのLenis必須確認項目
```
🚨 Lenisスムーススクロールの必須確認項目（HP案件専用）：
□ @studio-freight/lenis がインストールされているか
□ useLenis カスタムフックが src/hooks/useLenis.ts に作成されているか
□ layout.tsx で useLenis() が呼び出されているか
□ duration: 1.2, smooth: true の設定が実装されているか
□ smoothTouch: false でタッチデバイスのパフォーマンスが考慮されているか
□ カスタムイージング関数が設定されているか
□ ナビゲーションで lenis.scrollTo() が実装されているか
□ requestAnimationFrame(raf) でフレーム更新が動作しているか
□ lenis.destroy() でクリーンアップが実装されているか
□ window.lenis でグローバルアクセスが可能になっているか

🚨 案件タイプ別確認項目：
□ HP案件（カフェ、ジム、企業サイト等）→ Lenis必須導入
□ システム案件（管理画面、ダッシュボード等）→ Lenis導入禁止
□ 案件タイプに応じた適切な導入判断がされているか
□ プレミアムで滑らかなスクロール体験が提供されているか
□ パフォーマンスとUXのバランスが適切に取れているか
```

### トラブルシューティング

#### よくある問題と解決方法
```typescript
// 問題1: Lenisが初期化されない
// 解決: useEffectでの初期化確認
useEffect(() => {
  console.log('Lenis initialized:', window.lenis)
}, [])

// 問題2: スクロールが動作しない
// 解決: CSSの確認
// body { overflow-x: hidden; } が設定されているか確認

// 問題3: パフォーマンスの問題
// 解決: smoothTouch: false の設定確認
const lenis = new Lenis({
  smoothTouch: false, // 重要：タッチデバイスでは無効化
})

// 問題4: ナビゲーションが動作しない
// 解決: Lenisインスタンスの存在確認
const scrollTo = (target: string) => {
  const lenisInstance = (window as any).lenis
  if (lenisInstance) {
    lenisInstance.scrollTo(target)
  }
}
```

## 🔒 セキュリティガイドライン

### 認証・認可（Supabase Auth）
```typescript
// 環境変数管理（必須）
const requiredEnvVars = [
  'NEXT_PUBLIC_SUPABASE_URL',
  'NEXT_PUBLIC_SUPABASE_ANON_KEY',
  'SUPABASE_SERVICE_ROLE_KEY' // サーバーサイドのみ
] as const;

// 環境変数バリデーション
const validateEnvVars = () => {
  requiredEnvVars.forEach(envVar => {
    if (!process.env[envVar]) {
      throw new Error(`Missing required environment variable: ${envVar}`);
    }
  });
};
```

### データ保護・バリデーション
```typescript
// Zodを使用した厳密なバリデーション
import { z } from 'zod';

const UserSchema = z.object({
  email: z.string().email('無効なメールアドレスです'),
  password: z.string().min(8, 'パスワードは8文字以上である必要があります'),
  name: z.string().min(1, '名前は必須です').max(100, '名前は100文字以下である必要があります'),
});

// 電話番号（日本の形式）
phone: z.string().regex(/^0\d{1,4}-\d{1,4}-\d{4}$/, '正しい電話番号形式で入力してください'),

// 郵便番号（日本の形式）
zipCode: z.string().regex(/^\d{3}-\d{4}$/, '正しい郵便番号形式で入力してください（例: 123-4567）'),

// UUID
uuid: z.string().uuid('無効なID形式です'),
```

### Row Level Security (RLS) ポリシー例
```sql
-- ユーザーデータのRLSポリシー
CREATE POLICY "Users can view own profile" ON profiles 
FOR SELECT USING (auth.uid() = id);

CREATE POLICY "Users can update own profile" ON profiles 
FOR UPDATE USING (auth.uid() = id);

-- 組織データのRLSポリシー（権限ベース）
CREATE POLICY "Organization members can view data" ON organizations 
FOR ALL USING (
  EXISTS (
    SELECT 1 FROM user_roles ur 
    WHERE ur.user_id = auth.uid() 
    AND ur.organization_id = organizations.id
  )
);

-- 管理者のみ作成・更新可能
CREATE POLICY "Admin can manage users" ON profiles 
FOR ALL USING (
  EXISTS (
    SELECT 1 FROM user_roles ur 
    WHERE ur.user_id = auth.uid() 
    AND ur.role = 'admin'
  )
);
```

### ログ・監視
```typescript
// セキュリティログ実装
import { createClient } from '@supabase/supabase-js';

const logSecurityEvent = async (event: {
  type: 'login' | 'logout' | 'failed_login' | 'unauthorized_access';
  userId?: string;
  metadata?: Record<string, any>;
}) => {
  await supabase.from('security_logs').insert({
    event_type: event.type,
    user_id: event.userId,
    metadata: event.metadata,
    ip_address: getClientIP(),
    user_agent: getUserAgent(),
    timestamp: new Date().toISOString()
  });
};
```

## ⚡ パフォーマンス最適化

### 画像最適化（Vercel + Next.js）
```typescript
import Image from 'next/image';

// 最適化された画像コンポーネント
export const OptimizedImage: React.FC<{
  src: string;
  alt: string;
  width: number;
  height: number;
  priority?: boolean;
}> = ({ src, alt, width, height, priority = false }) => (
  <Image
    src={src}
    alt={alt}
    width={width}
    height={height}
    priority={priority}
    placeholder="blur"
    blurDataURL="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/..."
    sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
    style={{
      objectFit: 'cover',
      objectPosition: 'center'
    }}
  />
);
```

### データフェッチ最適化
```typescript
// React Query + Supabaseの組み合わせ
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

export const useUserProfile = (userId: string) => {
  return useQuery({
    queryKey: ['user', userId],
    queryFn: async () => {
      const { data, error } = await supabase
        .from('profiles')
        .select('*')
        .eq('id', userId)
        .single();
      
      if (error) throw error;
      return data;
    },
    staleTime: 5 * 60 * 1000, // 5分間キャッシュ
    cacheTime: 10 * 60 * 1000, // 10分間保持
  });
};
```

## 🧪 テスト戦略

### テスト構成（必須80%カバレッジ）
```typescript
// Vitest設定
export default defineConfig({
  test: {
    environment: 'jsdom',
    setupFiles: ['./src/test/setup.ts'],
    coverage: {
      threshold: {
        global: {
          branches: 80,
          functions: 80,
          lines: 80,
          statements: 80
        }
      }
    }
  }
});
```

### コンポーネントテスト例
```typescript
// src/components/ui/Button/Button.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from './Button';

describe('Button', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: 'Click me' })).toBeInTheDocument();
  });

  it('calls onClick when clicked', async () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    await userEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('applies correct variant styles', () => {
    render(<Button variant="primary">Primary</Button>);
    expect(screen.getByRole('button')).toHaveClass('bg-primary');
  });
});
```

## 🚀 CI/CDパイプライン（Vercel自動デプロイ）

### GitHub Actions設定
```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm run test:coverage
      - run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - run: vercel --prod --token ${{ secrets.VERCEL_TOKEN }}
```

## 📊 データベース設計原則（PostgreSQL + Supabase）

### 基本テーブル設計
```sql
-- ユーザーテーブル（Supabase Auth連携）
CREATE TABLE profiles (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  email TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  avatar_url TEXT,
  created_by UUID REFERENCES users(id),
  updated_by UUID REFERENCES users(id),
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  deleted_at TIMESTAMPTZ,
  deleted_by UUID REFERENCES users(id),
  
  CONSTRAINT profiles_email_check CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$')
);

-- 組織テーブル
CREATE TABLE organizations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  description TEXT,
  logo_url TEXT,
  website_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  
  CONSTRAINT organizations_slug_format CHECK (slug ~* '^[a-z0-9-]+$')
);

-- ユーザー・組織中間テーブル
CREATE TABLE user_organizations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  role TEXT NOT NULL DEFAULT 'member',
  status TEXT NOT NULL DEFAULT 'pending',
  invited_by UUID REFERENCES users(id),
  invited_at TIMESTAMPTZ DEFAULT NOW(),
  joined_at TIMESTAMPTZ,
  left_at TIMESTAMPTZ,
  approved_by UUID REFERENCES users(id),
  approved_at TIMESTAMPTZ,
  
  UNIQUE(user_id, organization_id),
  CONSTRAINT user_organizations_role_check CHECK (role IN ('owner', 'admin', 'member')),
  CONSTRAINT user_organizations_status_check CHECK (status IN ('pending', 'active', 'inactive'))
);
```

### インデックス戦略
```sql
-- パフォーマンス最適化インデックス
CREATE INDEX idx_profiles_user_id ON profiles(user_id);
CREATE INDEX idx_profiles_email ON profiles(email);
CREATE INDEX idx_user_organizations_user_id ON user_organizations(user_id);
CREATE INDEX idx_user_organizations_organization_id ON user_organizations(organization_id);
CREATE INDEX idx_user_organizations_status ON user_organizations(status);

-- 複合インデックス
CREATE INDEX idx_user_organizations_user_status ON user_organizations(user_id, status);
CREATE INDEX idx_profiles_created_at_desc ON profiles(created_at DESC);
```

## 📚 ドキュメント要件

### 必須ドキュメント
- README.md：プロジェクト概要・セットアップ手順
- API.md：API仕様・エンドポイント
- DEPLOYMENT.md：デプロイメント手順
- SECURITY.md：セキュリティ仕様
- 技術仕様書：アーキテクチャ・データベース設計
- システム構成図
- ER図：データベース関係図
- ユーザーマニュアル：操作手順

### コードコメント規則
```typescript
/**
 * ユーザープロファイルを取得する
 * @param userId - ユーザーID
 * @returns ユーザープロファイル情報
 * @throws {Error} ユーザーが見つからない場合
 */
export const getUserProfile = async (userId: string): Promise<UserProfile> => {
  // バリデーション
  if (!userId) {
    throw new Error('ユーザーIDが必要です');
  }

  // Supabaseからデータ取得
  const { data, error } = await supabase
    .from('profiles')
    .select('*')
    .eq('id', userId)
    .single();

  if (error) {
    throw new Error(`ユーザープロファイル取得エラー: ${error.message}`);
  }

  return data;
};
```

## ⚠️ 注意事項・ベストプラクティス

### 禁止事項（絶対厳守）
- **Lucide React**: `lucide-react` - 一切使用禁止
- **Heroicons**: `@heroicons/react` - インポートエラーが発生
- **React Icons**: `react-icons` - 依存関係の問題
- **その他禁止ライブラリ**: Material-UI Icons、Ant Design Icons等

### 推奨代替案
- **Heroicons**: `@heroicons/react` の代わりに直接SVGインポート
- **Phosphor Icons**: `phosphor-icons` パッケージ使用
- **カスタムSVG**: 独自アイコンの実装推奨

### エラー対策
```bash
# 禁止ライブラリのアンインストール
npm uninstall lucide-react @heroicons/react react-icons

# 推奨ライブラリのインストール
npm install @heroicons/react phosphor-icons
```

### パフォーマンス監視
- **Core Web Vitals**: LCP < 2.5s, FID < 100ms, CLS < 0.1
- **Lighthouse Score**: 90点以上維持
- **Bundle Size**: 初期読み込み < 1MB
- **画像最適化**: WebP/AVIF形式使用

### SEO対策
```typescript
// Next.js App Router対応メタデータ
export const metadata: Metadata = {
  title: 'サイトタイトル',
  description: 'サイト説明（120文字以内）',
  keywords: 'キーワード1, キーワード2, キーワード3',
  openGraph: {
    title: 'OGタイトル',
    description: 'OG説明文',
    images: ['/og-image.jpg'],
    type: 'website',
  },
  twitter: {
    card: 'summary_large_image',
    title: 'Twitterタイトル',
    description: 'Twitter説明文',
    images: ['/twitter-image.jpg'],
  },
};
```

### アクセシビリティ要件
- **WCAG 2.1 AA準拠**: コントラスト比4.5:1以上
- **キーボードナビゲーション**: Tab順序の最適化
- **スクリーンリーダー対応**: aria-label、alt属性の適切な設定
- **フォーカス表示**: 明確なフォーカスインジケーター

### ブラウザサポート
- **モダンブラウザ**: Chrome、Firefox、Safari、Edge最新版
- **モバイル**: iOS Safari、Chrome Mobile
- **IE対応**: 不要（2024年基準）

---

## 🎯 まとめ

このドキュメントは日本市場に特化したClaude Code エージェント開発のための包括的なガイドラインです。高品質で安全、かつ保守性の高いアプリケーション開発を実現するため、すべての項目を遵守してください。

**開発開始前の必須確認事項**:
1. `.cursor/rules/` 全ファイルの内容理解
2. 禁止ライブラリの確認
3. セキュリティ要件の把握
4. パフォーマンス目標の設定
5. テスト戦略の理解 
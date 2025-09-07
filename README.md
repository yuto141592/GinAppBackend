serviceURL: https://gin-app.vercel.app

frontend: https://github.com/yuto141592/GinApp

Ginの勉強のために開発しました。

OAuth実装の試み→https://github.com/yuto141592/GinAppBackendOAuth

# Firebase + React + Gin (Go) Demo App

このアプリは **Firebase Authentication** を利用したログイン機能と、  
**Go (Gin) バックエンド API** を組み合わせたフルスタック Web アプリです。  

## 🚀 技術構成

### フロントエンド
- **React (TypeScript)** + **React Router**
- **Firebase Authentication**
  - メールアドレス & パスワードでサインアップ
  - メール認証リンクを踏むとアカウント有効化
  - ログイン時に Firebase から `ID トークン (JWT)` を取得
- 認証済みユーザーのみ `/home` にアクセス可能 (`PrivateRoute` 実装)
- 環境変数は `.env` で管理し、Vercel の環境変数機能を利用して本番環境へ注入
- ログインページにMaterial UIを適用

### バックエンド
- **Go + Gin**
  - Firebase Admin SDK を使用して **ID トークンを検証**
  - フロントエンドから送信された `Authorization: Bearer <token>` を検証
  - 有効なら API 実行、不正なら `401 Unauthorized`
- **Firestore**
  - ユーザーが任意の文字列を登録・編集・削除・閲覧可能
  - 認証済みユーザーに紐づくデータの CRUD API を Gin で実装
- **CORS 設定**
  - 開発時は `*`（全許可）
  - 本番では `https://gin-app.vercel.app` のみ許可
- **Google Cloud Run** にデプロイ
  - Docker コンテナ化
  - Secret Manager / 環境変数で Firebase サービスアカウントを安全に管理

## 🌐 デプロイ構成
- **フロントエンド**: GitHub → Vercel で自動デプロイ  
- **バックエンド**: DockerイメージをArtifact RegistryにプッシュしてCloud Runにデプロイ 

## 🔑 ログインフロー

```mermaid
sequenceDiagram
    participant User
    participant React App
    participant Firebase Auth
    participant Gin Backend

    User->>React App: メール ＆ パスワードでログイン
    React App->>Firebase Auth: signInWithEmailAndPassword()
    Firebase Auth-->>React App: IDトークン(JWT)を返す
    React App->>Gin Backend: Authorization: Bearer <JWT>
    Gin Backend->>Firebase Auth: トークン検証
    Firebase Auth-->>Gin Backend: OK
    Gin Backend-->>React App: 保護されたデータを返す

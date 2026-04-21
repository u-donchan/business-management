# 顧客管理アプリ 運用ガイド

## アプリ構成

```
URL: https://business-management-gold.vercel.app
データベース: Supabase (ootpmvitsggulcqvufsd) ← 開発・生産管理と共通
GitHub: https://github.com/u-donchan/business-management
```

## 権限の種類

| ロール | できること |
|---|---|
| **管理者（admin）** | 議事録・決定事項・**見積り** 全て |
| **一般ユーザー** | 議事録・決定事項のみ（見積りタブ非表示） |

---

## 管理者権限の付け方・外し方

### ① 新しい管理者を追加

1. https://supabase.com/dashboard にアクセス
2. `ootpmvitsggulcqvufsd` プロジェクトを開く
3. 左メニュー「**SQL Editor**」→「New query」
4. 以下を実行（メールアドレスを変更）：

```sql
UPDATE auth.users 
SET raw_user_meta_data = raw_user_meta_data || '{"role":"admin"}'::jsonb
WHERE email = '新しい管理者のメール@example.com';
```

5. 対象ユーザーが顧客管理アプリに**再ログイン**する
6. ヘッダーに 👑 マークが表示されれば成功

### ② 管理者権限を外す

```sql
UPDATE auth.users 
SET raw_user_meta_data = raw_user_meta_data - 'role'
WHERE email = 'メール@example.com';
```

### ③ 現在の管理者一覧を確認

```sql
SELECT email, raw_user_meta_data->>'role' AS role 
FROM auth.users 
WHERE raw_user_meta_data->>'role' = 'admin';
```

---

## 新しいユーザーを追加する

### 方法1: 本人に新規登録してもらう
アプリにアクセスしてもらい「新規アカウント作成」でメール+パスワード登録
→ 確認メールのリンクをクリック

### 方法2: 管理画面から追加
1. Supabaseダッシュボード →「Authentication」→「Users」
2. 右上「Add user」ボタン
3. メール+パスワードを入力
4. 本人にパスワードを伝える

---

## ユーザーを削除する

1. Supabaseダッシュボード →「Authentication」→「Users」
2. 対象メールをクリック
3. 右パネル下部で「Delete user」

---

## トラブルシューティング

### Q. 管理者にしたのに👑が出ない
A. **再ログイン**してください。ログイン情報を一度キャッシュするので、更新にはログアウト→ログインが必要です。

### Q. 見積りタブに金額を入れても反映されない
A. ブラウザをリロードしてください。保存は自動で走っていますが、表示更新が遅れることがあります。

### Q. 品番が表示されない
A. 開発・生産管理アプリで品番を登録してください。同じ年度・同じ会社の品番が自動で表示されます。

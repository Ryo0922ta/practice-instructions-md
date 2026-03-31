# 機能要件

## 1. SQL生成機能
ユーザーがUIで指定した条件に基づき、SQL文を生成する。

### 入力（フロント → API）
- table: テーブル名
- operation: SQL種別（SELECT / INSERT / UPDATE / DELETE）
- columns: カラム配列
- where: 条件配列（column / operator / value）
- groupBy: カラム配列（任意）
- orderBy: 配列（column / direction）（任意）

### 出力（API → フロント）
- sql: 生成されたSQL文字列

---

## 2. スキーマ管理機能
- テーブル定義（テーブル名・カラム名・型）を保持する
- フロントでプルダウン表示に使用する
- 将来的にDBから自動取得可能な設計にする

---

## 3. UI機能

### 入力UI
- テーブル選択（プルダウン）
- 操作種別選択（SELECT / INSERT / UPDATE / DELETE）
- カラム選択（複数選択可能）
- WHERE条件入力（複数行）
  - カラム選択（プルダウン）
  - 演算子選択（=, >, <, >=, <= など）
  - 値入力
- GROUP BY選択（任意）
- ORDER BY選択（任意）

### 出力UI
- SQL表示エリア（textarea）
- 「SQL生成」ボタン
- 「クリップボードコピー」ボタン

---

# 非機能要件

## パフォーマンス
- SQL生成は即時（100ms以内目安）

## 可用性
- シンプル構成（単一APIサーバ）

## 保守性
- SQL生成ロジックはService層に集約

---

# API設計

## 1. SQL生成API
POST /api/sql/generate

### Request
```json
{
  "table": "string",
  "operation": "SELECT",
  "columns": ["string"],
  "where": [
    {
      "column": "string",
      "operator": "=",
      "value": "string"
    }
  ],
  "groupBy": ["string"],
  "orderBy": [
    {
      "column": "string",
      "direction": "ASC"
    }
  ]
}
```

### Response
```json
{
  "sql": "string"
}
```

## 2. スキーマ取得API

GET /api/schema

### Response
```json
{
  "tables": [
    {
      "name": "orders",
      "columns": [
        { "name": "user_id", "type": "int" },
        { "name": "amount", "type": "numeric" }
      ]
    }
  ]
}
```

---
---

# SQL生成仕様

## 基本ルール
- PostgreSQL構文を使用
- SQL文のみを返す（説明・コメント禁止）
- SELECT * は禁止
- 不要なサブクエリ禁止
- JOIN は使用しない（リレーション指定がないため）

## 型ルール
- 文字列: `'value'`
- 数値: クォートなし
- 日付: `'YYYY-MM-DD'`

## WHERE句
- 条件は AND で結合
- 空の場合は出力しない

## 操作別仕様

### SELECT
- 指定カラムのみ SELECT
- GROUP BY と整合性を取る
- ORDER BY 適用

### INSERT
- columns と values を対応させる

### UPDATE
- columns を SET 対象として扱う
- WHERE 適用

### DELETE
- WHERE 適用

---

# バックエンド構成

## Controller
- リクエスト受信・レスポンス返却

## Service
- SQL生成ロジック（コア）

## Repository（任意）
- スキーマ取得

---

# フロント構成

## 画面
- 単一ページ構成

## 通信
- fetch API を使用

## 状態管理
- JavaScript で管理（フレームワーク不使用）

---

# 開発ステップ

1. SQL生成ロジックを Service で実装
2. API化（Spring Boot）
3. スキーマ取得API実装
4. フロント UI 作成
5. API 連携
6. クリップボード機能追加

---

# 制約
- 自然言語入力は禁止
- UI 入力のみを信頼する
- 不正なカラムは使用しない

---

# 完成条件
- UI操作のみで SQL が生成できる
- ボタン押下で SQL がコピーできる
- 誤ったSQL が生成されない
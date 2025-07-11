# インタフェース仕様書

## 概要
本仕様書は、映画館チケットカウンター業務を自動化するMCPサーバーのAPIインタフェース仕様を定義します。各APIはAzure Functions上で動作し、MCPクライアントツールから呼び出されることを前提とします。

## 共通事項
- APIはHTTPトリガー（POST/GET）で提供。
- リクエスト/レスポンスはJSON形式。
- 各エンドポイントはPython Azure Functions v2プログラミングモデルで実装。

---

## 1. 映画情報取得
### エンドポイント
`GET /movies`

### リクエスト
なし

### レスポンス
```json
{
  "movies": [
    {
      "id": "string",
      "title": "string",
      "description": "string",
      "duration": "integer",
      "genre": "string"
    }
  ]
}
```

---

## 2. 上映スケジュール取得
### エンドポイント
`GET /schedules?movie_id={movie_id}`

### リクエスト
- `movie_id`: 映画ID（必須）

### レスポンス
```json
{
  "schedules": [
    {
      "schedule_id": "string",
      "movie_id": "string",
      "start_time": "string (ISO8601)",
      "screen": "string"
    }
  ]
}
```

---

## 3. 座席情報取得
### エンドポイント
`GET /seats?schedule_id={schedule_id}`

### リクエスト
- `schedule_id`: スケジュールID（必須）

### レスポンス
```json
{
  "seats": [
    {
      "seat_id": "string",
      "row": "string",
      "number": "string",
      "is_reserved": "boolean"
    }
  ]
}
```

---

## 4. 予約登録
### エンドポイント
`POST /reserve`

### リクエスト
```json
{
  "user_id": "string",
  "schedule_id": "string",
  "seats": ["seat_id1", "seat_id2", ...]
}
```

### レスポンス
```json
{
  "reservation_id": "string",
  "status": "confirmed | failed",
  "message": "string"
}
```

---

## 5. 予約確認
### エンドポイント
`GET /reservation?reservation_id={reservation_id}`

### リクエスト
- `reservation_id`: 予約ID（必須）

### レスポンス
```json
{
  "reservation": {
    "reservation_id": "string",
    "user_id": "string",
    "schedule_id": "string",
    "seats": ["seat_id1", "seat_id2", ...],
    "status": "confirmed | failed"
  }
}
```

---

## 6. ユーザー入力補正（曖昧入力対応）
### エンドポイント
`POST /fuzzy-input`

### リクエスト
```json
{
  "input": "string"
}
```

### レスポンス
```json
{
  "corrected_input": "string",
  "candidates": ["string", ...]
}
```

---

## 7. レコメンド（おすすめ映画・座席）
### エンドポイント
`GET /recommend?user_id={user_id}`

### リクエスト
- `user_id`: ユーザーID（必須）

### レスポンス
```json
{
  "recommendations": [
    {
      "type": "movie | seat",
      "id": "string",
      "title": "string (type=movie)",
      "row": "string (type=seat)",
      "number": "string (type=seat)"
    }
  ]
}
```

---

## 8. ユーザー対話履歴取得
### エンドポイント
`GET /user-interaction?user_id={user_id}`

### リクエスト
- `user_id`: ユーザーID（必須）

### レスポンス
```json
{
  "interactions": [
    {
      "timestamp": "string (ISO8601)",
      "action": "string",
      "details": "object"
    }
  ]
}
```

---

## エラー応答（共通）
```json
{
  "error": {
    "code": "string",
    "message": "string"
  }
}
```

---

## 備考
- 各APIはAzure FunctionsのHTTPトリガーで実装。
- 認証・認可は将来拡張可能。
- MCPクライアントツールからの呼び出しを前提。

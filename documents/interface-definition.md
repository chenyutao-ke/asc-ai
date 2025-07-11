# インタフェース仕様書

## 概要
本仕様書は、映画館チケットカウンター業務を自動化するMCPサーバーのAPIインタフェース仕様を定義します。各APIはAzure Functions上で動作し、MCPクライアントツールから呼び出されることを前提とします。

## 共通事項
- APIはHTTPトリガー（POST/GET）で提供。
- リクエスト/レスポンスはJSON形式。
- 各エンドポイントはPython Azure Functions v2プログラミングモデルで実装。

---

## 1. 映画情報取得
### Endpoint
`GET /movies`

### Request
None

### Response
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
### Endpoint
`GET /schedules?movie_id={movie_id}`

### Request
- `movie_id`: Movie ID (required)

### Response
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
### Endpoint
`GET /seats?schedule_id={schedule_id}`

### Request
- `schedule_id`: Schedule ID (required)

### Response
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
### Endpoint
`POST /reserve`

### Request
```json
{
  "user_id": "string",
  "schedule_id": "string",
  "seats": ["seat_id1", "seat_id2", ...]
}
```

### Response
```json
{
  "reservation_id": "string",
  "status": "confirmed | failed",
  "message": "string"
}
```

---

## 5. 予約確認
### Endpoint
`GET /reservation?reservation_id={reservation_id}`

### Request
- `reservation_id`: Reservation ID (required)

### Response
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
### Endpoint
`POST /fuzzy-input`

### Request
```json
{
  "input": "string"
}
```

### Response
```json
{
  "corrected_input": "string",
  "candidates": ["string", ...]
}
```

---

## 7. レコメンド（おすすめ映画・座席）
### Endpoint
`GET /recommend?user_id={user_id}`

### Request
- `user_id`: User ID (required)

### Response
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
### Endpoint
`GET /user-interaction?user_id={user_id}`

### Request
- `user_id`: User ID (required)

### Response
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
* All APIs are implemented as HTTP triggers in Azure Functions.
* Authentication and authorization are extensible in the future.
* Designed to be called from MCP client tools.

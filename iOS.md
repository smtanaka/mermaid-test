# mermaid-test
```mermaid
sequenceDiagram
    participant Module
    participant SecureEnclave
    participant AppAttest
    participant CCA_Server
    participant Apple_Server
    participant App
    participant User

    Note over Module: ＜初回ログイン時＞

    Module->>SecureEnclave: 鍵ペア作成要求 & 公開鍵要求
    SecureEnclave-->>Module: 公開鍵返却
    alt アプリから要求あり
        Module->>CCA_Server: attestationObject用challenge要求
        CCA_Server-->>Module: challenge返却
        Module->>AppAttest: attestationObject要求
        AppAttest-->>Module: attestationObject返却
    end

    Module->>CCA_Server: Client Attestation JWT発行API呼び出し (attestationObject, 公開鍵)
    alt attestationObjectあり
        CCA_Server->>Apple_Server: 署名検証 & 公開鍵取得
    end
    CCA_Server-->>Module: Client Attestation JWT返却 (検証結果 + 公開鍵)

    Module->>Module: Client Attestation PoP JWT作成
    Module->>CCA_Server: PARエンドポイント呼び出し (JWTヘッダ付与)
    CCA_Server->>CCA_Server: Client Attestation JWT検証 & Client Attestation PoP JWT検証
    CCA_Server-->>Module: リクエストURI返却
    Module->>CCA_Server: 認可エンドポイント呼び出し (URI付与)
    User->>CCA_Server: ユーザ認証
    CCA_Server-->>Module: 認可コード返却
    Module->>Module: Client Attestation PoP JWT作成
    Module->>CCA_Server: トークンエンドポイント呼び出し (JWTヘッダ付与)
    CCA_Server->>CCA_Server: Client Attestation JWT検証 & Client Attestation PoP JWT検証
    CCA_Server-->>Module: 各種トークン返却
    Module->>App: リフレッシュトークン以外を返却

    Note over Module: ＜トークンリフレッシュ時＞
    Module->>Module: Client Attestation PoP JWT作成
    Module->>CCA_Server: トークンエンドポイント呼び出し (JWTヘッダ付与)
    CCA_Server->>CCA_Server: Client Attestation JWT検証 & Client Attestation PoP JWT検証
    CCA_Server-->>Module: 各種トークン返却
    Module->>App: リフレッシュトークン以外を返却

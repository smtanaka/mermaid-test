# mermaid-test
```mermaid
sequenceDiagram
    participant Module
    participant KeyStore
    participant CCA_Server
    participant Google_Server
    participant PlayIntegrityAPI
    participant User

    Note over Module: ＜初回ログイン時＞

    Module->>KeyStore: 鍵ペア作成要求 & 公開鍵要求
    KeyStore-->>Module: 公開鍵返却
    Module->>CCA_Server: KeyAttestation用challenge要求
    CCA_Server-->>Module: challenge返却
    Module->>KeyStore: KeyAttestation要求
    KeyStore-->>Module: KeyAttestation返却
    Module->>PlayIntegrityAPI: Play Integrityトークン要求
    PlayIntegrityAPI->>Module: Play Integrityトークン返却

    Module->>CCA_Server: Client Attestation JWT発行API呼び出し (KeyAttestation, PlayIntegrityトークン, 公開鍵)
    alt KeyAttestationあり
        CCA_Server->>Google_Server: 署名検証 & 公開鍵取得
    end
    alt PlayIntegrityトークンあり
        CCA_Server->>Google_Server: トークン検証
    end
    CCA_Server-->>Module: Client Attestation JWT返却

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

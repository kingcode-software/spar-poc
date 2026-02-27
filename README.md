 ╔══════════════════════════════════════════════════════════════════════════╗
  ║          SPAR SAP CDC — OIDC PROXY PAGE & PASSKEY POC                  	║
  ╠═════════════════════════════════════════════════════════════════════════╣
  ║                                                                         ║
  ║  API Key   : 4_q7bWSrrfARGv_vP1knVRIg                                  	║
  ║  Data DC   : EU1  (cdns.eu1.gigya.com / fidm.eu1.gigya.com)            	║
  ║  Client ID : U_XwKfuhT-wL5cbVQYZ47En0                                  	║
  ║  This URL  : https://kingcode-software.github.io/spar-poc/              ║
  ║                                                                         ║
  ╠═════════════════════════════════════════════════════════════════════════╣
  ║  PURPOSE 1 — Normal POC Test Page                                       ║
  ║    Register a FIDO2 passkey, do standard login, check session,          ║
  ║    dump account payload to browser console.                             ║
  ║                                                                         ║
  ║  PURPOSE 2 — OIDC Authorization Code Flow Proxy Page                    ║
  ║                                                                         ║
  ║  SAP CDC cannot redirect the user directly to the Relying Party (RP)    ║
  ║  after authentication. Instead it redirects to THIS page with a signed  ║
  ║  context JWT in the URL (?context=...). This page must then:            ║
  ║                                                                         ║
  ║    1. Detect ?context= in the URL                                       ║
  ║    2. Call gigya.oidc.op.getContextData() to decode the context JWT     ║
  ║       → reveals: client_id, redirect_uri, issuer, expiration            ║
  ║    3. Call gigya.accounts.getAccountInfo() to get the active session    ║
  ║       → reveals: UID, UIDSignature, signatureTimestamp                  ║
  ║       → if no session: show login screen then reload to retry           ║
  ║    4. Build a CDC /authorize URL with UID + session proof parameters    ║
  ║    5. Redirect the browser to that /authorize URL                       ║
  ║       → CDC validates the session, issues an authorization code         ║
  ║       → browser redirected to redirect_uri?code=XXXX                    ║
  ║       → Postman intercepts, exchanges code for access_token (JWT)       ║
  ║                                                                         ║
  ║  OIDC FLOW DIAGRAM                                                      ║
  ║  ────────────────────────────────────────────────────────────────────── ║
  ║                                                                         ║
  ║   Postman ──/authorize──► CDC fidm                                      ║
  ║                               │                                         ║
  ║               302 to this page + ?context=JWT                           ║
  ║                               │                                         ║
  ║   onGigyaServiceReady detects ?context                                  ║
  ║   gigya.oidc.op.getContextData()  ← decodes context JWT                 ║
  ║   gigya.accounts.getAccountInfo() ← gets UID + session signature        ║
  ║                               │                                         ║
  ║   browser ──/authorize+UID──► CDC fidm                                  ║
  ║                               │                                         ║
  ║               302 to redirect_uri?code=AUTH_CODE                        ║
  ║                               │                                         ║
  ║   Postman ──/token+code──► CDC fidm                                     ║
  ║                               │                                         ║
  ║               access_token JWT (with scoped claims) ✅                  ║
  ║                                                                          ║
  ║  REQUIRED ADMIN CONSOLE SETTINGS (SAP CDC → OIDC OP)                     ║
  ║    Login / Proxy Page URL   : https://kingcode-software.github.io/spar-poc/ ║
  ║    RP Redirect URI          : https://oauth.pstmn.io/v1/callback         ║
  ║    RP Proxy Page Override   : https://kingcode-software.github.io/spar-poc/ ║
  ║                                                                          ║
  ║  IMPORTANT NOTE ON gigya.oidc.js                                         ║
  ║    gigya.oidc.js is a DATA HELPER only. It exposes:                      ║
  ║      gigya.oidc.op.getContextData()   — decodes the context JWT          ║
  ║      gigya.oidc.op.getGroupsContext() — B2B groups context               ║
  ║    It does NOT auto-complete the OIDC flow or issue any redirect.        ║
  ║    All redirects are driven manually in onGigyaServiceReady below.       ║
  ║                                                                          ║
  ╚══════════════════════════════════════════════════════════════════════════╝

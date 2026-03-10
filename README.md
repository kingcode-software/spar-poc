SPAR SAP Customer Data Cloud CIAM POC 
Authorization Code + PKCE + Confidential Backend Client
                                                              
  Step 1  User logs in via CDC Screen-Set (gigya.js)            
 
  Step 2  SPA generates PKCE (code_verifier + challenge + state)
  
  Step 3  [CDC-SPECIFIC] Fetch session proof — UID + UIDSignature 
          Needed because cdns.eu1 ≠ fidm.eu1 (different domains) 
          -> WSO2 future state: this step is removed entirely    
  
  Step 4  Browser redirects to CDC /authorize (PKCE + proof)    
  
  Step 5  CDC returns ?code= to redirect_uri                    
  
  Step 6  SPA validates state, POSTs code + verifier to backend 
 
  Step 7  Backend exchanges code at CDC /token (secret server-side)
 
  Step 8  Backend validates JWT, sets HttpOnly session cookie   
 
  Step 9  SPA calls /oidc/openid and /oidc/basic_identity         

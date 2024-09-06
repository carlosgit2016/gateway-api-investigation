# Gateways

## HTTP routing

```bash
curl -SsL reflection.staging.telematicsplatform.tools/* # -> should be routed to user svc
curl -SsL -H "Purpose: inventory" reflection.staging.telematicsplatform.tools/* # -> should be routed to inventory svc
curl -SsL reflection.staging.telematicsplatform.tools/warehouse # -> should be routed to wareshouse svc
```

## HTTP redirects and rewrites
```bash
curl -SsL reflection.staging.telematicsplatform.tools:80 # ->  should be redirected to 443
curl -SsL reflection.staging.telematicsplatform.tools/path/to/rewrite # ->  should be rewrited to /warehouse
curl -SsL reflection.staging.telematicsplatform.tools/add-a-request-header # ->  should be rewrited to / with the header "Origin: mc-gateway"
curl -SsL -H "Source: bla" reflection.staging.telematicsplatform.tools/modify-a-header # ->  should be rewrited to / with the header "Source: mc-gateway"
curl -SsL -H "Source: bla" reflection.staging.telematicsplatform.tools/delete-a-header # ->  should be rewrited to / and have the header deleted 
```

## Traffic splitting
```bash
curl -SsL -H "Canary: true" reflection.staging.telematicsplatform.tools # Should match and redirect to service user-v1 with weight 10 and user-v2 with weight 90
curl -SsL -H "blue-green: true" reflection.staging.telematicsplatform.tools # Should match and redirect to service user-v1 that has the label blue with weight 1 and user-v2 with weight 0
```

## Mirroring
```bash
curl -SsL -H "Mirroring: true" reflection.staging.telematicsplatform.tools # Should match and redirect to service user-v1 and mirror to user-v2
```

## JWT basic validation
```bash
wget --no-verbose https://raw.githubusercontent.com/istio/istio/release-1.23/security/tools/jwt/samples/gen-jwt.py # Download the script that generated new JWTs
wget --no-verbose https://raw.githubusercontent.com/istio/istio/release-1.23/security/tools/jwt/samples/key.pem # Download the istio private key example

TOKEN=$(python3 ./gen-jwt.py ./key.pem --expire 30) # Generates a token that will expire in 30 seconds
curl -SsL -H "Authorization: Bearer $TOKEN" reflection.staging.telematicsplatform.tools | jq '.'
```
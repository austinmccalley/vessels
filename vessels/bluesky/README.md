Based on https://github.com/bluesky-social/pds/blob/main/pdsadmin/account.sh

After installing, run the following to create an account. Check the `_secret.yaml` for prefilled out versions:

```bash
# Create invite code
curl --silent --show-error --request POST --header "Content-Type: application/json" \
    --user "admin:${PDS_ADMIN_PASSWORD}" \
    --data '{"useCount": 1}' \
    "https://super.fish/xrpc/com.atproto.server.createInviteCode" | jq --raw-output '.code'

# Create account
curl --silent --show-error --request POST --header "Content-Type: application/json" \
    --data "{\"email\":\"${EMAIL}\", \"handle\":\"${HANDLE}.super.fish\", \"password\":\"${PASSWORD}\", \"inviteCode\":\"${INVITE_CODE}\"}" \
    "https://super.fish/xrpc/com.atproto.server.createAccount"

# Delete account (get the DID from https://username.super.fish/.well-known/atproto-did)
PAYLOAD="$(cat <<EOF
    {
      "subject": {
        "\$type": "com.atproto.admin.defs#repoRef",
        "did": "${DID}"
      },
      "takedown": {
        "applied": true,
        "ref": "$(date +%s)"
      }
    }
EOF
)"
curl --silent --show-error --request POST --header "Content-Type: application/json" \
    --user "admin:${PDS_ADMIN_PASSWORD}" \
    --data "${PAYLOAD}" \
    "https://super.fish/xrpc/com.atproto.admin.updateSubjectStatus"

# Reset password

```

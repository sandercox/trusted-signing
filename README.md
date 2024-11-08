# Trusted Signing Helper Actions

This sets up an Azure trusted signing sign tool script

## Settings

| Name               | Description                                                                                                                                                                                                                                                                                                                                                                                                                | Required | Default                              |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------------------------------------ |
| cache-dependencies | A boolean value (true/false) that indicates if the dependencies for this action should be cached by GitHub or not. The default value is true. When using self-hosted runners, caches from workflow runs are stored on GitHub-owned cloud storage. A customer-owned storage solution is only available with GitHub Enterprise Server. When enabled, this option can reduce the duration of the action by at least 1 minute. | false    | 'true'                               |
| endpoint           | The endpoint to use for signing.                                                                                                                                                                                                                                                                                                                                                                                           | false    | 'https://weu.code-signing.azure.com' |
| account_name       | The name of the account to use for signing.                                                                                                                                                                                                                                                                                                                                                                                | true     |                                      |
| profile_name       | The name of the profile to use for signing.                                                                                                                                                                                                                                                                                                                                                                                | true     |                                      |
| correlation_id     | The correlation ID to use for signing - this helps keeping track in the Azure portal where the signing request came from.                                                                                                                                                                                                                                                                                                  | false    | ''                                   |

## Example usage

```yaml
- name: Prepare for codesigning
  uses: ./.github/trusted-signing
  with:
    endpoint: "https://weu.codesigning.azure.net"
    account_name: "<<TRUSTED_SIGNING_ACCOUNT_NAME>>"
    profile_name: "<<TRUSTED_SIGNING_PROFILE_NAME>>"
    correlation_id: "github-codesign-test"
  if: matrix.platform == 'windows-latest'

# Sign a binary
- name: sign
  if: matrix.platform == 'windows-latest'
  shell: pwsh
  env:
    # This is used to authenticate with the trusted signing service on Azure
    AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
    AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
    AZURE_CLIENT_SECRET: ${{ secrets.AZURE_CLIENT_SECRET }}
  run: |
    & $env:TRUSTED_SIGNTOOL "<<binary to sign>>"
```

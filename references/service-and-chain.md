# Service and chain status

Four read-only tools tell the agent which network the server settles on, whether
Robinhood Chain answers, whether the product API fleet is up, and which wallet sends writes.
Run them at the start of any session. They need no key.

## Workflow: confirm the connection

**1. Describe the server**

```json
{ "tool": "veya_describe", "arguments": {} }
```

Expected (Mainnet profile):

```json
{
  "name": "@veyanet/mcp",
  "publicUrl": "https://mcp.veyanet.tech/mcp",
  "settlement": {
    "network": "Robinhood Chain mainnet",
    "contract": "0x87dC582eEA544A434a5C1e30Ca3d12c1e548a674",
    "explorer": "https://robinhoodchain.blockscout.com",
    "rpc": "https://rpc.mainnet.chain.robinhood.com"
  },
  "productApi": "https://api.veyanet.tech",
  "sealed": "AES-256-GCM sealed-node",
  "writes": "On-chain writes need a product API key (veya_dev_ / veya_live_) and your funded wallet on the selected network."
}
```

On a Testnet host, `settlement.network` is `Robinhood Chain testnet` and `settlement.contract` is the Testnet Veya.sol.
Use `settlement.explorer` for every link you show the user.

**2. Ping the chain**

```json
{ "tool": "veya_ping_chain", "arguments": {} }
```

Check that `blockNumber` is a recent block and `config.contractAddress` matches `settlement.contract`.
If the ping fails, verify and write tools will fail too; report an RPC problem.

**3. Check the product API and fleet**

```json
{ "tool": "veya_api_health", "arguments": {} }
```

Returns `{ "httpStatus": 200, "body": { "status": "ok", "validators": { "reachable": … }, "sealed": { "reachable": … } } }`.
`degraded` usually means validators or the sealed node are offline. Describe, ping and
verify keep working; consensus and sealed tools will report the outage.

**4. Explain writes (before the first write)**

```json
{ "tool": "veya_writes_status", "arguments": {} }
```

Returns `userPaidWrites: true`, what a write `requires`, the `gasPayer` sentence, and the
`emptyWallet` message for the selected network (plus `faucetUrl` on Testnet).

## HTTP endpoints (outside MCP)

| Method | Path | Purpose |
|---|---|---|
| `GET` | `https://mcp.veyanet.tech/` | Landing page with the paste URL |
| `GET` | `https://mcp.veyanet.tech/health` | `status`, `service`, `version`, `publicMcpUrl`, `network`, `contractAddress`, `writesEnabled`, `operatorRelayerWrites`, `sealed`, `paidCallGuard`, `settlement` |
| `POST` | `https://mcp.veyanet.tech/mcp` | Stateless Streamable HTTP MCP |
| `POST` | `https://mcp.veyanet.tech/mcp/session` | Optional session path (`MCP-Session-Id`, 30 min idle) |

## Tools

### `veya_describe`

Service info: AES-256-GCM sealed execution and the selected settlement profile

- **Access:** Public
- **Returns:** `name`, `version`, `publicUrl`, `settlement` {`network`,`contract`,`explorer`,`rpc`}, `productApi`, `sealed`, `mainnet`, `writes`, `toolSurface`, `fleet`, `publicPasteUrl`, `paidCallGuard` (when the Metered Call Guard is on)

_No arguments._

### `veya_ping_chain`

Ping Robinhood Chain RPC — block, contract

- **Access:** Public
- **Returns:** `blockNumber` and `config` {`network`,`rpcUrl`,`contractAddress`,`explorerUrl`,`payerConfigured`,`requestTimeoutMs`,`validators`,`sealedNodeConfigured`}

_No arguments._

### `veya_api_health`

Check the VEYA API /health status (api.veyanet.tech), including fleet status

- **Access:** Public
- **Returns:** `{ httpStatus, body }`; body has `status` (`ok`/`degraded`), `validators.reachable`, `sealed.reachable`

_No arguments._

### `veya_writes_status`

Reports how on-chain writes work on this server: your own wallet sends them, the host signer does not

- **Access:** Public
- **Returns:** `userPaidWrites`, `operatorRelayerWrites`, `requires` {`apiKey`,`payerPrivateKey`}, `gasPayer`, `emptyWallet`, `faucetUrl` (Testnet)

_No arguments._


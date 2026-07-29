# @academix-admin/domain-types

Single source of truth for Academix domain types, shared across **web / lambda / desktop** (TypeScript)
and **mobile** (Flutter/Dart). Private repo; consumed via git dependencies — never published to npm/pub.

## Layout

| Path | What |
|---|---|
| `contracts/contracts.schema.json` | **The source.** Language-neutral JSON Schema — each `definitions` key is a wire type (authored from its RPC/Lambda `RETURN` builder). |
| `src/generated/contracts.ts` | Generated TS wire types (snake_case, matches the JSON). **Do not edit.** |
| `src/database.types.ts` | Generated Supabase table Row/Insert/Update + fn Args (`npm run gen:db`). |
| `src/{responses,enums,index}.ts` | Barrel: re-exports generated + status enums (`GateStatus`, `FeatureKey`, `ResultEnvelope`). |
| `dart/` | Generated Dart package `academix_domain_types` (`fromJson`/`toJson`). **Do not edit `lib/src`.** |

## Codegen

```bash
npm install
npm run gen        # contracts.schema.json -> TS (src/generated) + Dart (dart/lib/src)
npm run gen:db     # regenerate Supabase row types (needs SUPABASE_ACCESS_TOKEN)
npm run verify     # regen + `git diff --exit-code` (drift check — CI runs this)
npm run build      # tsup -> dist (also runs on install via `prepare`, so git consumers get built types)
```

To change a type: edit `contracts/contracts.schema.json`, run `npm run gen`, commit. CI fails if the
committed generated files drift from the schema.

## Consume it

**Web / lambda / desktop (npm):**
```jsonc
// package.json
"dependencies": {
  "@academix-admin/domain-types": "git+https://github.com/academix-admin/domain-types.git#v0.1.0"
}
```
```ts
import type { BackendUserBalanceModel, GateStatus } from '@academix-admin/domain-types';
import type { Database } from '@academix-admin/domain-types/database';
```

**Flutter (pub):**
```yaml
# pubspec.yaml
dependencies:
  academix_domain_types:
    git:
      url: https://github.com/academix-admin/domain-types.git
      path: dart
      ref: v0.1.0
```
```dart
import 'package:academix_domain_types/academix_domain_types.dart';
```

Pin a tag (`ref`) for reproducibility; bump it when the contracts change.

# OpenCircuit Electrical Rule Checker

An extensible, graph-based electrical rule checker for OpenCircuit designs. It catches structural and electrical mistakes before simulation or manufacturing and can publish SARIF findings to GitHub code scanning.

## Built-in checks

- Missing ground, duplicate identifiers, unknown terminals, and self-connections.
- Unused and dangling nodes.
- Floating connected networks.
- Nodes lacking a conductive DC path to ground.
- Contradictory loops of ideal voltage sources using weighted union-find constraints.
- LEDs without a resistive element in their connected network.
- Missing independent sources.
- Suspicious engineering values that may indicate unit errors.
- Duplicate parallel component groups.

## CLI

```bash
npm install
npx oc-erc check design.json
npx oc-erc check design.json --format json --output erc.json
npx oc-erc check design.json --format sarif --output erc.sarif
```

The command exits with status `1` when the report contains errors. Warnings and informational findings do not fail the command.

## Library API

```js
import { checkCircuit, createRule } from '@opencircuitlabs/electrical-rule-checker';

const requireAuthor = createRule({
  id: 'require-author',
  defaultSeverity: 'warning',
  check: context => context.circuit.metadata.author
    ? []
    : [{ message: 'Author is required' }]
});

const report = checkCircuit(circuit, {
  rules: [requireAuthor],
  disabledRules: ['no-source'],
  severityOverrides: { 'require-author': 'error' }
});
```

The report is deterministic and sorted by severity, rule, and affected object.

## Scope

ERC findings are design diagnostics, not safety certification. Rules such as the LED current-limiting check are conservative heuristics and can be disabled or replaced for specialized circuits.

## License

MIT © 2026 OpenCircuitLabs

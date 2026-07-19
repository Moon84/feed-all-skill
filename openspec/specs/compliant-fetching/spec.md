# Compliant Fetching

## Design Contract

The skill directs Agents to prefer RSS/Atom, respect source policies, identify
the client, limit request pressure, and stop at authentication or anti-bot
boundaries. It must never instruct an Agent to bypass access controls.

# Worked example: the DNS record that passed every local test

## Symptom

A deployed agent service was unreachable for an external user. Health checks run by the team passed. The service responded locally. From the outside, nothing.

## What guessing did

Attempts cycled through service configuration, ports, firewall rules, and restarts. Each attempt was plausible. None was based on a new fact, so none moved the diagnosis. This is the failure mode the core rule exists to ban: the second, third, and fourth attempts were all drawn from the same picture of the problem, and the picture was wrong.

## What evidence said

Pulling the surfaces side by side produced the mismatch:

- The service itself was healthy and listening.
- The public DNS record for the service pointed at an address inside a private address range (a carrier grade NAT range, the kind a mesh VPN assigns), not at the host's real public address.
- Local checks passed because the test machine belonged to that private network, so the record resolved fine from inside and was unroutable from everywhere else.

No single log contained the answer. The frontend view said fine, the backend said fine, and the truth lived between the DNS surface and the network surface.

## The fix

Replace the record with the host's real public address, then verify from a machine outside the private network (a phone on mobile data works). Minutes, once the right surface was finally consulted.

## The lessons this repo is built on

1. A passing local test can be evidence of your network position, not of correctness. Always ask which surface a green result actually exercised.
2. Configuration you wrote yourself is the least searchable problem class in the world. Nobody has your setup. Your surfaces are the only documentation.
3. The attempt count is a signal. When attempt three starts, the cheapest move is almost never a better guess. It is a surface you have not looked at yet.

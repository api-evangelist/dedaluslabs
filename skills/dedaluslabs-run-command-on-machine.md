---
name: Create a Dedalus Machine and run a command
description: Provision a persistent Linux VM, run a shell command on it, read the output, then sleep it to stop billing.
api: openapi/dedaluslabs-machines-openapi-original.yml
base_url: https://dcs.dedaluslabs.ai
operations: [createMachine, createMachineExecution, getMachineExecutionOutput, sleepMachine, deleteMachine]
---

# Create a Dedalus Machine and run a command

Use the Dedalus Cloud Services (Machines) API to give an agent a real, persistent Linux
box: create it, execute commands, read output, and sleep it when idle so you stop paying.

## Auth
Send your Dedalus API key as `Authorization: Bearer <DEDALUS_API_KEY>` or
`x-api-key: <DEDALUS_API_KEY>` on every request.

## Idempotency
On mutating requests (create machine, create execution) send an `Idempotency-Key` header so
retries are safe. Reusing a key with a different body returns 409 Conflict.

## Steps
1. Create the VM with `createMachine` (`POST /v1/machines`). Capture the returned
   `machine_id`.
2. Run a command with `createMachineExecution`
   (`POST /v1/machines/{machine_id}/executions`). Capture the `execution_id`.
3. Read results with `getMachineExecutionOutput`
   (`GET /v1/machines/{machine_id}/executions/{execution_id}/output`). For live progress,
   stream `listMachineExecutionEvents` over SSE instead.
4. When idle, call `sleepMachine` (`POST /v1/machines/{machine_id}/sleep`) — filesystem,
   packages, and env persist and billing pauses. Wake later with `wakeMachine`.
5. To tear down permanently, call `deleteMachine` (`DELETE /v1/machines/{machine_id}`).

## Rules
- Idle machines are free — sleep rather than delete when you may reuse the box.
- List endpoints paginate with `cursor` + `limit`.
- Errors follow an RFC 9457-shaped ErrorModel (see errors/dedaluslabs-problem-types.yml);
  401 = bad/missing key, 403 = no access, 429 = rate limited, 503 = dependency down.

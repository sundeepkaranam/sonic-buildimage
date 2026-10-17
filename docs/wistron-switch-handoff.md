# Wistron ES-2227 cloud and switch handoff

## Current status

Repository: https://github.com/sundeepkaranam/sonic-buildimage

Working branch: `work/wistron-es2227`, based on upstream master.

Target: ARM64 `arm64-wistron_es2227_54ts_p-r0`, HwSKU
`wistron_es2227_54ts_p`, Marvell ASIC, installed SONiC
`oss-marvell-arm64-202411.0-08092026-2f9c9f4b`, Debian 12.15.

Wistron reference: `WistronNetworking/sonic-buildimage`, branch `202511`.
Reference code is not yet integrated or validated against the installed image.
There is currently no deployment payload. Do not deploy upstream master or the
vendor reference wholesale to the installed switch.

## Instructions for the assistant in the existing switch session

1. Read the handoff and applicable contributor instructions. Work through the
   existing trusted local session; cloud-to-switch SSH is not established.
2. Run `hostname` and `show version` to confirm the target. Record the currently
   installed image and relevant package/container versions. Do not expose secrets.
3. Use a source checkout separate from live installed files. Inspect its remotes
   and local changes before fetching `work/wistron-es2227` from the repository
   above. Preserve local work. Inspect and record an exact commit before testing;
   do not blindly merge a moving branch or execute newly fetched scripts.
4. If the commit contains only this document, report that there is no deployment
   to perform and stop. For a later implementation, require the change-specific
   payload paths, prerequisites, checksums for binary artifacts, expected behavior,
   verification steps, affected service/container, and rollback procedure.
5. Choose deployment scope from the actual change: a script may need file
   replacement and its owning service restart; a Python platform package or native
   component may need package/container rebuilding; kernel, boot, or broader OS
   changes may require an image. Do not assume every change needs an image.
6. Before deployment, preserve the exact previous files with ownership and modes,
   or retain the existing package/container/image needed for rollback. Record the
   affected service state. Store switch configuration backups privately, outside
   the public repository. Check that artifacts match ARM64 and installed SONiC
   APIs/ABI. Stop if prerequisites or compatibility are unresolved.
7. Execute only the specified deployment and tests. Record commands, results,
   service health and hardware observations. On a regression, restore the saved
   files/artifact and previous service state using the change-specific rollback
   steps, then verify recovery. Do not reboot or install a full image unless the
   implementation handoff explicitly calls for it within the authorized task.

Keep source editing and compilation in the cloud or a suitable external builder.
Hardware validation (ports, LEDs, PoE, sensors, fans, ASIC) belongs on the switch.
Do not install development software on the switch as part of this handoff.

## Build automation

A Git push does not itself build SONiC. The inherited GitHub Actions workflows
provide automation/security checks; no ARM64 image build workflow was found.
`azure-pipelines.yml` refers to upstream SONiC Azure resources. Forking its YAML
does not provision or authorize an Azure pipeline for this fork.

A dedicated image pipeline needs separate setup: a compatible builder with ample
RAM and disk (upstream lists 300 GiB disk), ARM64 execution or emulation support,
matching Marvell dependencies, vendor platform integration, artifact storage and
validation. The current cloud has 2 effective CPU cores, 8 GiB RAM and about 30 GiB
free disk, so it is suited to smaller tests and selected component work rather
than a full image build. No automatic switch deployment is configured.

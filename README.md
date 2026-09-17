# Windows 11 Enterprise CIS

## Configure a Windows 11 Enterprise system to be [CIS](https://www.cisecurity.org/cis-benchmarks/) compliant

### Based on [ Microsoft Windows 11 Enterprise Benchmark v3.0.0 - 02-22-2024 ](https://www.cisecurity.org/cis-benchmarks/)

---

![Org Stars](https://img.shields.io/github/stars/ansible-lockdown?label=Org%20Stars&style=social)
![Stars](https://img.shields.io/github/stars/ansible-lockdown/Windows-11-CIS?label=Repo%20Stars&style=social)
![Forks](https://img.shields.io/github/forks/ansible-lockdown/Windows-11-CIS?style=social)
![followers](https://img.shields.io/github/followers/ansible-lockdown?style=social)
[![X URL](https://img.shields.io/twitter/url/https/x.com/AnsibleLockdown.svg?style=social&label=Follow%20%40AnsibleLockdown)](https://x.com/AnsibleLockdown)

<!-- ![Ansible Galaxy Quality](https://img.shields.io/ansible/quality/56324?label=Quality&&logo=ansible) -->
![Discord Badge](https://img.shields.io/discord/925818806838919229?logo=discord)

![Release Branch](https://img.shields.io/badge/Release%20Branch-Main-brightgreen)
![Release Tag](https://img.shields.io/github/v/tag/ansible-lockdown/Windows-11-CIS?label=Release%20Tag&&color=success)
![Main Release Date](https://img.shields.io/github/release-date/ansible-lockdown/Windows-11-CIS?label=Release%20Date)

[![Main Pipeline Status](https://github.com/ansible-lockdown/Windows-11-CIS/actions/workflows/main_pipeline_validation.yml/badge.svg?)](https://github.com/ansible-lockdown/Windows-11-CIS/actions/workflows/main_pipeline_validation.yml)

[![Devel Pipeline Status](https://github.com/ansible-lockdown/Windows-11-CIS/actions/workflows/devel_pipeline_validation.yml/badge.svg?)](https://github.com/ansible-lockdown/Windows-11-CIS/actions/workflows/devel_pipeline_validation.yml)
![Devel Commits](https://img.shields.io/github/commit-activity/m/ansible-lockdown/Windows-11-CIS/devel?color=dark%20green&label=Devel%20Branch%20Commits)

![Issues Open](https://img.shields.io/github/issues-raw/ansible-lockdown/Windows-11-CIS?label=Open%20Issues)
![Issues Closed](https://img.shields.io/github/issues-closed-raw/ansible-lockdown/Windows-11-CIS?label=Closed%20Issues&&color=success)
![Pull Requests](https://img.shields.io/github/issues-pr/ansible-lockdown/Windows-11-CIS?label=Pull%20Requests)

![License](https://img.shields.io/github/license/ansible-lockdown/Windows-11-CIS?label=License)

---

## Looking For Support?

[Lockdown Enterprise](https://www.lockdownenterprise.com#GH_AL_WINDOWS_11_cis)

[Ansible Support](https://www.mindpointgroup.com/cybersecurity-products/ansible-counselor#GH_AL_WINDOWS_11_cis)

### Community

On our [Discord Server](https://www.lockdownenterprise.com/discord) to ask questions, discuss features, or just chat with other Ansible-Lockdown users

### Contributing

Bug reports and feature requests are welcome from everyone, please raise an issue.

Pull requests are accepted from approved contributors only. To be onboarded, join the [Discord Server](https://www.lockdownenterprise.com/discord) and request contributor access. See [CONTRIBUTING.md](CONTRIBUTING.md) for the full process.

---

## Caution(s)

This role **will make changes to the system** which may have unintended consequences. This is not an auditing tool but rather a remediation tool to be used after an audit has been conducted.

Check Mode is not a compliance check! A `--check` run reports what the role would change; it does not tell you whether the host meets the benchmark. Use the audit for that.

A check run does complete. The read-only discovery tasks carry `check_mode: false` so they still gather the state the rest of the role branches on. Two things are skipped deliberately: the section 19 user hive load, because `REG LOAD` mounts registry hives and is not a read-only operation, and the pre/post remediation audit, because it runs a real scan and writes result files.

This role was developed against a clean install of the Windows 11 Enterprise 22H2 Operating System. If you are implementing to an existing system please review this role for any site specific changes that are needed.

To use release version please point to main branch and relevant release for the cis benchmark you wish to work with.

---

## Domain Members

Account Policy is domain scoped. The Default Domain Policy overwrites local
`[System Access]` at every Group Policy refresh, so section 1 cannot hold on a domain
joined host - anything applied locally reverts within about two hours, and an audit
run straight afterwards reports a compliance that does not last.

Measured on a workstation joined to a Server 2025 domain:

| setting | hardened standalone | on joining | after re-remediation | after `gpupdate /force` |
|---|---|---|---|---|
| Minimum password length | 14 | 7 | 14 | 7 |
| Maximum password age | 365 | 42 | 365 | 42 |
| Lockout threshold | 10 | Never | Never | Never |
| Force user logoff | Never | 0 | 0 | 0 |

The fourth column is why the role no longer tries: prelim detects a domain joined host
and skips the ten secedit backed controls in section 1, warning rather than applying a
setting that will not survive. Set them in the Default Domain Policy instead. 1.1.6 is
a genuine registry value and still applies.

### Controls the domain overrides

On a domain joined host these are set by the Default Domain Policy, so the role does
not apply them and the audit reports them as skipped, with the reason in
`meta.skip_reason`. Both decide from the host itself: the role from its prelim facts,
the audit from `run_audit.ps1`, which reads `Win32_ComputerSystem.PartOfDomain` and
passes `win11cis_domain_joined` inline.

| Control | Setting | Why the domain wins | Role | Audit |
|---|---|---|---|---|
| 1.1.1 - 1.1.5, 1.1.7 | Password policy | Default Domain Policy sets `[System Access]` | Skipped, with a warning | Skipped, with the reason |
| 1.2.1 - 1.2.4 | Account lockout policy | Default Domain Policy sets `[System Access]` | Skipped, with a warning | Skipped, with the reason |
| 2.3.11.6 | Force logoff when logon hours expire | Default Domain Policy sets `ForceLogoffWhenHourExpire = 0` | Skipped, with a warning | Skipped, with the reason |
| 1.1.6 | Relax minimum password length limits | Not overridden - a registry value, not account policy | Applied | Asserted |

Set the skipped controls in the Default Domain Policy. On a standalone host every one
of them is applied and asserted as normal.

Nothing else is affected. On the same host a full cycle took the audit from 34
failures to 6, remediating every domain only control - LAPS, the Domain firewall
profile, Group Policy processing, domain sign-in, NetBIOS and multicast, WDigest.

---

## Matching A Security Level For CIS

It is possible to only run level 1 or level 2 controls for CIS as well as a variety of other tags that are available for this role.
This is managed using tags:

- level1-corporate-enterprise-environment
- level2-high-security-sensitive-data-environment
- next-generation-windows-security
- level1-bitlocker
- level2-bitlocker
- bitlocker

The controls found in defaults/main also need to reflect those control numbers due to aligning every control to the audit component.

## Coming From A Previous Release

CIS releases always contain changes, so it is highly recommended to review the new references and available variables. This has changed significantly since the ansible-lockdown initial release.
This is now compatible with python3 if it is found to be the default interpreter. This does come with prerequisites that configure the system accordingly.

Further details can be seen in the [Changelog](./ChangeLog.md)

## Auditing (beta)

**The audit component is in beta while we gather feedback.** It is usable and its
results are meaningful, and we would like to hear how it behaves on your estate.
Please raise an issue, or come and talk to us on the [Discord Server](https://www.lockdownenterprise.com/discord).
Remediation is unaffected - `run_audit` and `setup_audit` both default to `false`,
so none of this runs unless you ask for it.

This role is paired with [Windows-11-CIS-Audit], a set of syver specs that assert
the effective state of all 540 controls. The audit is driven from this role's own
variables, so it tests what the role was actually asked to do rather than a
separate copy of the settings that can drift.

### Switches

| Variable | Default | Effect |
|---|---|---|
| `setup_audit` | `false` | Place the syver binary and the audit content on the host |
| `run_audit` | `false` | Run the audit before and after remediation |
| `audit_only` | `false` | Run the pre-remediation audit, then stop without remediating |
| `fetch_audit_output` | `false` | Collect the result files after the run |

```bash
# stage the binary and content, remediate nothing
ansible-playbook site.yml -e setup_audit=true

# audit the host and stop
ansible-playbook site.yml -e run_audit=true -e audit_only=true

# remediate with a before and after audit
ansible-playbook site.yml -e run_audit=true
```

### Requirements

`syver.exe` is never committed to this repository. The default
`get_audit_binary_method: download` fetches it from the public release named in
`audit_bin_version` (v0.11.1) and verifies the published SHA256. Keep that digest
set: `win_get_url` checks it, and that check is what stands between a compromised
mirror and every audited host running someone else's binary elevated. No Windows
ARM64 asset is published for v0.11.1, so `ARM64_checksum` is deliberately empty
and a download on such a host fails the lookup rather than fetching an amd64
binary.

To supply your own build instead, set `get_audit_binary_method: copy` and point
`audit_bin_copy_location` at it; it defaults to `syver-windows-amd64.exe` in this
role directory.

The audit content comes from `audit_conf_source`, which defaults to the sibling
`Windows-11-CIS-Audit` checkout on the controller. `git` and `get_url` are also
supported; `git` needs Git for Windows on the managed node.

### Reading the results

The post-remediation audit runs **after** the reboot in `post.yml`, because many
Windows CIS settings do not take effect until then. When `skip_reboot` is `true`
(the default) and a reboot was required, some controls are applied but not yet in
effect - the audit prints a warning saying so. Those failures are pending, not
genuine; re-run the audit after a reboot to confirm.

A summary is also written to `C:\ProgramData\ansible\facts.d\compliance_facts.json`.
Windows has no default `fact_path`, so to read it back you must pass one, and
note it appears as `ansible_compliance_facts` rather than under `ansible_local`
as it would on Linux:

```yaml
- ansible.windows.setup:
      fact_path: 'C:\ProgramData\ansible\facts.d'
      gather_subset: ['local']
- ansible.builtin.debug:
      var: ansible_compliance_facts
```

## Documentation

- [Read The Docs](https://ansible-lockdown.readthedocs.io/en/latest/)
- [Getting Started](https://www.lockdownenterprise.com/docs/getting-started-with-lockdown#GH_AL_WINDOWS_11_cis)
- [Customizing Roles](https://www.lockdownenterprise.com/docs/customizing-lockdown-enterprise#GH_AL_WINDOWS_11_cis)
- [Per-Host Configuration](https://www.lockdownenterprise.com/docs/per-host-lockdown-enterprise-configuration#GH_AL_WINDOWS_11_cis)
- [Getting the Most Out of the Role](https://www.lockdownenterprise.com/docs/get-the-most-out-of-lockdown-enterprise#GH_AL_WINDOWS_11_cis)

## Requirements

**General:**

- Basic knowledge of Ansible, below are some links to the Ansible documentation to help get started if you are unfamiliar with Ansible

  - [Main Ansible documentation page](https://docs.ansible.com)
  - [Ansible Getting Started](https://docs.ansible.com/ansible/latest/user_guide/intro_getting_started.html)
  - [Tower User Guide](https://docs.ansible.com/ansible-tower/latest/html/userguide/index.html)
  - [Ansible Community Info](https://docs.ansible.com/ansible/latest/community/index.html)
- Functioning Ansible and/or Tower Installed, configured, and running. This includes all of the base Ansible/Tower configurations, needed packages installed, and infrastructure setup.
- Please read through the tasks in this role to gain an understanding of what each control is doing. Some of the tasks are disruptive and can have unintended consequences in a live production system. Also familiarize yourself with the variables in the defaults/main.yml file.

**Technical Dependencies:**

- Windows 11 Enterprise 25H2 - Other versions are not supported
- Python3 Ansible run environment
- passlib
- python-xmltodict
- pywinrm or pypsrp

Package 'python-xmltodict' is required if you enable the OpenSCAP tool installation and run a report. Packages python(2)-passlib and python-jmespath are required for tasks with custom filters or modules. These are all required on the controller host that executes Ansible.

## Role Variables

This role is designed so that the end user should not have to edit the tasks themselves. All customizing should be done via the defaults/main.yml file or with extra vars within the project, job, workflow, etc.

## Tags

There are many tags available for added control precision. Each control has it's own set of tags noting what level, if its automated or manual check, if it's a patch or audit, and the rule number.

Below is an example of the tag section from a control within this role. Using this example if you set your run to skip all controls with the tag manage_updates_offered_from_windows_update, this task will be skipped. The opposite can also happen where you run only controls tagged with manage_updates_offered_from_windows_update.

```sh
  tags:
      - level1-corporate-enterprise-environment
      - patch
      - automated
      - rule_18.10.92.4.3
      - manage_updates_offered_from_windows_update
      - updates
```
Tags for entire sections can be run using the tags that are in the main.yml files for each section.  In this particular case this is the following example and how it breaks down for sections.

Section 18 - administrative_templates_computer <br>
Section 18.10 - windows-components <br>
Section 18.10.3 - app-package-deployment

```sh
- name: "SECTION | 18.10.3 | App Package Deployment"
  ansible.builtin.import_tasks:
      file: cis_18.10.3.x.yml
  tags:
      - administrative_templates_computer
      - windows_components
      - app_package_deployment
```

## Community Contribution

We encourage you (the community) to contribute to this role. Please read the rules below.

- Your work is done in your own individual branch. Make sure to Signed-off and GPG sign all commits you intend to merge.
- All community Pull Requests are pulled into the devel branch
- Pull Requests into devel will confirm your commits have a GPG signature, Signed-off, and a functional test before being approved
- Once your changes are merged and a more detailed review is complete, an authorized member will merge your changes into the main branch for a new release

## Pipeline Testing

uses:

- ansible-core 2.16.x
- ansible collections - pulls in the latest version based on requirements file
- runs the audit using the devel branch
- This is an automated test that occurs on pull requests into devel
- self-hosted runners using OpenTofu

## Local Testing

  - Ansible
    - ansible-core >= 2.16.0 - python 3.11

## Credits and Thanks

Massive thanks to the fantastic community and all its members.

This includes a huge thanks and credit to the original authors and maintainers.

[Windows-11-CIS-Audit]: https://github.com/ansible-lockdown/Windows-11-CIS-Audit

# Changelog

## CIS Benchmark v3.0.0 - September 2026 Updates

  - fix: 2.3.11.6 writes ForceLogoffWhenHourExpire through win_security_policy, not LanManServer EnableForcedLogOff
  - fix: 2.3.11.6 skipped on a domain joined host, with a warning; the Default Domain Policy sets it to 0
  - fix: 2.3.7.5 legal notice written through the security database; blank lines dropped, commas kept
  - fix: audit_content copy and archive remove the previous audit content first
  - fix: audit summary capture fails when the results file does not parse or the summary is empty
  - fix: 18.9.25.5 warning is counted when the LAPS password length is below 15, not at 15 or above
  - fix: the 5.12 SSH auto-skip warning is now counted
  - fix: prelim account lockout ordering skipped on a domain joined host, like the rest of section 1
  - docs: README table of the controls the domain overrides, and how the role and audit report them
  - fix: section 1 is skipped on a domain joined host, where the Default Domain Policy overwrites local [System Access] at every refresh and anything applied reverts within about two hours. Prelim sets discovered_account_policy_is_domain_scoped and warns; 1.1.6 is a registry value and still applies
  - docs: README explains why section 1 cannot hold on a domain member, with the measured before and after values
  - fix: 18.10.49.1 and 18.10.71.1 aborted the whole play on Windows 11 25H2 - ucpd.sys refuses the writes to Windows Feeds\EnableFeeds and Dsh\AllowNewsAndInterests for every process outside its allow list, elevated or not. Both now tolerate that one error and raise a warning, so the remaining sections still run; the setting has to come from a GPO
  - fix: discovered_domain_joined defaulted to true, and the role's own fallback gather (distribution,!all,!min) returns no domain facts, so a play with gather_facts: false applied the domain-only settings to a standalone host. It now defaults to false and prelim gathers the windows_domain subset when the fact is missing
  - fix: prelim REG LOADed the default and per-user hives and nothing ever unloaded them, so a run left NTUSER.DAT locked. tasks/unload_hives.yml unloads exactly what the role loaded, after the post-remediation audit
  - fix: the eight AuditPol /set tasks in 17.9 carried changed_when: false and failed_when: false inherited from the /get task above them, so section 17.9 never reported changed and an AuditPol failure was swallowed
  - fix: the OS assert required 'Microsoft Windows 11 Enterprise', refusing Pro, Education and LTSC - it now matches Microsoft Windows 11

  - feat: paired with Windows11-CIS-Audit - setup_audit, run_audit, audit_only and fetch_audit_output now work as they do on the Linux roles
  - feat: templates/lockdown_audit.yml.j2 renders the audit's vars from this role's own variables, so the audit asserts what the role was asked to do - domain membership and win_skip_for_test come from the role's own facts instead of being assumed
  - feat: compliance_facts.json written to C:\ProgramData\ansible\facts.d - JSON not INI, because ansible.windows.setup reads only .ps1 and .json
  - refactor: defaults/main.yml split to defaults/main/main.yml plus defaults/main/audit.yml, matching the estate convention
  - fix: the post-remediation audit runs after post.yml so settings that need the reboot are not reported as failures, and reloads the user hives the reboot unloaded so section 19 is still measurable

  - fix: 1.2.1, 1.2.2, 1.2.3 and 1.2.4 wrote to the RRAS AccountLockout registry key and never set the account lockout policy - net accounts still reported the Windows defaults after a full run. They now use community.windows.win_security_policy, matching Windows-2025-CIS
  - fix: prelim normalises ResetLockoutCount before 1.2.1 when the current value would block it, so the fixed order 1.2.2, 1.2.1, 1.2.4, 1.2.3 is valid from any starting state - this replaces the cloud-vs-local task-ordering split and cis_1.2_cloud_lockout_order.yml is retired
  - refactor: section_5/cis_5.x.yml split into cis_5.1.x.yml, cis_5.12.x.yml, cis_5.23.x.yml and cis_5.34.x.yml (11 controls each), imported by section_5/main.yml
  - fix: 5.12 disabled the OpenSSH SSH Server on hosts managed over SSH, severing the control connection mid-run - it is now skipped automatically when prelim detects an SSH connection, and honours win_skip_for_test
  - fix: 18.9.25.6 conditional logic for password age values
  - fix: 18.9.25.5 used the invalid Jinja operator '=>' instead of '>=', which aborted the play with a when-expression syntax error - only reachable on a domain member, because all of 18.9.25.x sits behind the domain gate on the section import
  - fix: a --check run aborted in prelim on an unguarded stdout_lines and never evaluated a single control. The 41 read-only discovery tasks now carry check_mode: false so they still gather, matching Windows-2025-CIS section 17. The section 19 user hive load and the pre/post audit are skipped under --check, because REG LOAD mounts registry hives and the audit writes result files
  - style: .yamllint now enforces indentation spaces: 2, and all 146 YAML files are re-indented to match. Whitespace only - every file was verified to parse to an identical structure, and block scalars were shifted as a unit so the PowerShell inside win_shell is untouched
  - style: task keys reordered to name, when, tags, then the module, matching how blocks already read and matching Windows-2025-CIS. 134 files, key order only - every file was verified to keep the same set of lines and to parse to an identical structure
  - refactor: the eight registers in prelim.yml renamed from discovered_ to prelim_, matching every other role in the estate - a PRELIM task registers prelim_, a control task in section_ registers discovered_. The five prelim set_fact names are deliberately unchanged: set_fact follows no prefix convention in any role. Audit output is byte-identical after the rename
  - fix: win_skip_for_test now covers 1.2.1, 1.2.2, 1.2.3 and 1.2.4 and the prelim task that normalises ResetLockoutCount before them. The toggle documented itself as skipping disruptive changes but did not cover the account lockout policy, so a converge with it set to true still locked the operator out of a WinRM-managed host after five failed authentications. Host-verified: with the toggle true the seven tasks skip and 'net accounts' keeps its existing lockout threshold, while section 1.1 still applies
  - fix: 2.3.7.1 task name said CTRL-ALT-DEL where the benchmark and the audit both say CTRL+ALT+DEL
  - refactor: removed the hosted_virtual_system_override / discovered_cloud_based_system cloud detection. It existed to reorder the 1.2.x secedit lockout controls, which the prelim ResetLockoutCount normalisation replaced, and the fact had no remaining consumers. Private-Windows-2025-CIS had already dropped it
  - feat: syver is public, so get_audit_binary_method now defaults to download - audit_bin_url points at the krameff/syver releases and audit_bin_version pins v0.11.1 with the SHA256 from the signed release sums. The binary no longer has to be staged on the control node, and win_get_url verifies the digest before writing it. ARM64 is left empty deliberately: v0.11.1 publishes no Windows ARM64 asset
  - split sections to smaller groups of tasks

Alignment against CIS Microsoft Windows 11 Enterprise Benchmark v3.0.0
  - feat: added missing control 18.8.2 - Remove Personalized Website Recommendations from the Recommended section in the Start Menu
  - feat: added missing control 18.10.92.4.4 - Enable optional updates
  - fix: 18.9.36.1 was gated on win11cis_rule_18_9_35_1 instead of win11cis_rule_18_9_36_1
  - fix: removed retired toggle win11cis_rule_18_10_72_1 - no matching control in v3.0.0
  - fix: 17.7.4 level tag used underscores (level1_corporate_enterprise_environment)
  - fix: 5.23 and 18.10.3.2 tagged level2, both are Level 1 in v3.0.0
  - fix: 1.2.3 and 2.3.11.6 tagged automated, both are Manual in v3.0.0
  - fix: sub-tasks in the 17.2.3 block were numbered 17.2.6
  - fix: sub-task in the 9.2.5 block was numbered 9.2.6
  - fix: 18.4.2 task name carried a duplicated control ID
  - fix: 2.3.7.5 named "Message title", it sets LegalNoticeText - "Message text"
  - fix: 18.9.7.1.5 carried 18.9.7.1.4's title
  - fix: 18.6.14.1 title omitted Require Privacy, which the task already sets
  - fix: 2.2.16 and 2.2.20 titles omitted Local account, which the tasks already deny
  - fix: task titles realigned to the v3.0.0 wording (2.2.14, 2.2.24, 2.3.10.8, 5.11,
    5.13, 5.34, 17.5.5, 18.5.1, 18.5.6, 18.5.9, 18.5.10, 18.6.9.1, 18.9.47.11.1,
    18.10.42.7.1, 18.10.42.17, 18.10.55.1)

QA pass 2026-09-03
  - fix: 18.10.9.1.7 wrote FDVHideRecoveryPage instead of FDVActiveDirectoryBackup, so the
    control was never implemented and it also reverted 18.10.9.1.6
  - fix: 18.10.4.1 wrote LetAppsActivateWithVoiceAboveLock=1 (Force Allow); benchmark requires 2 (Force Deny)
  - fix: 18.9.19.7 wrote DisableBkGndGroupPolicy into a self-created subkey of the same name;
    now removes the value from the real key, which is the benchmark's compliant state
  - fix: 18.10.79.1 and 18.10.79.2 used SOFTWARE\Microsoft\Policies\Microsoft\WindowsInkWorkspace,
    a path Windows never reads; corrected to SOFTWARE\Policies\Microsoft\WindowsInkWorkspace
  - fix: 18.10.9.1.1 and 18.10.9.3.1 passed data: [] for a REG_SZ; win_regedit only coerces null
    to an empty string, so the value written was undefined. Now data: ""
  - fix: control 5.6 referenced win11cis_uninstall_iis_service_admin, which is defined nowhere
    (defaults has win11cis_uninstall_iis_admin_service); aborted the play on any host with IIS
  - fix: prelim WDAG check ran the Hyper-V command, so 2.2.29 keyed WDAGUtilityAccount rights
    off Hyper-V state instead of Windows Defender Application Guard
  - fix: 18.9.4.1 was tagged rule_18.9.3.1 and 18.10.9.2.18 was tagged rule_18.10.9.2.1,
    breaking --tags selection in both directions
  - fix: 18.10.9.1.5 wrote win11cis_48_digit_recovery_password_setting while gating on
    win11cis_256bit_recovery_key_setting
  - fix: register names diccovered_17_3_2_audit and rights_check; register/changed_when order on 2.2.5
  - fix: wn11cis_secreatetokenprivilege renamed to win11cis_secreatetokenprivilege
  - fix: min_ansible_version raised to 2.16.1 in meta/main.yml and defaults/main.yml
  - fix: 11 sub-tasks labelled AUDIT while writing state, or PATCH while only printing a warning
  - fix: duplicate automated tag on 18.10.80.2 and 18.10.9.2.4
  - fix: win_skip_for_test comment named controls 5.22/5.40; the gated controls are 5.21/5.39
  - fix: LICENSE company casing, CONTRIBUTING canonical heading, spelling and grammar corrections
  - fix: .gitignore no longer ignores .github/ wholesale (it was silently untracking new workflow
    files); added secret and QA-artifact patterns
  - fix: actions/checkout pinned to v7.0.0
  - fix: added the canonical private-repo workflows benchmark_tracking_controller.yml and
    export_badges_private.yml, sourced from Private-Windows-2025-CIS
  - fix: removed update_galaxy.yml - it is a public-mirror-only workflow
  - fix: 18.9.19.7 now also removes the stray DisableBkGndGroupPolicy subkey that earlier role
    versions created, so already-hardened hosts are cleaned up

QA pass 2 2026-09-03 (non-registry controls)
  - fix: 5.3 used the service display name "Computer Browser". win_service_info matches the
    service name only, so .exists was always false and both the disable and uninstall branches
    were skipped - the control hardened nothing. Now uses Browser
  - fix: 2.2.29 gated its Hyper-V/WDAG auto-detection on '"" in win11cis_seservicelogonright'.
    The default is [], so "" in [] is always false: all four auto-detect branches were dead and
    the user-defined branch always ran, stripping SeServiceLogonRight to No One even on hosts
    where Hyper-V or WDAG needed it. Now gated on list length
  - fix: 5.10 uninstall branch was identical to its disable branch and never set state: absent
  - fix: 5.5, 5.9, 5.12, 5.17 and 5.35 task names referenced the wrong service or wrong action
  - fix: 17.5.3 registered discovered_7_5_3_audit (missing the 17)
  - fix: SeReLabelPrivilege -> SeRelabelPrivilege on 2.2.31
  - fix: 35 single-item when:/tags: lists converted to inline form, matching the estate convention
  - fix: 137 block-style tasks used name -> block -> when -> tags. Canonical Lockdown order is
    name -> when -> tags -> block, which every other role in the estate already follows. Reordered;
    ansible-lint key-order[task] now reports zero

#### May 2026
General Updates
  - Corrected user attributes configurations in Windows 11 CIS section 2 2.2.16
  - License updated to reflect 2026
#### April 2026
General Updates
  - Updated registry issues
  - Updated the cloud check with new variable
  - Github Actions Version update
  - Pr Message Added
Issues Addressed:
  - [#30](https://github.com/ansible-lockdown/Windows-11-CIS/pull/30) - Thanks @exu-g

## Release 3.0.3
General Updates
  - Removed 9.3.4 Win_Skip Tag
  - Updated Default Main win_skip_for_test 9.3.5 to 9.3.4 - Thanks @mikeeq

## Release 3.0.2
General Updates
  - Fixed 18.9.5.2 Variable and registry name
  - Fixed 2.3.7.6 when statement

## Release 3.0.1

#### February 2025
General Updates
  - Updated Prelim To Add Always Tags To All Tasks
  - Control 2.3.7.5 & 2.3.7.6 Fixed
  - Control 5.3 Updated Name
  - Control 18.10.65.5 Fixed Name From DisableOSUpgrade to RemoveWindowsStore - Thanks @mfortin

Issues Addressed:
  - [#15](https://github.com/ansible-lockdown/Windows-11-CIS/issues/15) - Thanks @davidstanaway
  - [#14](https://github.com/ansible-lockdown/Windows-11-CIS/issues/14) - Thanks @dwierima-aspentech
  - [#12](https://github.com/ansible-lockdown/Windows-11-CIS/issues/12) - Thanks @dwierima-aspentech

## Release 3.0.0

#### January 2025
General Updates
  - hku_loaded_list renamed to discovered_hku_loaded_list
  - General findings fixed when comparing win10 to win11
  - Added additional when statements for domain joined systems. - Thanks @mfortin

Controls Changed
  - Updated Control 2.2.29 with proper variable.
  - Control 5.9 Tag updated.
  - Control 17.2.1 added tag (- rule_17.2.1)
  - Control 18.9.5.2 Title Update
  - Control 18.9.25.5 Fixed Variable In It.

Things To Do
  - Move to 2 spacing
  - Update formatting
  - Add NIST

## Release 2.0.0

#### July 2024
General Updates
  - Benchmark 3.0.0 Update
  - Added Tag "always" to Hyper-V Prelim Task
  - Tags: All tags contain underscores except for Level Tags (use dashes). (Need to finish)
  - Enhanced/Reordered Tags

Controls Changed
  - Control 1.2.3: Changed from Audit to Patch in Tags
  - Control 2.2.14: Updated when statement to stdout
  - Control 2.3.4.1: Removed "Ensure 'Devices: Allowed to format and eject removable media' is set to 'Administrators and Interactive Users'"
  - Control 2.3.4.2: Renamed to 2.3.4.1
  - Control 2.3.10.8: Added "Is Configured" to the Title
  - Control 2.3.11.11: Added
  - Control 2.3.11.12: Added with variable in default main (Fix setting to 1 - Audit All, not 2 - Deny All)
  - Control 2.3.14.1: Changed to "level2-high-security-sensitive-data-environment"
  - Control 5.3: Updated name to "Not Installed"
  - Control 5.8: ICS Sharing Removed
  - Control 5.8 v3.0.0: Changed to "level2-high-security-sensitive-data-environment"
  - Control 5.9 v3.0.0: Updated name to include "LxssManager"
  - Control 5.11 v3.0.0: Changed to "level2-high-security-sensitive-data-environment"
  - Control 5.27 v3.0.0: Changed to "level1-corporate-enterprise-environment"
    - All Controls from 5.9 v2.0.0: Moved one control number lower in v3.0.0
  - Control 9.1.3 v2.0.0: Removed in v3.0.0
    - All subsequent controls moved one number lower
  - Control 9.2.3 v2.0.0: Removed in v3.0.0
    - All subsequent controls moved one number lower
  - Control 9.3.3 v2.0.0: Removed in v3.0.0
    - All subsequent controls moved one number lower
  - Control 17.6.3: Added tags
  - Control 18.4.5 v2.0.0: Moved to 19.4.6 in v3.0.0, all subsequent controls moved down one number
  - Control 18.4.5: New benchmark in v3.0.0
  - Control 18.5.2: Updated title
  - Control 18.5.3: Updated title
  - Control 18.5.8: Updated title
  - Control 18.6.4.1: Fixed when statement to "or" instead of "and"
  - Control 18.6.4.2: Fixed when statement to "or" instead of "and"
  - Control 18.6.14.1: Added RequirePrivacy=1 to the settings per v3.0.0
  - Control 18.6.21.1: Updated title
  - Control 18.6.21.2: Updated title
  - Control 18.7.6: Fixed when statement to "or" instead of "and"
  - Control 18.8.1.1: Changed to "level2-high-security-sensitive-data-environment"
  - Control 18.9.5.2: Fixed when statement to "or" instead of "and"
  - Control 18.9.19.4: Added new control in v3.0.0
  - Control 18.9.19.5: Added new control in v3.0.0
  - Controls 18.9.19.4 - 5 in v2.0.0 now moved to 18.9.19.6 - 7 in v3.0.0
  - Control 18.9.51.1.1: Changed to "level1-corporate-enterprise-environment"
  - Control 18.9.51.2.1: Changed to "level1-corporate-enterprise-environment," title updated
  - Control 18.10.5.2: Fixed title name
  - Control 18.10.9.1.1: Fixed value from none to []
  - Control 18.10.9.1.4: Added new variable
  - Control 18.10.9.1.11: Updated title name to match v3.0.0
  - Control 18.10.9.2.11: Fixed title name
  - Control 18.10.9.2.12: Fixed title name
  - Control 18.10.9.3.1: Fixed value from none to []
  - Control 18.10.9.3.4: Updated name from RDVManageDRA to RDVRecoveryPassword
  - Control 18.10.9.3.13: Fixed title name
  - Control 18.10.13.1: Fixed when statement to "or" instead of "and"
  - Control 18.10.15.1: Fixed when statement to "or" instead of "and"
  - Control 18.10.16.1: Fixed when statement lines
  - Control 18.10.75.1.1: New control, all existing controls moved down one
  - Control 18.10.75.2.1: Updated title
  - Control 18.10.76.3.1 - 2: Removed in v3.0.0
  - Control 18.10.79.2: Updated title
  - Control 18.10.86.1: Changed to "level2-high-security-sensitive-data-environment"
  - Control 18.10.86.2: Changed to "level2-high-security-sensitive-data-environment"
  - Control 18.10.90.2: Updated title
  - Control 18.10.92.2.1: Fixed when statement
  - Control 18.10.92.2.3: New, causing all others to move down one
    - Adjusted Registry to remove PP in Policies
  - Control 18.10.92.4.1: Data value changed to 1
  - Control 19.6.6.1: Renamed to 19.6.6.1.1 - Typo Fix
  - Control 19.7.38.1: Added in v3.0.0
  - Section 18.10.26.x in v2.0.0: Moved to 18.10.25.x in v3.0.0, all controls updated, all variables adjusted, all Default/main toggles updated
  - Section 18.10.29.x in v2.0.0: Moved to 18.10.28.x in v3.0.0, all controls updated, all variables adjusted, all Default/main toggles updated
  - Section 18.10.37.x in v2.0.0: Moved to 18.10.36.x in v3.0.0, all controls updated, all variables adjusted, all Default/main toggles updated
  - Section 18.10.43.x in v2.0.0: Moved to 18.10.42.x in v3.0.0, all controls updated, all variables adjusted, all Default/main toggles updated

Section Moves
  - Section 18.3.x v2.0.0: Removed in v3.0.0, all variables removed in defaults/main
  - Section 18.9.25 in v2.0.0: Moved to 18.9.26 in v3.0.0
  - Section 18.9.26 in v2.0.0: Moved to 18.9.27 in v3.0.0
  - Section 18.9.30 in v2.0.0: Moved to 18.9.31 in v3.0.0
  - Section 18.9.32 in v2.0.0: Moved to 18.9.33 in v3.0.0
  - Section 18.9.34 in v2.0.0: Moved to 18.9.35 in v3.0.0
  - Section 18.9.35 in v2.0.0: Moved to 18.9.36 in v3.0.0
  - Section 18.9.37 in v2.0.0: Moved to 18.9.28 in v3.0.0
  - Section 18.9.46 in v2.0.0: Moved to 18.9.47 in v3.0.0
  - Section 18.9.48 in v2.0.0: Moved to 18.9.49 in v3.0.0
  - Section 18.9.50 in v2.0.0: Moved to 18.9.51 in v3.0.0
  - Section 18.10.33: Moved to 10.10.32, removed all controls in v3.0.0
  - Section 18.10.41.x in v2.0.0: Moved to 18.10.40.x in v3.0.0
  - Section 18.10.42.x in v2.0.0: Moved to 18.10.41.x in v3.0.0
  - Section 18.10.44.x in v2.0.0: Moved to 18.10.43.x in v3.0.0
  - Section 18.10.50.x in v2.0.0: Moved to 18.10.49.x in v3.0.0
  - Section 18.10.51.x in v2.0.0: Moved to 18.10.50.x in v3.0.0
  - Section 18.10.56.x in v2.0.0: Moved to 18.10.55.x in v3.0.0
  - Section 18.10.57.x in v2.0.0: Moved to 18.10.56.x in v3.0.0
  - Section 18.10.58.x in v2.0.0: Moved to 18.10.57.x in v3.0.0
  - Section 18.10.59.x in v2.0.0: Moved to 18.10.58.x in v3.0.0
  - Section 18.10.63.x in v2.0.0: Moved to 18.10.62.x in v3.0.0
  - Section 18.10.66.x in v2.0.0: Moved to 18.10.65.x in v3.0.0
  - Section 18.10.72.x in v2.0.0: Moved to 18.10.71.x in v3.0.0
  - Section 18.10.76.x in v2.0.0: Moved to 18.10.75.x in v3.0.0
  - Section 18.10.78.x in v2.0.0: Moved to 18.10.77.x in v3.0.0
  - Section 18.10.79.x in v2.0.0: Moved to 18.10.78.x in v3.0.0
  - Section 18.10.80.x in v2.0.0: Moved to 18.10.79.x in v3.0.0
  - Section 18.10.81.x in v2.0.0: Moved to 18.10.80.x in v3.0.0
  - Section 18.10.82.x in v2.0.0: Moved to 18.10.81.x in v3.0.0
  - Section 18.10.87.x in v2.0.0: Moved to 18.10.86.x in v3.0.0
  - Section 18.10.89.x in v2.0.0: Moved to 18.10.88.x in v3.0.0
  - Section 18.10.90.x in v2.0.0: Moved to 18.10.89.x in v3.0.0
  - Section 18.10.91.x in v2.0.0: Moved to 18.10.90.x in v3.0.0
  - Section 18.10.92.x in v2.0.0: Moved to 18.10.91.x in v3.0.0
  - Section 18.10.93.x in v2.0.0: Moved to 18.10.92.x in v3.0.0
  - Section 19.1.3.x v2.0.0: Removed in v3.0.0
  - Section 19.7.7.x in v2.0.0: Renumbered to 19.7.8.x in v3.0.0, all controls updated
  - Section 19.7.25.x in v2.0.0: Renumbered to 19.7.26.x in v3.0.0
  - Section 19.7.25.1 in v2.0.0: Moved to 19.7.26.1 in v3.0.0
  - Control 19.7.40.1 in v2.0.0: Moved to 19.7.42.1 in v3.0.0
  - Control 19.7.47.2.1 in v2.0.0: Moved to 19.7.44.2.1 in v3.0.0

#### June 2024
  - Updated 18.9.19.5 To 0 "Disabled" - Thanks @dennisharder-alight
  - Updated 18.10.43.10.2 To 0 "Disabled"
  - Updated 18.5.1 Value to name. - Thanks @mfortin
  - Updated 18.5.1 path missing \ between Software and Microsoft.
  - Updated 2.2.11 To allow variables to be input if site requires it.
  - Updated PRELIM | Set Fact If Cloud Based System to include ansible_system_vendor. - Thanks @mfortin
  - Updated Pipelines - Thanks @mfortin
  - Added discovered to Prelim registered names.
  - Added discovered_controlid to controls that register values.
  - Verified 1.1.6 RelaxMinimumPasswordLengthLimits is using registry style entry not win_security_policy.
  - Verified 18.10.93.4.1 ManagePreviewBuildsPolicyValue is set to 0 value.
  - Control 17.9.5 updated changed_when.
  - Removed all win_regedit state: present: (Default) value for the module.

## Release 1.0.0

#### March 2024
  - Updated Section 19 To Take Into Account All HKU Accounts And Windows Default Template.
  - Fixed A Number Of Typos
  - Updated Readme
  - Added Option For skip_reboot And Warning Message For It.
  - Added Two New Controls To Win_Skip_For_Test
    - 18.10.89.1.2
    - 18.10.89.2.3
- Removed When Checks For Domain, Member Server, And Standalone

#### September 2023
  - Initial Release For Benchmark 2.0.0 Released 03.07.2023

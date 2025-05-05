# Changelog

## Release 3.0.3
General Updates
  - Removed 9.3.4 Win_Skip Tag
  - Updated Default Main win_skip_for_test 9.3.5 to 9.3.4 - Thanks @mikeeq

## Release 3.0.2
General Updates
  - Fixed 18.9.5.2 Variable and registry name
  - Fixed 2.3.7.6 when statement

## Release 3.0.1

#### Feburary 2025
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

#### Janurary 2025
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
  - Added discovered to Prelim reistered names.
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
  - Added Two New Comtrols To Win_Skip_For_Test
    - 18.10.89.1.2
    - 18.10.89.2.3
- Removed When Checks For Domain, Member Server, And Standalone

#### September 2023
  - Initial Release For Benchmark 2.0.0 Released 03.07.2023



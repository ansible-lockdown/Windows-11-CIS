# Changelog

June 2024
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

March 2024
  - Updated Section 19 To Take Into Account All HKU Accounts And Windows Default Template.
  - Fixed A Number Of Typos
  - Updated Readme
  - Added Option For skip_reboot And Warning Message For It.
  - Added Two New Comtrols To Win_Skip_For_Test
    - 18.10.89.1.2
    - 18.10.89.2.3
- Removed When Checks For Domain, Member Server, And Standalone

September 2023
  - Initial Release For Benchmark 2.0.0 Released 03.07.2023

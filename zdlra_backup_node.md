# what things to check for oracle secure backup in ZDLRA ( osbadmin & osbmedia)

- tape library side ( things we could check)

  - tape media - OK except ( two tapes ABA004 & ABA013 found no EOD)

    - oracle recommend to unload and take them out
    - From docs ( Doc ID 2561068.1 ) explains that NO EOD means no End of Data
      said we could reformatted those two drives be readonly so they become scratch tapes / unassign tape media

  - tape drive - OK
  - library - OK

- Application side ( OSB )

  - list both drives and library
    ```sh
    ob> lsdev
    ```
  - list volumes in OSB inventory

    ```sh
    ob > lsvol --library --long
    ```

  - can ping to library and tape drive
    ```sh
    ob> pingdev library_name
    ```
  - can load and unload tape media

    - load

    ```sh
    ob> loadvol --drive robot0_tape01 1
    ```

    - unload

    ```sh
    ob> unloadvol --drive robot0_tape01 1
    ```

  - check module top drive 1 with auto not in service - due to when we replace that failed drive in tape libray
    and status in OSB not refresh
    ```sh
    ob> chdev --type tape --inservice  robot01_tape_drive01
    ```
  - check specific tape drive status and see SN of drive if it match SN in tape library side because
    when load volume in drive will compare SN in both side
    ```sh
    ob> lsdev --drive robot01_tape_drive01
    ```
  - if server up then check serial number if it's auto updated in OSB side if not then update it
    ```sh
    chdev --type tape --updateserialnumber robot01_tape_drive01
    ```
  - check if we could scan the content of tape library since
    Oracle Secure Backup does not automatically detect changes to a tape library
    ```sh
    ob> inventory --library robot01 --force
    ```
  - check if run success then it will show all volumes(tape media) visible in OSB side
    ```sh
    ob> lsvol -L robot01 --long
    ```
  - if show all volumes then that means we more volumes visible to OSB side

  - Next we could run backup job run oracle enterprise cloud manager

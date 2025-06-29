It looks like you're trying to:

1. Loop through a list of VMs.


2. Collect multiple advanced settings per VM.


3. Export all results to one CSV file (appending to it, not overwriting).


4. Ensure it runs cleanly with PowerCLI.



Here’s a cleaned-up and functional PowerShell script to do exactly that:


---

✅ Final Script to Append All Advanced Settings per VM to One CSV

# Connect to vCenter first (assumes session or credentials already handled)

# Output CSV path
$outputFile = "C:\Path\To\output.csv"

# Optional: Clear or create the file once at the beginning if needed
if (!(Test-Path $outputFile)) {
    # Create the file with headers
    "" | Select-Object @{Name="VMName";Expression={""}}, @{Name="SettingName";Expression={""}}, @{Name="SettingValue";Expression={""}} |
    Export-Csv -Path $outputFile -NoTypeInformation
}

# List of settings you want to query
$settingNames = @(
    'isolation.tools.diskShrink.disable',
    'isolation.tools.copy.disable',
    'isolation.tools.paste.disable',
    'isolation.tools.setGUIOptions.enable',
    'tools.guestlib.enableHostInfo',
    'log.log.keepOld',
    'log.rotateSize',
    'isolation.tools.memSchedFakeSampleStats.disable',
    'isolation.device.edit.disable',
    'remoteDisplay.vnc.enabled',
    'remoteDisplay.maxConnections',
    'tools.setInfo.sizeLimit',
    'isolation.device.connectable.disable',
    'isolation.tools.unity.taskbar.disable',
    'isolation.tools.ghi.protocolhandler.info.disable',
    'isolation.tools.ghi.autologon.disable',
    'isolation.tools.unityActive.disable',
    'isolation.tools.unity.windowContents.disable',
    'isolation.tools.unity.push.update.disable',
    'isolation.tools.vmxDnDVersionGet.disable',
    'isolation.tools.guestDnDVersionSet.disable',
    'isolation.tools.hgfsServerSet.disable',
    'isolation.tools.getCreds.disable',
    'isolation.tools.unity.disable',
    'isolation.tools.unityInterlockOperation.disable',
    'isolation.tools.trashFolderState.disable',
    'isolation.tools.dispTopoRequest.disable',
    'isolation.tools.ghi.launchmenu.change'
)

# Get all VMs
$vms = Get-VM

foreach ($vm in $vms) {
    foreach ($settingName in $settingNames) {
        $setting = Get-AdvancedSetting -Entity $vm -Name $settingName -ErrorAction SilentlyContinue
        if ($setting) {
            $result = [pscustomobject]@{
                VMName       = $vm.Name
                SettingName  = $setting.Name
                SettingValue = $setting.Value
            }

            # Append to CSV
            $result | Export-Csv -Path $outputFile -NoTypeInformation -Append
        }
    }
}

# Disconnect from vCenter
Disconnect-VIServer -Confirm:$false


---

🔍 Notes:

Appending: -Append flag ensures the data adds to the existing file without overwriting.

Error Handling: -ErrorAction SilentlyContinue skips missing settings gracefully.

Dynamic VM list: Pulls all VMs using Get-VM.

Efficient CSV write: Writes only if the setting is found.



---

Would you like this to run on a schedule or remotely against multiple vCenters? I can help tailor it further.

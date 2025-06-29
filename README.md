# Banking-Application
There are three services loan,customer and cards
All services able to communicate with each other either syncrohnus or asynchronous
# Connect to vCenter
Connect-VIServer -Server "your_vcenter_server"

# Output CSV file path
$outputFile = "C:\VM_AdvancedSettings.csv"

# Initialize an array to store results
$results = @()

# Get all VMs
$vmList = Get-VM

foreach ($vm in $vmList) {
    # Get advanced settings for each VM
    $advSettings = Get-AdvancedSetting -Entity $vm

    foreach ($setting in $advSettings) {
        # Create custom object for each setting
        $results += [PSCustomObject]@{
            VMName         = $vm.Name
            SettingName    = $setting.Name
            SettingValue   = $setting.Value
        }
    }
}

# Export results to CSV
$results | Export-Csv -Path $outputFile -NoTypeInformation

# Disconnect from vCenter
Disconnect-VIServer -Confirm:$false

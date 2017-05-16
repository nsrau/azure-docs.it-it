
Per diagnosticare i problemi con un servizio cloud di Microsoft Azure è necessario raccogliere i file di log del servizio nelle macchine virtuali non appena si verificano i problemi. È possibile usare l'estensione AzureLogCollector su richiesta per eseguire una raccolta occasionale di log da una o più macchine virtuali del servizio cloud (da ruoli Web e ruoli di lavoro) e trasferire i file raccolti in un account di archiviazione di Azure, senza accedere in modalità remota ad alcuna macchina virtuale.

> [!NOTE]
> Le descrizioni per la maggior parte delle informazioni registrate sono disponibili all'indirizzo http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.
> 
> 

Sono disponibili due modalità di raccolta a seconda dei tipi di file da raccogliere.

* Solo log di agenti guest di Azure (GA). Questa modalità di raccolta include tutti i log relativi agli agenti guest di Azure e ad altri componenti di Azure.
* Tutti i log (Completa). Questa modalità di raccolta raccoglierà tutti i file in modalità Agenti guest, oltre a:
  
  * log eventi di sistema e dell'applicazione
  * log degli errori HTTP
  * log di IIS
  * log di installazione
  * altri log di sistema

In entrambe le modalità di raccolta, è possibile specificare cartelle di raccolta dati aggiuntive usando una raccolta con la struttura seguente:

* **Nome**: nome della raccolta, che verrà usato come nome della sottocartella all'interno del file ZIP da raccogliere.
* **Percorso**: percorso della cartella nella macchina virtuale in cui verrà raccolto il file.
* **Modello di ricerca**: modello dei nomi di file da raccogliere. Il valore predefinito è "*".
* **Ricorsiva**: se i file verranno raccolti in modo ricorsivo nella cartella.

## <a name="prerequisites"></a>Prerequisiti
* Per poter salvare i file ZIP generati dall'estensione è necessario disporre di un account di archiviazione.
* È necessario assicurarsi che siano in uso i cmdlet di Azure PowerShell v0.8.0 o versioni successive. Per altre informazioni, vedere la pagina relativa ai [Download di Azure](https://azure.microsoft.com/downloads/).

## <a name="add-the-extension"></a>Aggiungere l'estensione
Per aggiungere l'estensione AzureLogCollector, è possibile usare i cmdlet di [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) o le [API REST di gestione dei servizi](https://msdn.microsoft.com/library/ee460799.aspx).

Per i servizi cloud è possibile usare il cmdlet di Azure Powershell esistente, **Set-AzureServiceExtension**, per abilitare l'estensione per le istanze del ruolo servizio cloud. Ogni volta che questa estensione viene abilitata tramite questo cmdlet, viene attivata la raccolta di log nelle istanze del ruolo selezionate dei ruoli specificati.

Per le macchine virtuali è possibile usare il cmdlet di Azure Powershell esistente, **Set-AzureVMExtension**, per abilitare l'estensione per le macchine virtuali. Ogni volta che questa estensione viene abilitata tramite i cmdlet, viene attivata la raccolta di log in ogni istanza.

Internamente, questa estensione usa le configurazioni PublicConfiguration e PrivateConfiguration basate su JSON. Di seguito è riportato il layout del codice JSON di esempio per la configurazione pubblica e privata.

### <a name="publicconfiguration"></a>PublicConfiguration
    {
        "Instances":  "*",
        "Mode":  "Full",
        "SasUri":  "SasUri to your storage account with sp=wl",
        "AdditionalData":
        [
          {
                  "Name":  "StorageData",
                  "Location":  "%roleroot%storage",
                  "SearchPattern":  "*.*",
                  "Recursive":  "true"
          },
          {
                "Name":  "CustomDataFolder2",
                "Location":  "c:\customFolder",
                "SearchPattern":  "*.log",
                "Recursive":  "false"
          },
        ]
    }

### <a name="privateconfiguration"></a>PrivateConfiguration
    {

    }

> [!NOTE]
> Per questa estensione non richiede l'uso della configurazione **privateConfiguration**. È semplicemente possibile specificare una struttura vuota per l'argomento **– PrivateConfiguration** .
> 
> 

È possibile seguire uno dei due passaggi seguenti per aggiungere AzureLogCollector a uno o più istanze di un servizio Cloud o di una macchina virtuale dei ruoli selezionati, che attiva le raccolte in ogni macchina virtuale per eseguire e inviare i file raccolti all'account Azure specificato.

## <a name="adding-as-a-service-extension"></a>Aggiunta come estensione del servizio
1. Seguire le istruzioni per la connessione di Azure PowerShell alla sottoscrizione.
2. Specificare il nome del servizio, lo slot, i ruoli e le istanze del ruolo per cui si vuole aggiungere e abilitare l'estensione AzureLogCollector.
   
        #Specify your cloud service name
        $ServiceName = 'extensiontest2'
   
        #Specify the slot. 'Production' or 'Staging'
        $slot = 'Production'
   
        #Specified the roles on which the extension will be installed and enabled
        $roles = @("WorkerRole1","WebRole1")
   
        #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
        $instances = @("*")
   
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"
3. Specificare la cartella dei dati aggiuntivi per cui verranno raccolti i file (questo passaggio è facoltativo).
   
        #add one location
        $a1 = New-Object PSObject
   
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
   
        $AdditionalDataList+= $a1
              #more locations can be added....
   
   > [!NOTE]
   > È possibile usare token `%roleroot%` per specificare l'unità radice del ruolo, perché non usa un'unità fissa.
   > 
   > 
4. Specificare il nome e la chiave dell'account di archiviazione di Azure in cui verranno caricati i file raccolti.
   
        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
5. Chiamare il cmdlet SetAzureServiceLogCollector.ps1 (incluso alla fine dell'articolo) come indicato di seguito per abilitare l'estensione AzureLogCollector per un servizio cloud. Al termine dell'esecuzione, il file caricato sarà disponibile in `https://YouareStorageAccountName.blob.core.windows.net/vmlogs`
   
        .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList

Di seguito è riportata la definizione dei parametri passati allo script. (Copiati anche sotto.)

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
      [Parameter(Mandatory=$true)]
    [string]   $ServiceName,

    [Parameter(Mandatory=$false)]
    [string[]] $Roles ,

    [Parameter(Mandatory=$false)]
    [string[]] $Instances,

    [Parameter(Mandatory=$false)]
    [string]   $Slot = 'Production',

    [Parameter(Mandatory=$false)]
    [string]   $Mode = 'Full',

    [Parameter(Mandatory=$false)]
    [string]   $StorageAccountName,

    [Parameter(Mandatory=$false)]
    [string]   $StorageAccountKey,

    [Parameter(Mandatory=$false)]
    [PSObject[]] $AdditionDataLocationList = $null
    )

* *ServiceName*: nome del servizio cloud.
* *Roles*: elenco di ruoli, ad esempio "WebRole1" o "WorkerRole1".
* *Istances*: elenco di nomi di istanze del ruolo separati da virgole; usare la stringa con caratteri jolly ("*") per tutte le istanze del ruolo.
* *Slot*: nome dello slot. "Production" o "Staging".
* *Mode*: modalità di raccolta. "Full" o "GA".
* *StorageAccountName*: nome dell'account di archiviazione di Azure per l'archiviazione dei dati raccolti.
* *StorageAccountKey*: nome della chiave dell'account di archiviazione di Azure.
* *AdditionalDataLocationList*: elenco con la struttura seguente:
  
      {
      String Name,
      String Location,
      String SearchPattern,
      Bool   Recursive
      }

## <a name="adding-as-a-vm-extension"></a>Aggiunta come estensione VM
Seguire le istruzioni per la connessione di Azure PowerShell alla sottoscrizione.

1. Specificare il nome del servizio, la macchina virtuale e la modalità di raccolta.
   
        #Specify your cloud service name
        $ServiceName = 'YourCloudServiceName'
   
        #Specify the VM name
        $VMName = "'YourVMName'"
   
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"
   
        Specify the additional data folder for which files will be collected (this step is optional).
   
        #add one location
        $a1 = New-Object PSObject
   
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
   
        $AdditionalDataList+= $a1
              #more locations can be added....
2. Specificare il nome e la chiave dell'account di archiviazione di Azure in cui verranno caricati i file raccolti.
   
        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
3. Chiamare il cmdlet SetAzureVMLogCollector.ps1 (incluso alla fine dell'articolo) come indicato di seguito per abilitare l'estensione AzureLogCollector per un servizio cloud. Al termine dell'esecuzione, è possibile trovare il file caricato in https://NomeAccountArchiviazione.blob.core.windows.net/vmlogs

Di seguito è riportata la definizione dei parametri passati allo script. (Copiati anche sotto.)

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
        [Parameter(Mandatory=$true)]
      [string]   $ServiceName,

      [Parameter(Mandatory=$false)]
      [string] $VMName ,

        [Parameter(Mandatory=$false)]
      [string]   $Mode = 'Full',

      [Parameter(Mandatory=$false)]
      [string]   $StorageAccountName,

      [Parameter(Mandatory=$false)]
      [string]   $StorageAccountKey,

      [Parameter(Mandatory=$false)]
      [PSObject[]] $AdditionDataLocationList = $null
      )

* ServiceName: nome del servizio cloud.
* VMName: nome della macchina virtuale.
* Mode: modalità di raccolta. "Full" o "GA".
* StorageAccountName: nome dell'account di archiviazione di Azure per l'archiviazione dei dati raccolti.
* StorageAccountKey: nome della chiave dell'account di archiviazione di Azure.
* AdditionalDataLocationList: elenco con la struttura seguente:

```
      {
        String Name,
        String Location,
        String SearchPattern,
        Bool   Recursive
      }
```

## <a name="extention-powershell-script-files"></a>File script di PowerShell per l'estensione
SetAzureServiceLogCollector.ps1

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
                  [Parameter(Mandatory=$true)]
                  [string]   $ServiceName,

                  [Parameter(Mandatory=$false)]
                  [string[]] $Roles ,

                  [Parameter(Mandatory=$false)]
                  [string[]] $Instances = '*',

                  [Parameter(Mandatory=$false)]
                  [string]   $Slot = 'Production',

                  [Parameter(Mandatory=$false)]
                  [string]   $Mode = 'Full',

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountName,

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountKey,

                  [Parameter(Mandatory=$false)]
                  [PSObject[]] $AdditionDataLocationList = $null
            )

    $publicConfig = New-Object PSObject

    if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be seperated by ,
    {
        $instanceText = $Instances[0]
        for ($i = 1;$i -lt $Instances.Count;$i++)
        {
              $instanceText = $instanceText+ "," + $Instances[$i]
          }
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
    }
    else  #For all instances if not specified.  The value should be a space or *
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
    }

    if ($Mode -ne $null )
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }

    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }

    $ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }

    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json
    "publicConfig is:  $publicConfigJSON"

    #we just provide a empty privateConfig object
    $privateconfig = "{
    }"

    if ($Roles -ne $null)
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
    }
    else
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
    }

    #
    #This is an optional step: generate a sasUri to the container so it can be shared with other people if nened
    #
    $SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
    $SasUri = $SasUri + "&restype=container&comp=list"
    Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"


SetAzureVMLogCollector.ps1

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
                  [Parameter(Mandatory=$true)]
                  [string]   $ServiceName,

                  [Parameter(Mandatory=$false)]
                  [string] $VMName ,

                  [Parameter(Mandatory=$false)]
                  [string]   $Mode = 'Full',

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountName,

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountKey,

                  [Parameter(Mandatory=$false)]
                  [PSObject[]] $AdditionDataLocationList = $null
            )

    $publicConfig = New-Object PSObject
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

    if ($Mode -ne $null )
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }

    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }

    $ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }

    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json

    Write-Output "PublicConfigurtion is: \r\n$publicConfigJSON"

    #
    #we just provide a empty privateConfig object
    #
    $privateconfig = "{
    }"

    if ($VMName -ne $null )
    {
          $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
          $VM.VM.OSVirtualHardDisk.OS

          if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
          {
                Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

                #
                #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
                #the presence of SubstatusList field.
                #
                $Completed = $false
                while ($Completed -ne $true)
                {
                        $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                        $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                        if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                        {
                            Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                              $Completed = $true
                        }
                        elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                        {
                              $Completed = $false
                              Write-Output "Waiting for operation to complete..."
                        }
                        else
                        {
                              $Completed = $true
                              Write-Output "Operation completed."

                        $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                              $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                      #
                            # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                              #
                              $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                              $ReadSasUri = New-AzureStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                            Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                              #
                              #This is an optional step:  Remove the extension after we are done
                              #
                              Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                        }
                        Start-Sleep -s 5
                }
          }
          else
          {
              Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
          }

    }
    else
    {
      Write-Output "VM name is not specified, the extension cannot be enabled"
    }

## <a name="next-steps"></a>Passaggi successivi
È ora possibile esaminare o copiare i log da una posizione semplificata.


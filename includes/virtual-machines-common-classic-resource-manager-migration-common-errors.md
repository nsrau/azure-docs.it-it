# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>Errori comuni durante la migrazione dalla distribuzione classica ad Azure Resource Manager
Questo articolo descrive gli errori più comuni e le relative soluzioni per agevolare la migrazione delle risorse IaaS dal modello di distribuzione classica di Azure allo stack di Azure Resource Manager.

## <a name="list-of-errors"></a>Elenco degli errori
| Stringa di errore | Mitigazione |
| --- | --- |
| Errore interno del server |In alcuni casi, si tratta di un errore temporaneo che si risolve con un nuovo tentativo. Se l'errore persiste, [contattare il supporto tecnico Azure](../articles/azure-supportability/how-to-create-azure-support-request.md) perché esamini i log della piattaforma. <br><br> **NOTA:** una volta che il team di supporto ha rilevato l'evento imprevisto, non tentare altre soluzioni in autonomia per evitare conseguenze impreviste nell'ambiente. |
| La migrazione non è supportata per la distribuzione {nome-distribuzione} in HostedService {nome-servizio-ospitato} perché si tratta di una distribuzione PaaS (Web/di lavoro). |Ciò si verifica quando una distribuzione contiene un ruolo Web o di lavoro. Dal momento che la migrazione è supportata solo per le macchine virtuali, rimuovere il ruolo Web o di lavoro dalla distribuzione e tentare nuovamente la migrazione. |
| Distribuzione del modello {nome modello} non riuscita. CorrelationId={guid} |Nel back-end del servizio di migrazione, vengono usati i modelli di Azure Resource Manager per creare risorse nello stack di Azure Resource Manager. Dal momento che i modelli sono idempotenti, in genere è possibile ritentare in modo sicuro l'operazione di migrazione per superare l'errore. Se l'errore persiste, [contattare il supporto tecnico Azure](../articles/azure-supportability/how-to-create-azure-support-request.md) e fornire il CorrelationId. <br><br> **NOTA:** una volta che il team di supporto ha rilevato l'evento imprevisto, non tentare altre soluzioni in autonomia per evitare conseguenze impreviste nell'ambiente. |
| La rete virtuale {nome-rete-virtuale} non esiste. |Questa situazione può verificarsi se la rete virtuale è stata creata nel nuovo portale di Azure. Il nome effettivo della rete virtuale segue il modello "Group * <VNET name>" |
| La VM {nome-vm} in HostedService {nome-servizio-ospitato} contiene l'estensione {nome-estensione} non supportata in Azure Resource Manager. È consigliabile disinstallare l'estensione dalla macchina virtuale prima di continuare con la migrazione. |Le estensioni XML, ad esempio BGInfo 1.*, non sono supportate in Azure Resource Manager. Queste estensioni non possono essere migrate. Se queste estensioni non vengono disinstallate dalla macchina virtuale, vengono automaticamente disinstallate prima di completare la migrazione. |
| La VM {nome-vm} in HostedService {nome-servizio-installato} contiene l'estensione VMSnapshot/VMSnapshotLinux, attualmente non supportata per la migrazione. Disinstallare l'estensione dalla macchina virtuale e riaggiungerla tramite Azure Resource Manager al termine della migrazione |Questo è lo scenario in cui la macchina virtuale è configurata per Backup di Azure. Poiché si tratta di uno scenario attualmente non supportato, attenersi alla soluzione in https://aka.ms/vmbackupmigration |
| La VM {nome-vm} in HostedService {nome-servizio-ospitato} contiene l'estensione {nome-estensione} il cui stato non viene segnalato dalla macchina virtuale. Di conseguenza, non è possibile eseguire la migrazione di questa VM. Assicurarsi che venga segnalato lo stato dell'estensione o disinstallare l'estensione dalla macchina virtuale e ripetere la migrazione. <br><br> La VM {nome-vm} in HostedService {nome-servizio-ospitato} contiene l'estensione {nome-estensione} con Stato Handler: {stato-handler}. Di conseguenza, non è possibile eseguire la migrazione della VM. Assicurarsi che lo stato handler dell'estensione segnalato sia {stato-handler} o disinstallare l'estensione dalla macchina virtuale e ripetere la migrazione. <br><br> L'agente VM per la macchina virtuale {nome-vm} in HostedService {nome-servizio-ospitato} indica lo stato generale dell'agente Non pronto. Di conseguenza, se dispone di un'estensione migrabile, la macchina virtuale potrebbe non essere migrata. Assicurarsi che l'agente VM indichi lo stato dell'agente generale Pronto. Fare riferimento a https://aka.ms/classiciaasmigrationfaqs. |L'agente guest di Azure e le estensioni delle macchine virtuali hanno bisogno di accesso Internet in uscita per consentire all'account di archiviazione della macchina virtuale di popolare il proprio stato. Le cause più comuni degli errori di stato includono <li> Un gruppo di sicurezza di rete che blocca l'accesso Inernet in uscita <li> La rete virtuale dispone di server DNS locali e la connettività DNS si perde <br><br> Se si continua a visualizzare lo stato non supportato, è possibile disinstallare le estensioni per ignorare il controllo e procedere con la migrazione. |
| La migrazione non è supportata per la distribuzione {nome-distribuzione} in HostedService {nome-servizio-ospitato} poiché esso dispone di più set di disponibilità. |Attualmente è possibile migrare solo i servizi ospitati che dispongono di un massimo di 1 set di disponibilità. Per risolvere questo problema, spostare il set di disponibilità aggiuntivi e le macchine virtuali di questi set di disponibilità su un servizio ospitato differente. |
| La migrazione non è supportata per la distribuzione {nome-distribuzione} in HostedService {nome-servizio-ospitato} poiché esso dispone di macchine virtuali che non fanno parte del set di disponibilità, anche se HostedService ne contiene uno. |La soluzione alternativa per questo scenario è spostare tutte le macchine virtuali in un unico set di disponibilità o rimuovere tutte le macchine virtuali dal set di disponibilità del servizio ospitato. |
| Account di archiviazione/HostedService/Rete virtuale {nome-rete-virtuale} sono in fase di migrazione e pertanto non possono essere modificati. |Questo errore si verifica quando l'operazione di migrazione "Prepara" è stata completata sulla risorsa e viene attivata un'operazione che apporta una modifica alla risorsa. A causa del blocco sul piano di gestione dopo l'operazione "Prepara", vengono bloccate tutte le modifiche alla risorsa. Per sbloccare il piano di gestione è possibile eseguire l'operazione di migrazione "Esegui commit" per completare la migrazione o l'operazione di migrazione "Interrompi" per ripristinare l'operazione "Prepara". |
| La migrazione non è consentita per HostedService {nome-servizio-ospitato} perché comprende una macchina virtuale {nome-vm} con stato: RoleStateUnknown. La migrazione è consentita solo quando la macchina virtuale si trova in uno dei seguenti stati: In esecuzione, Arrestato, Arrestato deallocato. |La macchina virtuale potrebbe essere sottoposta a una transizione di stato che generalmente si verifica quando durante un'operazione di aggiornamento sul HostedService, ad esempio il riavvio, l'installazione dell'estensione e così via. È consigliabile completare l'operazione di aggiornamento su HostedService prima di tentare la migrazione. |
| La distribuzione {nome-distribuzione} in HostedService {nome-servizio-ospitato} contiene una macchina virtuale {nome-macchina-virtuale} con disco dati {nome-disco-dati} le cui dimensioni BLOB fisiche di {dimensioni-BLOB-disco-rigido-virtuale-per-backup-disco-dati} byte non corrispondono alle dimensioni logiche del disco dati della macchina virtuale di {dimensioni-disco-dati-specificato-in-api-macchina-virtuale} byte. La migrazione viene eseguita senza specificare una dimensione per il disco dati per la macchina virtuale di Azure Resource Manager. Se si desidera correggere le dimensioni del disco dati prima di procedere con la migrazione, visitare il sito Web https://aka.ms/vmdiskresize. | Questo errore si verifica se sono stati ridimensionati i dati BLOB del disco rigido virtuale senza aggiornare le dimensioni nel modello dell'API della macchina virtuale. Procedure di prevenzione dettagliate sono descritte [di seguito](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes).|
| Si è verificata un'eccezione di archiviazione durante la convalida del disco dati {nome del disco dati} con collegamento ai file multimediali {URI disco dati} per la macchina virtuale {nome VM} nel servizio Cloud {nome del servizio Cloud}. Verificare che il collegamento ai file multimediali del disco rigido virtuale sia accessibile per questa macchina virtuale. | Questo errore può verificarsi se i dischi della macchina virtuale sono stati eliminati o non sono più accessibili. Verificare che esistano i dischi per la macchina virtuale.|
| La macchina virtuale {vm-name} nel servizio ospitato {cloud-service-name} contiene un disco che presenta il collegamento multimediale {vhd-uri} e il nome di BLOB {vhd-blob-name} che non è supportato in Azure Resource Manager. | Questo errore si verifica quando il nome del BLOB include un carattere "/", che non è attualmente supportato nel provider di risorse di calcolo. |


## <a name="detailed-mitigations"></a>Soluzioni di prevenzione dettagliate

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>Macchina virtuale con un disco dati le cui dimensioni BLOB fisiche (in byte) non corrispondono alle dimensioni logiche del disco dati della macchina virtuale (in byte).

Ciò si verifica quando le dimensioni logiche del disco dati non sono sincronizzate con le dimensioni BLOB del disco rigido virtuale effettive. Il problema può essere verificato con facilità usando questi comandi:

#### <a name="verifying-the-issue"></a>Verifica del problema

```PowerShell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzureStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>Prevenzione del problema

```PowerShell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Spostare una macchina virtuale in un'altra sottoscrizione dopo il completamento della migrazione

Dopo aver completato il processo di migrazione può essere necessario spostare la macchina virtuale in un'altra sottoscrizione. Se, tuttavia, nella macchina virtuale che fa riferimento alla risorsa insieme di credenziali delle chiavi esiste un segreto/certificato, lo spostamento non è supportato. Le istruzioni seguenti consentono di risolvere il problema. 

#### <a name="powershell"></a>PowerShell
```powershell
$vm = Get-AzureRmVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzureRmVMSecret -VM $vm
Update-AzureRmVM -ResourceGroupName "MyRG" -VM $vm
```
#### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

```bash
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```

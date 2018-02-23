## <a name="overview"></a>Panoramica
Quando si crea una nuova macchina virtuale (VM) in un gruppo di risorse distribuendo un'immagine da [Azure Marketplace](https://azure.microsoft.com/marketplace/), l'unità del sistema operativo predefinita è spesso di 127 GB. Per impostazione predefinita. alcune immagini hanno dimensioni minori per il disco del sistema operativo. Anche se è possibile aggiungere dischi dati alla VM (il numero dipende dalla SKU scelta) ed è consigliabile installare applicazioni e carichi di lavoro con uso intensivo della CPU su tali dischi aggiuntivi, spesso i clienti devono espandere l'unità del sistema operativo per supportare determinati scenari, come i seguenti:

1. Supporto di applicazioni legacy che installano componenti nell'unità del sistema operativo.
2. Migrazione di un computer fisico o di una macchina virtuale locali a un'unità del sistema operativo più grande.

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Gestione risorse e la distribuzione classica. Questo articolo illustra l'uso del modello di Gestione risorse. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.
> 
> 
> [!WARNING]
> Il ridimensionamento del disco del sistema operativo di una macchina virtuale di Azure ne determinerà il riavvio.
>

## <a name="resize-the-os-drive"></a>Ridimensionare l'unità del sistema operativo
In questo articolo verrà eseguito il ridimensionamento dell'unità del sistema operativo usando i moduli di Gestione risorse di [Azure Powershell](/powershell/azureps-cmdlets-docs). Verrà illustrato il ridimensionamento dell'unità del sistema operativo sia per i dischi non gestiti che per i dischi gestiti, poiché l'approccio è diverso.

### <a name="for-resizing-unmanaged-disks"></a>Per il ridimensionamento dei dischi non gestiti:

Aprire la finestra di Powershell ISE o Powershell in modalità amministrativa e seguire questa procedura:

1. Accedere all'account Microsoft Azure in modalità Gestione risorse e selezionare la sottoscrizione come segue:
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Impostare il nome del gruppo di risorse e il nome della VM come segue:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Ottenere un riferimento alla VM come segue:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Arrestare la VM prima di ridimensionare il disco come segue:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. A questo punto, impostare le dimensioni del disco del sistema operativo non gestito sul valore desiderato e aggiornare la VM come segue:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito per i dischi del sistema operativo è 2048 GB. È possibile espandere il BLOB del disco rigido virtuale oltre tale dimensione, ma il sistema operativo potrà usare solo i primi 2048 GB di spazio.
   > 
   > 
6. L'aggiornamento della VM potrebbe richiedere alcuni secondi. Al termine dell'esecuzione del comando, riavviare la VM come segue:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>Per il ridimensionamento dei dischi gestiti:

Aprire la finestra di Powershell ISE o Powershell in modalità amministrativa e seguire questa procedura:

1. Accedere all'account Microsoft Azure in modalità Gestione risorse e selezionare la sottoscrizione come segue:
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Impostare il nome del gruppo di risorse e il nome della VM come segue:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Ottenere un riferimento alla VM come segue:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Arrestare la VM prima di ridimensionare il disco come segue:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Ottenere un riferimento al disco del sistema operativo gestito. Impostare le dimensioni del disco del sistema operativo gestito sul valore desiderato e aggiornare il disco come segue:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito per i dischi del sistema operativo è 2048 GB. È possibile espandere il BLOB del disco rigido virtuale oltre tale dimensione, ma il sistema operativo potrà usare solo i primi 2048 GB di spazio.
   > 
   > 
6. L'aggiornamento della VM potrebbe richiedere alcuni secondi. Al termine dell'esecuzione del comando, riavviare la VM come segue:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

L'operazione è terminata. Con il protocollo RDP applicato alla VM, aprire Gestione computer (o Gestione disco) ed espandere l'unità usando lo spazio appena allocato.

## <a name="summary"></a>Summary
In questo articolo sono stati usati i moduli Azure Resource Manager di Powershell per espandere l'unità del sistema operativo di una macchina virtuale IaaS. Per riferimento, di seguito è riportato lo script completo per i dischi non gestiti e gestiti:

Dischi non gestiti:

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
Dischi gestiti:

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>Passaggi successivi
Sebbene questo articolo si concentri principalmente sull'espansione del disco del sistema operativo gestito/non gestito della VM, lo script sviluppato potrebbe essere usato anche per l'espansione dei dischi dati collegati alla VM. Per espandere ad esempio il primo disco dati collegato alla VM, sostituire l'oggetto ```OSDisk``` di ```StorageProfile``` con la matrice ```DataDisks``` e usare un indice numerico per ottenere un riferimento al primo disco dati collegato, come illustrato di seguito:

Disco non gestito:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Disco gestito:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

Analogamente è possibile fare riferimento agli altri dischi dati collegati alla VM usando un indice come quello illustrato in precedenza oppure la proprietà ```Name``` del disco, come illustrato di seguito:

Disco non gestito:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Disco gestito:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Per informazioni su come collegare i dischi a una VM di Azure Resource Manager, consultare questo [articolo](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


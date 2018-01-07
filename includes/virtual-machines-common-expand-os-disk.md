## <a name="overview"></a>Panoramica
Quando si crea una nuova macchina virtuale (VM) in un gruppo di risorse distribuendo un'immagine da [Azure Marketplace](https://azure.microsoft.com/marketplace/), l'unità del sistema operativo predefinita è spesso di 127 GB. Per impostazione predefinita. alcune immagini hanno dimensioni minori per il disco del sistema operativo. Anche se è possibile aggiungere dischi dati alla VM (il numero dipende dalla SKU scelta) ed è consigliabile installare applicazioni e carichi di lavoro con uso intensivo della CPU su tali dischi aggiuntivi, spesso i clienti devono espandere l'unità del sistema operativo per supportare determinati scenari, come i seguenti:

1. Supporto di applicazioni legacy che installano componenti nell'unità del sistema operativo.
2. Migrazione di un computer fisico o di una macchina virtuale locali a un'unità del sistema operativo più grande.

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Gestione risorse e la distribuzione classica. Questo articolo illustra l'uso del modello di Gestione risorse. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.
> 
> 

## <a name="resize-the-os-drive"></a>Ridimensionare l'unità del sistema operativo
In questo articolo verrà eseguito il ridimensionamento dell'unità del sistema operativo usando i moduli di Gestione risorse di [Azure Powershell](/powershell/azureps-cmdlets-docs). Viene illustrato il ridimensionamento delle unità del sistema operativo per i dischi Unamanged sia gestito poiché l'approccio per ridimensionare i dischi differisce tra entrambi i tipi di disco.

### <a name="for-resizing-unmanaged-disks"></a>Per il ridimensionamento dei dischi non gestita:

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
5. A questo punto, Impostare le dimensioni del disco del sistema operativo non gestito per il valore desiderato e aggiornare la macchina virtuale come indicato di seguito:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito è di 2048 GB per i dischi del sistema operativo. È possibile espandere il BLOB del disco rigido virtuale oltre tale dimensione, ma il sistema operativo potrà usare solo i primi 2048 GB di spazio.
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
5. Ottenere un riferimento al disco del sistema operativo gestito. Impostare le dimensioni del disco del sistema operativo gestito sul valore desiderato e aggiornare il disco come indicato di seguito:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito è di 2048 GB per i dischi del sistema operativo. È possibile espandere il BLOB del disco rigido virtuale oltre tale dimensione, ma il sistema operativo potrà usare solo i primi 2048 GB di spazio.
   > 
   > 
6. L'aggiornamento della VM potrebbe richiedere alcuni secondi. Al termine dell'esecuzione del comando, riavviare la VM come segue:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

L'operazione è terminata. Con il protocollo RDP applicato alla VM, aprire Gestione computer (o Gestione disco) ed espandere l'unità usando lo spazio appena allocato.

## <a name="summary"></a>Summary
In questo articolo sono stati usati i moduli Azure Resource Manager di Powershell per espandere l'unità del sistema operativo di una macchina virtuale IaaS. Riprodotta sotto è lo script completo per il riferimento per i dischi non gestito e gestito:

Dischi Unamanged:

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

## <a name="next-steps"></a>Fasi successive
Sebbene sia in questo articolo è incentrato principalmente su l'espansione del disco del sistema operativo Unamanged/gestito della macchina virtuale, di script sviluppato può essere usato anche per l'espansione di dischi dati collegati alla macchina virtuale. Per espandere ad esempio il primo disco dati collegato alla VM, sostituire l'oggetto ```OSDisk``` di ```StorageProfile``` con la matrice ```DataDisks``` e usare un indice numerico per ottenere un riferimento al primo disco dati collegato, come illustrato di seguito:

Disco Unamanged:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Disco gestito:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

Analogamente è possibile fare riferimento agli altri dischi dati collegati alla VM usando un indice come quello illustrato in precedenza oppure la proprietà ```Name``` del disco, come illustrato di seguito:

Disco Unamanged:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Disco gestito:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Per informazioni su come collegare i dischi a una VM di Azure Resource Manager, consultare questo [articolo](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


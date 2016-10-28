## Panoramica
Quando si crea una nuova macchina virtuale (VM) in un gruppo di risorse distribuendo un'immagine da [Azure Marketplace](https://azure.microsoft.com/marketplace/), l'unità del sistema operativo predefinita è di 127 GB. Anche se è possibile aggiungere dischi dati alla VM (il numero dipende dalla SKU scelta) ed è consigliabile installare applicazioni e carichi di lavoro con uso intensivo della CPU su tali dischi aggiuntivi, spesso i clienti devono espandere l'unità del sistema operativo per supportare determinati scenari, come i seguenti:

1.  Supporto di applicazioni legacy che installano componenti nell'unità del sistema operativo.
2.  Migrazione di un computer fisico o di una macchina virtuale locali a un'unità del sistema operativo più grande.

>[AZURE.IMPORTANT]Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Gestione risorse e la distribuzione classica. Questo articolo illustra l'uso del modello di Gestione risorse. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

## Ridimensionare l'unità del sistema operativo
In questo articolo verrà eseguito il ridimensionamento dell'unità del sistema operativo usando i moduli di Gestione risorse di [Azure Powershell](../articles/powershell-install-configure.md). Aprire la finestra di Powershell ISE o Powershell in modalità amministrativa e seguire questa procedura:

1.  Accedere all'account Microsoft Azure in modalità Gestione risorse e selezionare la sottoscrizione come segue:

    ```Powershell
    Login-AzureRmAccount
    Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
    ```

2.  Impostare il nome del gruppo di risorse e il nome della VM come segue:

    ```Powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3.  Ottenere un riferimento alla VM come segue:

    ```Powershell
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Arrestare la VM prima di ridimensionare il disco come segue:

    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```

5.  A questo punto, impostare le dimensioni del disco del sistema operativo sul valore desiderato e aggiornare la VM come segue:

    ```Powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    ```

    >[AZURE.WARNING]Le nuove dimensioni devono essere maggiori delle dimensioni del disco esistente. Il valore massimo consentito è 1023 GB.

6.  L'aggiornamento della VM potrebbe richiedere alcuni secondi. Al termine dell'esecuzione del comando, riavviare la VM come segue:

    ```Powershell
    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```

L'operazione è terminata. Con il protocollo RDP applicato alla VM, aprire Gestione computer (o Gestione disco) ed espandere l'unità usando lo spazio appena allocato.

## Riepilogo
In questo articolo sono stati usati i moduli Azure Resource Manager di Powershell per espandere l'unità del sistema operativo di una macchina virtuale IaaS. Di seguito è riportato lo script completo per riferimento:

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

## Passaggi successivi
Sebbene questo articolo si concentri principalmente sull'espansione del disco del sistema operativo della VM, lo script sviluppato potrebbe essere usato anche per l'espansione dei dischi dati collegati alla VM modificando una singola riga di codice. Ad esempio, per espandere il primo disco dati collegato alla VM, sostituire l'oggetto ```OSDisk``` di ```StorageProfile``` con la matrice ```DataDisks``` e usare un indice numerico per ottenere un riferimento al primo disco dati collegato, come illustrato di seguito:

```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Analogamente è possibile fare riferimento agli altri dischi dati collegati alla VM usando un indice come quello illustrato in precedenza oppure la proprietà ```Name``` del disco, come illustrato di seguito:

```Powershell
($vm.StorageProfile.DataDisks | Where {$_.Name -eq 'my-second-data-disk'})[0].DiskSizeGB = 1023
```

Per informazioni su come collegare i dischi a una VM di Azure Resource Manager, consultare questo [articolo](../articles/virtual-machines/virtual-machines-windows-attach-disk-portal.md).

<!---HONumber=AcomDC_0323_2016-->
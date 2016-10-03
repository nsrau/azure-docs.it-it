<properties
	pageTitle="Distribuire un'applicazione nei set di scalabilità delle macchine virtuali | Microsoft Azure"
	description="Distribuire un'applicazione nei set di scalabilità delle macchine virtuali"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/> 

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="guybo"/> 


# Aggiornare un set di scalabilità di macchine virtuali

Questo articolo descrive come eseguire un aggiornamento del sistema operativo a un set di scalabilità di VM di Azure senza tempi di inattività. In questo contesto, un aggiornamento del sistema operativo sta modificando la versione/SKU del sistema operativo oppure l'URI di un'immagine personalizzata. L'aggiornamento senza tempi di inattività implica l'aggiornamento di una VM alla volta o delle VM in gruppi, ad esempio un dominio di errore alla volta, anziché contemporaneamente. In questo modo è possibile mantenere in esecuzione tutte le VM non in fase di aggiornamento.

Per evitare ambiguità, si distinguono tre tipi di aggiornamento del sistema operativo che è possibile eseguire:

1. La modifica della versione o della SKU di un'immagine della piattaforma. Ad esempio la modifica della versione Ubuntu 14.04.2-LTS da 14.04.201506100 a 14.04.201507060 o la modifica della SKU Ubuntu 15.10/più recente a 16.04.0-LTS/più recente. Questo scenario è illustrato in questo articolo.

2. È stata compilata una nuova versione dell'immagine personalizzata e si vuole modificare l'URI che punta all'immagine (properties->virtualMachineProfile->storageProfile->osDisk->image->uri). Questo scenario è illustrato in questo articolo.

3. L'applicazione di patch del sistema operativo all'interno di una VM, ad esempio installazione di una patch di protezione, l'uso di Windows Update e così via. Questo scenario è supportato ma non è trattato in questo articolo.

I primi 2 elementi sono requisiti supportati. Per il terzo, almeno per il momento, è necessario creare un nuovo set di scalabilità per questo scopo. Questo articolo descrive le opzioni 1 e 2. Nota: i set di scalabilità delle VM che vengono distribuiti come parte di un cluster [Service Fabric](https://azure.microsoft.com/services/service-fabric/) non sono trattati qui.

La sequenza di base per la modifica della versione/SKU del sistema operativo di un'immagine della piattaforma o dell'URI di un'immagine personalizzata è simile alla seguente:

* Ottenere il modello del set di scalabilità delle VM.

* Modificare la versione, la SKU o il valore dell'URI del modello.

* Aggiornare il modello.

* Eseguire una chiamata manualUpgrade sulle VM nel set di scalabilità. Questo passaggio è applicabile solo se la proprietà upgradePolicy del set di scalabilità è impostata su "Manuale". Se è impostata su "Automatico", tutte le VM vengono aggiornate contemporaneamente, con consequente tempo di inattività.


Tenendo conto di queste informazioni generali, esaminiamo come è possibile aggiornare la versione di un set di scalabilità in PowerShell e tramite l'API REST. Questi esempi riguardano il caso di un'immagine di piattaforma, ma nella migliore delle ipotesi le informazioni fornite saranno sufficienti ad adattare il processo per un'immagine personalizzata.

## PowerShell

Questo esempio aggiorna un set di scalabilità di una VM Windows a una nuova versione "4.0.20160229". Dopo l'aggiornamento del modello viene eseguito un aggiornamento di un'istanza della VM alla volta.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the VMSS model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Nel caso di un aggiornamento dell'URI per un'immagine personalizzata anziché della modifica di una versione di immagine della piattaforma, è possibile sostituire la riga "set the new version" con un elemento simile al seguente:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## Con un'API REST

Di seguito sono riportati due esempi di Python che usano l'API REST di Azure per implementare un aggiornamento di versione del sistema operativo. Entrambi usano la libreria [azurerm](https://pypi.python.org/pypi/azurerm) semplificata della funzione wrapper dell'API REST di Azure per eseguire un'operazione GET sul modello del set di scalabilità, quindi un'operazione PUT con un modello aggiornato. Gli esempi esaminano anche le viste delle istanze di VM per l'individuazione delle VM tramite il dominio di aggiornamento.

### vmssupgrade

vmssupgrade è uno script Python utile per implementare un aggiornamento del sistema operativo per un set di scalabilità di VM in esecuzione, un dominio di aggiornamento alla volta. È disponibile [qui](https://github.com/gbowerman/vmsstools).

![schermata di vmssupgrade](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png) 

Questo script consente di scegliere specifiche VM da aggiornare o di specificare un dominio di aggiornamento e supporta la modifica di una versione di immagine della piattaforma o dell'URI di un'immagine personalizzata.

### vmsseditor

Questo strumento è un editor generico per i set di scalabilità di VM che mostra lo stato della VM come heatmap in cui una riga rappresenta un dominio di aggiornamento. Tra le altre cose, è possibile aggiornare il modello per un set di scalabilità di macchine virtuali con una nuova versione, SKU o URI dell'immagine personalizzata e quindi selezionare i domini di errore da aggiornare. A questo scopo, tutte le VM in questo dominio di aggiornamento vengono aggiornate al nuovo modello. In alternativa, è possibile eseguire un aggiornamento in sequenza in base alla dimensione di batch scelta. vmsseditor è disponibile nel seguente [repository github](https://github.com/gbowerman/vmssdashboard)

Ad esempio, qui è stato aggiornato il modello di un set di scalabilità a Ubuntu 14.04-2LTS versione 14.04.201507060. Si noti che questa schermata non è aggiornata poiché molte opzioni sono state aggiunte a questo strumento.

![schermata 1 di vmsseditor](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png) 

Dopo avere selezionato l'aggiornamento e quindi ottenuto i dettagli, inizia l'aggiornamento delle VM nel dominio di aggiornamento 0.

![schermata 2 di vmsseditor](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png) 

<!---HONumber=AcomDC_0921_2016-->
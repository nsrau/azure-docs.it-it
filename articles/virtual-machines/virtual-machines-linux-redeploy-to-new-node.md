<properties 
	pageTitle="Ridistribuzione della macchine virtuali di Linux | Microsoft Azure" 
	description="Descrive come ridistribuire le macchine virtuali di Linux per mitigare i problemi di connessione SSH." 
	services="virtual-machines-linux" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/>  
	

<tags 
	ms.service="virtual-machines-linux" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure" 
	ms.date="09/19/2016" 
	ms.author="iainfou" 
/>  

# Ridistribuzione della macchina virtuale su un nuovo nodo di Azure

Se si stanno riscontrando difficoltà nella risoluzione dei problemi relativi a SSH o all'accesso delle applicazioni a una macchina virtuale (VM) di Azure, potrebbe essere utile la ridistribuzione. Quando si ridistribuisce una VM, quest'ultima viene spostata su un nuovo nodo dell'infrastruttura di Azure, quindi viene riattivata conservando tutte le opzioni di configurazione e le risorse associate. In questo articolo viene illustrato come ridistribuire una VM con l'interfaccia della riga di comando di Azure o il portale di Azure.

> [AZURE.NOTE] Dopo la ridistribuzione di una VM, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.


## Utilizzare l'interfaccia della riga di comando di Azure

Assicurarsi che sulla macchina [sia installata la versione più recente dell'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e di trovarsi in modalità Resource Manager (`azure config mode arm`).

Usare questo comando dell'interfaccia della riga di comando di Azure per ridistribuire la macchina virtuale:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Si può notare che lo stato della VM cambia in base alla fase della procedura di ridistribuzione. `PowerState` della VM passa da "In esecuzione" ad "Aggiornamento in corso", poi ad "Avvio in corso" per tornare a "In esecuzione" in base alla fase della procedura di ridistribuzione di un nuovo host. Verificare lo stato delle VM in un gruppo di risorse con:

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## Passaggi successivi
In caso di problemi di connessione alla VM, è possibile trovare assistenza specifica sulla [risoluzione dei problemi relativi alle connessioni SSH](virtual-machines-linux-troubleshoot-ssh-connection.md) o sui [passaggi dettagliati per la risoluzione dei problemi SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md). Se non si riesce ad accedere a un'applicazione in esecuzione sulla VM, è possibile leggere l'articolo sulle [difficoltà nella risoluzione dei problemi relativi alle applicazioni](virtual-machines-linux-troubleshoot-app-connection.md).

<!---HONumber=AcomDC_0921_2016-->
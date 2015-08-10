<properties
	pageTitle="Creare una macchina virtuale personalizzata che esegue Linux in Azure"
	description="Informazioni su come reare una macchina virtuale personalizzata che esegue Linux in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="dkshir"/>

# Come creare una macchina virtuale personalizzata su cui è in esecuzione Linux in Azure

In questo argomento viene descritto come creare una macchina virtuale *personalizzata* tramite l’interfaccia della riga di comando di Azure per Mac, Linux e Windows in modalità Gestione servizi di Azure. Verrà utilizzata un'immagine Linux dalle **IMMAGINI** disponibili in Azure. I comandi dell’interfaccia della riga di comando di Azure offrono le opzioni di configurazione seguenti:

- Connessione della macchina virtuale a una rete virtuale
- Aggiunta della macchina virtuale a un servizio cloud esistente
- Aggiunta della macchina virtuale a un account di archiviazione esistente
- Aggiunta della macchina virtuale a un set di disponibilità o a un percorso

> [AZURE.IMPORTANT]se si vuole usare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=294063).

<p/>
[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Creare una macchina virtuale che esegue Linux](virtual-machines-linux-tutorial.md)


## Come creare una macchina virtuale Linux con Gestione servizi di Azure

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-LinuxVM.md)]

<!---HONumber=July15_HO5-->
<properties
	pageTitle="Creare una macchina virtuale Linux | Microsoft Azure"
	description="Come creare una macchina virtuale personalizzata con il modello di distribuzione classico che esegue il sistema operativo Linux."
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
	ms.date="10/14/2015"
	ms.author="dkshir"/>

# Come creare una macchina virtuale Linux personalizzata

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] []Resource Manager model](virtual-machines-linux-tutorial.md).

Questo argomento descrive come creare una macchina virtuale *personalizzata* con l’interfaccia della riga di comando di Azure usando il modello di distribuzione classico. Verrà utilizzata un'immagine Linux dalle **IMMAGINI** disponibili in Azure. I comandi dell’interfaccia della riga di comando di Azure offrono le opzioni di configurazione seguenti:

- Connessione della macchina virtuale a una rete virtuale
- Aggiunta della macchina virtuale a un servizio cloud esistente
- Aggiunta della macchina virtuale a un account di archiviazione esistente
- Aggiunta della macchina virtuale a un set di disponibilità o a un percorso

> [AZURE.IMPORTANT]se si vuole usare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=294063).


## Come creare una macchina virtuale Linux usando il modello di distribuzione classico

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!---HONumber=Oct15_HO4-->
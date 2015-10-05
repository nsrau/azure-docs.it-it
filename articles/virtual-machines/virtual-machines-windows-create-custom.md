<properties
	pageTitle="Creare una macchina virtuale che esegue Windows | Microsoft Azure"
	description="Come creare una macchina virtuale personalizzata su cui è in esecuzione Linux in Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

#Creare una macchina virtuale che esegue Windows in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione di una risorsa con il modello di distribuzione classica.

Una macchina virtuale *personalizzata* indica semplicemente una macchina virtuale creata usando l’opzione **Da raccolta**, che offre più possibilità di configurazione rispetto a **Creazione rapida**. Tali opzioni includono:

- Collegare una macchina virtuale a Rete virtuale.
- Installazione dell'agente e delle estensioni della macchina virtuale, come per il software antimalware.
- Aggiunta della macchina virtuale a un servizio cloud esistente.
- Aggiunta della macchina virtuale a un account di archiviazione esistente.
- Aggiunta della macchina virtuale a un set di disponibilità.

> [AZURE.IMPORTANT]Se si vuole usare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale](virtual-networks-overview.md).

##Per creare la macchina virtuale

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=Sept15_HO4-->
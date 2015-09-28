<properties
	pageTitle="Creare una macchina virtuale personalizzata in Azure"
	description="Informazioni su come creare una macchina virtuale personalizzata in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

#Come creare una macchina virtuale personalizzata

Una macchina virtuale *personalizzata* indica semplicemente una macchina virtuale creata usando l’opzione **Da raccolta**, che offre più possibilità di configurazione rispetto a **Creazione rapida**. Tali opzioni includono:

- Collegare una macchina virtuale a Rete virtuale.
- Installare l'agente di macchina virtuale di Azure e le estensioni di macchina virtuale di Azure, ad esempio per antimalware.
- Aggiungere la macchina virtuale ai servizi cloud esistenti.
- Aggiungere la macchina virtuale a un account di archiviazione esistente.
- Aggiungere una macchina virtuale a un set di disponibilità.

> [AZURE.IMPORTANT]Se si vuole che la macchina virtuale usi una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale](virtual-networks-overview.md).

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=Sept15_HO3-->
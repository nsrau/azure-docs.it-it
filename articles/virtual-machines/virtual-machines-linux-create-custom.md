<properties 
	pageTitle="Creare una macchina virtuale personalizzata che esegue Linux in Azure" 
	description="Informazioni su come reare una macchina virtuale personalizzata che esegue Linux in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/23/2015" 
	ms.author="kathydav"/>

#Come creare una macchina virtuale personalizzata su cui è in esecuzione Linux in Azure

Una macchina virtuale *personalizzata* indica semplicemente una macchina virtuale creata usando l’opzione **Da raccolta**, che offre più possibilità di configurazione rispetto a **Creazione rapida**. Tali opzioni includono:

- Connessione della macchina virtuale a una rete virtuale
- Installazione dell'agente VM e di estensioni, ad esempio di tipo antimalware 
- Aggiunta della macchina virtuale a un servizio cloud esistente
- Aggiunta della macchina virtuale a un account di archiviazione esistente
- Aggiunta della macchina virtuale a un set di disponibilità

> [AZURE.IMPORTANT]se si vuole usare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=294063).

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-LinuxVM.md)]


 

<!---HONumber=July15_HO2-->
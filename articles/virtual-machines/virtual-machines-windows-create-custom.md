<properties
	pageTitle="Creare una macchina virtuale che esegue Windows in Azure"
	description="Come creare una macchina virtuale personalizzata su cui è in esecuzione Linux in Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="kathydav"/>

#Creare una macchina virtuale che esegue Windows in Azure

Una macchina virtuale *personalizzata* indica semplicemente una macchina virtuale creata utilizzando l'opzione **Da raccolta**, che offre più possibilità di configurazione rispetto all'opzione **Creazione rapida**. Tali opzioni includono:

- Connessione della macchina virtuale a una rete virtuale.
- Installazione dell'agente e delle estensioni della macchina virtuale, come per il software antimalware.
- Aggiunta della macchina virtuale a un servizio cloud esistente.
- Aggiunta della macchina virtuale a un account di archiviazione esistente.
- Aggiunta della macchina virtuale a un set di disponibilità.

> [AZURE.IMPORTANT]Se si desidera che la macchina virtuale utilizzi una rete virtuale in modo da poter effettuare la connessione direttamente tramite il nome host o configurare connessioni cross-premise, assicurarsi di specificare la rete virtuale quando viene creata la macchina virtuale. Una macchina virtuale può essere configurata per accedere a una rete virtuale solo quando viene creata la macchina virtuale. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=294063).

##Per creare la macchina virtuale

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

<!---HONumber=July15_HO4-->
<properties urlDisplayName="Create a custom VM" pageTitle=" Creare una macchina virtuale personalizzata in Azure" metaKeywords="Azure custom vm, creating custom vm" description="Informazioni su come creare una macchina virtuale personalizzata in Azure." metaCanonical="http://www.windowsazure.com/it-it/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav" />

#Come creare una macchina virtuale personalizzata

Una macchina virtuale *personalizzata* indica semplicemente una macchina virtuale creata usando **Da raccolta**, che offre più opzioni di configurazione rispetto a **Creazione rapida**. Tali opzioni includono:

- Connessione della macchina virtuale a una rete virtuale
- Installazione dell'agente VM e di estensioni, ad esempio di tipo antimalware 
- Aggiunta della macchina virtuale a un servizio cloud esistente 
- Aggiunta della macchina virtuale a un set di disponibilità

**Importante**: se si vuole usare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=294063).

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]



<!--HONumber=35.1-->

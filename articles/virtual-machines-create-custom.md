<properties linkid="manage-linux-howto-custom-create-vm" urlDisplayName="Create a custom VM" pageTitle=" Create a custom virtual machine running Linux in Azure" metaKeywords="Azure custom vm, creating custom vm" description="Learn how to create a custom virtual machine in Azure." metaCanonical="http://www.windowsazure.com/it-it/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Come creare una macchina virtuale personalizzata

Una macchina virtuale *personalizzata* è una macchina virtuale creata mediante **Da raccolta**, che offre più opzioni di configurazione rispetto a **Creazione rapida**. Ad esempio:

-   Numero maggior di opzioni di immagini per la creazione della macchina virtuale (VM)
-   Connessione della macchina virtuale a una rete virtuale
-   Installazione dell'agente VM e di estensioni, ad esempio di tipo antimalware
-   Aggiunta della macchina virtuale a un servizio cloud esistente
-   Aggiunta della macchina virtuale a un set di disponibilità

**Importante**: se si desidera utilizzare una rete virtuale in modo da connettersi direttamente mediante nome host o per configurare connessioni cross-premise, accertarsi di specificare la rete virtuale quando si crea la macchina virtuale. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale][Panoramica di Rete virtuale].

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

  [Panoramica di Rete virtuale]: http://go.microsoft.com/fwlink/p/?LinkID=294063

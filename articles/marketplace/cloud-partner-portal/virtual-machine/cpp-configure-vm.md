---
title: Configurare la macchina virtuale ospitata in Microsoft Azure per Azure Marketplace | Microsoft Docs
description: Illustra come ridimensionare, aggiornare e generalizzare una macchina virtuale ospitata in Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c1db8c99b1bd3f9bbb768572ca1f5f7a4e1e0de4
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639148"
---
# <a name="configure-the-azure-hosted-vm"></a>Configurare la macchina virtuale ospitata in Azure

Questo articolo illustra come ridimensionare, aggiornare e generalizzare una macchina virtuale (VM, Virtual Machine) ospitata in Azure.  Questa procedura è necessaria per preparare la macchina virtuale alla distribuzione da Azure Marketplace.


## <a name="sizing-the-vhds"></a>Ridimensionamento dei dischi rigidi virtuali

<!--TD: Check if the following assertion is true. I didn't understand the original content. --> Se è stata selezionata una delle macchine virtuali preconfigurate con un sistema operativo (e, facoltativamente, se sono stati selezionati altri servizi), è già stata scelta una dimensione di macchina virtuale di Azure standard, come descritto nella [scheda SKU della macchina virtuale](./cpp-skus-tab.md).  Questo è l'approccio consigliato.  Se tuttavia si intende installare un sistema operativo manualmente, è necessario ridimensionare il disco rigido virtuale principale nell'immagine di macchina virtuale:

- Per Windows, il disco rigido virtuale del sistema operativo deve essere creato come disco rigido virtuale con formato fisso da 127-128 GB. 
- Per Linux, questo disco rigido virtuale deve essere creato come disco rigido virtuale con formato fisso da 30-50 GB.

Se la dimensione fisica è inferiore a 127-128 GB, il disco rigido virtuale deve essere di tipo sparse. Le immagini Windows e SQL Server di base fornite soddisfano già questi requisiti. Evitare di modificare il formato o la dimensione del VHD ottenuto. 

I dischi dati possono avere dimensioni fino a 1 TB. Nello stabilire le dimensioni dei dischi, tenere presente che i clienti non possono ridimensionare i VHD all'interno di un'immagine in fase di distribuzione. I dischi rigidi virtuali per i dischi dati devono essere creati come dischi rigidi virtuali con formato fisso, ma devono anche essere di tipo sparse. I dischi dati inizialmente possono essere vuoti o contenere dati.


## <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

Le immagini di base delle macchine virtuali del sistema operativo contengono gli aggiornamenti più recenti fino alla data di pubblicazione. Prima di pubblicare il disco rigido virtuale del sistema operativo creato, assicurarsi di aggiornare il sistema operativo e tutti i servizi installati con tutte le patch di sicurezza e manutenzione più recenti.

Per Windows Server 2016, eseguire il comando **Controlla aggiornamenti**.  Per versioni di Windows precedenti, vedere [Come ottenere un aggiornamento tramite Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows Update installerà automaticamente gli aggiornamenti di sicurezza critici e importanti più recenti.

Per le distribuzioni Linux, gli aggiornamenti vengono in genere scaricati e installati tramite uno strumento da riga di comando o un'utilità grafica.  Ubuntu Linux, ad esempio, per l'aggiornamento del sistema operativo mette a disposizione il comando [apt-get](http://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e lo strumento [Update Manager](http://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html).


## <a name="perform-additional-security-checks"></a>Eseguire controlli di sicurezza aggiuntivi

È necessario mantenere un livello di sicurezza elevato per le immagini delle soluzioni in Azure Marketplace.  L'articolo [Raccomandazioni sulla sicurezza per le immagini di Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images) semplifica questo obiettivo offrendo un elenco di configurazioni e procedure di sicurezza a cui attenersi.  Alcune di queste indicazioni sono destinate specificamente a immagini basate su Linux, ma la maggior parte si applica a qualsiasi immagine di macchina virtuale. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Eseguire una configurazione personalizzata e attività pianificate

Se si rendono necessarie configurazioni aggiuntive, l'approccio consigliato consiste nell'usare un'attività pianificata da eseguire all'avvio per apportare eventuali modifiche finali alla macchina virtuale dopo la distribuzione.  Tenere anche conto dei consigli seguenti:
- Per le attività da eseguire una sola volta, è consigliabile eliminare l'attività automaticamente dopo il completamento.
- Evitare di eseguire configurazioni su unità diverse da C o D, perché queste sono le uniche due unità la cui presenza è sempre garantita. L'unità C corrisponde al disco del sistema operativo e l'unità D al disco locale temporaneo.

Per altre informazioni sulle personalizzazioni di Linux, vedere [Estensioni della macchina virtuale e funzionalità per Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Generalizzare l'immagine

Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. Per ottenere questa riusabilità, il disco rigido virtuale del sistema operativo deve essere *generalizzato*. La generalizzazione è un'operazione che rimuove tutti gli identificatori specifici di istanze e tutti i driver software da una macchina virtuale.

### <a name="windows"></a>Windows

La generalizzazione dei dischi con sistema operativo Windows viene eseguita con lo [strumento sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se in seguito si aggiorna o si riconfigura il sistema operativo, è necessario eseguire nuovamente sysprep. 

> [!WARNING]
>  Poiché gli aggiornamenti possono essere eseguiti automaticamente, dopo aver eseguito sysprep, è necessario arrestare la macchina virtuale finché non viene distribuita.  Questo arresto eviterà che gli aggiornamenti successivi possano apportare modifiche specifiche di istanza al sistema operativo o ai servizi installati nel disco rigido virtuale.

Per altre informazioni sull'esecuzione di sysprep, vedere [Procedura per generalizzare un disco rigido virtuale] (https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd)

### <a name="linux"></a>Linux

Il processo in due passaggi seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata.  Per altre informazioni, vedere [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../../../virtual-machines/linux/capture-image.md). 

#### <a name="remove-the-azure-linux-agent"></a>Rimuovere l'agente Linux di Azure
1.  Connettersi alla VM Linux tramite un client SSH.
2.  Nella finestra SSH digitare il comando seguente: <br/>
    `sudo waagent -deprovision+user`
3.  Digitare `y` per continuare. È possibile aggiungere il parametro `-force` al comando precedente per evitare questo passaggio di conferma.
4.  Al termine dell'esecuzione del comando, digitare `exit` per chiudere il client SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Acquisire l'immagine
1.  Passare al portale di Azure, selezionare il gruppo di risorse (RG) e deallocare la macchina virtuale.
2.  Il disco rigido virtuale è ora generalizzato ed è possibile usarlo per creare una nuova macchina virtuale.


## <a name="create-one-or-more-copies"></a>Creare una o più copie

La creazione di copie della macchina virtuale è spesso utile a scopo di backup, test, failover personalizzato o bilanciamento del carico, per offrire diverse configurazioni di una soluzione e così via. Per informazioni su come duplicare e scaricare un disco rigido virtuale primario per creare un clone non gestito, vedere:

- Macchina virtuale Linux: [Scaricare un disco rigido virtuale Linux da Azure](../../../virtual-machines/linux/download-vhd.md)
- Macchina virtuale Windows: [Scaricare un disco rigido virtuale Windows da Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Passaggi successivi

Dopo la configurazione della macchina virtuale, è possibile [distribuire la macchina virtuale da un disco rigido virtuale](./cpp-deploy-vm-vhd.md).

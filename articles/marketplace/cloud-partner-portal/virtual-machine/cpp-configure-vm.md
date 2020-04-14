---
title: Configurare la macchina virtuale ospitata da Microsoft Azure per Azure MarketplaceConfigure the Microsoft Azure-hosted VM for the Azure Marketplace
description: Illustra come ridimensionare, aggiornare e generalizzare una macchina virtuale ospitata in Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: f20beced7d977668d12c06375ceb8a2554c6d335
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273427"
---
# <a name="configure-the-azure-hosted-vm"></a>Configurare la macchina virtuale ospitata in Azure

> [!IMPORTANT]
> A partire dal 13 aprile 2020, inizieremo la gestione mobile delle offerte di Macchine virtuali di Azure al Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Creare gli asset tecnici della macchina virtuale](https://aka.ms/AzureVMTechAsset) di Azure per gestire le offerte migrate.

Questo articolo illustra come ridimensionare, aggiornare e generalizzare una macchina virtuale (VM, Virtual Machine) ospitata in Azure.  Questa procedura è necessaria per preparare la macchina virtuale alla distribuzione da Azure Marketplace.

## <a name="sizing-the-vhds"></a>Ridimensionamento dei dischi rigidi virtuali

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Se è stata selezionata una delle macchine virtuali preconfigurata con un sistema operativo (e facoltativamente servizi aggiuntivi), è già stata scelta una dimensione standard della macchina virtuale di Azure, come descritto nella [scheda SKU macchina virtuale](./cpp-skus-tab.md).  L'avvio della soluzione con un sistema operativo preconfigurato è l'approccio consigliato.  Se tuttavia si intende installare un sistema operativo manualmente, è necessario ridimensionare il disco rigido virtuale principale nell'immagine di macchina virtuale:

- Per Windows, il disco rigido virtuale del sistema operativo deve essere creato come disco rigido virtuale con formato fisso da 127-128 GB. 
- Per Linux, questo disco rigido virtuale deve essere creato come disco rigido virtuale con formato fisso da 30-50 GB.

Se la dimensione fisica è inferiore a 127-128 GB, il disco rigido virtuale deve essere di tipo sparse. Le immagini Windows e SQL Server di base fornite soddisfano già questi requisiti. Evitare di modificare il formato o la dimensione del VHD ottenuto. 

I dischi dati possono avere dimensioni fino a 1 TB. Nello stabilirne le dimensioni, tenere presente che i clienti non possono ridimensionare i dischi rigidi virtuali all'interno di un'immagine in fase di distribuzione. I dischi rigidi virtuali per i dischi dati devono essere creati come dischi rigidi virtuali con formato fisso, ma devono anche essere di tipo sparse. I dischi dati inizialmente possono essere vuoti o contenere dati.


## <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

Le immagini di base delle macchine virtuali del sistema operativo contengono gli aggiornamenti più recenti fino alla data di pubblicazione. Prima di pubblicare il disco rigido virtuale del sistema operativo creato, assicurarsi di aggiornare il sistema operativo e tutti i servizi installati con tutte le patch di sicurezza e manutenzione più recenti.

Per Windows Server 2016, eseguire il comando **Controlla aggiornamenti**.  Per versioni di Windows precedenti, vedere [Come ottenere un aggiornamento tramite Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows Update installerà automaticamente gli aggiornamenti di sicurezza critici e importanti più recenti.

Per le distribuzioni Linux, gli aggiornamenti vengono in genere scaricati e installati tramite uno strumento da riga di comando o un'utilità grafica.  Ubuntu Linux, ad esempio, per l'aggiornamento del sistema operativo mette a disposizione il comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e lo strumento [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html).


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

Per altre informazioni sull'esecuzione di sysprep, vedere [Procedura per generalizzare un disco rigido virtualeFor more](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) information about running sysprep, see Steps to generalize a VHD

### <a name="linux"></a>Linux

Il processo in due passaggi seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata. Questi due passaggi sono solo l'essenziale del processo. Per altre informazioni su questi due passaggi e sul motivo per cui è necessario eseri, vedere [Come creare un'immagine di una macchina virtuale o](../../../virtual-machines/linux/capture-image.md)di un disco rigido virtuale. Ai fini della creazione del disco rigido virtuale per l'offerta di Azure Marketplace, è possibile interrompersi quando si raggiunge la sezione "Creare una macchina virtuale dall'immagine acquisita".

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

Dopo che la macchina virtuale è stata generalizzata, è stata deallocata ed è stata creata un'immagine della macchina virtuale, è possibile [distribuire una macchina virtuale da un disco rigido virtuale.](./cpp-deploy-vm-vhd.md)

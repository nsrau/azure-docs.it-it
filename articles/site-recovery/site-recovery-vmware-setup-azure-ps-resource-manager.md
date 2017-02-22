---
title: " Gestire un server di elaborazione in esecuzione in Azure (Resource Manager) | Documentazione Microsofts"
description: Questo articolo descrive come configurare un server di elaborazione failback (Resource Manager) in Azure.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/2/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: bb24e1c79f6a77ec157fa05a8a212109ce8ae7ad
ms.openlocfilehash: 8ea724c49664ca4803b1c91aefb94b490bd3d283

---

# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Gestire un server di elaborazione in esecuzione in Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Gestione risorse](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Classica](./site-recovery-vmware-setup-azure-ps-classic.md)

Durante il failback è consigliabile distribuire un server di elaborazione in Azure se è presente una latenza elevata tra la rete virtuale di Azure e la rete locale. Questo articolo descrive come impostare, configurare e gestire i server di elaborazione in esecuzione in Azure.

> [!NOTE]
> Questo articolo è utile se è stato usato **Resource Manager** come modello di distribuzione per le macchine virtuali durante il failover. Se è stato usato il modello di distribuzione **classica**, seguire i passaggi indicati in [Come impostare e configurare un server di elaborazione failback (modalità classica)](./site-recovery-vmware-setup-azure-ps-classic.md)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Distribuire un server di elaborazione in Azure
1. Selezionare il server di configurazione in Insieme di credenziali > **Infrastruttura di Site Recovery** (sotto l'intestazione "Gestisci") > **Server di configurazione**, sotto l'intestazione "For VMware and Physical Machines" (Per VMware e computer fisici).
2. Nella pagina dei dettagli del server di configurazione, fare clic su "+ Server di elaborazione"

  ![Raccolta per l'aggiunta del server di elaborazione](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  Nella pagina **Aggiungere il server di elaborazione** selezionare i valori seguenti

  ![Elemento della raccolta per l'aggiunta del server di elaborazione](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Nome campo**|**Valore**|
|-|-|
|Scegliere la posizione in cui distribuire il server di elaborazione|Selezionare il valore **Distribuire un server di elaborazione di failback in Azure** |
|Subscription|Selezionare la sottoscrizione di Azure in cui è stato eseguito il failover delle macchine virtuali|
|Gruppo di risorse|È possibile creare un gruppo di risorse per distribuire il server di elaborazione o scegliere di distribuire il server di elaborazione in un gruppo di risorse esistente|
|Percorso|Selezionare il data center di Azure in cui è stato eseguito il failover delle macchine virtuali|
|Azure Network|Selezionare la rete virtuale di Azure in cui è stato eseguito il failover delle macchine virtuali. Se è stato eseguito il failover delle macchine virtuali in più reti virtuali di Azure, è necessario distribuire un server di elaborazione per ogni rete virtuale|

4. Compilare le altre proprietà per il server di elaborazione

  ![Riepilogo dell'aggiunta del server di elaborazione](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Nome campo**|**Valore**|
|-|-|
|Server Name|Nome visualizzato/nome host per la macchina virtuale del server di elaborazione|
| User Name|Un nome utente che diventa amministratore sulla macchina virtuale|
|Account di archiviazione|Nome dell'account di archiviazione in cui si trovano i dischi virtuali della macchina virtuale|
|Subnet|Subnet della rete virtuale di Azure a cui è collegata la macchina virtuale|
| Indirizzo IP|Indirizzo IP che il server di elaborazione dovrà assumere dopo l'avvio|
5. Fare clic sul pulsante OK per avviare la distribuzione della macchina virtuale del server di elaborazione.

> [!NOTE]
> Per poter usare questo server di elaborazione per il failback è necessario registrarlo con il server di configurazione locale.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrazione del server di elaborazione in esecuzione in Azure con un server di configurazione locale

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Aggiornamento del server di elaborazione alla versione più recente.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Annullamento della registrazione del server di elaborazione in esecuzione in Azure con un server di configurazione locale

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]



<!--HONumber=Feb17_HO1-->



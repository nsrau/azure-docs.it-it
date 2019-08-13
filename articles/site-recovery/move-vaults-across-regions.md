---
title: Spostamento della configurazione di Azure Site Recovery in un'altra area di Azure | Microsoft Docs
description: Indicazioni per spostare la configurazione di Azure Site Recovery in un'altra area di Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708464"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>Spostamento dell'insieme di credenziali di Servizi di ripristino e della configurazione di Azure Site Recovery in un'altra area di Azure

Esistono diversi scenari in cui è opportuno spostare le risorse di Azure esistenti da un'area a un'altra, per motivi di gestibilità o di governance, a causa di fusioni/acquisizioni aziendali e così via. Una delle risorse correlate che può essere opportuno spostare in caso di spostamento delle VM di Azure è la configurazione di ripristino di emergenza per tali VM. Non esiste un modo ottimale per spostare una configurazione di ripristino di emergenza esistente da un'area a un'altra. Ciò è dovuto essenzialmente al fatto che l'area di destinazione sarà configurata in base all'area di origine delle VM. Quando si decide di modificare quest'ultima, le configurazioni precedentemente esistenti dell'area di destinazione non possono essere riusate e devono essere reimpostate. Questo articolo definisce il processo dettagliato per riconfigurare la configurazione di ripristino di emergenza e passare a un'altra area.

In questo documento si apprenderà come:

> [!div class="checklist"]
> * Verificare i prerequisiti per lo spostamento
> * Identificare le risorse usate in precedenza da Azure Site Recovery 
> * Disabilitare la replica
> * Eliminare le risorse 
> * Riconfigurare Site Recovery in base alla nuova area di origine delle VM

> [!IMPORTANT]
> Attualmente non esiste un modo ottimale per spostare un insieme di credenziali di Servizi di ripristino e la configurazione di ripristino di emergenza in un'altra area. Questo documento illustra il processo per disabilitare la replica e reimpostarla nella nuova area.

## <a name="prerequisites"></a>Prerequisiti

- Prima di provare a spostare le VM di Azure in un'altra area, assicurarsi di rimuovere ed eliminare la configurazione di ripristino di emergenza. 

> [!NOTE]
> Se la nuova area di destinazione per la VM di Azure coincide con l'area di destinazione del ripristino di emergenza, è possibile usare la configurazione di replica esistente ed eseguire lo spostamento seguendo la procedura descritta [qui](azure-to-azure-tutorial-migrate.md). 

- Assicurarsi che la decisione sia basata su informazioni aggiornate e che gli stakeholder siano informati del fatto la VM non sarà protetta in caso di emergenza finché non ne sarà completato lo spostamento. 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identificare le risorse usate in precedenza da Azure Site Recovery
È consigliabile eseguire questo passaggio prima di procedere con il successivo perché sarà più semplice identificare le risorse rilevanti mentre è ancora in corso la replica delle VM.

Per ognuna delle VM di Azure replicate, passare a **Elementi protetti** > **Elementi replicati**>**Proprietà** e identificare le risorse seguenti:

- Gruppo di risorse di destinazione
- Account di archiviazione della cache
- Account di archiviazione di destinazione (in caso di VM di Azure basata su dischi non gestiti) 
- Rete di destinazione


## <a name="disable-existing-disaster-recovery-configuration"></a>Disabilitare la configurazione di ripristino di emergenza esistente

1. Passare a **Insieme di credenziali di Servizi di ripristino**. 
2.  In **Elementi protetti** > **Elementi replicati** fare clic con il pulsante destro del mouse sul computer e quindi scegliere **Disabilita replica**.
3. Ripetere questa operazione per tutte le VM da spostare.
> [!NOTE]
> Il servizio di mobilità non verrà disinstallato dai server protetti e dovrà essere disinstallato manualmente. Se si prevede di proteggere di nuovo il server, si può omettere la disinstallazione del servizio di mobilità.

## <a name="delete-the-resources"></a>Eliminare le risorse

1. Passare a **Insieme di credenziali di Servizi di ripristino**.
2. Fare clic su **Elimina**.
3. Procedere con l'eliminazione di tutte le altre risorse identificate nel [passaggio precedente](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Spostare le VM di Azure nella nuova area di destinazione

Per spostare le VM di Azure nell'area di destinazione, seguire la procedura indicata di seguito corrispondente alle proprie esigenze.

- [Spostare macchine virtuali di Azure in un'altra area](azure-to-azure-tutorial-migrate.md)
- [Spostare macchine virtuali di Azure nelle zone di disponibilità](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Riconfigurare Site Recovery in base alla nuova area di origine delle VM

Configurare il ripristino di emergenza per le VM di Azure che sono state spostate nella nuova area seguendo la procedura descritta [qui](azure-to-azure-tutorial-enable-replication.md).

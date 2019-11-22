---
title: Spostare un insieme di credenziali di Azure Site Recovery in un'altra area
description: Questo articolo descrive come spostare un insieme di credenziali di Servizi di ripristino (Azure Site Recovery) in un'altra area di Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090306"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Spostare un insieme di credenziali di Servizi di ripristino e la configurazione di Azure Site Recovery in un'altra area di Azure

Esistono vari scenari in cui può essere opportuno spostare le risorse di Azure esistenti da un'area a un'altra, ad esempio per la gestibilità, motivi di governance o per fusioni e acquisizioni aziendali. Una delle risorse correlate che può essere opportuno spostare in caso di spostamento delle VM di Azure è la configurazione di ripristino di emergenza. 

Non esiste un modo ottimale per spostare una configurazione di ripristino di emergenza esistente da un'area a un'altra. Questo perché è stata configurata l'area di destinazione in base all'area VM di origine. Quando si decide di modificare l'area di origine, le configurazioni precedentemente esistenti dell'area di destinazione non possono essere riutilizzate e devono essere reimpostate. Questo articolo definisce il processo dettagliato per riconfigurare la configurazione di ripristino di emergenza e passarla a un'altra area.

In questo documento si apprenderà come:

> [!div class="checklist"]
> * Verificare i prerequisiti per lo spostamento.
> * Identificare le risorse usate in precedenza da Azure Site Recovery.
> * Disabilitare la replica.
> * Eliminare le risorse.
> * Configurare Site Recovery in base alla nuova area di origine delle VM.

> [!IMPORTANT]
> Attualmente, non esiste un metodo di prima classe per spostare un insieme di credenziali di Servizi di ripristino e la configurazione di ripristino di emergenza in un'area diversa. Questo articolo illustra il processo di disabilitazione della replica e di configurazione nella nuova area.

## <a name="prerequisites"></a>Prerequisiti

- Prima di provare a spostare le VM di Azure in un'altra area, assicurarsi di rimuovere ed eliminare la configurazione di ripristino di emergenza. 

  > [!NOTE]
  > Se la nuova area di destinazione per la VM di Azure coincide con l'area di destinazione del ripristino di emergenza, è possibile usare la configurazione di replica esistente ed eseguire lo spostamento. Seguire la procedura in [Spostare macchine virtuali IaaS di Azure in un'altra area di Azure](azure-to-azure-tutorial-migrate.md).

- Assicurarsi di prendere una decisione informata e che gli stakeholder siano informati. La macchina virtuale non sarà protetta da situazioni di emergenza fino al completamento dello spostamento della macchina virtuale.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identificare le risorse usate in precedenza da Azure Site Recovery
Si consiglia di eseguire questo passaggio prima di procedere con quello successivo. È più semplice identificare le risorse rilevanti durante la replica delle macchine virtuali.

Per ognuna delle VM di Azure replicate, passare a **Elementi protetti** > **Elementi replicati** > **Proprietà** e identificare le risorse seguenti:

- Gruppo di risorse di destinazione
- Account di archiviazione della cache
- Account di archiviazione di destinazione (nel caso di una VM di Azure basata su dischi non gestiti) 
- Rete di destinazione


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Disabilitare la configurazione di ripristino di emergenza esistente

1. Passare a Insieme di credenziali di Servizi di ripristino.
2. In **Elementi protetti** > **Elementi replicati** fare clic con il pulsante destro del mouse sul computer e selezionare **Disabilita replica**.
3. Ripetere questo passaggio per tutte le VM da spostare.

> [!NOTE]
> Il servizio di mobilità non verrà disinstallato dai server protetti ma è necessario disinstallarlo manualmente. Se si prevede di proteggere di nuovo il server, si può omettere la disinstallazione del servizio di mobilità.

## <a name="delete-the-resources"></a>Eliminare le risorse

1. Passare a Insieme di credenziali di Servizi di ripristino.
2. Selezionare **Elimina**.
3. Eliminare tutte le altre risorse [identificate in precedenza](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Spostare le VM di Azure nella nuova area di destinazione

Per spostare le VM di Azure nell'area di destinazione, seguire la procedura in questo articolo corrispondente alle proprie esigenze:

- [Spostare macchine virtuali di Azure in un'altra area](azure-to-azure-tutorial-migrate.md)
- [Spostare macchine virtuali di Azure nelle zone di disponibilità](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Configurare Site Recovery in base alla nuova area di origine delle VM

Configurare il ripristino di emergenza per le VM di Azure che sono state spostate nella nuova area seguendo la procedura descritta in [Configurare il ripristino di emergenza per le macchine virtuali di Azure](azure-to-azure-tutorial-enable-replication.md).

---
title: Deprecazione della funzionalità di crittografia dei dati di Azure Site Recovery Documenti Microsoft
description: Dettagli regolare la funzionalità di crittografia dei dati di Azure Site RecoveryDetails regarig Azure Site Recovery data encryption feature
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134996"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Deprecata la funzionalità di crittografia dei dati di Site Recovery

Questo documento descrive i dettagli di deprecazione e l'azione di correzione da eseguire se si usa la funzionalità di crittografia dei dati di Site Recovery durante la configurazione del ripristino di emergenza delle macchine virtuali Hyper-V in Azure.This document describes the deprecation details and the remediation action you need to take if you are using the Site Recovery data encryption feature while configuring disaster recovery of Hyper-V virtual machines to Azure. 

## <a name="deprecation-information"></a>Informazioni sulla deprecazione


La funzionalità di crittografia dei dati di Site Recovery era disponibile per i clienti che proteggono le macchine virtuali Hyper-V per garantire che i dati replicati siano protetti da minacce alla sicurezza. questa funzionalità sarà deprecata entro il **30 dicembre 2019**. Viene sostituito dalla funzionalità [crittografia al rest](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) più avanzata, che utilizza la crittografia del servizio di [archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE). Con SSE, i dati vengono crittografati prima di essere resipersistenti nell'archiviazione e decrittografati al momento del recupero e, dopo il failover in Azure, le macchine virtuali verranno eseguite dagli account di archiviazione crittografati, consentendo un migliore obiettivo di tempo di ripristino (RTO).

Si prega di notare che se sei un cliente esistente che utilizza questa funzione, si sarebbe ricevuto comunicazioni con i dettagli di deprecazione e la procedura di correzione. 


## <a name="what-are-the-implications"></a>Quali sono le implicazioni?

Dopo il **30 dicembre 2019**, le macchine virtuali che usano ancora la funzionalità di crittografia ritirata non potranno eseguire il failover. 

## <a name="required-action"></a>Azione richiesta
Per continuare le operazioni di failover riuscite e le repliche, attenersi alla procedura descritta di seguito:

Seguire questi passaggi per ogni macchina virtuale:Follow these steps for each VM: 
1.  [Disabilitare la replica](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Creare un nuovo criterio di replica.](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy)
3.  [Abilitare la replica](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) e selezionare un account di archiviazione con SSE abilitato.

Dopo aver completato la replica iniziale agli account di archiviazione con SSE abilitato, le macchine virtuali utilizzeranno la crittografia inattivi con Azure Site Recovery.After completing the initial replication to storage accounts with SSE enabled, your VMs will be using Encryption at Rest with Azure Site Recovery.


## <a name="next-steps"></a>Passaggi successivi
Pianificare l'esecuzione dei passaggi di correzione ed eseguirli al più presto. Nel caso in cui si dispone di eventuali domande relative a questa deprecazione, si prega di contattare il supporto tecnico Microsoft. Per altre informazioni sullo scenario da Hyper-V ad Azure, fare riferimento [qui](hyper-v-vmm-architecture.md).


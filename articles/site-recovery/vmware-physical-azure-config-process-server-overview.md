---
title: Sulla configurazione di Azure Site Recovery, processo e i server di destinazione master | Microsoft Docs
description: Questo articolo offre una panoramica della configurazione, processo e i server di destinazione master tramite durante la configurazione di ripristino di emergenza di macchine virtuali VMware locali in Azure con Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 6f501e251f0b006bbbb4f64090cac5c3d61b7bf2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523554"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informazioni sui componenti di Site Recovery (processo di configurazione, server di destinazione master)

Questo articolo descrive la configurazione, processo e i server di destinazione master utilizzati per la replica di macchine virtuali VMware e server fisici in Azure con il [Site Recovery](site-recovery-overview.md) servizio.

## <a name="configuration-server"></a>Server di configurazione

Per il ripristino di emergenza di macchine virtuali VMware locali e server fisici, è necessario un ripristino del sito del server di configurazione distribuito in locale.

**Impostazione** | **Dettagli** | **Links**
--- | --- | ---
**Componenti**  | Il server di configurazione esegue tutti i componenti di Site Recovery in locale, che includono il server di configurazione, server di elaborazione e server di destinazione master.<br/><br/> Quando si configura il server di configurazione, tutti i componenti vengono installati automaticamente. | [Lettura](vmware-azure-common-questions.md#configuration-server) il server di configurazione domande frequenti.
**Ruolo** | Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati. | Altre informazioni sull'architettura per [VMware](vmware-azure-architecture.md) e [server fisici](physical-azure-architecture.md) ripristino di emergenza in Azure.
**Requisiti di VMware** | Per il ripristino di emergenza di macchine virtuali VMware in locale, è necessario installare ed eseguire il server di configurazione come una locali, VM VMware a disponibilità elevata. | [Informazioni su](vmware-azure-deploy-configuration-server.md#prerequisites) i prerequisiti.
**Distribuzione di VMware** | È consigliabile distribuire il server di configurazione usando un modello OVA scaricato. Questo metodo offre un semplice modo per configurare un server di configurazione che è conforme a tutti i requisiti e prerequisiti.<br/><br/> Se per qualche motivo è possibile distribuire una VM VMware usando un modello con estensione OVA, è possibile impostare le macchine di server di configurazione manualmente, come illustrato di seguito per il ripristino di emergenza di computer fisico. | [Distribuire](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) con un modello con estensione OVA.
**Requisiti per i server fisici** | Per il ripristino di emergenza in server fisici in locale, distribuire il server di configurazione manualmente. | [Informazioni su](physical-azure-set-up-source.md#prerequisites) i prerequisiti.
**Distribuzione di server fisici** | Se non può essere installato come VM VMware, è possibile installarlo in un server fisico. | [Distribuire](physical-azure-set-up-source.md#set-up-the-source-environment) il server di configurazione manualmente.


## <a name="process-server"></a>Server di elaborazione

**Impostazione** | **Dettagli** | **Links**
--- | --- | ---
**Distribuzione**  | Per la replica di macchine virtuali VMware locali e server fisici e il ripristino di emergenza, è necessario un server di elaborazione locale. Per impostazione predefinita, il server di elaborazione viene installato nel server di configurazione quando viene distribuito. | [Altre informazioni](vmware-azure-architecture.md?#architectural-components)
**Ruolo (in locale** | -Riceve i dati di replica da computer abilitato per la replica.<br/> -Consente di ottimizzare i dati di replica attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad archiviazione di Azure.<br/> -Esegue un'installazione push del servizio Mobility di Site Recovery in macchine virtuali VMware locali e server fisici che si vuole replicare.<br/> -Esegue l'individuazione automatica delle macchine virtuali locali. | [Altre informazioni](vmware-physical-azure-config-process-server-overview.md#process-server) 
**Ruolo (il failback da Azure)** | Dopo il failover dal sito locale, configuri un server di elaborazione in Azure, come una VM di Azure per gestire il failback nel percorso locale.<br/><br/> Il server di elaborazione in Azure è temporaneo. È possibile eliminare la macchina virtuale di Azure dopo il termine del failback. | [Altre informazioni](vmware-azure-set-up-process-server-azure.md)
**Ridimensionamento** | Per distribuzioni più grandi, in locale è possibile configurare i server di elaborazione aggiuntivi, scalabilità orizzontale. Altri server di scalabilità orizzontale della capacità, gestendo un numero maggiore di replica delle macchine e volumi più elevati di traffico di replica.<br/><br/> È possibile spostare le macchine tra due server di elaborazione, per il bilanciamento del traffico di replica. | [Altre informazioni](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>Server master di destinazione

Il server di destinazione master gestisce i dati di replica durante il failback da Azure.

- Viene installato per impostazione predefinita nel server di configurazione.
- Per distribuzioni di grandi dimensioni, è possibile aggiungere un altro server di destinazione master separato per il failback.


## <a name="next-steps"></a>Passaggi successivi
- Rivedere le [architettura](vmware-azure-architecture.md) per il ripristino di emergenza di macchine virtuali VMware e server fisici.
- Rivedere le [i requisiti e prerequisiti](vmware-physical-azure-support-matrix.md) per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure. 

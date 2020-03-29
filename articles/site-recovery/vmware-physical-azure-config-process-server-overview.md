---
title: Informazioni sui server di configurazione/processo/destinazione master di Azure Site Recovery
description: Questo articolo offre una panoramica dei server di destinazione di configurazione, processo e master che usano quando si configura il ripristino di emergenza delle macchine virtuali VMware locali in Azure con Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062081"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informazioni sui componenti di Site Recovery (configurazione, processo, destinazione master)

Questo articolo descrive i server di configurazione, processo e destinazione master usati dal servizio Site Recovery per replicare le macchine virtuali VMware e i server fisici in Azure.This article describes the configuration, process, and master target servers used by the [Site Recovery](site-recovery-overview.md) service to replicate VMware VMs and physical servers to Azure.

## <a name="configuration-server"></a>Server di configurazione

Per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici locali, distribuire un server di configurazione di Site Recovery locale.

**Impostazione** | **Dettagli** | **Link**
--- | --- | ---
**Componenti**  | Il computer server di configurazione esegue tutti i componenti di Site Recovery locali, che includono il server di configurazione, il server di elaborazione e il server di destinazione master.<br/><br/> Quando si configura il server di configurazione, tutti i componenti vengono installati automaticamente. | [Leggere](vmware-azure-common-questions.md#configuration-server) le domande frequenti sul server di configurazione.
**Ruolo** | Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati. | Altre informazioni sull'architettura per [VMware](vmware-azure-architecture.md) e il ripristino di emergenza del server fisico in Azure.Learn more about the architecture for VMware and [physical server](physical-azure-architecture.md) disaster recovery to Azure.
**Requisiti di VMware** | Per il ripristino di emergenza delle macchine virtuali VMware locali, è necessario installare ed eseguire il server di configurazione come macchina virtuale VMware locale a disponibilità elevata. | [Informazioni sui](vmware-azure-deploy-configuration-server.md#prerequisites) prerequisiti.
**Distribuzione di VMware** | È consigliabile distribuire il server di configurazione utilizzando un modello OVA scaricato. Questo metodo fornisce semplicemente un modo per impostare un server di configurazione conforme a tutti i requisiti e i prerequisiti.<br/><br/> Se per qualche motivo non è possibile distribuire una macchina virtuale VMware usando un modello OVA, è possibile configurare manualmente i computer del server di configurazione, come descritto di seguito per il ripristino di emergenza dei computer fisici. | [Eseguire la distribuzione](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) con un modello OVA.
**Requisiti per i server fisici** | Per il ripristino di emergenza nei server fisici locali, distribuire il server di configurazione manualmente. | [Informazioni sui](physical-azure-set-up-source.md#prerequisites) prerequisiti.
**Distribuzione del server fisico** | Se non può essere installato come vm VMware, è possibile installarlo in un server fisico. | [Distribuire](physical-azure-set-up-source.md#set-up-the-source-environment) il server di configurazione manualmente.

## <a name="process-server"></a>Server di elaborazione

Un server di elaborazione gestisce i dati di replica durante il failover e il failback e installa il servizio Mobility per le macchine virtuali VMware locali e i server fisici.

**Impostazione** | **Dettagli** | **Link**
--- | --- | ---
**Distribuzione**  | Per impostazione predefinita, quando viene distribuito il server di configurazione viene installato il server di elaborazione. <br/><br/> È necessario un server di elaborazione locale per il ripristino di emergenza e la replica delle macchine virtuali VMware locali e dei server fisici. | [Scopri di più](vmware-azure-architecture.md#architectural-components).
**Ruolo (locale)** | Riceve i dati di replica dai computer abilitati per la replica. <br/><br/> Ottimizza i dati di replica con memorizzazione nella cache, compressione e crittografia e li invia ad Archiviazione di Azure.Optimizes replication data with caching, compression, and encryption, and sends it to Azure Storage. <br/><br/> Esegue un'installazione push del servizio Mobility di Site Recovery nelle macchine virtuali VMware locali e nei server fisici che si desidera replicare. <br/><br/> Esegue l'individuazione automatica dei computer locali. | [Scopri di più](vmware-azure-enable-replication.md).
**Ruolo (failback da Azure)Role (failback from Azure)** | Dopo il failover dal sito locale, si configura un server di elaborazione in Azure, come macchina virtuale di Azure, per gestire il failback nella posizione locale.<br/><br/> Il server di elaborazione in Azure è temporaneo. La macchina virtuale di Azure può essere eliminata al termine del failback. | [Scopri di più](vmware-azure-set-up-process-server-azure.md).
**Ridimensionamento** | Per distribuzioni di grandi dimensioni, in locale è possibile configurare server di elaborazione con scalabilità orizzontale aggiuntivi. Capacità di scalabilità orizzontale di server aggiuntivi, gestendo un numero maggiore di computer di replica e volumi maggiori di traffico di replica.<br/><br/> È possibile spostare i computer tra due server di elaborazione per bilanciare il carico del traffico di replica. | [Scopri di più](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Server master di destinazione

Il server di destinazione master gestisce i dati di replica durante il failback da Azure.

- Per impostazione predefinita, il server di destinazione master è installato nel server di configurazione.
- Per distribuzioni di grandi dimensioni, è possibile aggiungere un altro server di destinazione master separato per il failback.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare [l'architettura](vmware-azure-architecture.md) per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici.
- Esaminare [i requisiti e i prerequisiti](vmware-physical-azure-support-matrix.md) per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici in Azure.Review the requirements and prerequisites for disaster recovery of VMware VMs and physical servers to Azure.

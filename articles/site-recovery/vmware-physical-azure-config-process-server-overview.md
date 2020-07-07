---
title: Informazioni sui server di configurazione/processo/di destinazione master di Azure Site Recovery
description: Questo articolo fornisce una panoramica dei server di configurazione, di elaborazione e di destinazione master usando quando si configura il ripristino di emergenza di macchine virtuali VMware locali in Azure con Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80062081"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informazioni sui componenti di Site Recovery (configurazione, processo, destinazione master)

Questo articolo descrive i server di configurazione, di elaborazione e di destinazione master usati dal servizio [Site Recovery](site-recovery-overview.md) per replicare le macchine virtuali VMware e i server fisici in Azure.

## <a name="configuration-server"></a>Server di configurazione

Per il ripristino di emergenza di server fisici e VM VMware locali, distribuire un server di configurazione Site Recovery locale.

**Impostazione** | **Dettagli** | **Collegamenti**
--- | --- | ---
**Componenti**  | Il computer del server di configurazione esegue tutti i componenti Site Recovery locali, tra cui il server di configurazione, il server di elaborazione e il server di destinazione master.<br/><br/> Quando si configura il server di configurazione, tutti i componenti vengono installati automaticamente. | [Leggere](vmware-azure-common-questions.md#configuration-server) le domande frequenti sul server di configurazione.
**Role** | Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati. | Altre informazioni sull'architettura per il ripristino di emergenza di [VMware](vmware-azure-architecture.md) e [server fisici](physical-azure-architecture.md) in Azure.
**Requisiti di VMware** | Per il ripristino di emergenza di macchine virtuali VMware locali, è necessario installare ed eseguire il server di configurazione come macchina virtuale VMware a disponibilità elevata locale. | Informazioni [sui](vmware-azure-deploy-configuration-server.md#prerequisites) prerequisiti.
**Distribuzione VMware** | Si consiglia di distribuire il server di configurazione usando un modello OVA scaricato. Questo metodo fornisce un modo semplice per configurare un server di configurazione che è conforme a tutti i requisiti e prerequisiti.<br/><br/> Se per qualche motivo non è possibile distribuire una macchina virtuale VMware usando un modello OVA, è possibile configurare manualmente i computer server di configurazione, come descritto di seguito per il ripristino di emergenza del computer fisico. | Eseguire la [distribuzione](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) con un modello OVA.
**Requisiti per i server fisici** | Per il ripristino di emergenza sui server fisici locali, distribuire il server di configurazione manualmente. | Informazioni [sui](physical-azure-set-up-source.md#prerequisites) prerequisiti.
**Distribuzione server fisico** | Se non può essere installato come macchina virtuale VMware, è possibile installarlo in un server fisico. | [Distribuire](physical-azure-set-up-source.md#set-up-the-source-environment) il server di configurazione manualmente.

## <a name="process-server"></a>Server di elaborazione

Un server di elaborazione gestisce i dati di replica durante il failover e il failback e installa il servizio Mobility per le macchine virtuali VMware e i server fisici locali.

**Impostazione** | **Dettagli** | **Collegamenti**
--- | --- | ---
**Distribuzione**  | Per impostazione predefinita, quando viene distribuito il server di configurazione, viene installato il server di elaborazione. <br/><br/> Un server di elaborazione locale è necessario per il ripristino di emergenza e la replica di server fisici e macchine virtuali VMware locali. | [Altre informazioni](vmware-azure-architecture.md#architectural-components)
**Ruolo (locale**) | Riceve i dati di replica dai computer abilitati per la replica. <br/><br/> Ottimizza i dati di replica con la memorizzazione nella cache, la compressione e la crittografia e li invia ad archiviazione di Azure. <br/><br/> Esegue un'installazione push del servizio Site Recovery Mobility in macchine virtuali VMware locali e server fisici che si vuole replicare. <br/><br/> Esegue l'individuazione automatica dei computer locali. | [Altre informazioni](vmware-azure-enable-replication.md)
**Role (failback da Azure)** | Dopo il failover dal sito locale, è necessario configurare un server di elaborazione in Azure, come macchina virtuale di Azure, per gestire il failback nel percorso locale.<br/><br/> Il server di elaborazione in Azure è temporaneo. È possibile eliminare la macchina virtuale di Azure al termine del failback. | [Altre informazioni](vmware-azure-set-up-process-server-azure.md)
**Scalabilità** | Per distribuzioni di dimensioni maggiori, in locale è possibile configurare altri server di elaborazione con scalabilità orizzontale. I server aggiuntivi aumentano la capacità, gestendo un numero maggiore di macchine di replica e volumi più elevati di traffico di replica.<br/><br/> È possibile spostare i computer tra due server di elaborazione, per bilanciare il carico del traffico di replica. | [Altre informazioni](vmware-azure-set-up-process-server-scale.md)

## <a name="master-target-server"></a>Server master di destinazione

Il server di destinazione master gestisce i dati di replica durante il failback da Azure.

- Per impostazione predefinita, il server di destinazione master è installato nel server di configurazione.
- Per distribuzioni di grandi dimensioni, è possibile aggiungere un altro server di destinazione master separato per il failback.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare l' [architettura](vmware-azure-architecture.md) per il ripristino di emergenza di macchine virtuali VMware e server fisici.
- Esaminare i [requisiti e i prerequisiti](vmware-physical-azure-support-matrix.md) per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure.

---
title: Come funziona la migrazione Hyper-V in Azure Migrate?
description: Informazioni sulla migrazione hyper-V con Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185871"
---
# <a name="how-does-hyper-v-replication-work"></a>Come funziona la replica Hyper-V?

Questo articolo offre una panoramica dell'architettura e dei processi usati durante la migrazione delle macchine virtuali Hyper-V con lo strumento di migrazione del server di Azure Migrate.This article provides an overview of the architecture and processes used when you migrate Hyper-V V V Ms with the Azure Migrate Server Migration tool.

[Azure Migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure di carichi di lavoro e app locali, oltre che di VM del cloud privato/pubblico. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, nonché offerte di ISV terzi.

## <a name="agentless-migration"></a>Migrazione senza agente

Lo strumento di migrazione del server di Azure Migra fornisce la replica senza agente per le macchine virtuali Hyper-V locali, usando un flusso di lavoro di migrazione ottimizzato per Hyper-V.The Azure Migrate Server Migration tool provides agentless replication for on-premises Hyper-V VMs, using a migration workflow that's optimized for Hyper-V. Si installa un agente software solo su host Hyper-V o nodi del cluster. Non è necessario installare nulla nelle macchine virtuali Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migrazione dei server e Ripristino configurazione di azure

Azure Migrate Server Migration is a tool for migrating on-premises workloads, and cloud-based VMs, to Azure. Site Recovery è uno strumento di ripristino di emergenza. Gli strumenti condividono alcuni componenti tecnologici comuni utilizzati per la replica dei dati, ma hanno scopi diversi. 


## <a name="architectural-components"></a>Componenti dell'architettura

![Architecture](./media/hyper-v-replication-architecture/architecture.png)



**Componente** | **Distribuzione** | 
--- | --- 
**Provider di replica** | Il provider di Microsoft Azure Site Recovery è installato negli host Hyper-V e registrato con la migrazione del server di migrazione di Azure.The Microsoft Azure Site Recovery provider is installed on Hyper-V hosts, and registered with Azure Migration Server Migration.<br/> Il provider orchestra la replica per le macchine virtuali Hyper-V.
**Agente di Servizi di ripristino** | L'agente del servizio di ripristino di Microsoft Azure gestisce la replica dei dati. Funziona con il provider per replicare i dati dalle macchine virtuali Hyper-V in Azure.It works with the provider to replicate data from Hyper-V VMs to Azure.<br/> I dati replicati vengono caricati in un account di archiviazione nella sottoscrizione di Azure.The replicated data is uploaded to a storage account in your Azure subscription. Lo strumento di migrazione Server elabora i dati replicati e li applica ai dischi di replica nella sottoscrizione. I dischi di replica vengono usati per creare le macchine virtuali di Azure durante la migrazione.

- I componenti vengono installati da un singolo file di installazione, scaricato da Azure Migrate Server Migration nel portale.
- Il provider e l'appliance usano le connessioni 443 della porta HTTPS in uscita per comunicare con la migrazione del server di Azure Migrate.The provider and appliance use outbound HTTPS port 443 connections to communicate with Azure Migrate Server Migration.
- Le comunicazioni dal provider e dall'agente sono sicure e crittografate.


## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale Hyper-V, viene eseguita la replica iniziale.
2. Viene creato uno snapshot della macchina virtuale Hyper-V.A Hyper-V VM snapshot is taken.
3. I dischi rigidi virtuali nella macchina virtuale vengono replicati uno alla volta, fino a quando non vengono tutti copiati in Azure.VHDs on the VM are replicated one-by-one, until they're all copied to Azure. Il tempo di replica iniziale dipende dalle dimensioni della macchina virtuale e dalla larghezza di banda della rete.
4. Le modifiche al disco che si verificano durante la replica iniziale vengono rilevate tramite replica Hyper-V e archiviate in file di registro (file hrl).
    - I file di registro si trovano nella stessa cartella dei dischi.
    - A ogni disco è associato un file hrl inviato all'archiviazione secondaria.
    - Si noti che lo snapshot e i file di log usano risorse del disco durante l'esecuzione della replica iniziale.
4. Al termine della replica iniziale, lo snapshot della macchina virtuale viene eliminato e inizia la replica delta.
5. Le modifiche incrementali del disco vengono rilevate nei file hrl. I log di replica vengono caricati periodicamente in un account di archiviazione di Azure dall'agente di Servizi di ripristino.


## <a name="performance-and-scaling"></a>Prestazioni e ridimensionamento

Le prestazioni di replica per Hyper-V sono influenzate da fattori che includono le dimensioni della macchina virtuale, la frequenza di modifica dei dati (varianza) delle macchine virtuali, lo spazio disponibile nell'host Hyper-V per l'archiviazione dei file di log, la larghezza di banda di caricamento per i dati di replica e l'archiviazione di destinazione in Azure.Replication performance for Hyper-V performance factors are influenced factors that include VM size, the data change rate (churn) of the VMs, available space on the Hyper-V host for log file storage, upload bandwidth for replication data, and target storage in Azure.

- Se si replicano più computer contemporaneamente, usare [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) per Hyper-V per ottimizzare la replica.
- Pianificare la replica Hyper-V e distribuire la replica sugli account di archiviazione di Azure, in base alla capacità.

### <a name="control-upload-throughput"></a>Controllare la velocità effettiva di caricamentoControl upload throughput

È possibile limitare la quantità di larghezza di banda usata per caricare i dati in Azure in ogni host Hyper-V.You can limit the amount of bandwidth used to upload data to Azure on each Hyper-V host. Stai attento. Se si impostano valori troppo bassi, l'impatto negativo sulla replica avrà un impatto negativo e ritarderà la migrazione.


1. Accedere al nodo host o cluster Hyper-V.
2. Per aprire lo snap-in MMC Windows Azure Backup, eseguire **il percorso C:.**
3. Nello snap-in selezionare **Modifica proprietà**.
4. In **Limitazione**selezionare **Abilita limitazione utilizzo larghezza di banda Internet per le operazioni**di backup . Impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Influenzare l'efficienza di caricamento

Se si dispone di larghezza di banda di riserva per la replica e si desidera aumentare i caricamenti, è possibile aumentare il numero di thread allocati per l'attività di caricamento, come indicato di seguito:

1. Aprire il Registro di sistema con Regedit.
2. Passare alla chiave HKEY_LOCAL_MACHINE SOFTWARE Microsoft Windows Azure Backup , Replica , UploadThreadsPerVM
3. Aumentare il valore per il numero di thread usati per il caricamento dei dati per ogni macchina virtuale di replica. Il valore predefinito è 4 e il valore massimo è 32. 




## <a name="next-steps"></a>Passaggi successivi

Provare la migrazione Hyper-V con Azure Migrate Server Migration.Try out [Hyper-V migration](tutorial-migrate-hyper-v.md) using Azure Migrate Server Migration.

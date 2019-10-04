---
title: Come funziona la migrazione di Hyper-V con migrazione del Server eseguire la migrazione di Azure? | Microsoft Docs
description: Viene fornita una panoramica della migrazione di Hyper-V in migrazione di Server di eseguire la migrazione di Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811713"
---
# <a name="how-does-hyper-v-replication-work"></a>Come funziona la replica Hyper-V?

Questo articolo offre una panoramica dell'architettura e i processi usati quando si esegue la migrazione di macchine virtuali Hyper-V con lo strumento di migrazione del Server eseguire la migrazione di Azure.

[Azure Migrate](migrate-services-overview.md) fornisce un hub centrale per tenere traccia di individuazione, valutazione e migrazione delle App in locale e i carichi di lavoro e cloud privati/pubblici macchine virtuali di Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e migrazione, nonché le offerte di fornitori di software indipendenti di terze parti.

## <a name="agentless-migration"></a>Migrazione senza agenti

Lo strumento di migrazione del Server eseguire la migrazione di Azure offre la replica senza agente per le macchine virtuali Hyper-V locali, usando un flusso di lavoro di migrazione che è ottimizzato per Hyper-V. Si installa un agente software solo in host Hyper-V o i nodi del cluster. Non deve essere installato nelle macchine virtuali Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migrazione del server e Azure Site Recovery

Migrazione del Server eseguire la migrazione di Azure è uno strumento per la migrazione dei carichi di lavoro locali e basati sul cloud macchine virtuali di Azure. Site Recovery è uno strumento di ripristino di emergenza. Gli strumenti condividono alcuni componenti con tecnologia comune usati per la replica dei dati, ma per scopi diversi. 


## <a name="architectural-components"></a>Componenti dell'architettura

![Architettura](./media/hyper-v-replication-architecture/architecture.png)



**Componente** | **Distribuzione** | 
--- | --- 
**Provider di replica** | Il provider di Microsoft Azure Site Recovery è installato negli host Hyper-V e registrato con la migrazione di Server di migrazione di Azure.<br/> Il provider Orchestra la replica per le macchine virtuali Hyper-V.
**Agente di Servizi di ripristino** | L'agente del servizio di Microsoft Azure Site Recovery gestisce la replica dei dati. Funziona con il provider per replicare i dati dalle macchine virtuali Hyper-V ad Azure.<br/> I dati replicati viene caricati in un account di archiviazione nella sottoscrizione di Azure. La migrazione del Server strumento i processi i dati replicati e applicarlo ai dischi di replica nella sottoscrizione. I dischi di replica vengono usati per creare macchine virtuali di Azure quando esegue la migrazione.

- I componenti vengono installati per un singolo file di installazione, scaricato dalla migrazione del Server eseguire la migrazione di Azure nel portale.
- Il provider e appliance usano connessioni HTTPS in uscita porta 443 per comunicare con la migrazione di Server eseguire la migrazione di Azure.
- Le comunicazioni dal provider e agente sono protette e crittografate.


## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale Hyper-V, viene avviata la replica iniziale.
2. Viene creato uno snapshot macchina virtuale Hyper-V.
3. I dischi rigidi virtuali nella macchina virtuale vengono replicati uno alla volta, fino a quando non sono stati tutti copiati in Azure. Ora della replica iniziale varia a seconda di dimensioni della macchina virtuale e della larghezza di banda di rete.
4. Le modifiche ai dischi che si verificano durante la replica iniziale vengono rilevate tramite la Replica Hyper-V e archiviati nel file di log (file hrl).
    - File di log sono nella stessa cartella dei dischi.
    - Ogni disco è un file hrl associato che viene inviato all'archiviazione secondaria.
    - Si noti che lo snapshot e i file di log usano risorse del disco durante l'esecuzione della replica iniziale.
4. Al termine della replica iniziale, lo snapshot della macchina virtuale viene eliminato e viene avviata la replica differenziale.
5. Le modifiche ai dischi incrementali vengono registrate in file hrl. I log di replica vengono caricati periodicamente dall'agente di servizi di ripristino a un account di archiviazione di Azure.


## <a name="performance-and-scaling"></a>Prestazioni e ridimensionamento

Le prestazioni della replica per Hyper-V sono influenzata da fattori che includono le dimensioni della macchina virtuale, la frequenza di modifica dei dati (varianza) delle macchine virtuali, lo spazio disponibile nell'host Hyper-V per archiviazione file di log, larghezza di banda di caricamento per i dati di replica e archiviazione di destinazione in Azure.

- Se si esegue la replica di più computer allo stesso tempo, usare il [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) per Hyper-V, per ottimizzare la replica.
- Pianificare la replica Hyper-V e distribuire la replica in account di archiviazione di Azure, in base alla capacità.

### <a name="control-upload-throughput"></a>Velocità effettiva di caricamento di controllo

È possibile limitare la quantità di larghezza di banda usata per caricare dati in Azure in ogni host Hyper-V. Stai attento. Se si impostano i valori troppo basso che un impatto negativo sulle replica e la migrazione di ritardo.


1. Accedere al nodo host o cluster Hyper-V.
2. Eseguire **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**per aprire lo snap-in MMC Backup di Windows Azure.
3. Nello snap-in selezionare **Modifica proprietà**.
4. Nelle **limitazione**, selezionare **abilita la limitazione delle richieste per operazioni di backup all'utilizzo della larghezza di banda di internet**. Impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Influenza l'efficienza di caricamento

Se si dispone di larghezza di banda disponibile per la replica e vogliono aumentare caricamenti, è possibile aumentare il numero di thread allocati per l'attività di caricamento, come indicato di seguito:

1. Aprire il Registro di sistema con Regedit.
2. Passare alla chiave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Aumentare il valore per il numero di thread usati per il caricamento dei dati per ogni macchina virtuale di replica. Il valore predefinito è 4 e il valore massimo è 32. 




## <a name="next-steps"></a>Passaggi successivi

Provare [migrazione Hyper-V](tutorial-migrate-hyper-v.md) utilizzando la migrazione di Server eseguire la migrazione di Azure.

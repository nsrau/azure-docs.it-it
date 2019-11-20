---
title: Funzionamento della migrazione di Hyper-V in Azure Migrate
description: Informazioni sulla migrazione di Hyper-V con Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185871"
---
# <a name="how-does-hyper-v-replication-work"></a>Funzionamento della replica Hyper-V

Questo articolo fornisce una panoramica dell'architettura e dei processi usati quando si esegue la migrazione di macchine virtuali Hyper-V con lo strumento di migrazione di Azure Migrate server.

[Azure Migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure di carichi di lavoro e app locali, oltre che di VM del cloud privato/pubblico. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti.

## <a name="agentless-migration"></a>Migrazione senza agenti

Lo strumento di migrazione di Azure Migrate Server fornisce la replica senza agenti per le macchine virtuali Hyper-V locali, usando un flusso di lavoro di migrazione ottimizzato per Hyper-V. Si installa un agente software solo negli host Hyper-V o nei nodi del cluster. Non è necessario installare nulla nelle VM Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migrazione del server e Azure Site Recovery

Azure Migrate migrazione del server è uno strumento per la migrazione di carichi di lavoro locali e macchine virtuali basate sul cloud in Azure. Site Recovery è uno strumento di ripristino di emergenza. Gli strumenti condividono alcuni componenti tecnologici comuni usati per la replica dei dati, ma servono a scopi diversi. 


## <a name="architectural-components"></a>Componenti dell'architettura

![Architettura](./media/hyper-v-replication-architecture/architecture.png)



**Componente** | **Distribuzione** | 
--- | --- 
**Provider di replica** | Il provider di Site Recovery Microsoft Azure viene installato negli host Hyper-V e registrato con la migrazione del server di migrazione di Azure.<br/> Il provider orchestra la replica per le VM Hyper-V.
**Agente di Servizi di ripristino** | L'agente del servizio di ripristino Microsoft Azure gestisce la replica dei dati. Funziona con il provider per replicare i dati dalle macchine virtuali Hyper-V ad Azure.<br/> I dati replicati vengono caricati in un account di archiviazione nella sottoscrizione di Azure. Lo strumento di migrazione del server elabora i dati replicati e li applica ai dischi di replica nella sottoscrizione. I dischi di replica vengono usati per creare le macchine virtuali di Azure quando si esegue la migrazione.

- I componenti vengono installati da un singolo file di installazione, scaricati da Azure Migrate migrazione del server nel portale.
- Il provider e l'appliance utilizzano le connessioni porta HTTPS in uscita 443 per comunicare con Azure Migrate migrazione del server.
- Le comunicazioni tra il provider e l'agente sono sicure e crittografate.


## <a name="replication-process"></a>Processo di replica

1. Quando si Abilita la replica per una macchina virtuale Hyper-V, viene avviata la replica iniziale.
2. Viene effettuato uno snapshot della macchina virtuale Hyper-V.
3. I dischi rigidi virtuali nella macchina virtuale vengono replicati uno alla volta, fino a quando non vengono tutti copiati in Azure. Il tempo di replica iniziale dipende dalle dimensioni della macchina virtuale e dalla larghezza di banda di rete.
4. Le modifiche del disco che si verificano durante la replica iniziale vengono rilevate tramite la replica Hyper-V e archiviate in file di log (file HRL).
    - I file di log si trovano nella stessa cartella dei dischi.
    - A ogni disco è associato un file HRL che viene inviato all'archiviazione secondaria.
    - Si noti che lo snapshot e i file di log usano risorse del disco durante l'esecuzione della replica iniziale.
4. Al termine della replica iniziale, lo snapshot della macchina virtuale viene eliminato e delta replication inizia.
5. Le modifiche incrementali del disco vengono registrate nei file HRL. I log di replica vengono caricati periodicamente in un account di archiviazione di Azure dall'agente di servizi di ripristino.


## <a name="performance-and-scaling"></a>Prestazioni e ridimensionamento

Le prestazioni di replica per Hyper-V sono influenzate da fattori che includono dimensioni della macchina virtuale, frequenza di modifica dei dati (varianza) delle VM, spazio disponibile nell'host Hyper-V per l'archiviazione di file di log, larghezza di banda di caricamento per i dati di replica e archiviazione di destinazione in Azure.

- Se si esegue la replica di più computer allo stesso tempo, usare la [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) per Hyper-V per ottimizzare la replica.
- Pianificare la replica Hyper-V e distribuire la replica tramite gli account di archiviazione di Azure in base alla capacità.

### <a name="control-upload-throughput"></a>Controllo della velocità effettiva di caricamento

È possibile limitare la quantità di larghezza di banda usata per caricare i dati in Azure in ogni host Hyper-V. Stai attento. Se si impostano i valori troppo bassi, la replica avrà un impatto negativo e la migrazione verrà ritardata.


1. Accedere all'host o al nodo del cluster Hyper-V.
2. Eseguire **C:\Programmi\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**per aprire lo snap-in MMC di Windows Azure Backup.
3. Nello snap-in selezionare **Modifica proprietà**.
4. In **limitazione**selezionare **Abilita limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**. Impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Influenza l'efficienza del caricamento

Se si dispone di larghezza di banda di riserva per la replica e si desidera aumentare i caricamenti, è possibile aumentare il numero di thread allocati per l'attività di caricamento, come indicato di seguito:

1. Aprire il registro di sistema con Regedit.
2. Passare a chiave HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Aumentare il valore per il numero di thread usati per il caricamento dei dati per ogni macchina virtuale di replica. Il valore predefinito è 4 e il valore massimo è 32. 




## <a name="next-steps"></a>Passaggi successivi

Provare a eseguire la [migrazione di Hyper-V](tutorial-migrate-hyper-v.md) usando Azure migrate migrazione del server.

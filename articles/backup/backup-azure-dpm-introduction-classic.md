---
title: Backup di carichi di lavoro DPM nel portale di Azure classico | Documentazione Microsoft
description: Introduzione al backup dei server DPM di Azure usando il servizio Backup di Azure
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: System Center Data Protection Manager, data protection manager, backup di dpm
ms.assetid: 8f23972b-d167-4231-b331-e198db3b18b4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: nkolli;giridham;markgal
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: a9a516cfdfaf4b95c4f0121a66e90f6e71206e9f
ms.contentlocale: it-it
ms.lasthandoff: 08/03/2017

---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparazione del backup dei carichi di lavoro in Azure con DPM
> [!div class="op_single_selector"]
> * [Server di backup di Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Server di Backup di Azure (classico)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (classico)](backup-azure-dpm-introduction-classic.md)
>
>

Questo articolo offre un'introduzione all'uso del servizio Backup di Microsoft Azure per proteggere i server e i carichi di lavoro DPM di System Center. Le informazioni dell'articolo riguardano:

* Il funzionamento del backup del server DPM di Azure
* I prerequisiti per eseguire un backup senza problemi
* Gli errori tipici rilevati e come gestirli
* Scenari supportati

DPM di System Center esegue il backup di file e dati delle applicazioni. I dati sottoposti a backup su DPM possono essere archiviati su nastro, disco oppure sottoposti a backup in Azure usando il servizio Backup di Microsoft Azure. DPM interagisce con Backup di Azure nei modi seguenti:

* **DPM distribuito come server fisico o come macchina virtuale locale** : se DPM viene distribuito come server fisico o come una macchina virtuale di Hyper-V locale, è possibile eseguire il backup dei dati su un insieme di credenziali di Backup di Azure, oltre che su dischi e nastri di backup.
* **DPM distribuito come macchina virtuale di Azure** : a partire dalla versione di System Center 2012 R2 con aggiornamento 3, DPM può essere distribuito come macchina virtuale di Azure. Se DPM viene distribuito come macchina virtuale di Azure, sarà possibile eseguire il backup dei dati nei dischi di Azure associati alla macchina virtuale DPM di Azure oppure eseguire l'offload dell'archiviazione dei dati tramite il backup in un insieme di credenziali di Backup di Azure.

## <a name="why-backup-your-dpm-servers"></a>Motivi per eseguire il backup dei server DPM
Di seguito sono elencati i vantaggi aziendali derivanti dall'uso del servizio Backup di Azure per eseguire il backup dei server DPM:

* Per la distribuzione DPM locale è possibile usare il backup di Azure come alternativa alla distribuzione a lungo termine su nastro.
* Per le distribuzioni DPM in Azure, Backup di Azure permette di ridurre lo spazio occupato dai dischi archiviati sul disco di Azure e di aumentare la scalabilità archiviando i dati meno recenti in Backup di Azure e quelli nuovi su disco.

## <a name="how-does-dpm-server-backup-work"></a>Funzionamento del backup del server DPM
Per eseguire il backup di una macchina virtuale è prima necessario acquisire uno snapshot dei dati in un momento specifico. Il servizio Backup di Azure avvia il processo di backup all'ora pianificata e avvia l'estensione per il backup per acquisire uno snapshot. L'estensione per il backup si coordina con il servizio VSS in-guest per assicurare coerenza e, dopo averla ottenuta, richiama l'API snapshot del BLOB del servizio Archiviazione di Azure. Questa operazione viene eseguita per ottenere uno snapshot coerente dei dischi della macchina virtuale senza che sia necessario arrestarla.

Dopo l'acquisizione dello snapshot, il servizio Backup di Azure trasferisce i dati all'insieme di credenziali per il backup. Il servizio provvede a identificare e trasferire soltanto i blocchi che sono stati modificati dopo l'ultimo backup, rendendo in questo modo efficienti l'archiviazione dei backup e la rete. Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino. È possibile visualizzare tale punto di ripristino nel portale di Azure classico.

> [!NOTE]
> Per quanto riguarda le macchine virtuali Linux, è possibile eseguire soltanto backup coerenti a livello di file.
>
>

## <a name="prerequisites"></a>Prerequisiti
Di seguito viene descritto come preparare il servizio Backup di Azure all'esecuzione del backup dei dati DPM:

1. **Creare un insieme di credenziali di backup**. Se non è stato ancora creato un insieme di credenziali di backup nella sottoscrizione, vedere la versione relativa al Portale di Azure di questo articolo: [Preparazione del backup dei carichi di lavoro in Azure con DPM](backup-azure-dpm-introduction.md).

  > [!IMPORTANT]
  > A partire da marzo 2017, non è più possibile usare il portale classico per creare insiemi di credenziali di backup.
  > È ora possibile aggiornare gli insiemi di credenziali di Backup ad insiemi di credenziali dei servizi di ripristino. Per altre informazioni, vedere l'articolo [Aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft consiglia di aggiornare gli insiemi di credenziali di Backup a insiemi di credenziali dei servizi di ripristino.<br/> Dopo il 15 ottobre 2017 non sarà possibile usare PowerShell per creare insiemi di credenziali di backup. **Entro il 1° novembre 2017**:
  >- Tutti gli insiemi di credenziali di backup rimanenti verranno aggiornati automaticamente a insiemi di credenziali dei servizi di ripristino.
  >- e non sarà più possibile accedere ai dati di backup nel portale classico. Sarà possibile invece usare il portale di Azure per accedere ai dati di backup negli insiemi di credenziali di servizi di ripristino.
  >

2. **Scaricare le credenziali di insieme** : in Backup di Azure caricare il certificato di gestione creato nell'insieme di credenziali.
3. **Installare Azure Backup Agent e registrare il server** : da Backup di Azure, installare l'agente su ogni server DPM e registrare tale server nell'insieme di credenziali di backup.

[!INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[!INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## <a name="requirements-and-limitations"></a>Requisiti e limitazioni
* DPM può essere eseguito come server fisico o come macchina virtuale Hyper-V installata in System Center 2012 SP1 o System Center 2012 R2. Inoltre, è possibile eseguirlo come macchina virtuale di Azure in System Center 2012 R2 (con almeno l'aggiornamento cumulativo 3 di DPM 2012 R2) oppure come macchina virtuale di Windows in VMware con System Center 2012 R2 e almeno il relativo aggiornamento cumulativo 5.
* Se DPM viene eseguito con System Center 2012 SP1, è necessario installare l'aggiornamento cumulativo 2 per System Center Data Protection Manager SP1. Tale procedura è necessaria prima di poter installare Azure Backup Agent.
* È necessario che nel server DPM siano installati Windows PowerShell e .Net Framework 4.5.
* DPM può eseguire il backup della maggior parte dei carichi di lavoro nel servizio Backup di Azure. Per visualizzare un elenco completo degli elementi supportati, consultare gli elementi supportati di Backup di Azure riportati di seguito.
* Usando l'opzione "Copia su nastro", non è possibile ripristinare i dati memorizzati in Backup di Azure.
* È necessario disporre di un account di Azure su cui è abilitata la funzionalità Backup di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Informazioni sui [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
* Per usare Backup di Azure è necessario che Azure Backup Agent sia installato sui server da sottoporre a backup. Le dimensioni di ogni server devono essere almeno il 10% di quelle dei dati sottoposti a backup. Lo spazio deve essere disponibile come archiviazione locale. Se, ad esempio, si esegue il backup di 100 GB di dati, è necessario un minimo di 10 GB di spazio disponibile nello spazio di lavoro. Anche se la dimensione minima è 10%, è consigliabile usare uno spazio di archiviazione locale del 15% per il percorso della cache.
* I dati verranno memorizzati nell'archiviazione relativa all'insieme di credenziali di Azure. Non esistono limiti relativi alla quantità di dati che è possibile includere nel backup in un insieme di credenziali di Backup di Azure, tuttavia le dimensioni dell'origine dati (ad esempio, un database o una macchina virtuale) non devono superare 54.400 GB.

Il backup in Azure è supportato per i tipi di file seguenti:

* Crittografati (solo backup completi)
* Compressi (backup incrementali supportati)
* Sparse (backup incrementali supportati)
* Compressi e sparse (considerati come sparse)

Questi tipi di file non sono supportati:

* I server nei file system che distinguono fra minuscole e maiuscole non sono supportati.
* Collegamenti reali (ignorati)
* Reparse point (ignorati)
* Crittografati e compressi (ignorati)
* Crittografati e sparse (ignorati)
* Flusso compresso
* Flusso di tipo sparse

> [!NOTE]
> A partire dalla versione di System Center 2012 DPM con SP1, è possibile eseguire il backup di carichi di lavoro protetti da DPM in Azure, usando il servizio Backup di Microsoft Azure.
>
>


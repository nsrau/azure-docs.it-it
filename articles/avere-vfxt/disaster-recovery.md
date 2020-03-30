---
title: Indicazioni sul ripristino di emergenza per Avere vFXT per AzureDisaster recovery guidance for Avere vFXT for Azure
description: Come proteggere i dati in Avere vFXT per Azure da eliminazioni o interruzioni accidentaliHow to protect data in Avere vFXT for Azure from accidental deletion or outages
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966658"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Indicazioni sul ripristino di emergenza per Avere vFXT per AzureDisaster recovery guidance for Avere vFXT for Azure

Questo articolo descrive le strategie per proteggere il flusso di lavoro di Avere vFXT for Azure e fornisce indicazioni per il backup dei dati in modo da poter eseguire il ripristino da incidenti o interruzioni.

Avere vFXT per Azure archivia temporaneamente i dati nella cache. I dati vengono archiviati a lungo termine nei sistemi di archiviazione back-end: sistemi hardware locali, contenitori di archiviazione BLOB di Azure o entrambi.

Per evitare interruzioni e possibili perdite di dati, considerare queste quattro aree:To ere out out out out, possible data loss, consider these four areas:

* Protezione contro i tempi di inattività se un sistema Avere vFXT per Azure non è più disponibile
* Protezione dei dati nella cache del cluster
* Protezione dei dati nell'hardware NAS back-end
* Protezione dei dati nell'archiviazione cloud BLOB di Azure back-end

Ogni cliente Avere vFXT for Azure deve creare un piano di ripristino di emergenza completo che includa piani per questi elementi. È anche possibile integrare la resilienza nelle applicazioni usate con il cluster vFXT. Leggere i collegamenti in [Passaggi successivi](#next-steps) per assistenza.

## <a name="protect-against-downtime"></a>Protezione dai tempi di inattività

La ridondanza è incorporata nel prodotto Avere vFXT for Azure:Redundancy is built in to the Avere vFXT for Azure product:

* Il cluster è a disponibilità elevata e i singoli nodi del cluster possono eseguire il failover con un'interruzione minima.
* I dati modificati nella cache vengono scritti regolarmente nei filer principali back-end (NAS hardware o BLOB di Azure) per l'archiviazione a lungo termine.

Ogni cluster Avere vFXT per Azure deve trovarsi in una singola zona di disponibilità, ma è possibile usare cluster ridondanti situati in zone diverse o aree diverse per fornire rapidamente l'accesso in caso di interruzione a livello di area.

È anche possibile posizionare i contenitori di archiviazione in più aree se si desidera perdere l'accesso ai dati. Tuttavia, tenere presente che le transazioni tra aree hanno una latenza maggiore e un costo più elevato rispetto alle transazioni che rimangono all'interno di un'area.

## <a name="protect-data-in-the-cluster-cache"></a>Proteggere i dati nella cache del clusterProtect data in the cluster cache

I dati memorizzati nella cache vengono sempre scritti nei filer principali prima di un arresto normale, ma in un arresto incontrollato, i dati modificati nella cache possono andare persi.

Se si utilizza il cluster per ottimizzare solo le letture dei file, non vi sono modifiche da perdere. Se si utilizza anche il cluster per memorizzare nella cache le modifiche ai file (scritture), valutare se modificare o meno i criteri di [cache](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) dei filer principali<!-- link to legacy doc --> per personalizzare la frequenza di scrittura dei dati nell'archiviazione a lungo termine.

In generale, il piano di ripristino deve concentrarsi sul backup dei sistemi di archiviazione back-end, che contengono più dati e in genere sono più importanti per ristabilire il flusso di lavoro dopo un errore.

## <a name="protect-data-in-nas-core-filers"></a>Proteggere i dati nei filer principali NAS

Utilizzare i metodi accettati per proteggere i dati archiviati in un filer di base hardware NAS locale, inclusi snapshot e backup completi, come consigliato dal provider NAS. Il ripristino di emergenza per questi filer di base non rientra nell'ambito di questo articolo.

## <a name="protect-data-in-azure-blob-storage"></a>Proteggere i dati nell'archiviazione BLOB di AzureProtect data in Azure Blob storage

Avere vFXT per Azure usa l'archiviazione con ridondanza locale (LRS) per i filer principali BLOB di Azure.Have vFXT for Azure uses locally-redundant storage (LRS) for Azure Blob core filers. Ciò significa che i dati nei contenitori BLOB vengono copiati automaticamente per la protezione da errori hardware temporanei all'interno di un data center.

In questa sezione vengono forniti suggerimenti su come proteggere ulteriormente i dati nell'archiviazione BLOB da rare interruzioni a livello di area o eliminazioni accidentali.

Le procedure consigliate per la protezione dei dati nell'archiviazione BLOB di Azure includono:Best practices for protecting data in Azure Blob storage include:

* Copiare spesso i dati critici in un altro account di archiviazione in un'altra area, con la stessa frequenza stabilita dal piano di ripristino di emergenza.
* Controllare l'accesso ai dati in tutti i sistemi di destinazione per evitare eliminazioni accidentali o danneggiamento. Prendere in considerazione l'uso di [blocchi delle risorse nell'archiviazione](../azure-resource-manager/management/lock-resources.md) dei dati.
* Abilitare la funzionalità [snapshot cloud](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) Avere vFXT for Azure per i filer principali BLOB.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Copia i dati del filer di base di Avere vFXT in un account di backup

Seguire questi passaggi per stabilire un backup dei dati in un altro account.

1. Se necessario, generare una nuova chiave di crittografia e archiviarla in modo sicuro all'esterno dei sistemi interessati.

   Se i dati sono crittografati dal cluster Avere vFXT for Azure, è necessario generare una nuova chiave di crittografia prima di copiarli in un altro account di archiviazione. Archiviare la chiave e la password in modo sicuro in una struttura sicura e non interessata da un errore a livello regionale.

   È necessario fornire questa chiave quando si aggiunge il contenitore a un cluster, anche se lo si aggiunge nuovamente al cluster originale.

   Leggere [le impostazioni di crittografia cloudRead Cloud encryption settings](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> per informazioni dettagliate.

   Se il contenitore usa solo la crittografia predefinita di Azure, è possibile ignorare questo passaggio.

1. Rimuovere il filer principale dal sistema. In questo modo il cluster scriverà tutti i dati modificati nell'archivio back-end.

   Anche se sarà necessario aggiungere nuovamente il filer principale dopo il backup, rimuoverlo è il modo migliore per garantire che tutti i dati siano completamente scritti nel back-end. (L'opzione "suspend" può talvolta lasciare i dati modificati nella cache.) <!-- xxx true? or just metadata? -->

   Prendere nota delle informazioni sul nome e sul nodo del filer principale (elencate nella pagina **Spazio dei nomi** nel pannello di controllo) in modo da poterle replicare quando si aggiunge nuovamente il contenitore dopo il backup.

   Utilizzare il pannello di controllo del cluster per rimuovere il filer principale. [Aprire il pannello di controllo del cluster](avere-vfxt-cluster-gui.md) e scegliere **Filer** > di base**Gestisci filer principali**. Individuare il sistema di archiviazione di cui si desidera eseguire il backup e utilizzare il pulsante **Rimuovi** per eliminarlo dal cluster.

1. Creare un nuovo contenitore di archiviazione BLOB vuoto in un altro account di archiviazione in un'altra area.

1. Utilizzare qualsiasi comodo strumento di copia per copiare i dati sul filer principale nel nuovo contenitore. The copy must replicate the data without changes, and without disrupting the proprietary cloud filesystem format used by Avere vFXT for Azure. Gli strumenti basati su Azure includono [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)e [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Dopo aver copiato i dati nel contenitore di backup, aggiungere nuovamente il contenitore originale al cluster come descritto in [Configurare l'archiviazione](avere-vfxt-add-storage.md).

   * Utilizzare lo stesso nome del filer principale e le stesse informazioni sul nodo, in modo che i flussi di lavoro client non debbano essere modificati.
   * Impostare il valore **Contenuto bucket** sull'opzione dati esistenti.
   * Se il contenitore è stato crittografato dal cluster, è necessario immettere la chiave di crittografia corrente per il relativo contenuto. Questa è la chiave aggiornata nel passaggio uno.

Per i backup dopo il primo, non è necessario creare un nuovo contenitore di archiviazione. Tuttavia, è consigliabile generare una nuova chiave di crittografia ogni volta che si esedia un backup per assicurarsi di avere la chiave corrente archiviata in un luogo che si ricorda.

### <a name="access-a-backup-data-source-during-an-outage"></a>Accedere a un'origine dati di backup durante un'interruzione

Per accedere al contenitore di backup da un cluster Avere vFXT for Azure, seguire questa procedura:To access the backup container from an Avere vFXT for Azure cluster, follow this process:

1. Se necessario, creare un nuovo cluster Avere vFXT per Azure in un'area non interessata.

   > [!TIP]
   > Quando si crea un cluster Avere vFXT per Azure, è possibile salvare una copia del modello di creazione e dei parametri. Se si salvano queste informazioni durante la creazione del cluster primario, è possibile utilizzarle per creare un cluster sostitutivo con le stesse proprietà. Nella pagina di [riepilogo,](avere-vfxt-deploy.md#validation-and-purchase) fare clic sul collegamento **Scarica modello e parametri.** Salvare le informazioni in un file prima di creare il cluster.

1. Aggiungere un nuovo filer di core cloud che punta al contenitore BLOB duplicato.

   Assicurarsi di specificare che il contenitore di destinazione contiene già i dati nell'impostazione **Contenuto bucket** della procedura guidata di creazione del filer principale. (Il sistema dovrebbe avvisare se si lascia accidentalmente questo set su **Vuoto**.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Se necessario, aggiornare i client in modo che montino il nuovo cluster o il nuovo filer principale anziché l'originale. Se si aggiunge il filer principale sostitutivo con lo stesso nome e percorso di giunzione del contenitore originale, non sarà necessario aggiornare i processi client a meno che non sia necessario montare il nuovo cluster in un nuovo indirizzo IP.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla personalizzazione delle impostazioni per Avere vFXT for Azure, vedere [Ottimizzazione del cluster](avere-vfxt-tuning.md).
* Altre informazioni sul ripristino di emergenza e sulla creazione di applicazioni resilienti in Azure:Learn more about disaster recovery and building resilient applications in Azure:

  * [Indicazioni tecniche sulla resilienza di Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [Ripristino da un'interruzione del servizio a livello di areaRecover from a region-wide service disruption](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->

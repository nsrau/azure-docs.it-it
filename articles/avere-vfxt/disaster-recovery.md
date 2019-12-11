---
title: Linee guida per il ripristino di emergenza per vFXT per Azure
description: Come proteggere i dati in vFXT per Azure da eliminazioni accidentali o interruzioni
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 4a8dbe9d1c66955e585f7b2cbf842193c965252e
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74998771"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Linee guida per il ripristino di emergenza per vFXT per Azure

Questo articolo illustra le strategie per proteggere il flusso di lavoro di vFXT per Azure e fornisce indicazioni per il backup dei dati in modo da potersi ripristinare da incidenti o interruzioni.

VFXT per Azure archivia temporaneamente i dati nella cache. I dati sono archiviati a lungo termine nei sistemi di archiviazione back-end: sistemi hardware locali, contenitori di archiviazione BLOB di Azure o entrambi.

Per proteggersi da interruzioni e da possibili perdite di dati, prendere in considerazione le quattro aree seguenti:

* Protezione dai tempi di inattività se un vFXT di sicurezza di rete per il sistema Azure diventa non disponibile
* Protezione dei dati nella cache del cluster
* Protezione dei dati in un archivio hardware NAS back-end
* Protezione dei dati nell'archiviazione cloud di back-end di Azure

Ogni vFXT per il cliente di Azure deve creare il proprio piano di ripristino di emergenza completo che include i piani per questi elementi. È anche possibile creare resilienza nelle applicazioni usate con il cluster vFXT. Leggere i collegamenti nei [passaggi successivi](#next-steps) per la guida.

## <a name="protect-against-downtime"></a>Proteggi dai tempi di inattività

La ridondanza è incorporata nel prodotto vFXT per Azure:

* Il cluster è a disponibilità elevata e i singoli nodi del cluster possono eseguire il failover con interruzioni minime.
* I dati modificati nella cache vengono scritti regolarmente nei file di base del back-end (hardware NAS o BLOB di Azure) per l'archiviazione a lungo termine.

Ogni vFXT di ricerca per cluster di Azure deve trovarsi in una singola zona di disponibilità, ma è possibile usare cluster ridondanti che si trovano in aree diverse o in aree diverse per fornire rapidamente l'accesso in caso di interruzione a livello di area.

È anche possibile posizionare i contenitori di archiviazione in più aree se si è interessati alla perdita dell'accesso ai dati. Tenere tuttavia presente che le transazioni tra le aree hanno una latenza più elevata e un costo superiore rispetto alle transazioni che restano in un'area.

## <a name="protect-data-in-the-cluster-cache"></a>Proteggere i dati nella cache del cluster

I dati memorizzati nella cache vengono sempre scritti nei file di base prima di un arresto regolare, ma in un arresto non controllato i dati modificati nella cache possono andare persi.

Se si usa il cluster per ottimizzare solo le letture di file, non ci sono modifiche da perdere. Se si usa anche il cluster per memorizzare nella cache le modifiche del file (scritture), valutare se modificare o meno i [criteri di cache](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) dei file di base<!-- link to legacy doc --> per personalizzare la frequenza con cui i dati vengono scritti nell'archiviazione a lungo termine.

In generale, il piano di ripristino dovrebbe concentrarsi sull'esecuzione del backup dei sistemi di archiviazione back-end, che contengono più dati e, in genere, più importanti per ristabilire il flusso di lavoro in seguito a un errore.

## <a name="protect-data-in-nas-core-filers"></a>Proteggere i dati nei filer Core NAS

Usare i metodi accettati per proteggere i dati archiviati in un filer Core hardware NAS locale, tra cui snapshot e backup completi, come consigliato dal provider NAS. Il ripristino di emergenza per questi file di base esula dall'ambito di questo articolo.

## <a name="protect-data-in-azure-blob-storage"></a>Proteggere i dati nell'archiviazione BLOB di Azure

VFXT per Azure usa l'archiviazione con ridondanza locale (con ridondanza locale) per i file di base dei BLOB di Azure. Ciò significa che i dati nei contenitori BLOB vengono copiati automaticamente per la protezione da errori hardware temporanei all'interno di un data center.

Questa sezione fornisce suggerimenti su come proteggere ulteriormente i dati nell'archiviazione BLOB da interruzioni rare a livello di area o da eliminazioni accidentali.

Le procedure consigliate per la protezione dei dati nell'archivio BLOB di Azure includono:

* Copiare spesso i dati critici in un altro account di archiviazione in un'altra area (spesso come determinato dal piano di ripristino di emergenza).
* Controllare l'accesso ai dati in tutti i sistemi di destinazione per evitare eliminazioni accidentali o danneggiamenti. Si consiglia di usare i [blocchi delle risorse](../azure-resource-manager/resource-group-lock-resources.md) nell'archivio dati.
* Abilitare la funzionalità di vFXT per [snapshot cloud](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) di Azure per i file di base di BLOB.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Copiare i dati di vFXT Core filer in un account di backup

Per stabilire un backup dei dati in un altro account, seguire questa procedura.

1. Se necessario, generare una nuova chiave di crittografia e archiviarla in modo sicuro all'esterno dei sistemi interessati.

   Se i dati vengono crittografati con il cluster vFXT per Azure, è necessario generare una nuova chiave di crittografia prima di copiare i dati in un altro account di archiviazione. Archiviare in modo sicuro la chiave e la password in una struttura sicura che non verrà interessata da un errore a livello di area.

   È necessario specificare questa chiave quando si aggiunge il contenitore a un cluster, anche se viene aggiunto nuovamente al cluster originale.

   Leggere [le impostazioni di crittografia cloud](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> per informazioni dettagliate.

   Se il contenitore usa solo la crittografia predefinita di Azure, è possibile ignorare questo passaggio.

1. Rimuovere il file principale dal sistema. In questo modo, il cluster deve scrivere tutti i dati modificati nell'archiviazione back-end.

   Sebbene sia necessario aggiungere nuovamente il file di base dopo il backup, rimuoverlo è il modo migliore per garantire che tutti i dati vengano scritti completamente nel back-end. L'opzione "Sospendi" può talvolta lasciare i dati modificati nella cache. <!-- xxx true? or just metadata? -->

   Annotare le informazioni sul nome e sulla giunzione di core, elencate nella pagina **spazio dei nomi** nel pannello di controllo, in modo che sia possibile replicarle quando si aggiunge di nuovo il contenitore dopo il backup.

   Usare il pannello di controllo del cluster per rimuovere il file di base. [Aprire il pannello di controllo del cluster](avere-vfxt-cluster-gui.md) e scegliere **core filer** > **gestire i file di base**. Trovare il sistema di archiviazione di cui si vuole eseguire il backup e usare il pulsante **Rimuovi** per eliminarlo dal cluster.

1. Creare un nuovo contenitore di archiviazione BLOB vuoto in un altro account di archiviazione in un'altra area.

1. Usare uno strumento di copia pratico per copiare i dati nel file di base nel nuovo contenitore. La copia deve replicare i dati senza modifiche e senza compromettere il formato del filesystem cloud proprietario usato da vFXT per Azure. Gli strumenti basati su Azure includono [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)e [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Dopo aver copiato i dati nel contenitore di backup, aggiungere di nuovo il contenitore originale al cluster, come descritto in [configurare l'archiviazione](avere-vfxt-add-storage.md).

   * Usare lo stesso nome e le stesse informazioni di giunzione di base per i flussi di lavoro dei client che non devono essere modificati.
   * Impostare il valore del **contenuto del bucket** sull'opzione dati esistente.
   * Se il contenitore è stato crittografato dal cluster, è necessario immettere la chiave di crittografia corrente per il relativo contenuto. Si tratta della chiave aggiornata nel passaggio 1.

Per i backup dopo il primo, non è necessario creare un nuovo contenitore di archiviazione. Tuttavia, provare a generare una nuova chiave di crittografia ogni volta che si esegue un backup per assicurarsi di avere la chiave corrente archiviata in una posizione che si ricorda.

### <a name="access-a-backup-data-source-during-an-outage"></a>Accedere a un'origine dati di backup durante un'interruzione

Per accedere al contenitore di backup da un cluster vFXT per Azure, seguire questa procedura:

1. Se necessario, creare una nuova vFXT per il cluster di Azure in un'area non interessata.

   > [!TIP]
   > Quando si crea un cluster vFXT per Azure, è possibile salvare una copia del modello e dei parametri di creazione. Se si salvano queste informazioni durante la creazione del cluster primario, è possibile usarle per creare un cluster sostitutivo con le stesse proprietà. Nella pagina [convalida e riepilogo](avere-vfxt-deploy.md#validation-and-purchase) fare clic sul collegamento **Scarica modello e parametri** . Salvare le informazioni in un file prima di fare clic sul pulsante **OK** per creare il cluster.

1. Aggiungere un nuovo filer di cloud core che punti al contenitore BLOB duplicato.

   Assicurarsi di specificare che il contenitore di destinazione contiene già dati nell'impostazione del **contenuto del bucket** della creazione guidata del file principale. Il sistema deve ricevere un avviso se si lascia accidentalmente questo oggetto impostato su **vuoto**.  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Se necessario, aggiornare i client in modo che Montino il nuovo cluster o il nuovo core filer anziché quello originale. Se si aggiunge il componente di base di sostituzione con lo stesso nome e lo stesso percorso di giunzione del contenitore originale, non sarà necessario aggiornare i processi client a meno che non sia necessario montare il nuovo cluster in un nuovo indirizzo IP.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla personalizzazione delle impostazioni per vFXT per Azure, vedere Ottimizzazione del [cluster](avere-vfxt-tuning.md).
* Scopri di più sul ripristino di emergenza e sulla creazione di applicazioni resilienti in Azure:

  * [Indicazioni tecniche sulla resilienza di Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [Ripristino da un'interferenza del servizio a livello di area](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->

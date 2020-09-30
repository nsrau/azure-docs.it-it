---
title: Backup online e ripristino dei dati su richiesta in Azure Cosmos DB
description: Questo articolo descrive come funziona il backup automatico, il ripristino dei dati su richiesta, come configurare l'intervallo di backup e la conservazione in Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 310fee91ed98409e5a724d1be8de7bc9ccb5601b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570914"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Backup online e ripristino dei dati su richiesta in Azure Cosmos DB

Azure Cosmos DB esegue automaticamente il backup dei dati a intervalli regolari. I backup automatici vengono eseguiti senza impatto sulle prestazioni o sulla disponibilità delle operazioni del database. Tutti i backup sono archiviati separatamente in un servizio di archiviazione e replicati a livello globale per garantire la resilienza in caso di emergenze a livello di area. I backup automatici sono utili in caso di eliminazione accidentale o aggiornamento dell'account, di database o di contenitori Azure Cosmos, quando in seguito occorre ripristinare i dati.

## <a name="automatic-and-online-backups"></a>Backup automatici e online

Con Azure Cosmos DB, non solo i dati ma anche i relativi backup sono altamente ridondanti e resilienti in caso di emergenze a livello di area. Nei passaggi seguenti viene illustrato il modo in cui Azure Cosmos DB esegue il backup dei dati:

* Azure Cosmos DB esegue automaticamente un backup completo del database ogni 4 ore e in qualsiasi momento, per impostazione predefinita vengono archiviati solo i due backup più recenti. Se gli intervalli predefiniti non sono sufficienti per i carichi di lavoro, è possibile modificare l'intervallo di backup e il periodo di memorizzazione dal portale di Azure. È possibile modificare la configurazione di backup durante o dopo la creazione dell'account Azure Cosmos. Se il contenitore o il database viene eliminato, Azure Cosmos DB conserva gli snapshot esistenti di un determinato contenitore o database per 30 giorni.

* Azure Cosmos DB archivia questi backup nell'archiviazione BLOB di Azure, mentre i dati effettivi si trovano localmente all'interno Azure Cosmos DB.

* Per garantire una bassa latenza, lo snapshot del backup viene archiviato nell'archivio BLOB di Azure nella stessa area dell'area di scrittura corrente o in **una** delle aree di scrittura, nel caso in cui si disponga di una configurazione di scrittura in più aree. Per garantire la resilienza in caso di emergenze a livello di area, ogni snapshot dei dati di backup nell'archivio BLOB di Azure viene a sua volta replicato in un'altra area tramite l'archiviazione con ridondanza geografica. L'area in cui viene replicato il backup dipende dall'area di origine e dalla coppia di aree associata all'area di origine. Per altre informazioni, vedere l'[elenco di coppie di aree di Azure con ridondanza geografica](../best-practices-availability-paired-regions.md). Non è possibile accedere direttamente a questo backup. Il team di Azure Cosmos DB ripristinerà il backup quando viene richiesto attraverso una richiesta di supporto.

   L'immagine seguente mostra come viene eseguito il backup di un contenitore di Azure Cosmos con tutte le tre partizioni fisiche primarie nell'area Stati Uniti occidentali in un account di Archiviazione BLOB di Azure remoto negli Stati Uniti occidentali e quindi replicato negli Stati Uniti orientali:

  :::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Backup completi periodici di tutte le entità di Cosmos DB nell'archiviazione con ridondanza geografica di Azure" border="false":::

* I backup vengono eseguiti senza effetti sulle prestazioni o la disponibilità dell'applicazione. Azure Cosmos DB esegue il backup dei dati in background senza utilizzare velocità effettiva di cui è stato effettuato il provisioning aggiuntiva (UR) o senza influire sulle prestazioni e la disponibilità del database.

## <a name="options-to-manage-your-own-backups"></a>Opzioni per la gestione dei backup

Con gli account API SQL di Azure Cosmos DB è anche possibile gestire backup propri adottando uno degli approcci seguenti:

* Usare [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) per spostare periodicamente i dati in una risorsa di archiviazione di propria scelta.

* Usare Azure Cosmos DB [feed di modifiche](change-feed.md) per leggere i dati periodicamente per i backup completi o per le modifiche incrementali e archiviarli nella propria risorsa di archiviazione.

## <a name="modify-the-backup-interval-and-retention-period"></a>Modificare l'intervallo di backup e il periodo di memorizzazione

Azure Cosmos DB esegue automaticamente un backup completo dei dati ogni 4 ore e in qualsiasi momento, vengono archiviati i due backup più recenti. Questa configurazione è l'opzione predefinita e viene offerta senza costi aggiuntivi. È possibile modificare l'intervallo di backup e il periodo di memorizzazione predefiniti durante la creazione dell'account Azure Cosmos o dopo la creazione dell'account. La configurazione del backup è impostata a livello di account Azure Cosmos e deve essere eseguita in ogni account. Dopo aver configurato le opzioni di backup per un account, questo viene applicato a tutti i contenitori all'interno dell'account. Al momento è possibile modificare le opzioni di backup solo dal portale di Azure.

Se i dati sono stati eliminati o danneggiati in modo accidentale, **prima di creare una richiesta di supporto per il ripristino dei dati, assicurarsi di aumentare la conservazione del backup per l'account per almeno sette giorni. È consigliabile aumentare la conservazione entro 8 ore da questo evento.** In questo modo, il team di Azure Cosmos DB avrà tempo a sufficienza per ripristinare l'account.

Per modificare le opzioni di backup predefinite per un account Azure Cosmos esistente, attenersi alla procedura seguente:

1. Accedere al [portale di Azure.](https://portal.azure.com/)
1. Passare all'account Azure Cosmos e aprire il riquadro **Backup & Restore** . Aggiornare l'intervallo di backup e il periodo di conservazione dei backup in modo obbligatorio.

   * **Intervallo di backup** : si tratta dell'intervallo in cui Azure Cosmos DB tenta di eseguire un backup dei dati. Il backup richiede un periodo di tempo diverso da zero e in alcuni casi potrebbe avere esito negativo a causa di dipendenze downstream. Azure Cosmos DB tenta di eseguire un backup nell'intervallo configurato, tuttavia, non garantisce che il backup venga completato entro tale intervallo di tempo. È possibile configurare questo valore in ore o minuti. L'intervallo di backup non può essere minore di 1 ora e maggiore di 24 ore. Quando si modifica questo intervallo, il nuovo intervallo diventa effettivo a partire dall'ora in cui è stato effettuato l'ultimo backup.

   * **Conservazione dei backup** : rappresenta il periodo di conservazione di ogni backup. È possibile configurarlo in ore o giorni. Il periodo di memorizzazione minimo non può essere minore di due volte l'intervallo di backup (in ore) e non può essere maggiore di 720 ore.

   * **Copie dei dati conservati** . per impostazione predefinita, vengono offerte gratuitamente due copie di backup dei dati. Se sono necessarie più di due copie, è previsto un addebito aggiuntivo. Per conoscere il prezzo esatto per le copie aggiuntive, vedere la sezione Archiviazione utilizzata della [pagina Prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).

   :::image type="content" source="./media/online-backup-and-restore/configure-backup-interval-retention.png" alt-text="Backup completi periodici di tutte le entità di Cosmos DB nell'archiviazione con ridondanza geografica di Azure" border="true":::

Se si configurano le opzioni di backup durante la creazione dell'account, è possibile configurare i **criteri di backup**, che sono **periodici** o **continui**. Il criterio periodico consente di configurare l'intervallo di backup e la conservazione dei backup. Il criterio continuo è attualmente disponibile solo per l'iscrizione. Il team di Azure Cosmos DB valuterà il carico di lavoro e approverà la richiesta.

:::image type="content" source="./media/online-backup-and-restore/configure-periodic-continuous-backup-policy.png" alt-text="Backup completi periodici di tutte le entità di Cosmos DB nell'archiviazione con ridondanza geografica di Azure" border="true":::

## <a name="restore-data-from-an-online-backup"></a>Ripristinare i dati da un backup online

I dati possono essere eliminati o modificati accidentalmente in uno dei seguenti scenari:  

* Eliminare l'intero account Azure Cosmos.

* Eliminare uno o più database di Azure Cosmos.

* Eliminare uno o più contenitori di Azure Cosmos.

* Eliminare o modificare gli elementi di Azure Cosmos, ad esempio i documenti, all'interno di un contenitore. Questo caso specifico viene in genere definito danneggiamento dei dati.

* Un database di offerta condivisa o contenitori all'interno di un database di offerta condivisa vengono eliminati o danneggiati.

Azure Cosmos DB può ripristinare i dati in tutti gli scenari precedenti. Quando si esegue il ripristino, viene creato un nuovo account Azure Cosmos per conservare i dati ripristinati. Il nome del nuovo account, se non è specificato, avrà il formato `<Azure_Cosmos_account_original_name>-restored1` . L'ultima cifra viene incrementata quando si tenta di eseguire più ripristini. Non è possibile ripristinare i dati in un account Azure Cosmos creato in precedenza.

Quando si elimina accidentalmente un account Azure Cosmos, è possibile ripristinare i dati in un nuovo account con lo stesso nome, purché il nome dell'account non sia in uso. Quindi, è consigliabile non ricreare l'account dopo averlo eliminato. Poiché non solo impedisce ai dati ripristinati di utilizzare lo stesso nome, ma rende anche più difficile l'individuazione dell'account corretto.

Quando si elimina accidentalmente un database di Azure Cosmos, è possibile ripristinare l'intero database o un subset dei contenitori all'interno del database. È anche possibile selezionare contenitori specifici tra database e ripristinarli in un nuovo account Azure Cosmos.

Quando si elimina o si modifica accidentalmente uno o più elementi all'interno di un contenitore (caso di danneggiamento dei dati), è necessario specificare l'ora in cui eseguire il ripristino. Il tempo è importante in caso di danneggiamento dei dati. Poiché il contenitore è attivo, il backup è ancora in esecuzione, pertanto se si attende il superamento del periodo di memorizzazione (il valore predefinito è otto ore), i backup verranno sovrascritti. **Per evitare la sovrascrittura del backup, aumentare la conservazione del backup per l'account per almeno sette giorni. È consigliabile aumentare la conservazione entro 8 ore dal danneggiamento dei dati.**

Se i dati sono stati eliminati o danneggiati per errore, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro 8 ore, in modo che il team di Azure Cosmos DB possa aiutare a ripristinare i dati dai backup. In questo modo il team di supporto di Azure Cosmos DB avrà tempo sufficiente per ripristinare l'account.

Se si effettua il provisioning della velocità effettiva a livello di database, in questo caso il processo di backup e ripristino viene eseguito a livello dell'intero database e non a livello dei singoli contenitori. In questi casi, non è possibile selezionare un subset di contenitori da ripristinare.

## <a name="migrate-data-to-the-original-account"></a>Migrare i dati all'account originale

L'obiettivo principale del ripristino dei dati è ripristinare i dati eliminati o modificati accidentalmente. Pertanto, è consigliabile esaminare prima di tutto il contenuto dei dati ripristinati per assicurarsi che siano quelli previsti. In seguito è possibile eseguire la migrazione dei dati all'account primario. Sebbene sia possibile usare l'account ripristinato come nuovo account attivo, non è un'opzione consigliata se si dispone di carichi di lavoro di produzione.  

Di seguito sono indicati i vari modi disponibili per eseguire la migrazione dei dati nell'account Azure Cosmos originale:

* Usare l' [utilità di migrazione dati Azure Cosmos DB](import-data.md).
* Usare il [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Usare il [feed delle modifiche](change-feed.md) in Azure Cosmos DB.
* È possibile scrivere codice personalizzato.

Assicurarsi di eliminare gli account ripristinati non appena è stata eseguita la migrazione dei dati, perché saranno addebitati addebiti continui.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile consultare informazioni su come ripristinare i dati da un account Azure Cosmos o su come eseguire la migrazione dei dati in un account Azure Cosmos

* Per creare una richiesta di ripristino, contattare il supporto tecnico di Azure e [aprire un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Come ripristinare i dati di Azure Cosmos DB da un backup](how-to-backup-and-restore.md)
* [Usare un feed di modifiche di Cosmos DB](change-feed.md) per spostare i dati in Azure Cosmos DB.
* [Usare Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) per spostare i dati in Azure Cosmos DB.


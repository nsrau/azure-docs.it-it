---
title: Backup online e ripristino dei dati su richiesta in Azure Cosmos DB
description: Questo articolo descrive come funziona il backup automatico, il ripristino dei dati su richiesta, come configurare l'intervallo di backup e la conservazione, come contattare il supporto per un ripristino dei dati in Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 7c506d66c101c2770cffb8cc8d105b2f841c539a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279489"
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

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Modificare l'intervallo di backup e il periodo di memorizzazione

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

## <a name="request-data-restore-from-a-backup"></a>Richiedere il ripristino dei dati da un backup

Se si elimina accidentalmente il database o un contenitore, è possibile [archiviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o [chiamare il supporto di Azure](https://azure.microsoft.com/support/options/) per ripristinare i dati dai backup online automatici. Il supporto tecnico di Azure è disponibile solo per i piani selezionati, ad esempio **standard**, **Developer**e piani superiori a quelli previsti. Il supporto di Azure non è disponibile con il piano **Basic**. Per altre informazioni sui diversi piani di supporto, vedere la pagina [Piani di supporto per Azure](https://azure.microsoft.com/support/plans/).

Per il ripristino di uno snapshot specifico del backup, Azure Cosmos DB richiede che i dati siano stati disponibili per la durata del ciclo di backup per tale snapshot.
Prima di richiedere un ripristino, è necessario avere a disposizione i dettagli seguenti:

* ID della sottoscrizione.

* A seconda del modo in cui i dati sono stati accidentalmente eliminati o modificati, prepararsi per avere a disposizione ulteriori informazioni. Si consiglia di procurarsi le informazioni richieste in anticipo, per ridurre al minimo lo scambio di comunicazioni che potrebbe essere deleterio nei casi in cui la tempestività è cruciale.

* Se viene eliminato l'intero account Azure Cosmos DB, è necessario specificare il nome dell'account eliminato. Se si crea un altro account con lo stesso nome dell'account eliminato, condividerlo con il team di supporto perché ciò permette di determinare l'account corretto da scegliere. È consigliabile archiviare ticket di supporto diversi per ogni account eliminato, perché riduce al minimo la confusione per lo stato di ripristino.

* Se vengono eliminati uno o più database, è necessario comunicare l'account Azure Cosmos, nonché i nomi dei database di Azure Cosmos e specificare se esiste un nuovo database con lo stesso nome.

* Se vengono eliminati uno o più contenitori, è necessario comunicare il nome dell'account Azure Cosmos, i nomi dei database e i nomi dei contenitori. Specificare inoltre se esiste un contenitore con lo stesso nome.

* Se i dati sono stati eliminati o danneggiati per errore, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro 8 ore, in modo che il team di Azure Cosmos DB possa aiutare a ripristinare i dati dai backup. **Prima di creare una richiesta di supporto per il ripristino dei dati, assicurarsi di [aumentare la conservazione del backup](#configure-backup-interval-retention) per l'account per almeno sette giorni. È consigliabile aumentare la conservazione entro 8 ore da questo evento.** In questo modo il team di supporto di Azure Cosmos DB avrà tempo sufficiente per ripristinare l'account.

Oltre al nome dell'account Azure Cosmos, ai nomi dei database e ai nomi dei contenitori, è necessario specificare il punto nel tempo in cui è possibile ripristinare i dati. È importante essere il più precisi possibile per consentire a Microsoft di determinare i backup disponibili migliori nell'intervallo di tempo specificato. **È anche importante specificare l'ora in formato UTC.**

Lo screenshot seguente illustra come creare una richiesta di supporto per un contenitore (raccolta/grafo/tabella) per ripristinare i dati tramite il portale di Azure. Specificare ulteriori dettagli, come tipo di dati, scopo del ripristino e ora di eliminazione dei dati per consentire a Microsoft di definire più facilmente la priorità della richiesta.

:::image type="content" source="./media/online-backup-and-restore/backup-support-request-portal.png" alt-text="Backup completi periodici di tutte le entità di Cosmos DB nell'archiviazione con ridondanza geografica di Azure":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Considerazioni per il ripristino dei dati da un backup

I dati possono essere eliminati o modificati accidentalmente in uno dei seguenti scenari:  

* Eliminare l'intero account Azure Cosmos.

* Eliminare uno o più database di Azure Cosmos.

* Eliminare uno o più contenitori di Azure Cosmos.

* Eliminare o modificare gli elementi di Azure Cosmos, ad esempio i documenti, all'interno di un contenitore. Questo caso specifico viene in genere definito danneggiamento dei dati.

* Un database di offerta condivisa o contenitori all'interno di un database di offerta condivisa vengono eliminati o danneggiati.

Azure Cosmos DB può ripristinare i dati in tutti gli scenari precedenti. Quando si esegue il ripristino, viene creato un nuovo account Azure Cosmos per conservare i dati ripristinati. Il nome del nuovo account, se non è specificato, avrà il formato `<Azure_Cosmos_account_original_name>-restored1` . L'ultima cifra viene incrementata quando si tenta di eseguire più ripristini. Non è possibile ripristinare i dati in un account Azure Cosmos creato in precedenza.

Quando si elimina accidentalmente un account Azure Cosmos, è possibile ripristinare i dati in un nuovo account con lo stesso nome, purché il nome dell'account non sia in uso. Quindi, è consigliabile non ricreare l'account dopo averlo eliminato. Poiché non solo impedisce ai dati ripristinati di utilizzare lo stesso nome, ma rende anche più difficile l'individuazione dell'account corretto.

Quando si elimina accidentalmente un database di Azure Cosmos, è possibile ripristinare l'intero database o un subset dei contenitori all'interno del database. È anche possibile selezionare contenitori specifici tra database e ripristinarli in un nuovo account Azure Cosmos.

Quando si elimina o si modifica accidentalmente uno o più elementi all'interno di un contenitore (caso di danneggiamento dei dati), è necessario specificare l'ora in cui eseguire il ripristino. Il tempo è importante in caso di danneggiamento dei dati. Poiché il contenitore è attivo, il backup è ancora in esecuzione, pertanto se si attende il superamento del periodo di memorizzazione (il valore predefinito è otto ore), i backup verranno sovrascritti. Per evitare la sovrascrittura del backup, aumentare la conservazione del backup per l'account per almeno sette giorni. È consigliabile aumentare la conservazione entro 8 ore dal danneggiamento dei dati.

Se i dati sono stati eliminati o danneggiati per errore, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro 8 ore, in modo che il team di Azure Cosmos DB possa aiutare a ripristinare i dati dai backup. In questo modo il team di supporto di Azure Cosmos DB avrà tempo sufficiente per ripristinare l'account.

> [!NOTE]
> Dopo aver ripristinato i dati, non tutte le funzionalità o le impostazioni di origine vengono trasferite nell'account ripristinato. Le impostazioni seguenti non vengono trasferite al nuovo account:
> * Elenchi di controllo di accesso di VNET
> * Stored procedure, trigger e funzioni definite dall'utente
> * Impostazioni per più aree  

Se si effettua il provisioning della velocità effettiva a livello di database, in questo caso il processo di backup e ripristino viene eseguito a livello dell'intero database e non a livello dei singoli contenitori. In questi casi, non è possibile selezionare un subset di contenitori da ripristinare.

## <a name="options-to-manage-your-own-backups"></a>Opzioni per la gestione dei backup

Con gli account API SQL di Azure Cosmos DB è anche possibile gestire backup propri adottando uno degli approcci seguenti:

* Usare [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) per spostare periodicamente i dati in una risorsa di archiviazione di propria scelta.

* Usare Azure Cosmos DB [feed di modifiche](change-feed.md) per leggere i dati periodicamente per i backup completi o per le modifiche incrementali e archiviarli nella propria risorsa di archiviazione.

## <a name="post-restore-actions"></a>Azioni successive al ripristino

L'obiettivo principale del ripristino dei dati è ripristinare i dati eliminati o modificati accidentalmente. Pertanto, è consigliabile esaminare prima di tutto il contenuto dei dati ripristinati per assicurarsi che siano quelli previsti. Se tutto sembra corretto, è possibile eseguire la migrazione dei dati all'account primario. Sebbene sia possibile usare l'account ripristinato come nuovo account attivo, non è un'opzione consigliata se si dispone di carichi di lavoro di produzione. 

Dopo aver ripristinato i dati, si riceverà una notifica riguardante il nome del nuovo account (in genere nel formato `<original-name>-restored1`) e l'ora del ripristino dell'account. L'account ripristinato avrà la stessa velocità effettiva di cui è stato effettuato il provisioning, gli stessi criteri di indicizzazione e sarà incluso nella stessa area dell'account originale. Un utente che è l'amministratore della sottoscrizione o un coamministratore può visualizzare l'account ripristinato.

### <a name="migrate-data-to-the-original-account"></a>Migrare i dati all'account originale

Di seguito sono riportati diversi modi per eseguire la migrazione dei dati all'account originale:

* Usare l' [utilità di migrazione dati Azure Cosmos DB](import-data.md).
* Usare il [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Usare il [feed delle modifiche](change-feed.md) in Azure Cosmos DB.
* È possibile scrivere codice personalizzato.

Si consiglia di eliminare il contenitore o il database immediatamente dopo la migrazione dei dati. Se non si eliminano i database o i contenitori ripristinati, questi elementi genereranno costi per unità richieste, archiviazione e dati in uscita.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile consultare informazioni su come ripristinare i dati da un account Azure Cosmos o su come eseguire la migrazione dei dati in un account Azure Cosmos

* Per creare una richiesta di ripristino, contattare il supporto tecnico di Azure e [aprire un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Usare un feed di modifiche di Cosmos DB](change-feed.md) per spostare i dati in Azure Cosmos DB.
* [Usare Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) per spostare i dati in Azure Cosmos DB.


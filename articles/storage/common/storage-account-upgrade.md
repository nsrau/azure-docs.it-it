---
title: Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico V2-archiviazione di Azure | Microsoft Docs
description: Eseguire l'aggiornamento a account di archiviazione per utilizzo generico V2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: tamram
ms.openlocfilehash: e24b7efb9f4af9f730ce79751e2fc5a9d210edbd
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806984"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico V2

Gli account di archiviazione per utilizzo generico V2 supportano le funzionalità di archiviazione di Azure più recenti e incorporano tutte le funzionalità degli account di archiviazione BLOB e V1 per utilizzo generico. Gli account per utilizzo generico v2 rappresentano la scelta consigliata per la maggior parte degli scenari. Gli account per utilizzo generico v2 offrono i prezzi per gigabyte più bassi per Archiviazione di Azure, oltre a prezzi per transazione competitivi a livello di settore. Gli account per utilizzo generico V2 supportano i livelli di accesso account predefinito di livello frequente o sporadico e a livello di BLOB tra accesso frequente, ad accesso sporadico o archivio.

L'aggiornamento a un account di archiviazione per utilizzo generico V2 dagli account di archiviazione BLOB o V1 per utilizzo generico è semplice. È possibile eseguire l'aggiornamento tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> L'aggiornamento di un account di archiviazione BLOB o per utilizzo generico V1 a utilizzo generico V2 è permanente e non può essere annullato.

# <a name="portaltabazure-portal"></a>[di Microsoft Azure](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione.
3. Nella sezione **Impostazioni** fare clic su **Configurazione**.
4. In **Tipologia account** fare clic su **Aggiorna**.
5. In **Conferma aggiornamento** digitare il nome dell'account.
6. Fare clic su **Aggiorna** nella parte inferiore del pannello.

    ![Tipo di account di aggiornamento](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per aggiornare un account per utilizzo generico V1 a un account per utilizzo generico V2 usando PowerShell, aggiornare prima di tutto PowerShell per usare la versione più recente del modulo **AZ. storage** . Per informazioni su come installare PowerShell, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Chiamare quindi il comando seguente per aggiornare l'account, sostituendo il nome del gruppo di risorse, il nome dell'account di archiviazione e il livello di accesso dell'account desiderato.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

Per aggiornare un account di uso generico V1 a un account per utilizzo generico V2 usando l'interfaccia della riga di comando di Azure, installare prima la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni sull'installazione dell'interfaccia della riga di comando, vedere [Install the Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Installare l'interfaccia della riga di comando di Azure 2.0).

Chiamare quindi il comando seguente per aggiornare l'account, sostituendo il nome del gruppo di risorse, il nome dell'account di archiviazione e il livello di accesso dell'account desiderato.

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Specificare un livello di accesso per i dati BLOB

Gli account per utilizzo generico V2 supportano tutti i servizi di archiviazione di Azure e gli oggetti dati, ma i livelli di accesso sono disponibili solo per i BLOB in blocchi nell'archivio BLOB. Quando si esegue l'aggiornamento a un account di archiviazione per utilizzo generico V2, è possibile specificare un livello di accesso account predefinito di accesso frequente o sporadico, che indica il livello predefinito in cui verranno caricati i dati BLOB come se il singolo parametro del livello di accesso BLOB non fosse specificato.

I livelli di accesso ai BLOB consentono di scegliere la risorsa di archiviazione più conveniente in base ai modelli di utilizzo previsti. I BLOB in blocchi possono essere archiviati in livelli ad accesso frequente, ad accesso sporadico o archivio. Per altre informazioni sui livelli di accesso, vedere [Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio](../blobs/storage-blob-storage-tiers.md).

Per impostazione predefinita, viene creato un nuovo account di archiviazione nel livello di accesso frequente e un account di archiviazione per utilizzo generico V1 può essere aggiornato al livello di account frequente o ad accesso sporadico. Se non è stato specificato un livello di accesso dell'account durante l'aggiornamento, verrà aggiornato a attivo per impostazione predefinita. Se si sta esplorando il livello di accesso da usare per l'aggiornamento, considerare lo scenario di utilizzo dei dati corrente. Esistono due scenari utente tipici per la migrazione a un account per utilizzo generico V2:

* Si dispone di un account di archiviazione di uso generico V1 esistente e si vuole valutare un aggiornamento a un account di archiviazione per utilizzo generico V2, con il livello di accesso di archiviazione appropriato per i dati BLOB.
* Si è deciso di usare un account di archiviazione per utilizzo generico v2 o se ne ha già uno e si vuole valutare se usare il livello di accesso di archiviazione ad accesso frequente o sporadico per i dati BLOB.

In entrambi i casi, la prima priorità consiste nel stimare i costi di archiviazione, accesso e funzionamento dei dati archiviati in un account di archiviazione per utilizzo generico V2 e confrontarli con i costi attuali.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

L'aggiornamento di un account di archiviazione V1 a un account per utilizzo generico V2 è gratuito. È possibile specificare il livello di account desiderato durante il processo di aggiornamento. Se non si specifica un livello di account durante l'aggiornamento, verrà `Hot`il livello di account predefinito dell'account aggiornato. Tuttavia, la modifica del livello di accesso alle archiviazioni dopo l'aggiornamento può comportare modifiche alla fattura, in modo che sia consigliabile specificare il nuovo livello di account durante l'aggiornamento.

Tutti gli account di archiviazione usano per l'archivio BLOB un modello di determinazione prezzi basato sul livello di ogni BLOB. Quando si usa un account di archiviazione, tenere conto delle considerazioni seguenti relative alla fatturazione:

* **Costi di archiviazione**: oltre alla quantità di dati archiviati, il costo di archiviazione dei dati varia a seconda del livello di accesso di archiviazione. Il costo per gigabyte diminuisce passando a un livello ad accesso più sporadico.

* **Costi di accesso ai dati**: i costi di accesso ai dati aumentano passando a un livello ad accesso più sporadico. Per i dati nei livelli di archiviazione ad accesso sporadico e archivio vengono addebitati i costi per l'accesso ai dati per gigabyte per le operazioni di lettura.

* **Costi delle transazioni**: sono previsti costi per transazione per tutti i livelli e tali costi aumentano passando a un livello ad accesso più sporadico.

* **Costi di trasferimento dati con la replica geografica**: si applicano solo agli account per cui è configurata la replica geografica, incluse l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.

* **Costi**per il trasferimento di dati in uscita: i trasferimenti di dati in uscita (dati trasferiti da un'area di Azure) comportano la fatturazione per l'utilizzo della larghezza di banda per ogni gigabyte, coerenti con gli account di archiviazione per utilizzo generico.

* **Modifica del livello di accesso alla**risorsa di archiviazione: la modifica del livello di accesso di archiviazione account da sporadico a frequente comporta un addebito uguale alla lettura di tutti i dati esistenti nell'account di archiviazione. Il passaggio dell'account dal livello di accesso all'archiviazione ad accesso frequente a quello ad accesso sporadico comporta invece un addebito corrispondente a quello per la scrittura di tutti i dati nel livello ad accesso sporadico (solo per account per utilizzo generico v2).

> [!NOTE]
> Per altre informazioni sul modello di determinazione prezzi per gli account di archiviazione, vedere la pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). Per altre informazioni sugli addebiti per i trasferimenti dati in uscita, vedere la pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Stimare i costi per i modelli di utilizzo correnti

Per stimare il costo di archiviazione e accesso ai dati BLOB in un account di archiviazione per utilizzo generico V2 in un livello specifico, valutare il modello di utilizzo esistente o approssimare il modello di utilizzo previsto. In genere, è consigliabile conoscere quanto segue:

* Uso dell'archiviazione BLOB, in gigabyte, inclusi:
    - Quantità di dati archiviata nell'account di archiviazione
    - Variazione nel volume di dati su base mensile e se i dati precedenti vengono costantemente sostituiti da nuovi dati
* Modello di accesso primario per i dati di archiviazione BLOB, inclusi:
    - Quantità di dati letti e scritti nell'account di archiviazione
    - Numero di operazioni di lettura e di scrittura eseguite sui dati nell'account di archiviazione

Per scegliere il miglior livello di accesso per le proprie esigenze, può essere utile determinare la capacità di dati del BLOB e la modalità d'uso di tali dati. Questa scelta può essere fatta al meglio osservando le metriche di monitoraggio per l'account.

### <a name="monitoring-existing-storage-accounts"></a>Monitoraggio degli account di archiviazione esistenti

Per monitorare gli account di archiviazione esistenti e raccoglierne i dati, è possibile usare Analisi archiviazione di Azure, che esegue la registrazione e fornisce i dati delle metriche per un account di archiviazione. Analisi archiviazione può archiviare metriche che includono le statistiche delle transazioni aggregate e i dati sulla capacità relativi alle richieste a un servizio di archiviazione per i tipi di account di archiviazione BLOB, per utilizzo generico v1 e per utilizzo generico v2. I dati vengono archiviati in tabelle note nello stesso account di archiviazione.

Per altre informazioni, vedere [About Storage Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343258.aspx) (Informazioni sulle metriche di Analisi archiviazione) e [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx) (Schema di tabella della metrica di Analisi archiviazione)

> [!NOTE]
> Gli account di archiviazione BLOB espongono l'endpoint di servizio tabelle solo per l'archiviazione e l'accesso ai dati delle metriche per l'account specifico.

Per monitorare l'utilizzo delle risorse di archiviazione per l'archiviazione BLOB, è necessario abilitare la metrica della capacità.
Con questa impostazione abilitata, i dati sulla capacità vengono registrati ogni giorno per il servizio BLOB di un account di archiviazione e registrati come una voce di tabella che viene scritta nella tabella *$MetricsCapacityBlob* nello stesso account di archiviazione.

Per monitorare i modelli di accesso ai dati per l'archiviazione BLOB, è necessario abilitare la metrica delle transazioni orarie dall'API. Quando è abilitata questa metrica, le transazioni vengono aggregate ogni ora per ogni API e registrate come una voce della tabella che viene scritta nella tabella *$MetricsHourPrimaryTransactionsBlob* nello stesso account di archiviazione. La tabella *$MetricsHourSecondaryTransactionsBlob* registra le transazioni nell'endpoint secondario quando si usano account di archiviazione con ridondanza geografica e accesso in lettura.

> [!NOTE]
> Se si dispone di un account di archiviazione per utilizzo generico in cui sono archiviati BLOB di pagine e dischi di macchine virtuali oppure code, file o tabelle, insieme ai dati BLOB in blocchi e di Accodamento, questo processo di stima non è applicabile. I dati sulla capacità non distinguono i BLOB in blocchi dagli altri tipi, di conseguenza non offrono dati sulla capacità per altri tipi di dati. Se si usano questi tipi, una metodologia alternativa consiste nell'esaminare le quantità nella fattura più recente.

Per ottenere una buona approssimazione del modello di accesso e di utilizzo dei dati, è consigliabile scegliere un periodo di conservazione per le metriche che rappresenti l'utilizzo regolare ed estrapolarne i dati. Una possibilità consiste nel conservare i dati delle metriche per sette giorni e raccoglierli ogni settimana per analizzarli alla fine del mese. Un'altra possibilità è conservare i dati di metrica per gli ultimi 30 giorni e raccogliere e analizzare i dati alla fine del periodo di 30 giorni.

Per informazioni dettagliate sull'abilitazione, la raccolta e la visualizzazione dei dati di metrica, vedere [metriche di analisi archiviazione](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Anche l'archiviazione, l'accesso e il download dei dati di analisi vengono addebitati come avviene per i dati utente normali.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Uso della metrica di utilizzo per stimare i costi

#### <a name="capacity-costs"></a>Costi di capacità

L'ultima voce nella tabella della metrica della capacità *$MetricsCapacityBlob* con la chiave di riga *'data'* mostra la capacità di archiviazione utilizzata dai dati utente. L'ultima voce nella tabella della metrica della capacità *$MetricsCapacityBlob* con la chiave di riga *'analytics'* mostra la capacità di archiviazione utilizzata dai log di analisi.

Questa capacità totale utilizzata sia dai dati utente che dai log di analisi, se abilitati, può quindi essere usata per stimare i costi di archiviazione dei dati nell'account di archiviazione. Lo stesso metodo può essere usato anche per stimare i costi di archiviazione negli account di archiviazione per utilizzo generico v1.

#### <a name="transaction-costs"></a>Costi di transazione

La somma di *'TotalBillableRequests'* in tutte le voci relative a un'API nella tabella della metrica delle transazioni indica il numero totale di transazioni per quell'API specifica. *Ad esempio*, il numero totale di transazioni *'GetBlob'* in un dato periodo può essere calcolato dalla somma delle richieste fatturabili totali per tutte le voci con la chiave di riga *'user;GetBlob'* .

Per stimare i costi delle transazioni per gli account di archiviazione BLOB, è necessario suddividere le transazioni in tre gruppi, perché i prezzi vengono determinati in modo diverso.

* Transazioni di scrittura, ad esempio *'PutBlob'* , *'PutBlock'* , *'PutBlockList'* , *'AppendBlock'* , *'ListBlobs'* , *'ListContainers'* , *'CreateContainer'* , *'SnapshotBlob'* e *'CopyBlob'* .
* Transazioni di eliminazione, ad esempio *'DeleteBlob'* e *'DeleteContainer'* .
* Tutte le altre transazioni.

Per stimare i costi delle transazioni per gli account di archiviazione per utilizzo generico v1, è necessario aggregare tutte le transazioni indipendentemente dall'operazione o dall'API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Costi di trasferimento dati con replica geografica e di accesso ai dati

Mentre l'analisi dell'archiviazione non fornisce la quantità di dati in lettura e scrittura in un account di archiviazione, è possibile effettuare una stima approssimativa esaminando la tabella della metrica delle transazioni. La somma di *'TotalIngress'* in tutte le voci relative a un'API nella tabella della metrica delle transazione indica la quantità totale di dati in ingresso, espressa in byte, per quell'API specifica. In modo analogo, la somma di *'TotalEgress'* indica la quantità totale di dati in uscita, in byte.

Per stimare i costi di accesso ai dati per gli account di archiviazione BLOB, è necessario suddividere le transazioni in due gruppi.

* La quantità di dati recuperata dall'account di archiviazione può essere stimata esaminando la somma di *'TotalEgress'* principalmente per le operazioni *'GetBlob'* e *'CopyBlob'* .

* La quantità di dati scritta nell'account di archiviazione può essere stimata esaminando la somma di *'TotalIngress'* principalmente per le operazioni *'PutBlob'* , *'PutBlock'* , *'CopyBlob'* e *'AppendBlock'* .

I costi di trasferimento dati con replica geografica per gli account di archiviazione BLOB possono anche essere calcolati usando la stima per la quantità di dati scritti quando si usa un account di archiviazione con ridondanza geografica o con ridondanza geografica e accesso in lettura.

> [!NOTE]
> Per un esempio più dettagliato relativo al calcolo dei costi per l'uso del livello di accesso all'archiviazione ad accesso frequente o sporadico, vedere la domanda frequente *"Che cosa sono i livelli di archiviazione ad accesso frequente e accesso sporadico e come si determina quale usare?"* nella [pagina Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account di archiviazione](storage-quickstart-create-account.md)
- [Gestire gli account di archiviazione di Azure](storage-account-manage.md)

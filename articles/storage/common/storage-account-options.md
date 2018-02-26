---
title: Opzioni di account di archiviazione di Azure | Microsoft Docs
description: Informazioni sulle opzioni disponibili per l'uso di Archiviazione di Azure.
services: storage
author: jirwin
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: get-started-article
ms.date: 01/17/2018
ms.author: jirwin
ms.openlocfilehash: aa8704b9991dde639b50a5b6c9a27ad5cd08ad02
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="azure-storage-account-options"></a>Opzioni di account di archiviazione di Azure

## <a name="overview"></a>Panoramica
Archiviazione di Azure offre tre diverse opzioni di account, con differenze nei prezzi e nelle funzionalità supportate. È importante tenere in considerazione tali differenze prima di creare un account di archiviazione per determinare l'opzione ottimale per le proprie applicazioni. Le tre opzioni disponibili per l'account di archiviazione sono le seguenti:

* Account per **utilizzo generico v2** 
* Account per **utilizzo generico v1**
* Account di **archiviazione BLOB**

Ogni tipo di account viene descritto più dettagliatamente nella sezione seguente.

## <a name="storage-account-options"></a>Opzioni dell'account di archiviazione

### <a name="general-purpose-v2"></a>Utilizzo generico v2

Gli account per utilizzo generico v2 sono account di archiviazione che supportano tutte le più recenti funzionalità relative a BLOB, file, code e tabelle. Gli account per utilizzo generico v2 supportano tutte le API e le funzionalità supportate negli account di archiviazione per utilizzo generico v1 e di archiviazione BLOB. Supportano inoltre le stesse caratteristiche di tali tipi di account in termini di durabilità, disponibilità, scalabilità e prestazioni. Il listino prezzi degli account per utilizzo generico v2 è stato definito in modo da offrire i prezzi più bassi per gigabyte e garantire prezzi competitivi per transazione nel settore.

È possibile eseguire l'aggiornamento dall'account per utilizzo generico v1 a un account per utilizzo generico v2 tramite PowerShell o l'interfaccia della riga di comando di Azure. 

Per i BLOB in blocchi in un account di archiviazione per utilizzo generico v2, è possibile scegliere tra i livelli di archiviazione ad accesso frequente e sporadico a livello di account e i livelli ad accesso frequente, ad accesso sporadico e archivio a livello di BLOB, a seconda dei modelli di accesso. Per ottimizzare i costi, archiviare i dati a cui si accede frequentemente, poco frequentemente e raramente rispettivamente nei livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio. 

Gli account di archiviazione per utilizzo generico v2 espongono a livello di account l'attributo **Livello di accesso**, che consente di specificare **Frequente** o **Sporadico** come livello predefinito per l'account di archiviazione. Il livello predefinito per l'account di archiviazione viene applicato a tutti i BLOB per cui non è impostato un livello esplicito a livello di BLOB. Se il modello di utilizzo dei dati cambia, è anche possibile passare da uno di questi livelli di archiviazione a un altro in qualsiasi momento. Il **livello archivio** è applicabile solo a livello di BLOB.

> [!NOTE]
> La modifica del livello di archiviazione può comportare costi aggiuntivi. Per altre informazioni, vedere la sezione [Prezzi e fatturazione](#pricing-and-billing).
>
> Per la maggior parte degli scenari è consigliabile preferire gli account di archiviazione per utilizzo generico v2 agli account di archiviazione BLOB.

### <a name="upgrade-a-storage-account-to-gpv2"></a>Aggiornare un account di archiviazione a un account per utilizzo generico v2

Gli utenti possono eseguire in qualsiasi momento l'aggiornamento da un account per utilizzo generico v1 a un account per utilizzo generico v2 tramite PowerShell o l'interfaccia della riga di comando di Azure. Questa modifica non può essere annullata e non sono consentite altre modifiche.

#### <a name="upgrade-with-powershell"></a>Eseguire l'aggiornamento con PowerShell

Per aggiornare un account per utilizzo generico v1 a un account per utilizzo generico v2 con PowerShell, aggiornare prima PowerShell in modo che usi l'ultima versione del modulo **AzureRm.Storage**. Per informazioni su come installare PowerShell, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Chiamare quindi il comando seguente per aggiornare l'account, sostituendo il nome del gruppo di risorse e l'account di archiviazione:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

#### <a name="upgrade-with-azure-cli"></a>Eseguire l'aggiornamento con l'interfaccia della riga di comando di Azure

Per aggiornare un account per utilizzo generico v1 a un account per utilizzo generico v2 con l'interfaccia della riga di comando di Azure, installare prima l'ultima versione dell'interfaccia della riga di comando di Azure. Per informazioni sull'installazione dell'interfaccia della riga di comando, vedere [Install the Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Installare l'interfaccia della riga di comando di Azure 2.0). Chiamare quindi il comando seguente per aggiornare l'account, sostituendo il nome del gruppo di risorse e l'account di archiviazione:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```` 

### <a name="general-purpose-v1"></a>Utilizzo generico v1

Gli account per utilizzo generico v1 consentono di accedere a tutti i servizi di archiviazione di Azure, ma potrebbero non offrire le funzionalità più recenti o i prezzi più bassi per gigabyte. In questi account, ad esempio, non sono supportate l'archiviazione ad accesso sporadico e l'archiviazione archivio. Dal momento che i prezzi per le transazioni degli account per utilizzo generico v1 sono inferiori, questo tipo di account potrebbe essere vantaggioso per carichi di lavoro con varianza o frequenze di lettura elevate.

Gli account di archiviazione per utilizzo generico v1 sono il tipo di account di archiviazione meno recente e costituiscono l'unica tipologia utilizzabile con il modello di distribuzione classica. 

### <a name="blob-storage-accounts"></a>Account di archiviazione BLOB

Gli account di archiviazione BLOB supportano tutte le funzionalità per i BLOB in blocchi degli account per utilizzo generico v2, ma sono limitati al supporto dei soli BLOB in blocchi. I prezzi sono in linea di massima simili a quelli degli account per utilizzo generico v2. I clienti dovrebbero esaminare le differenze di prezzo tra gli account di archiviazione BLOB e per utilizzo generico v2 e valutare la possibilità di un aggiornamento ad account per utilizzo generico v2. Questo aggiornamento non può essere annullato.

Sarà presto possibile aggiornare gli account di archiviazione BLOB ad account per utilizzo generico v2.

> [!NOTE]
> Gli account di archiviazione BLOB supportano solo i BLOB in blocchi e i BLOB di aggiunta, non i BLOB di pagine.

## <a name="recommendations"></a>Raccomandazioni

Per altre informazioni sugli account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Per le applicazioni che richiedono solo archivi BLOB in blocchi o di aggiunta, è consigliabile usare account di archiviazione per utilizzo generico v2, per sfruttare il modello di determinazione prezzi differenziato dell'archiviazione a livelli. È però possibile che si vogliano usare account per utilizzo generico v1 in determinati scenari, come quelli descritti di seguito.

* È ancora necessario usare il modello di distribuzione classica. Gli account di archiviazione BLOB sono disponibili solo con il modello di distribuzione Azure Resource Manager.

* Si usano volumi elevati di transazioni o larghezza di banda di replica geografica, che presentano un costo superiore negli account di archiviazione BLOB e per utilizzo generico v2 rispetto agli account per utilizzo generico v1, e lo spazio di archiviazione non è sufficiente per trarre vantaggio dai costi inferiori per GB di archiviazione.

* Si usa una versione dell' [API REST dei servizi di archiviazione](https://msdn.microsoft.com/library/azure/dd894041.aspx) precedente alla 2014-02-14 o una libreria client con una versione precedente alla 4.x e non è possibile aggiornare l'applicazione.

> [!NOTE]
> Gli account di archiviazione BLOB sono attualmente supportati in tutte le aree di Azure.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione
Tutti gli account di archiviazione usano per l'archivio BLOB un modello di determinazione prezzi basato sul livello di ogni BLOB. Quando si usa un account di archiviazione, tenere conto delle considerazioni seguenti relative alla fatturazione:

* **Costi di archiviazione**: oltre alla quantità di dati archiviati, il costo di archiviazione dei dati varia a seconda del livello di archiviazione. Il costo per gigabyte diminuisce passando a un livello ad accesso più sporadico.

* **Costi di accesso ai dati**: i costi di accesso ai dati aumentano passando a un livello ad accesso più sporadico. Per i dati nei livelli di archiviazione ad accesso sporadico e archivio vengono addebitati i costi per l'accesso ai dati per gigabyte per le operazioni di lettura.

* **Costi delle transazioni**: sono previsti costi per transazione per tutti i livelli e tali costi aumentano passando a un livello ad accesso più sporadico.

* **Costi di trasferimento dati con la replica geografica**: si applicano solo agli account per cui è configurata la replica geografica, incluse l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.

* **Costi di trasferimento dati in uscita**: i trasferimenti dati in uscita (dati che vengono trasferiti al di fuori di un'area di Azure) vengono fatturati in base all'utilizzo di larghezza di banda per singolo gigabyte, come per gli account di archiviazione di uso generico.

* **Modifica del livello di archiviazione**: la modifica del livello di archiviazione da sporadico a frequente comporta un addebito corrispondente a quello per la lettura di tutti i dati esistenti nell'account di archiviazione. Il passaggio dell'account dal livello di archiviazione ad accesso frequente a quello ad accesso sporadico comporta invece un addebito corrispondente a quello per la scrittura di tutti i dati nel livello ad accesso sporadico (solo per account per utilizzo generico v2).

> [!NOTE]
> Per altre informazioni sul modello di determinazione prezzi per gli account di archiviazione BLOB, vedere la pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). Per altre informazioni sugli addebiti per i trasferimenti dati in uscita, vedere la pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Scenari introduttivi

Questa sezione presenta gli scenari seguenti usando il portale di Azure:

* Come creare un account di archiviazione per utilizzo generico v2.
* Come convertire un account di archiviazione BLOB o per utilizzo generico v1 in un account di archiviazione per utilizzo generico v2.
* Come impostare il livello dell'account o del BLOB in un account di archiviazione per utilizzo generico v2.

Negli esempi seguenti non è possibile impostare il livello di accesso su archivio perché tale impostazione si applica all'intero account di archiviazione. Il livello archivio può essere impostato solo per un BLOB specifico.

### <a name="create-a-gpv2-storage-account-using-the-azure-portal"></a>Creare un account di archiviazione per utilizzo generico v2 con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu Hub scegliere **Crea una risorsa** > **Dati e archiviazione** > **Account di archiviazione**.

3. Immettere un nome per l'account di archiviazione.

    Questo nome deve essere globalmente univoco. Viene usato come parte dell'URL usato per accedere agli oggetti nell'account di archiviazione.  

4. Selezionare **Resource Manager** come modello di distribuzione.

    L'archiviazione a livelli può essere usata solo con gli account di archiviazione di Resource Manager, che è il modello di distribuzione consigliato per le nuove risorse. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](../../azure-resource-manager/resource-group-overview.md).  

5. Nell'elenco a discesa **Tipologia account** selezionare **General Purpose v2** (Utilizzo generico v2).

    Quando si seleziona questa opzione, il livello di prestazioni viene impostato su Standard. L'archiviazione a livelli non è disponibile con il livello di prestazioni Premium.

6. Selezionare l'opzione di replica per l'account di archiviazione: **Archiviazione con ridondanza locale**, **Archiviazione con ridondanza della zona**, **Archiviazione con ridondanza geografica** o **Archiviazione con ridondanza geografica e accesso in lettura**. L'opzione predefinita è **Archiviazione con ridondanza geografica e accesso in lettura**.

    Le opzioni disponibili sono Archiviazione con ridondanza locale, Archiviazione con ridondanza della zona, Archiviazione con ridondanza geografica (due aree) e Archiviazione con ridondanza geografica e accesso in lettura (due aree con accesso in lettura alla seconda).

    Per altre informazioni sulle opzioni di replica di Archiviazione di Azure, vedere [Replica di Archiviazione di Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

7. Selezionare il livello di archiviazione corretto per le proprie esigenze: impostare il **livello di accesso** su **sporadico** o **frequente**. Il livello predefinito è **Frequente**.

8. Selezionare la sottoscrizione in cui creare il nuovo account di archiviazione.

9. Specificare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

10. Selezionare l'area per l'account di archiviazione.

11. Fare clic su **Crea** per creare l'account di archiviazione.

### <a name="convert-a-gpv1-account-to-a-gpv2-storage-account-using-the-azure-portal"></a>Convertire un account per utilizzo generico v1 in un account di archiviazione per utilizzo generico v2 con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per passare all'account di archiviazione, selezionare **Tutte le risorse** e quindi selezionare l'account di archiviazione.

3. Nella sezione Impostazioni fare clic su **Configurazione**.

4. In **Tipologia account** fare clic su **Aggiorna**.

5. In **Conferma aggiornamento** digitare il nome dell'account. 

5. Fare clic su Aggiorna nella parte inferiore del pannello.

### <a name="change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal"></a>Modificare il livello di archiviazione di un account di archiviazione per utilizzo generico v2 con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per passare all'account di archiviazione, selezionare **Tutte le risorse** e quindi selezionare l'account di archiviazione.

3. Nel pannello Impostazioni fare clic su **Configurazione** per visualizzare e/o modificare la configurazione dell'account.

4. Selezionare il livello di archiviazione corretto per le proprie esigenze: impostare il **livello di accesso** su **sporadico** o **frequente**.

5. Fare clic su Salva nella parte superiore del pannello.

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>Modificare il livello di archiviazione di un BLOB usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per passare al BLOB nell'account di archiviazione, selezionare **Tutte le risorse**, selezionare l'account di archiviazione, selezionare il contenitore e quindi selezionare il BLOB.

3. Nel pannello delle proprietà del BLOB fare clic sul menu a discesa **Livello di accesso** per selezionare il livello di archiviazione **Frequente**, **Sporadico** o **Archivio**.

5. Fare clic su Salva nella parte superiore del pannello.

> [!NOTE]
> La modifica del livello di archiviazione può comportare costi aggiuntivi. Per altre informazioni, vedere la sezione [Prezzi e fatturazione](#pricing-and-billing).


## <a name="evaluating-and-migrating-to-gpv2-storage-accounts"></a>Valutazione e migrazione ad account di archiviazione per utilizzo generico v2
Questa sezione illustra agli utenti come passare senza problemi all'uso di account di archiviazione per utilizzo generico v2 (anziché per utilizzo generico v1). Esistono due scenari utente:

* È disponibile un account di archiviazione per utilizzo generico v1 esistente e si vuole valutare una modifica per passare a un account di archiviazione per utilizzo generico v2 con il livello di archiviazione corretto.
* Si è deciso di usare un account di archiviazione per utilizzo generico v2 o ne già disponibile uno e si vuole valutare se è opportuno usare il livello di archiviazione ad accesso frequente oppure quello ad accesso sporadico.

In entrambi i casi, è prioritario stimare il costo di archiviazione e accesso ai dati archiviati in un account di archiviazione per utilizzo generico v2 e confrontarlo con i costi attuali.

## <a name="evaluating-gpv2-storage-account-tiers"></a>Valutazione dei livelli degli account di archiviazione per utilizzo generico v2

Per stimare il costo di archiviazione e accesso ai dati archiviati in un account di archiviazione per utilizzo generico v2, è necessario valutare il modello di utilizzo esistente o definire approssimativamente il modello di utilizzo previsto. In genere, è consigliabile conoscere quanto segue:

* Utilizzo dell'archiviazione: quanti dati vengono archiviati e come cambia questo valore ogni mese?

* Modelli di accesso alle risorse di archiviazione: quanti dati vengono letti e scritti nell'account, inclusi quelli nuovi? Quante transazioni vengono usate per accedere ai dati e che di che tipi di transazioni si tratta?

## <a name="monitoring-existing-storage-accounts"></a>Monitoraggio degli account di archiviazione esistenti

Per monitorare gli account di archiviazione esistenti e raccoglierne i dati, è possibile usare Analisi archiviazione di Azure, che esegue la registrazione e fornisce i dati delle metriche per un account di archiviazione. Analisi archiviazione può archiviare metriche che includono le statistiche delle transazioni aggregate e i dati sulla capacità relativi alle richieste a un servizio di archiviazione per i tipi di account di archiviazione BLOB, per utilizzo generico v1 e per utilizzo generico v2. I dati vengono archiviati in tabelle note nello stesso account di archiviazione.

Per altre informazioni, vedere [About Storage Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343258.aspx) (Informazioni sulle metriche di Analisi archiviazione) e [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx) (Schema di tabella della metrica di Analisi archiviazione).

> [!NOTE]
> Gli account di archiviazione BLOB espongono l'endpoint di servizio tabelle solo per l'archiviazione e l'accesso ai dati delle metriche per l'account specifico. Gli account di archiviazione con ridondanza della zona supportano la raccolta dei dati di metrica, mentre di archiviazione con ridondanza della zona (versione classica) non la supportano. Per altre informazioni sull'archiviazione con ridondanza della zona, vedere [Archiviazione con ridondanza della zona](storage-redundancy.md#zone-redundant-storage). 

Per monitorare l'utilizzo delle risorse di archiviazione per l'archiviazione BLOB, è necessario abilitare la metrica della capacità.
Con questa impostazione abilitata, i dati sulla capacità vengono registrati ogni giorno per il servizio BLOB di un account di archiviazione e registrati come una voce di tabella che viene scritta nella tabella *$MetricsCapacityBlob* nello stesso account di archiviazione.

Per monitorare i modelli di accesso ai dati per l'archiviazione BLOB, è necessario abilitare la metrica delle transazioni orarie dall'API. Quando è abilitata questa metrica, le transazioni vengono aggregate ogni ora per ogni API e registrate come una voce della tabella che viene scritta nella tabella *$MetricsHourPrimaryTransactionsBlob* nello stesso account di archiviazione. La tabella *$MetricsHourSecondaryTransactionsBlob* registra le transazioni nell'endpoint secondario quando si usano account di archiviazione con ridondanza geografica e accesso in lettura.

> [!NOTE]
> Se si ha un account di archiviazione per utilizzo generico in cui sono archiviati BLOB di pagine e dischi di macchine virtuali oppure code, file o tabelle insieme a dati di BLOB in blocchi e di aggiunta, questo processo di stima non è applicabile. I dati sulla capacità non distinguono i BLOB in blocchi dagli altri tipi, di conseguenza non offrono dati sulla capacità per altri tipi di dati. Se si usano questi tipi, una metodologia alternativa consiste nell'esaminare le quantità nella fattura più recente.

Per ottenere una buona approssimazione del modello di accesso e di utilizzo dei dati, è consigliabile scegliere un periodo di conservazione per le metriche che rappresenti l'utilizzo regolare ed estrapolarne i dati. Una possibilità consiste nel conservare i dati delle metriche per sette giorni e raccoglierli ogni settimana per analizzarli alla fine del mese. Un'altra possibilità è conservare i dati di metrica per gli ultimi 30 giorni e raccogliere e analizzare i dati alla fine del periodo di 30 giorni.

Per informazioni dettagliate su come abilitare, raccogliere e visualizzare i dati delle metriche, vedere [Abilitazione delle metriche di Archiviazione di Azure e visualizzazione dei dati delle metriche](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Anche l'archiviazione, l'accesso e il download dei dati di analisi vengono addebitati come avviene per i dati utente normali.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Uso della metrica di utilizzo per stimare i costi

#### <a name="storage-costs"></a>Costi di archiviazione

L'ultima voce nella tabella della metrica della capacità *$MetricsCapacityBlob* con la chiave di riga *'data'* mostra la capacità di archiviazione utilizzata dai dati utente. L'ultima voce nella tabella della metrica della capacità *$MetricsCapacityBlob* con la chiave di riga *'analytics'* mostra la capacità di archiviazione utilizzata dai log di analisi.

Questa capacità totale utilizzata sia dai dati utente che dai log di analisi, se abilitati, può quindi essere usata per stimare i costi di archiviazione dei dati nell'account di archiviazione. Lo stesso metodo può essere usato anche per stimare i costi di archiviazione negli account di archiviazione per utilizzo generico v1.

#### <a name="transaction-costs"></a>Costi di transazione

La somma di *'TotalBillableRequests'*in tutte le voci relative a un'API nella tabella della metrica delle transazioni indica il numero totale di transazioni per quell'API specifica. *Ad esempio*, il numero totale di transazioni *'GetBlob'* in un dato periodo può essere calcolato dalla somma delle richieste fatturabili totali per tutte le voci con la chiave di riga *'user;GetBlob'*.

Per stimare i costi delle transazioni per gli account di archiviazione BLOB, è necessario suddividere le transazioni in tre gruppi, perché i prezzi vengono determinati in modo diverso.

* Transazioni di scrittura, ad esempio *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transazioni di eliminazione, ad esempio *'DeleteBlob'* e *'DeleteContainer'*.
* Tutte le altre transazioni.

Per stimare i costi delle transazioni per gli account di archiviazione per utilizzo generico v1, è necessario aggregare tutte le transazioni indipendentemente dall'operazione o dall'API.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Costi di trasferimento dati con replica geografica e di accesso ai dati

Mentre l'analisi dell'archiviazione non fornisce la quantità di dati in lettura e scrittura in un account di archiviazione, è possibile effettuare una stima approssimativa esaminando la tabella della metrica delle transazioni. La somma di *'TotalIngress'* in tutte le voci relative a un'API nella tabella della metrica delle transazione indica la quantità totale di dati in ingresso, espressa in byte, per quell'API specifica. In modo analogo,, la somma di *'TotalEgress'* indica la quantità totale di dati in uscita, in byte.

Per stimare i costi di accesso ai dati per gli account di archiviazione BLOB, è necessario suddividere le transazioni in due gruppi.

* La quantità di dati recuperata dall'account di archiviazione può essere stimata esaminando la somma di *'TotalEgress'* principalmente per le operazioni *'GetBlob'* e *'CopyBlob'*.

* La quantità di dati scritta nell'account di archiviazione può essere stimata esaminando la somma di *'TotalIngress'* principalmente per le operazioni *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

I costi di trasferimento dati con replica geografica per gli account di archiviazione BLOB possono anche essere calcolati usando la stima per la quantità di dati scritti quando si usa un account di archiviazione con ridondanza geografica o con ridondanza geografica e accesso in lettura.

> [!NOTE]
> Per un esempio più dettagliato relativo al calcolo dei costi per l'uso del livello di archiviazione ad accesso frequente o sporadico, vedere la domanda frequente *Che cosa sono i livelli di accesso frequente e accesso sporadico e come si determina quale usare?* nella [pagina Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="migrating-existing-data"></a>Migrazione di dati esistenti

Un account per utilizzo generico v1 può essere facilmente aggiornato a un account per utilizzo generico v2 senza tempi di inattività, modifiche a livello di API o necessità di eseguire la migrazione dei dati. Per questo motivo, è consigliabile eseguire la migrazione degli account per utilizzo generico v1 ad account per utilizzo generico v2, invece che ad account di archiviazione BLOB.

Se è necessario eseguire la migrazione a un account di archiviazione BLOB, invece, è possibile usare le istruzioni seguenti.

Un account di archiviazione BLOB serve per archiviare solo BLOB in blocchi e BLOB di aggiunta. Gli account di archiviazione per uso generico esistenti, che consentono di archiviare tabelle, code, file e dischi oltre ai BLOB, non possono essere convertiti in account di archiviazione BLOB. Per usare i livelli di archiviazione, è necessario creare nuovi account di archiviazione BLOB ed eseguire la migrazione dei dati esistenti negli account appena creati.

È possibile usare i metodi seguenti per eseguire la migrazione dei dati esistenti agli account di archiviazione BLOB da dispositivi di archiviazione locali, da provider di archiviazione cloud di terze parti o dagli account di archiviazione di uso generico esistenti in Azure:

### <a name="azcopy"></a>AzCopy

AzCopy è un'utilità da riga di comando Windows progettata per offrire prestazioni elevate di copia dei dati da e verso Archiviazione di Azure. È possibile usare AzCopy per copiare i dati nell'account di archiviazione BLOB dagli account di archiviazione di uso generico esistenti o per caricare i dati da dispositivi di archiviazione locali all'account di archiviazione BLOB.

Per altre informazioni, vedere [Trasferire dati con l'utilità da riga di comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Libreria di spostamento dei dati

La libreria di spostamento dei dati di Archiviazione di Azure per .NET si basa sul framework di spostamento dei dati principali alla base di AzCopy. La libreria è progettata per operazioni di trasferimento dei dati affidabili, semplici e a prestazioni elevate simili a quelle di AzCopy. È possibile usarla per sfruttare tutti i vantaggi delle funzionalità fornite da AzCopy nell'applicazione in modo nativo senza dover eseguire e monitorare istanze esterne di AzCopy.

Per altre informazioni, vedere [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement) (Libreria per lo spostamento dei dati di Archiviazione di Azure per .NET).

### <a name="rest-api-or-client-library"></a>API REST o libreria client

È possibile creare un'applicazione personalizzata per eseguire la migrazione dei dati all'account di archiviazione BLOB usando una delle librerie client di Azure o l'API REST dei servizi di archiviazione di Azure. Archiviazione di Azure fornisce librerie client avanzate per più linguaggi e piattaforme, ad esempio .NET, Java, C++, Node.js, PHP, Ruby e Python. Le librerie client offrono funzionalità avanzate, ad esempio la logica di ripetizione dei tentativi, la registrazione e i caricamenti paralleli. È possibile sviluppare usando direttamente l'API REST, che può essere chiamata da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS.

Per altre informazioni, vedere [Introduzione all'archivio BLOB di Azure con .NET](../blobs/storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> I BLOB crittografati mediante la crittografia lato client archiviano i metadati correlati alla crittografia archiviati con il BLOB. È essenziale che qualsiasi meccanismo di copia garantisca la conservazione dei metadati dei BLOB e, in particolare modo, dei metadati correlati alla crittografia. Se si copiano i BLOB senza i metadati, il contenuto dei BLOB non è più recuperabile. Per altre informazioni sui metadati correlati alla crittografia, vedere [Crittografia lato client e Insieme di credenziali chiave Azure per Archiviazione di Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="faq"></a>Domande frequenti

**Gli account di archiviazione esistenti sono ancora disponibili?**

Sì, gli account di archiviazione esistenti (per utilizzo generico v1) sono ancora disponibili e non hanno subito variazioni di prezzo o di funzionalità. Gli account per utilizzo generico v1 non consentono di scegliere un livello di archiviazione e in futuro non avranno funzionalità di suddivisione in livelli.

**Perché e quando è consigliabile iniziare a usare gli account di archiviazione per utilizzo generico v2?**

Gli account di archiviazione per utilizzo generico v2 sono specializzati per offrire i costi per GB di archiviazione più bassi garantendo al tempo stesso costi di accesso ai dati e per transazione competitivi nel settore. Da ora in avanti è consigliabile usare account di archiviazione per utilizzo generico v2 per archiviare i BLOB, perché in futuro verranno introdotte funzionalità, come le notifiche di modifica, basate su questo tipo di account. La scelta del momento in cui eseguire l'aggiornamento dipende tuttavia dai requisiti aziendali dell'utente. Si può ad esempio scegliere di ottimizzare i modelli delle transazioni prima dell'aggiornamento.

Il downgrade da account per utilizzo generico v2 non è supportato, quindi considerare tutte le implicazioni in termini di prezzo prima di eseguire l'upgrade ad account per utilizzo generico v2.

**È possibile aggiornare un account di archiviazione esistente a un account di archiviazione per utilizzo generico v2?**

Sì. Gli account per utilizzo generico v1 possono essere facilmente aggiornati ad account per utilizzo generico v2 nel portale oppure tramite PowerShell o interfaccia della riga di comando. Gli account di archiviazione BLOB possono essere aggiornati ad account per utilizzo generico v2 tramite PowerShell o l'interfaccia della riga di comando. Sarà presto possibile aggiornare gli account di archiviazione BLOB ad account per utilizzo generico v2 nel portale.

Il downgrade da account per utilizzo generico v2 non è supportato, quindi considerare tutte le implicazioni in termini di prezzo prima di eseguire l'upgrade ad account per utilizzo generico v2.

**Si possono archiviare oggetti in entrambi i livelli di archiviazione nello stesso account?**

Sì. L'attributo **Livello di accesso** impostato a livello di account è il livello predefinito applicato a tutti gli oggetti in tale account in assenza di un livello impostato in modo esplicito. L'organizzazione a livello di BLOB, tuttavia, consente di impostare il livello di accesso a livello di oggetto indipendentemente dal livello di accesso impostato per l'account. I BLOB in tutti e tre i livelli di archiviazione (frequente, sporadico o archivio) possono trovarsi nello stesso account.

**È possibile modificare il livello di archiviazione dell'account di archiviazione per utilizzo generico v2?**

Sì. Si può modificare il livello di archiviazione dell'account impostando l'attributo **Livello di accesso** nell'account di archiviazione. La modifica del livello di archiviazione dell'account si applica a tutti gli oggetti archiviati nell'account per i quali non è impostato in modo esplicito un livello. Il passaggio dal livello di archiviazione ad accesso frequente a quello ad accesso sporadico comporta addebiti per le operazioni di scrittura (per decine di migliaia), solo per account per utilizzo generico v2, mentre la modifica da accesso sporadico ad accesso frequente comporta addebiti sia per le operazioni di lettura (per decine di migliaia) che per il recupero dati (per GB) per la lettura di tutti i dati nell'account.

**Con quale frequenza si può modificare il livello di archiviazione nell'account di archiviazione BLOB?**

Anche se non esistono limiti alla frequenza con cui è possibile modificare il livello di archiviazione, tenere presente che la modifica del livello di archiviazione da sporadico a frequente comporta addebiti elevati. Non si consiglia di modificare il livello di archiviazione di frequente.

**I BLOB nel livello di archiviazione ad accesso sporadico si comportano in modo diverso rispetto a quelli del livello di archiviazione ad accesso frequente?**

I BLOB nel livello di archiviazione ad accesso frequente degli account di archiviazione BLOB e per utilizzo generico v2 hanno la stessa latenza dei BLOB negli account di archiviazione per utilizzo generico v1. I BLOB nel livello di archiviazione ad accesso sporadico hanno una latenza simile, in millisecondi, a quella dei BLOB nel livello ad accesso frequente. I BLOB nel livello di archiviazione archivio prevedono diverse ore di latenza.

I BLOB nel livello di archiviazione ad accesso sporadico hanno un contratto di servizio con disponibilità leggermente inferiore rispetto a quello dei BLOB nel livello di archiviazione ad accesso frequente. Per altre informazioni, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage).

**È possibile archiviare i BLOB di pagine e i dischi delle macchine virtuali negli account di archiviazione BLOB?**

di serie Gli account di archiviazione BLOB supportano solo i BLOB in blocchi e i BLOB di aggiunta, non i BLOB di pagine. Il backup dei dischi delle macchine virtuali di Azure viene eseguito dai BLOB di pagine. Non sarà quindi possibile usare gli account di archiviazione BLOB per archiviare i dischi delle macchine virtuali. È tuttavia possibile archiviare i backup dei dischi delle macchine virtuali come BLOB in blocchi in un account di archiviazione BLOB. Questo è uno dei motivi per valutare la possibilità di usare account per utilizzo generico v2 invece di account di archiviazione BLOB.

**È necessario modificare le applicazioni esistenti per usare gli account di archiviazione per utilizzo generico v2?**

Gli account di archiviazione per utilizzo generico v2 sono completamente coerenti a livello di API con gli account di archiviazione BLOB e per utilizzo generico v1. Se l'applicazione usa BLOB in blocchi o di aggiunta e si usa la versione 2014-02-14 o successiva dell'[API REST dei servizi di archiviazione](https://msdn.microsoft.com/library/azure/dd894041.aspx), l'applicazione dovrebbe funzionare correttamente. Se si usa una versione meno recente del protocollo, è necessario aggiornare l'applicazione per poter usare la nuova versione e lavorare quindi facilmente con entrambi i tipi di account di archiviazione. In generale, è sempre consigliabile usare la versione più recente indipendentemente dal tipo di account di archiviazione usato.

I prezzi per gli account per utilizzo generico v2 sono in genere maggiori rispetto agli account per utilizzo generico v1 per transazioni e larghezza di banda. Può essere quindi necessario ottimizzare i modelli delle transazioni prima dell'aggiornamento per evitare un aumento dei costi totali.

**L'esperienza utente è diversa?**

Gli account di archiviazione per utilizzo generico v2 sono molto simili agli account di archiviazione per utilizzo generico v1 e supportano tutte le principali funzionalità di Archiviazione di Azure, inclusi i livelli elevati di durabilità, disponibilità, scalabilità, prestazioni e sicurezza. Fatta eccezione per le funzionalità e le restrizioni specifiche degli account di archiviazione BLOB e per i livelli di archiviazione indicati sopra, tutto il resto rimane invariato in caso di aggiornamento all'archiviazione BLOB o per utilizzo generico v2.

## <a name="next-steps"></a>Passaggi successivi

### <a name="evaluate-blob-storage-accounts"></a>Valutare gli account di archiviazione BLOB

[Verificare la disponibilità degli account di archiviazione BLOB in base all'area](https://azure.microsoft.com/regions/#services)

[Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Verificare i prezzi di Archiviazione di Azure per i BLOB in base all'area](https://azure.microsoft.com/pricing/details/storage/)

[Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-gpv2-storage-accounts"></a>Iniziare a usare account di archiviazione per utilizzo generico v2

[Introduzione all'archivio BLOB di Azure](../blobs/storage-dotnet-how-to-use-blobs.md)

[Spostamento dei dati da e verso Archiviazione di Azure](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Trasferire dati con l'utilità della riga di comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Visualizzare ed esplorare gli account di archiviazione](http://storageexplorer.com/)

<properties
   pageTitle="Cloud Cruiser e integrazione delle API di fatturazione di Microsoft Azure | Microsoft Azure"
   description="Fornisce un punto di vista unico del partner di fatturazione di Microsoft Azure, Cloud Cruiser, sulle esperienze di integrazione delle API di fatturazione di Azure nel prodotto.  Ciò è particolarmente utile per i clienti di Azure e Cloud Cruiser che sono interessati a utilizzare/provare Cloud Cruiser per Microsoft Azure Pack."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"
   />

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/08/2016"
   ms.author="mobandyo;sirishap;bryanla"/>


# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cloud Cruiser e integrazione delle API di fatturazione di Microsoft Azure

In questo articolo viene descritto in che modo le informazioni raccolte dalle nuove API di fatturazione di Microsoft Azure possono essere utilizzate in Cloud Cruiser per l'analisi e la simulazione dei costi e l’analisi dei flussi di lavoro.

## <a name="azure-ratecard-api"></a>API RateCard di Azure
L'API RateCard fornisce informazioni sui costi da Azure. Dopo l'autenticazione con le credenziali appropriate, è possibile eseguire query all’API per raccogliere i metadati relativi a servizi disponibili in Azure, insieme ai costi associati al proprio ID di offerta.

Di seguito viene fornita una risposta di esempio dell'API in cui sono mostrati i prezzi per l'istanza A0 (Windows):

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruiser’s-interface-to-azure-ratecard-api"></a>Interfaccia di Cloud Cruiser per l’API RateCard di Azure
Cloud Cruiser può utilizzare le informazioni dell’API RateCard in modi diversi. In questo articolo verrà illustrato come può essere utilizzata per eseguire la simulazione dei costi e l’analisi del carico di lavoro IaaS.

Per illustrare questo caso di utilizzo, si immagini un carico di lavoro di diverse istanze in esecuzione in Microsoft Azure Pack (WAP). L'obiettivo è quello di simulare questo stesso carico di lavoro in Azure e di stimare i costi dell’esecuzione di tale migrazione. Per creare questa simulazione, è necessario eseguire due attività principali:

1. **Importare ed elaborare le informazioni sul servizio raccolte dall'API RateCard.**  Questa attività viene eseguita anche sulle cartelle di lavoro, dove l'estratto dall'API RateCard viene trasformato e pubblicato in un nuovo piano tariffario. Questo nuovo piano tariffario verrà utilizzato nelle simulazioni per stimare i prezzi di Azure.

2. **Normalizzare i servizi WAP e i servizi di Azure per IaaS.** Per impostazione predefinita, i servizi WAP sono basati sulle singole risorse, come CPU, dimensioni della memoria, dimensioni del disco e così via, mentre i servizi di Azure si basano sulle dimensioni dell'istanza, come A0, A1, A2 e così via. Questa prima attività può essere eseguita dal motore ETL di Cloud Cruiser, denominato cartelle di lavoro, in cui queste risorse possono essere inserite in dimensioni di istanza, in modo analogo ai servizi di istanza di Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importare dati dall’API RateCard

Le cartelle di lavoro di Cloud Cruiser consentono di raccogliere ed elaborare informazioni dall'API RateCard in modo automatico.  Le cartelle di lavoro ETL (extract-transform-load) consentono di configurare la raccolta, la trasformazione e la pubblicazione dei dati nel database Cloud Cruiser.

Ogni cartella di lavoro può avere una o più raccolte consentendo di correlare le informazioni provenienti da origini diverse per integrare o estendere i dati di utilizzo. I due screenshot seguenti illustrano come creare una nuova *raccolta* in una cartella di lavoro esistente e l'importazione di informazioni nella *raccolta* dall'API RateCard:

![Figura 1 - Creazione di una nuova raccolta][1]

![Figura 2 - Importare i data dalla nuova raccolta][2]

Dopo aver importato i dati nella cartella di lavoro, è possibile creare più passaggi e processi di trasformazione per modificare e modellare i dati. Poiché in questo esempio l'interesse è incentrato unicamente su IaaS, è possibile usare la procedura di trasformazione per rimuovere le righe o i record non necessari correlati a servizi diversi da IaaS.

Lo screenshot seguente illustra la procedura di trasformazione usata per elaborare i dati raccolti dall'API RateCard:

![Figura 3 - Procedura di trasformazione per elaborare i dati raccolti dall'API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Definizione di nuovi servizi e piani tariffari

Esistono diversi modi per definire i servizi in Cloud Cruiser. Una delle opzioni consiste nell’importare i servizi dai dati di utilizzo. Questo metodo viene in genere utilizzato quando si lavora con i cloud pubblici, in cui i servizi sono già definiti dal provider.

Un piano tariffario è un set di costi o i prezzi che possono essere applicati a diversi servizi, in base alle date di validità oppure a un gruppo di clienti, tra le altre opzioni. I piani tariffari possono essere utilizzati in Cloud Cruiser per creare una simulazione o scenari di simulazione, per comprendere in che modo le modifiche apportate ai servizi possano influire sul costo totale di un carico di lavoro.

In questo esempio, si utilizzeranno le informazioni dell'API RateCard per definire nuovi servizi in Cloud Cruiser. Nello stesso modo, è possibile utilizzare i costi associate ai servizi per creare un nuovo piano tariffario in Cloud Cruiser.

Al termine del processo di trasformazione, è possibile creare un nuovo passaggio e pubblicare i dati dell'API RateCard come nuovi servizi e costi.

![Figura 4 - Pubblicazione di dati dall'API RateCard come nuovi servizi e tariffe][4]

### <a name="verify-azure-services-and-rates"></a>Verificare i servizi e i costi di Azure

Dopo la pubblicazione di servizi e costi, è possibile verificare l'elenco dei servizi importati nella scheda *Servizi* di Cloud Cruiser:

![Figura 5 - Verifica dei nuovi servizi][5]

Nella scheda *Piani tariffari* è possibile controllare il nuovo piano tariffario denominato "AzureSimulation" con i costi importati dall'API RateCard.

![Figura 6 - Verifica dei nuovi piani tariffari e delle tariffe associate][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizzare i servizi WAP e Azure

Per impostazione predefinita, WAP fornisce informazioni sull'utilizzo basate sull'uso delle risorse di calcolo, memoria e rete. In Cloud Cruiser è possibile definire i servizi direttamente in base all'allocazione o all’utilizzo a consumo di queste risorse. È possibile, ad esempio, impostare un costo di base per ogni ora di utilizzo della CPU o addebitare i GB di memoria allocata a un'istanza.

Per questo esempio, al fine di confrontare i costi tra WAP e Azure, è necessario aggregare l'utilizzo delle risorse in bundle WAP, che possono quindi essere mappati ai servizi di Azure. Questa trasformazione può essere implementata facilmente nelle cartelle di lavoro:

![Figure 7 - Trasformazione dei dati WAP in servizi normalizzati][7]

L'ultimo passaggio nella cartella di lavoro consiste nel pubblicare i dati nel database Cloud Cruiser. Durante questo passaggio, i dati di utilizzo sono ora aggregati in servizi (che eseguono il mapping ai servizi di Azure) e associati a costi predefiniti per creare gli addebiti.

Dopo avere completato la cartella di lavoro, è possibile automatizzare l'elaborazione dei dati aggiungendo un'attività nell'utilità di pianificazione e specificando la frequenza e l'ora di esecuzione della cartella di lavoro.

![Figura 8 - Pianificazione della cartella di lavoro][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Creare report per l'analisi di simulazione dei costi del carico di lavoro

Dopo che l'utilizzo viene raccolto e le spese vengono caricate nel database Cloud Cruiser, è possibile usare il modulo Insights di Cloud Cruiser per creare la simulazione dei costi del carico di lavoro desiderato.

Per illustrare questo scenario, è stato creato il report seguente:

![Confronto dei costi][9]

Il grafico superiore illustra un confronto dei costi per servizi confrontando il costo dell'esecuzione del carico di lavoro per ogni specifico servizio tra WAP (blu scuro) e Azure (blu chiaro).

Il grafico inferiore riporta gli stessi dati suddivisi per reparto. Mostra i costi sostenuti da ogni reparto per eseguire il carico di lavoro in WAP e in Azure e le relative differenze nella barra dei risparmi (verde).

## <a name="azure-usage-api"></a>API di utilizzo di Azure


### <a name="introduction"></a>Introduzione

Microsoft ha introdotto di recente l'API di utilizzo di Azure, che consente ai sottoscrittori di inserire dati di utilizzo a livello di codice per ottenere informazioni significative sul consumo. Questa è un'ottima notizia per i clienti di Cloud Cruiser che possono usufruire del set di dati più completo disponibile attraverso questa API.

Cloud Cruiser può sfruttare l'integrazione con l'API di utilizzo in diversi modi. La granularità (informazioni di utilizzo ogni ora) e i metadati delle risorse disponibili con l'API offrono il set di dati necessario per supportare modelli Showback o Chargeback flessibili. 

In questa esercitazione verrà presentato un esempio di come Cloud Cruiser possa trarre vantaggio dalle informazioni sull'API di utilizzo. In particolare verrà creato un gruppo di risorse in Azure, verranno associati tag per la struttura dei conti e quindi verrà descritto il processo di estrazione ed elaborazione delle informazioni sui tag in Cloud Cruiser.
 
L'obiettivo finale è riuscire a creare report come quello seguente e ad analizzare i costi e il consumo in base alla struttura del conto popolata dai tag.

![Figura 10 - Report con suddivisioni usando i tag][10]

### <a name="microsoft-azure-tags"></a>Tag di Microsoft Azure

I dati disponibili attraverso l'API di utilizzo di Azure includono non solo le informazioni sul consumo, ma anche i metadati delle risorse che includono eventuali tag associati. I tag offrono un modo semplice di organizzare le risorse, ma per essere efficaci, è necessario assicurarsi che:

- I tag vengano applicati correttamente alle risorse in fase di provisioning
- I tag vengano usati correttamente nel processo Showback/Chargeback per collegare l'utilizzo alla struttura dei conti dell'organizzazione.

Entrambi questi requisiti possono essere complessi, specialmente quando esiste un processo manuale sul lato del provisioning e degli addebiti. I clienti spesso si lamentano di tag errati, non corretti o addirittura mancanti quando li usano e questi errori possono creare molti problemi sul lato degli addebiti.

Cloud Cruiser con la nuova API di utilizzo di Azure può ottenere informazioni sull'aggiunta di tag alle risorse e attraverso uno strumento ETL sofisticato chiamato cartelle di lavoro, correggere i comuni errori di aggiunta tag. Attraverso la trasformazione che sfrutta le espressioni regolari e la correlazione dei dati, Cloud Cruiser riesce a identificare le risorse provviste di tag non corretti e applicare quelli corretti e assicurando l'associazione corretta delle risorse al consumatore.

Sul lato degli addebiti Cloud Cruiser automatizza il processo Showback/Chargeback e può sfruttare le informazioni sui tag per collegare l'utilizzo al consumatore appropriato (reparto, divisione, progetto e così via). Questa automazione offre un notevole miglioramento e può assicurare un processo di addebito coerente e controllabile.
 

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Creazione di un gruppo di risorse con tag in Microsoft Azure
Il primo passaggio in questa esercitazione consiste nel creare un gruppo di risorse nel portale di Azure e quindi creare nuovi tag da associare alle risorse. Per questo esempio verranno creati i seguenti tag: reparto, ambiente, proprietario, progetto.

Lo screenshot seguente illustra un gruppo di risorse di esempio con i tag associati.

![Figura 11 - Gruppo di risorse con tag associati nel portale di Azure][11]

Il passaggio successivo è estrarre le informazioni dall'API di utilizzo in Cloud Cruiser. L'API di utilizzo attualmente fornisce risposte nel formato JSON. Segue un esempio dei dati recuperati:


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importare dati dall'API di utilizzo in Cloud Cruiser

Le cartelle di lavoro di Cloud Cruiser consentono di raccogliere ed elaborare informazioni dall'API di utilizzo in modo automatico. Una cartella di lavoro ETL (extract-transform-load) consente di configurare la raccolta, la trasformazione e la pubblicazione dei dati nel database Cloud Cruiser.

Ogni cartella di lavoro può contenere una o più raccolte. In tal modo è possibile correlare le informazioni provenienti da origini diverse per integrare o estendere i dati di utilizzo. In questo esempio verrà creato un nuovo foglio nella cartella di lavoro del modello di Azure (_UsageAPI)_ e verrà impostata una nuova _raccolta_ per importare informazioni dall'API di utilizzo.

![Figura 3 - Utilizzo dei dati API importati nel foglio UsageAPI][12]

Si noti che questa cartella di lavoro ha già altri fogli per importare servizi da Azure (_ImportServices_) ed elabora le informazioni sul consumo dall'API di fatturazione (_PublishData_).

Verrà quindi usata l'API di utilizzo per popolare il foglio _UsageAPI_ e correlare le informazioni con i dati di consumo provenienti dall'API di fatturazione nel foglio _PublishData_.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Elaborazione delle informazioni sui tag provenienti dall'API di utilizzo

Dopo aver importato i dati nella cartella di lavoro, verranno creati passaggi di trasformazione nel foglio _UsageAPI_ per elaborare le informazioni provenienti dall'API. Il primo passaggio consiste nell'usare un processore "JSON split" per estrarre i tag da un campo singolo e creare campi per ogni tag (reparto, progetto, proprietario e ambiente).

![Figura 4 - Creare nuovi campi per le informazioni sui tag][13]

Si noti che nel servizio "Reti" mancano le informazioni sui tag (riquadro giallo), ma è possibile verificare che questo servizio fa parte dello stesso gruppo di risorse esaminando il campo _ResourceGroupName_ . Dal momento che ci sono tag per le altre risorse di questo gruppo di risorse, è possibile usare queste informazioni per applicare i tag mancanti a questa risorsa più avanti nel processo.

Il passaggio successivo è creare una tabella di ricerca associando le informazioni dei tag a _ResourceGroupName_. Questa tabella di ricerca verrà usata nel passaggio successivo per migliorare i dati sul consumo con le informazioni sui tag.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Aggiunta delle informazioni sui tag ai dati di consumo

Ora è possibile passare al foglio _PublishData_ , che elabora le informazioni sul consumo provenienti dall'API di fatturazione e aggiungere i campi estratti dai tag. Questo processo viene eseguito guardando la tabella di ricerca creata nel passaggio precedente, usando _ResourceGroupName_ come chiave per le ricerche.

![Figura 5 - Popolamento della struttura dei conti con le informazioni provenienti dalle ricerche][14]

Si noti che sono stati applicati i campi appropriati della struttura dei conti per il servizio "Reti", correggendo l'errore con i tag mancanti. Sono stati anche popolati i campi della struttura dei conti per risorse diverse dal gruppo di risorse di destinazione allo scopo di differenziarli nei report.

Ora è necessario solo aggiungere un passaggio per pubblicare i dati di utilizzo. Durante questo passaggio le tariffe appropriate per ogni servizio definito nel piano tariffario verranno applicate alle informazioni sull'utilizzo con l'addebito risultante caricato nel database.

Il vantaggio è che questo processo si deve eseguire solo una volta. Quando la cartella di lavoro è completata, è necessario solo aggiungerla all'utilità di pianificazione e verrà eseguita su base oraria o giornaliera all'ora pianificata. Poi si tratta solo di creare nuovi report o di personalizzare quelli esistenti, per analizzare i dati per ottenere informazioni significative dall'utilizzo del cloud.

### <a name="next-steps"></a>Passaggi successivi

+ Per istruzioni dettagliate sulla creazione di cartelle di lavoro e report Cloud Cruiser, fare riferimento alla [documentazione](http://docs.cloudcruiser.com/) online di Cloud Cruiser (è necessario un account di accesso valido).  Per altre informazioni su Cloud Cruiser, contattare [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Per una panoramica sulle API di utilizzo delle risorse e sulle API RateCard di Azure, vedere [Ottenere informazioni significative sul consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md) .
+ Per ulteriori informazioni su entrambe le API, appartenenti al set di API fornito da Gestione risorse di Azure, vedere il [riferimento all'API REST di fatturazione di Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) .
+ Se si desidera approfondire il codice di esempio, vedere gli esempi di codice dell'API di fatturazione di Microsoft Azure in [Esempi di codice di Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Ulteriori informazioni
+ Per ulteriori informazioni su Gestione risorse di Azure, vedere l'articolo [Panoramica su Gestione risorse di Azure](resource-group-overview.md) .

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figura 1 - Creazione di una nuova raccolta"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figura 2 - Importare i data dalla nuova raccolta"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figura 3 - Procedura di trasformazione per elaborare i dati raccolti dall'API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figura 4 - Pubblicazione di dati dall'API RateCard come nuovi servizi e tariffe"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figura 5 - Verifica dei nuovi servizi"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figura 6 - Verifica dei nuovi piani tariffari e delle tariffe associate"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figura 7 - Trasformazione dei dati WAP in servizi normalizzati"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figura 8 - Pianificazione della cartella di lavoro"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figura 9 - Report di esempio per lo scenario di confronto dei costi del carico di lavoro"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figura 10 - Report con suddivisioni usando i tag"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figura 11 - Gruppo di risorse con tag associati nel portale di Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figura 12 - Utilizzo dei dati API importati nel foglio UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figura 13 - Creare nuovi campi per le informazioni sui tag"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figura 14 - Popolamento della struttura dei conti con le informazioni provenienti dalle ricerche"



<!--HONumber=Oct16_HO2-->



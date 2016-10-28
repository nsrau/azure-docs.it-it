<properties
   pageTitle="Introduzione all'interfaccia della riga di comando di Azure Batch | Microsoft Azure"
   description="Introduzione rapida ai comandi per Batch nell'interfaccia della riga di comando di Azure per la gestione delle risorse del servizio Azure Batch"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>

# Introduzione all'interfaccia della riga di comando di Azure Batch

L'interfaccia della riga di comando multipiattaforma di Azure consente di gestire gli account Batch e risorse come pool, processi e attività in shell dei comandi Linux, Mac e Windows. Con l'interfaccia della riga di comando di Azure è possibile eseguire molte delle attività eseguite con le API Batch, il portale di Azure e i cmdlet di PowerShell per Batch e creare i relativi script.

Questo articolo si basa sulla versione 0.10.5 dell'interfaccia della riga di comando di Azure.

## Prerequisiti

* [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md)

* [Connettere l'interfaccia della riga di comando di Azure alla sottoscrizione di Azure](../xplat-cli-connect.md)

* Passare alla **modalità Resource Manager**: `azure config mode arm`

>[AZURE.TIP] È consigliabile aggiornare frequentemente l'installazione dell'interfaccia della riga di comando di Azure per sfruttare i vantaggi degli aggiornamenti e dei miglioramenti del servizio.

## Guida per i comandi

È possibile visualizzare il testo della Guida per ogni comando dell'interfaccia della riga di comando di Azure aggiungendo `-h` come unica opzione dopo il comando. Ad esempio:

* Per visualizzare la Guida per il comando `azure`, immettere `azure -h`
* Per ottenere un elenco di tutti i comandi per Batch nell'interfaccia della riga di comando, usare `azure batch -h`
* Per visualizzare la Guida in merito alla creazione di un account Batch, immettere `azure batch account create -h`

In caso di dubbi, usare l'opzione della riga di comando `-h` per visualizzare la Guida per qualsiasi comando dell'interfaccia della riga di comando di Azure.

## Creare un account Batch

Utilizzo:

    azure batch account create [options] <name>

Esempio:

	azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Crea un nuovo account Batch con i parametri specificati. È necessario specificare almeno una località, un gruppo di risorse e un nome di account. Se non si ha già un gruppo di risorse, crearne uno eseguendo `azure group create` e specificare una delle aree di Azure (ad esempio, "West US") per l'opzione `--location`. Ad esempio:

	azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] Il nome dell'account Batch deve essere univoco nell'area di Azure in cui viene creato l'account. Può contenere solo caratteri alfanumerici minuscoli e deve avere una lunghezza compresa tra 3 e 24 caratteri. Nei nomi degli account Batch non possono essere usati caratteri speciali come `-` o `_`.

### Account di archiviazione collegato (archiviazione automatica)

Facoltativamente, è possibile collegare un account di archiviazione **Utilizzo generico** all'account Batch quando questo viene creato. La funzionalità [Pacchetti dell'applicazione](batch-application-packages.md) di Batch usa l'archivio BLOB in un account di archiviazione per utilizzo generico collegato, proprio come la libreria [.NET Batch File Conventions](batch-task-output.md). Queste funzionalità facoltative facilitano la distribuzione delle applicazioni eseguite dalle attività di Batch e il salvataggio permanente dei dati prodotti.

Per collegare un account di archiviazione di Azure esistente a un nuovo account Batch durante la creazione, specificare l'opzione `--autostorage-account-id`. Questa opzione richiede l'ID risorsa completo dell'account di archiviazione.

Per prima cosa, visualizzare i dettagli dell'account di archiviazione:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Usare quindi il valore **Url** per l'opzione `--autostorage-account-id`. Il valore Url inizia con "/subscriptions/" e contiene l'ID sottoscrizione e il percorso della risorsa dell'account di archiviazione:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## Eliminare un account Batch

Utilizzo:

    azure batch account delete [options] <name>

Esempio:

	azure batch account delete --resource-group "resgroup001" "batchaccount001"

Elimina l'account Batch specificato. Quando richiesto, confermare che si vuole rimuovere l'account. Il completamento della rimozione dell'account può richiedere tempo.

## Gestire le chiavi di accesso dell'account

Per [creare e modificare risorse](#create-and-modify-batch-resources) nell'account Batch è necessaria una chiave di accesso.

### Elencare le chiavi di accesso

Utilizzo:

	azure batch account keys list [options] <name>

Esempio:

	azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Elenca le chiavi per l'account Batch specificato.

### Generare una nuova chiave di accesso

Utilizzo:

    azure batch account keys renew [options] --<primary|secondary> <name>

Esempio:

	azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Rigenera la chiave specificata per l'account Batch specificato.

## Creare e modificare le risorse Batch

È possibile usare l'interfaccia della riga di comando di Azure per creare, leggere, aggiornare ed eliminare risorse di Batch come pool, nodi di calcolo, processi e attività. Per queste operazioni CRUD (Create, Read, Update, Delete) sono necessari il nome, la chiave di accesso e l'endpoint dell'account Batch. È possibile specificare questi parametri con le opzioni `-a`, `-k` e `-u` oppure impostare [variabili di ambiente](#credential-environment-variables) che, se popolate, verranno usate automaticamente dall'interfaccia della riga di comando.

### Variabili di ambiente per le credenziali

Anziché specificare le opzioni `-a`, `-k` e `-u` nella riga di comando per ogni comando eseguito, è possibile impostare le variabili di ambiente `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY` e `AZURE_BATCH_ENDPOINT`. L'interfaccia della riga di comando di Batch usa queste variabili (se impostate) consentendo così di omettere le opzioni `-a`, `-k` e `-u`. Il resto di questo articolo presuppone che vengano usate queste variabili di ambiente.

>[AZURE.TIP] Elencare le chiavi con `azure batch account keys list` e visualizzare l'endpoint dell'account con `azure batch account show`.

### File JSON

Quando si creano risorse di Batch come pool e processi, è possibile specificare un file JSON contenente la configurazione della nuova risorsa anziché passarne i parametri come opzioni della riga di comando. Ad esempio:

`azure batch pool create my_batch_pool.json`

Nonostante molte operazioni di creazione di risorse possano essere eseguite usando solo opzioni della riga di comando, alcune funzionalità richiedono un file in formato JSON contenente i dettagli delle risorse. È necessario usare un file JSON, ad esempio, se si vogliono specificare file di risorse per un'attività di avvio.

Il codice JSON necessario per creare una risorsa è disponibile nella documentazione di [riferimento sulle API Batch REST][rest_api] su MSDN. Ogni argomento "Aggiungere *tipo di risorsa*" contiene codice JSON di esempio per creare la risorsa, che può essere usato come modello per i propri file JSON. Il codice JSON per la creazione di un pool, ad esempio, è disponibile in [Aggiungere un pool a un account][rest_add_pool].

>[AZURE.NOTE] Se si specifica un file JSON durante la creazione di una risorsa, tutti gli altri parametri specificati nella riga di comando per la risorsa verranno ignorati.

## Creare un pool

Utilizzo:

    azure batch pool create [options] [json-file]

Esempio (Configurazione macchina virtuale):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Esempio (Configurazione servizi cloud):

	azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Crea un pool di nodi di calcolo nel servizio Batch.

Come indicato nella [panoramica delle funzionalità di Batch](batch-api-basics.md#pool), quando si seleziona un sistema operativo per i nodi del pool sono disponibili due opzioni: **Configurazione macchina virtuale** e **Cloud Services Configuration** (Configurazione servizi cloud). Usare le opzioni `--image-*` per creare pool Configurazione macchina virtuale e `--os-family` per creare pool Cloud Services Configuration (Configurazione servizi cloud). Non è possibile specificare sia l'opzione `--os-family` che l'opzione `--image-*`.

È possibile specificare i [pacchetti dell'applicazione](batch-application-packages.md) del pool e la riga di comando per un'[attività di avvio](batch-api-basics.md#start-task). Per specificare file di risorse per l'attività di avvio, tuttavia, è necessario usare invece un [file JSON](#json-files).

Per eliminare un pool:

    azure batch pool delete [pool-id]

>[AZURE.TIP] Per informazioni sui valori appropriati per le opzioni `--image-*`, vedere [Elenco di immagini di macchine virtuali](batch-linux-nodes.md#list-of-virtual-machine-images).

## Creare un processo

Utilizzo:

    azure batch job create [options] [json-file]

Esempio:

    azure batch job create --id "job001" --pool-id "pool001"

Aggiunge un processo all'account Batch e specifica il pool in cui verranno eseguite le relative attività.

Per eliminare un processo:

    azure batch job delete [job-id]

## Elencare pool, processi, attività e altre risorse

Ogni tipo di risorsa di Batch supporta un comando `list` che esegue query sull'account Batch ed elenca le risorse di tale tipo. È ad esempio possibile elencare i pool dell'account e le attività di un processo:

    azure batch pool list
    azure batch task list --job-id "job001"

### Creazione più efficiente degli elenchi di risorse

Per velocizzare l'esecuzione di query, è possibile specificare opzioni per le clausole **select**, **filter** ed **expand** nelle operazioni `list`. Usare queste opzioni per limitare la quantità di dati restituita dal servizio Batch. Dato che tutte le operazioni di filtro vengono eseguite sul lato server, vengono trasmessi in rete solo i dati a cui si è interessati. Queste clausole consentono di risparmiare larghezza di banda (e quindi tempo) quando si eseguono operazioni di tipo elenco.

Il comando seguente, ad esempio, restituirà solo i pool i cui ID iniziano con "renderTask":

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

L'interfaccia della riga di comando di Batch supporta tutte e tre le clausole supportate dal servizio Batch:

* `--select-clause [select-clause]` restituisce un sottoinsieme di proprietà per ogni entità.
* `--filter-clause [filter-clause]` restituisce solo le entità corrispondenti all'espressione OData specificata.
* `--expand-clause [expand-clause]` ottiene le informazioni sulle entità in una singola chiamata REST sottostante. La clausola expand supporta attualmente solo la proprietà `stats`.

Per informazioni dettagliate sulle tre clausole e sull'esecuzione di query di tipo elenco con tali clausole, vedere [Eseguire query sul servizio Azure Batch in modo efficiente](batch-efficient-list-queries.md).

## Gestione dei pacchetti dell'applicazione

I pacchetti dell'applicazione semplificano la distribuzione di applicazioni nei nodi di calcolo dei pool. Con l'interfaccia della riga di comando di Azure, è possibile caricare i pacchetti dell'applicazione, gestirne le versioni ed eliminarli.

Per creare una nuova applicazione e aggiungere una versione del pacchetto, eseguire questi comandi.

**Creare** un'applicazione:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Aggiungere** un pacchetto dell'applicazione:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Attivare** il pacchetto:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Impostare la **versione predefinita** per l'applicazione:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### Distribuire un pacchetto dell'applicazione

Quando si crea un nuovo pool, è possibile specificare uno o più pacchetti dell'applicazione da distribuire. Quando si specifica un pacchetto al momento della creazione del pool, il pacchetto viene distribuito in ogni nodo quando questo viene aggiunto al pool. I pacchetti vengono distribuiti anche quando un nodo viene riavviato o ne viene ricreata l'immagine.

Specificare l'opzione `--app-package-ref` quando si crea un pool per distribuire un pacchetto dell'applicazione nei nodi del pool quando vengono aggiunti al pool. L'opzione `--app-package-ref` accetta un elenco di ID applicazione delimitati da punto e virgola da distribuire nei nodi di calcolo.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Quando si crea un pool usando le opzioni della riga di comando, non è attualmente possibile specificare *quale* versione del pacchetto dell'applicazione distribuire nei nodi di calcolo, ad esempio "1.10-beta3". Quindi è necessario specificare una versione predefinita per l'applicazione con `azure batch application set [options] --default-version <version-id>` prima di creare il pool. Vedere la sezione precedente. È tuttavia possibile specificare una versione del pacchetto per il pool se per la creazione del pool si usa un [file JSON](#json-files) invece di usare le opzioni della riga di comando.

Per altre informazioni sui pacchetti dell'applicazione, vedere [Distribuzione delle applicazioni con i pacchetti dell'applicazione di Azure Batch](batch-application-packages.md).

>[AZURE.IMPORTANT] Per usare i pacchetti dell'applicazione è necessario [collegare un account di archiviazione di Azure](#linked-storage-account-autostorage) all'account Batch.

### Aggiornare i pacchetti dell’applicazione di un pool

Per aggiornare le applicazioni assegnate a un pool esistente, eseguire il comando `azure batch pool set` con l'opzione `--app-package-ref`:

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Per distribuire il nuovo pacchetto dell'applicazione nei nodi di calcolo già presenti in un pool esistente, è necessario riavviare o ricreare l'immagine di tali nodi:

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] È possibile ottenere un elenco dei nodi in un pool, con gli ID nodo, con `azure batch node list`.

Ricordare che è necessario avere già configurato l'applicazione con una versione predefinita prima della distribuzione (`azure batch application set [options] --default-version <version-id>`).

## Suggerimenti per la risoluzione dei problemi

Questa sezione ha lo scopo di indicare le risorse da usare per risolvere i problemi relativi all'interfaccia della riga di comando di Azure. Nonostante non garantisca la risoluzione di tutti i problemi, può essere utile per restringere le cause e individuare le risorse di assistenza.

* Usare `-h` per visualizzare il **testo della Guida** per qualsiasi comando dell'interfaccia della riga di comando.

* Usare `-v` e `-vv` per visualizzare l'output **dettagliato** del comando. `-vv` restituisce un output "molto" dettagliato e visualizza le richieste e le risposte REST effettive. Queste opzioni sono utili per visualizzare l'output completo degli errori.

* È possibile visualizzare l'**output del comando in formato JSON** con l'opzione `--json`. Ad esempio, `azure batch pool show "pool001" --json` visualizza le proprietà di pool001 in formato JSON. È quindi possibile copiare e modificare tale output per l'uso in un `--json-file` (vedere [File JSON](#json-files) più indietro in questo articolo).

* Il [forum di Batch su MSDN][batch_forum], attentamente monitorato dal team di Batch, è un'eccellente risorsa di assistenza. Se si riscontrano problemi o si vuole assistenza per un'operazione specifica, pubblicare le proprie domande nel forum.

* Non tutte le operazioni sulle risorse di Batch sono attualmente supportate dall'interfaccia della riga di comando di Azure. Non è attualmente possibile, ad esempio, specificare una *versione* del pacchetto dell'applicazione per un pool. Si può specificare solo l'ID pacchetto. In tali casi, può essere necessario specificare un `--json-file` per il comando anziché usare opzioni della riga di comando. Assicurarsi di usare la versione più recente dell'interfaccia della riga di comando per usufruire dei futuri miglioramenti.

## Passaggi successivi

*  Per informazioni su come usare questa funzionalità per gestire e distribuire le applicazioni eseguite nei nodi di calcolo di Batch, vedere [Distribuzione delle applicazioni con i pacchetti dell'applicazione di Azure Batch](batch-application-packages.md).

* Per altre informazioni sulla riduzione del numero di elementi e del tipo di informazioni restituiti per le query su Batch, vedere [Eseguire query sul servizio Azure Batch in modo efficiente](batch-efficient-list-queries.md).

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/it-IT/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx

<!---HONumber=AcomDC_1005_2016-->
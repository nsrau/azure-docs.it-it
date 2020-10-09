---
title: Runtime di integrazione
description: Informazioni sul runtime di integrazione in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: b899485589f19a5b74d6d22b4e5dae5fbf3ff604
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827538"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Runtime di integrazione in Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le seguenti funzionalità di integrazione di dati in diversi ambienti di rete:

- **Flusso di dati**: eseguire un [flusso di dati](concepts-data-flow-overview.md) nell'ambiente di calcolo di Azure gestito.  
- **Spostamento dei dati**: copiare i dati tra archivi dati nella rete pubblica e negli archivi dati in una rete privata (in locale o in una rete privata virtuale). Fornisce il supporto per i connettori predefiniti, la conversione dei formati, il mapping di colonne e il trasferimento di dati scalabile e ad alte prestazioni.
- **Invio di attività**: inviare e monitorare le attività di trasformazione in esecuzione in diversi servizi di calcolo, ad esempio Azure Databricks, Azure HDInsight, Azure Machine Learning, database SQL di azure, SQL Server e altro ancora.
- **Esecuzione di pacchetti SSIS**: eseguire in modo nativo i pacchetti SQL Server Integration Services (SSIS) in un ambiente di calcolo Azure gestito.

In Data Factory, un'attività definisce l'azione da eseguire. Un servizio collegato definisce un archivio dati o un servizio di calcolo di destinazione. Un runtime di integrazione funge da ponte tra l'attività e i servizi collegati.  A cui fa riferimento il servizio collegato o l'attività e fornisce l'ambiente di calcolo in cui l'attività viene eseguita o da cui viene inviata. In questo modo, l'attività può essere eseguita nell'area più vicina possibile all'archivio dati o al servizio di calcolo di destinazione nel modo più efficiente soddisfacendo al contempo le esigenze di sicurezza e conformità.

I runtime di integrazione possono essere creati nel Azure Data Factory UX tramite l' [Hub di gestione](author-management-hub.md) e qualsiasi attività, set di dati o flusso di dati che vi fanno riferimento.

## <a name="integration-runtime-types"></a>Tipi di runtime di integrazione

Data Factory offre tre tipi di Integration Runtime (IR) ed è necessario scegliere il tipo che meglio serve le funzionalità di integrazione dei dati e l'ambiente di rete che si sta cercando.  Questi tre tipi sono:

- Azure
- Self-hosted
- Azure-SSIS

Nella tabella seguente vengono descritte le funzionalità e il supporto di rete per ogni tipo di runtime di integrazione:

Tipo di runtime di integrazione | Rete pubblica | Rete privata
------- | -------------- | ---------------
Azure | Flusso di dati<br/>Spostamento dati<br/>Invio di attività | Flusso di dati<br/>Spostamento dati<br/>Invio di attività
Self-hosted | Spostamento dati<br/>Invio di attività | Spostamento dati<br/>Invio di attività
Azure-SSIS | Esecuzione pacchetti SSIS | Esecuzione pacchetti SSIS


## <a name="azure-integration-runtime"></a>Runtime di integrazione di Azure

Un runtime di integrazione di Azure può:

- Eseguire flussi di dati in Azure 
- Eseguire l'attività di copia tra archivi dati cloud
- Inviare le seguenti attività di trasformazione nella rete pubblica: databricks Notebook/Jar/Python attività, attività hive di HDInsight, attività Pig HDInsight, attività HDInsight MapReduce, attività HDInsight Spark, attività di streaming HDInsight, attività Machine Learning esecuzione batch, attività Machine Learning aggiornamento risorse, attività stored procedure, Data Lake Analytics attività U-SQL, attività personalizzata .NET, attività Web, attività ricerca e attività Ottieni metadati.

### <a name="azure-ir-network-environment"></a>Ambiente di rete del runtime di integrazione di Azure

Azure Integration Runtime supporta la connessione a archivi dati e calcola i servizi con endpoint accessibili pubblicamente. Abilitando la rete virtuale gestita, Azure Integration Runtime supporta la connessione agli archivi dati tramite il servizio di collegamento privato in un ambiente di rete privata.

### <a name="azure-ir-compute-resource-and-scaling"></a>Risorsa di calcolo e ridimensionamento del runtime di integrazione di Azure
Il runtime di integrazione di Azure fornisce un calcolo senza server completamente gestito in Azure.  Non è necessario preoccuparsi del provisioning dell'infrastruttura, l'installazione del software, l'applicazione di patch o la scalabilità della capacità.  Inoltre si paga solo per la durata dell'utilizzo effettivo.

Il runtime di integrazione di Azure fornisce il calcolo nativo per spostare i dati tra gli archivi dati cloud in modo sicuro, affidabile e ad alte prestazioni.  È possibile impostare il numero di unità di integrazione dati da usare per l'attività di copia; le dimensioni di calcolo del runtime di integrazione di Azure vengono aumentate di conseguenza in modo elastico senza che sia necessario modificare in modo esplicito le dimensioni del runtime di integrazione di Azure. 

L'invio di attività è un'operazione semplice che consente di instradare l'attività al servizio di calcolo di destinazione, pertanto non è necessario aumentare le dimensioni di calcolo per questo scenario.

Per informazioni sulla creazione e la configurazione di un Azure IR, vedere [How to create and configure Azure Integration Runtime](create-azure-integration-runtime.md). 

> [!NOTE] 
> Il runtime di integrazione di Azure dispone di proprietà correlate al runtime del flusso di dati, che definisce l'infrastruttura di calcolo sottostante da usare per l'esecuzione dei flussi di dati. 

## <a name="self-hosted-integration-runtime"></a>Runtime di integrazione self-hosted

Un runtime di integrazione self-hosted è in grado di eseguire queste operazioni:

- Eseguire attività di copia tra gli archivi dati cloud e un archivio dati in una rete privata.
- Invio delle seguenti attività di trasformazione sulle risorse di calcolo in locale o nella rete virtuale di Azure: attività hive HDInsight (BYOC-Bring your own cluster), attività Pig HDInsight (BYOC), attività HDInsight MapReduce (BYOC), attività HDInsight Spark (BYOC), attività di streaming HDInsight (BYOC), attività di esecuzione batch Machine Learning, attività Machine Learning aggiornamento risorse, attività stored procedure, attività U-SQL, attività personalizzata (esecuzione in Azure batch) Data Lake Analytics , Attività Lookup e ottenere l'attività dei metadati.

> [!NOTE] 
> Usare il runtime di integrazione self-hosted per supportare gli archivi dati che richiedono un driver Bring Your Own, ad esempio SAP Hana, MySQL e così via.  Per altre informazioni, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) è una dipendenza del runtime di integrazione self-hosted. Assicurarsi di avere installato JRE nello stesso host.

### <a name="self-hosted-ir-network-environment"></a>Ambiente di rete del runtime di integrazione self-hosted

Se si vuole eseguire l'integrazione dei dati in modo sicuro in un ambiente di rete privata, che non ha una linea di visione diretta dall'ambiente cloud pubblico, è possibile installare un runtime di integrazione self-hosted nell'ambiente locale protetto dal firewall aziendale o all'interno di una rete privata virtuale.  Il runtime di integrazione self-hosted stabilisce solo connessioni basate su HTTP in uscita per accedere a Internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Risorsa di calcolo e ridimensionamento del runtime di integrazione self-hosted

Installare il runtime di integrazione self-hosted in un computer locale o in una macchina virtuale all'interno di una rete privata. Attualmente in un sistema operativo Windows è supportato solo il runtime di integrazione self-hosted.  

In termini di disponibilità elevata e scalabilità è possibile scalare orizzontalmente il runtime di integrazione self-hosted associando l'istanza logica con più computer locali in modalità attivo-attivo.  Per ulteriori informazioni, vedere la pagina relativa alla procedura per creare e configurare un runtime di integrazione [self-hosted](create-self-hosted-integration-runtime.md) in guide dettagliate.

## <a name="azure-ssis-integration-runtime"></a>Runtime di integrazione Azure-SSIS

Per eseguire in modalità lift-and-shift il carico di lavoro SSIS esistente, è possibile creare un runtime di integrazione Azure-SSIS per l'esecuzione di pacchetti SSIS in modo nativo.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente di rete del runtime di integrazione Azure-SSIS

È possibile eseguire il provisioning del runtime di integrazione Azure-SSIS nella rete pubblica o privata.  L'accesso ai dati locali è supportata aggiungendo il runtime di integrazione Azure-SSIS a una rete virtuale connessa alla rete locale.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Risorsa di calcolo e ridimensionamento del runtime di integrazione Azure-SSIS

Il runtime di integrazione Azure-SSIS è un cluster completamente gestito di macchine virtuali Azure dedicato all'esecuzione di pacchetti SSIS. È possibile portare il proprio database SQL di Azure o SQL Istanza gestita per il catalogo di progetti/pacchetti SSIS (SSISDB). È possibile aumentare la potenza di calcolo specificando la dimensione del nodo e scalare orizzontalmente specificando il numero di nodi nel cluster. È possibile gestire il costo di esecuzione del runtime di integrazione Azure-SSIS interrompendolo e avviandolo in base alle necessità.

Per altre informazioni, vedere l'articolo su come creare e configurare il runtime di integrazione SSIS di Azure nelle guide alle procedure.  Dopo la creazione è possibile distribuire e gestire i pacchetti SSIS esistenti con poche o nessuna modifica usando strumenti familiari, ad esempio SQL Server Data Tools (SSDT) e SQL Server Management Studio (SSMS), come si usa SSIS in locale.

Per altre informazioni sul runtime SSIS di Azure, vedere gli articoli seguenti: 

- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). Questo articolo fornisce istruzioni dettagliate per creare un Azure-SSIS IR e usa un database SQL di Azure per ospitare il catalogo SSIS. 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'uso di SQL Istanza gestita e sull'aggiunta del runtime di integrazione a una rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione. 
- [Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo offre informazioni concettuali sull'aggiunta di un runtime di integrazione Azure-SSIS a una rete virtuale di Azure. Indica inoltre i passaggi per usare il portale di Azure per configurare la rete virtuale in modo che il runtime di integrazione Azure-SSIS possa essere aggiunto alla rete virtuale. 

## <a name="integration-runtime-location"></a>Località del runtime di integrazione

### <a name="relationship-between-factory-location-and-ir-location"></a>Relazione tra posizione Factory e posizione IR

Quando il cliente crea un'istanza di data factory, è necessario specificare il percorso per l'data factory. La località di Data Factory è il punto in cui vengono archiviati i metadati di data factory e il punto da cui viene avviata l'attivazione della pipeline. I metadati per la factory vengono archiviati solo nell'area di scelta del cliente e non verranno archiviati in altre aree.

Nel frattempo, una data factory può accedere ad archivi dati e servizi di calcolo in altre aree di Azure per spostare i dati tra archivi dati o elaborare i dati usando i servizi di calcolo. Questo comportamento viene attuato tramite il [runtime di integrazione disponibile a livello globale](https://azure.microsoft.com/global-infrastructure/services/) per garantire la conformità dei dati, l'efficienza e costi in uscita della rete inferiori.

La località del runtime di integrazione definisce la località del calcolo back-end ed essenzialmente la località in cui vengono eseguiti lo spostamento dei dati, l'invio di attività e l'esecuzione di pacchetti SSIS. La località del runtime di integrazione può essere diversa dalla località della data factory a cui appartiene. 

### <a name="azure-ir-location"></a>Località del runtime di integrazione di Azure

È possibile impostare un determinato percorso di un Azure IR, nel qual caso l'esecuzione o l'invio dell'attività avverrà in tale area specifica.

Se si sceglie di usare la risoluzione automatica Azure IR nella rete pubblica, che è l'impostazione predefinita,

- Per l'attività di copia, ADF effettuerà il massimo sforzo per rilevare automaticamente il percorso dell'archivio dati sink, quindi usare il runtime di integrazione nella stessa area, se disponibile o quello più vicino nella stessa area geografica; Se l'area dell'archivio dati sink non è rilevabile, viene usato il runtime di integrazione nell'area data factory come alternativa.

  Ad esempio, è stata creata la Factory negli Stati Uniti orientali, 
  
  - Quando si copiano i dati nel BLOB di Azure negli Stati Uniti occidentali, se ADF ha rilevato che il BLOB si trova negli Stati Uniti occidentali, l'attività di copia viene eseguita su IR negli Stati Uniti occidentali; Se il rilevamento dell'area ha esito negativo, l'attività di copia viene eseguita su IR negli Stati Uniti orientali.
  - Quando si copiano dati in Salesforce di cui l'area non è rilevabile, l'attività di copia viene eseguita su IR negli Stati Uniti orientali.

  >[!TIP] 
  >Se sono presenti requisiti di conformità rigorosi per i dati e si deve garantire che i dati non lascino una certa area geografica, è possibile creare esplicitamente un runtime di integrazione di Azure in una determinata area e puntare il servizio collegato a questo runtime di integrazione tramite la proprietà ConnectVia. Ad esempio, se si vogliono copiare dati da un BLOB in Regno Unito meridionale ad Azure sinapsi Analytics in Regno Unito meridionale e si vuole garantire che i dati non lascino il Regno Unito, creare una Azure IR in Regno Unito meridionale e collegare entrambi i servizi collegati al runtime di integrazione.

- Per l'esecuzione dell'attività Lookup/GetMetadata/Delete (nota anche come attività Pipeline), l'invio di attività di trasformazione (note anche come attività esterne) e la creazione di operazioni (test connection, browse Folder List e Table list, data Preview), ADF usa il runtime di integrazione nell'area data factory.

- Per il flusso di dati, ADF usa il runtime di integrazione nell'area data factory. 

  > [!TIP] 
  > Una procedura consigliata consiste nel garantire che il flusso di dati venga eseguito nella stessa area degli archivi dati corrispondenti (se possibile). È possibile ottenere questo risultato risolvendo automaticamente Azure IR (se il percorso dell'archivio dati è uguale a quello Data Factory percorso) oppure creando una nuova istanza di Azure IR nella stessa area degli archivi dati, quindi eseguire il flusso di dati. 

Se si Abilita la rete virtuale gestita per la risoluzione automatica Azure IR, ADF usa il runtime di integrazione nell'area data factory. 

È possibile monitorare quale località del runtime di integrazione viene applicata durante l'esecuzione di attività nella vista di monitoraggio delle attività della pipeline nell'interfaccia utente o nel payload di monitoraggio delle attività.

### <a name="self-hosted-ir-location"></a>Località del runtime di integrazione self-hosted

Il runtime di integrazione self-hosted è registrato in modo logico al servizio Data Factory e il calcolo usato per supportare le relative funzionalità viene fornito dall'utente. Pertanto non esiste una proprietà location esplicita per il runtime di integrazione self-hosted. 

Quando viene usato per eseguire lo spostamento di dati, il runtime di integrazione self-hosted estrae i dati dall'origine e li scrive nella destinazione.

### <a name="azure-ssis-ir-location"></a>Località del runtime di integrazione Azure-SSIS

Selezionando la località corretta per il runtime di integrazione Azure-SSIS è fondamentale ottenere prestazioni elevate per ei flussi di lavoro di estrazione, trasformazione e caricamento (ETL).

- Il percorso del Azure-SSIS IR non deve corrispondere al percorso del data factory, ma deve essere uguale a quello del database SQL di Azure o di SQL Istanza gestita in cui SSISDB. In questo modo il runtime di integrazione Azure-SSIS può accedere facilmente a SSISDB senza incorrere in traffico eccessivo tra le diverse località.
- Se non si dispone di un database SQL o di un Istanza gestita SQL esistente, ma si dispone di origini dati/destinazioni locali, è necessario creare un nuovo database SQL di Azure o SQL Istanza gestita nella stessa posizione di una rete virtuale connessa alla rete locale.  In questo modo, è possibile creare il Azure-SSIS IR usando il nuovo database SQL di Azure o SQL Istanza gestita e aggiungendo la rete virtuale, il tutto nella stessa posizione, riducendo in modo efficace i movimenti dei dati in posizioni diverse.
- Se il percorso del database SQL di Azure esistente o del Istanza gestita SQL non è uguale a quello di una rete virtuale connessa alla rete locale, creare prima il Azure-SSIS IR usando un database SQL di Azure esistente o un Istanza gestita SQL e aggiungendo un'altra rete virtuale nella stessa posizione, quindi configurare una rete virtuale per la connessione di rete virtuale tra percorsi diversi.

Il diagramma seguente mostra le impostazioni relative alla località di Data Factory e dei relativi runtime di integrazione:

![Località del runtime di integrazione](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Determinazione del runtime di integrazione da usare

### <a name="copy-activity"></a>Attività di copia

Per l'attività di copia sono necessari i servizi collegati di origine e sink per definire la direzione del flusso di dati. Per determinare l'istanza del runtime di integrazione usata per eseguire la copia, viene usata la logica seguente: 

- **Copia tra due origini dati cloud**: quando entrambi i servizi collegati di origine e sink usano Azure IR, ADF usa il Azure IR regionale se è stato specificato o determina automaticamente il percorso di Azure IR se si sceglie il runtime di integrazione automatica (impostazione predefinita), come descritto nella sezione relativa al [percorso di Integration Runtime](#integration-runtime-location) .
- **Copia dei dati tra un'origine dati cloud e un'origine dati nella rete privata**: se il servizio collegato di origine o sink punta a un runtime di integrazione self-hosted, l'attività di copia viene eseguita su questo runtime di integrazione self-hosted.
- **Copia tra due origini dati nella rete privata**: il servizio collegato di origine e sink deve puntare alla stessa istanza di Integration Runtime e tale runtime di integrazione viene usato per eseguire l'attività di copia.

### <a name="lookup-and-getmetadata-activity"></a>Attività Lookup e GetMetadata

L'attività Lookup e GetMetadata viene eseguita sul runtime di integrazione associato al servizio collegato dell'archivio dati.

### <a name="external-transformation-activity"></a>Attività di trasformazione esterna

Ogni attività di trasformazione esterna che usa un motore di calcolo esterno ha un servizio collegato di calcolo di destinazione, che punta a un runtime di integrazione. Questa istanza di Integration Runtime determina la posizione in cui viene inviata l'attività di trasformazione codificata da mano esterna.

### <a name="data-flow-activity"></a>Attività flusso di dati

Le attività del flusso di dati vengono eseguite nel runtime di integrazione di Azure associato. Il calcolo Spark utilizzato dai flussi di dati è determinato dalle proprietà del flusso di dati nel Azure Integration Runtime e sono completamente gestite da ADF.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Creare il runtime di integrazione di Azure](create-azure-integration-runtime.md)
- [Creare un runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md)
- [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'uso di SQL Istanza gestita e sull'aggiunta del runtime di integrazione a una rete virtuale. 

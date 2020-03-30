---
title: Runtime di integrazione
description: Informazioni sul runtime di integrazione in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: 4077e1e00b606480ec93feacbad3c841c0de1ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336172"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Runtime di integrazione in Azure Data Factory
Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le seguenti funzionalità di integrazione di dati in diversi ambienti di rete:

- **Flusso di dati:** eseguire un [flusso di dati](concepts-data-flow-overview.md) nell'ambiente di calcolo di Azure gestito.  
- **Spostamento dei dati**: Copiare i dati tra gli archivi dati nella rete pubblica e gli archivi dati nella rete privata (locale o rete privata virtuale). Fornisce il supporto per i connettori predefiniti, la conversione dei formati, il mapping di colonne e il trasferimento di dati scalabile e ad alte prestazioni.
- **Invio di attività:** inviare e monitorare le attività di trasformazione in esecuzione in un'ampia gamma di servizi di calcolo, ad esempio Azure Databricks, Azure HDInsight, Azure Machine Learning, Database SQL di Azure, SQL Server e altro ancora.
- **Esecuzione di pacchetti SSIS**: eseguire in modo nativo i pacchetti SQL Server Integration Services (SSIS) in un ambiente di calcolo Azure gestito.

In Data Factory, un'attività definisce l'azione da eseguire. Un servizio collegato definisce un archivio dati o un servizio di calcolo di destinazione. Un runtime di integrazione funge da ponte tra l'attività e i servizi collegati.  Viene fatto riferimento dal servizio o dall'attività collegata e fornisce l'ambiente di calcolo in cui l'attività viene eseguita o viene inviata da. In questo modo, l'attività può essere eseguita nell'area più vicina possibile all'archivio dati o al servizio di calcolo di destinazione nel modo più efficiente soddisfacendo al contempo le esigenze di sicurezza e conformità.

## <a name="integration-runtime-types"></a>Tipi di runtime di integrazione
Il servizio Data Factory offre tre tipi di runtime di integrazione; è consigliabile scegliere il tipo più adatto alle funzionalità di integrazione e ai requisiti dell'ambiente di rete desiderato.  Questi tre tipi sono:

- Azure
- Self-hosted
- Azure-SSIS

Nella tabella seguente vengono descritte le funzionalità e il supporto di rete per ogni tipo di runtime di integrazione:

Tipo di runtime di integrazione | Rete pubblica | Rete privata
------- | -------------- | ---------------
Azure | Flusso di dati<br/>Spostamento dati<br/>Invio di attività | &nbsp;
Self-hosted | Spostamento dati<br/>Invio di attività | Spostamento dati<br/>Invio di attività
Azure-SSIS | Esecuzione pacchetti SSIS | Esecuzione pacchetti SSIS

Il diagramma seguente mostra come è possibile usare runtime di integrazione diversi in combinazione per offrire funzionalità avanzate di integrazione dei dati e supporto di rete:

![Tipi diversi di runtime di integrazione](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Runtime di integrazione di Azure
Un runtime di integrazione di Azure è in grado di eseguire queste operazioni:

- Esecuzione di flussi di dati in AzureRunning Data Flows in Azure 
- Eseguire attività di copia tra archivi dati cloud
- Invio delle seguenti attività di trasformazione nella rete pubblica: attività Databricks Notebook/ Jar/ Python, attività HDInsight Hive, attività HDInsight Pig, attività HDInsight MapReduce, attività HDInsight Spark, attività HDInsight Streaming, attività di esecuzione batch di Machine Learning, attività delle risorse di aggiornamento di Machine Learning, attività Stored Procedure, attività U-SQL di Data Lake Analytics, attività personalizzata .NET, attività Web, attività di ricerca e attività Get metadati.

### <a name="azure-ir-network-environment"></a>Ambiente di rete del runtime di integrazione di Azure
Il runtime di integrazione di Azure supporta la connessione agli archivi dati e ai servizi di calcolo con endpoint accessibili al pubblico. Usare un runtime di integrazione self-hosted per l'ambiente di rete virtuale Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Risorsa di calcolo e ridimensionamento del runtime di integrazione di Azure
Il runtime di integrazione di Azure fornisce un calcolo senza server completamente gestito in Azure.  Non devi preoccuparti del provisioning dell'infrastruttura, dell'installazione del software, dell'applicazione di patch o del ridimensionamento della capacità.  Inoltre si paga solo per la durata dell'utilizzo effettivo.

Il runtime di integrazione di Azure fornisce il calcolo nativo per spostare i dati tra gli archivi dati cloud in modo sicuro, affidabile e ad alte prestazioni.  È possibile impostare il numero di unità di integrazione dati da usare per l'attività di copia; le dimensioni di calcolo del runtime di integrazione di Azure vengono aumentate di conseguenza in modo elastico senza che sia necessario modificare in modo esplicito le dimensioni del runtime di integrazione di Azure. 

L'invio di attività è un'operazione leggera per instradare l'attività al servizio di calcolo di destinazione, pertanto non è necessario aumentare le dimensioni di calcolo per questo scenario.

Per maggiori dettagli, vedere le informazioni su come creare e configurare il runtime di integrazione nelle guide alle procedure. 

> [!NOTE] 
> Il runtime di integrazione di Azure include proprietà correlate al runtime del flusso di dati, che definisce l'infrastruttura di calcolo sottostante che verrebbe usata per eseguire i flussi di dati. 

## <a name="self-hosted-integration-runtime"></a>Runtime di integrazione self-hosted
Un runtime di integrazione self-hosted è in grado di eseguire queste operazioni:

- Eseguire attività di copia tra gli archivi dati cloud e un archivio dati in una rete privata.
- Invio delle attività di trasformazione seguenti sulle risorse di calcolo in locale o nella rete virtuale di Azure: attività di HDInsight Hive (BYOC-Bring Your Own Cluster), attività HDInsight Pig (BYOC), attività HDInsight MapReduce (BYOC), HDInsight Spark attività (BYOC), attività di streaming HDInsight (BYOC), attività di esecuzione batch di Machine Learning, attività delle risorse di aggiornamento di Machine Learning, attività Stored procedure, attività U-SQL di Data Lake Analytics, attività personalizzata (esecuzione in Azure Batch), Ricerca e l'attività Get Metadata.

> [!NOTE] 
> Utilizzare il runtime di integrazione self-hosted per supportare archivi dati che richiedono driver bring-your-own come SAP Hana, MySQL e così via.  Per ulteriori informazioni, vedere [Archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) è una dipendenza di Self Hosted IR. Assicurati di avere JRE installato sullo stesso host.

### <a name="self-hosted-ir-network-environment"></a>Ambiente di rete del runtime di integrazione self-hosted
Per eseguire l'integrazione dei dati in modo sicuro in un ambiente di rete privata, che non ha una visione diretta dall'ambiente cloud pubblico, è possibile installare un runtime di integrazione self-hosted nell'ambiente locale protetto dal firewall aziendale o all'interno un rete privata virtuale.  Il runtime di integrazione self-hosted stabilisce solo connessioni basate su HTTP in uscita per accedere a Internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Risorsa di calcolo e ridimensionamento del runtime di integrazione self-hosted
Il runtime di integrazione self-hosted deve essere installato su un computer locale o una macchina virtuale all'interno di una rete privata. Attualmente in un sistema operativo Windows è supportato solo il runtime di integrazione self-hosted.  

In termini di disponibilità elevata e scalabilità è possibile scalare orizzontalmente il runtime di integrazione self-hosted associando l'istanza logica con più computer locali in modalità attivo-attivo.  Per altre informazioni, vedere l'articolo su come [creare e configurare](create-self-hosted-integration-runtime.md) il prodotto a capo automatico in come visualizzare le guide per i dettagli.

## <a name="azure-ssis-integration-runtime"></a>Runtime di integrazione Azure-SSIS
Per eseguire in modalità lift-and-shift il carico di lavoro SSIS esistente, è possibile creare un runtime di integrazione Azure-SSIS per l'esecuzione di pacchetti SSIS in modo nativo.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente di rete del runtime di integrazione Azure-SSIS
È possibile eseguire il provisioning del runtime di integrazione Azure-SSIS nella rete pubblica o privata.  L'accesso ai dati locali è supportata aggiungendo il runtime di integrazione Azure-SSIS a una rete virtuale connessa alla rete locale.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Risorsa di calcolo e ridimensionamento del runtime di integrazione Azure-SSIS
Il runtime di integrazione Azure-SSIS è un cluster completamente gestito di macchine virtuali Azure dedicato all'esecuzione di pacchetti SSIS. È possibile usare il proprio server di Istanza gestita o di database SQL di Azure per ospitare il catalogo di progetti/pacchetti SSIS (SSISDB) da associare ad esso. È possibile aumentare la potenza di calcolo specificando la dimensione del nodo e scalare orizzontalmente specificando il numero di nodi nel cluster. È possibile gestire il costo di esecuzione del runtime di integrazione Azure-SSIS interrompendolo e avviandolo in base alle necessità.

Per altre informazioni, vedere l'articolo su come creare e configurare il runtime di integrazione SSIS di Azure nelle guide alle procedure.  Dopo la creazione è possibile distribuire e gestire i pacchetti SSIS esistenti con poche o nessuna modifica usando strumenti familiari, ad esempio SQL Server Data Tools (SSDT) e SQL Server Management Studio (SSMS), come si usa SSIS in locale.

Per altre informazioni sul runtime SSIS di Azure, vedere gli articoli seguenti: 

- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). Questo articolo fornisce istruzioni dettagliate per creare un iR Azure-SSIS e usa un database SQL di Azure per ospitare il catalogo SSIS. 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e offre istruzioni sull'uso dell'Istanza gestita di database SQL di Azure e sull'aggiunta del runtime di integrazione a una rete virtuale. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione. 
- [Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo offre informazioni concettuali sull'aggiunta di un runtime di integrazione Azure-SSIS a una rete virtuale di Azure. Indica inoltre i passaggi per usare il portale di Azure per configurare la rete virtuale in modo che il runtime di integrazione Azure-SSIS possa essere aggiunto alla rete virtuale. 

## <a name="integration-runtime-location"></a>Località del runtime di integrazione
La località di Data Factory è il punto in cui vengono archiviati i metadati di data factory e il punto da cui viene avviata l'attivazione della pipeline. Nel frattempo, una data factory può accedere ad archivi dati e servizi di calcolo in altre aree di Azure per spostare i dati tra archivi dati o elaborare i dati usando i servizi di calcolo. Questo comportamento viene attuato tramite il [runtime di integrazione disponibile a livello globale](https://azure.microsoft.com/global-infrastructure/services/) per garantire la conformità dei dati, l'efficienza e costi in uscita della rete inferiori.

La località del runtime di integrazione definisce la località del calcolo back-end ed essenzialmente la località in cui vengono eseguiti lo spostamento dei dati, l'invio di attività e l'esecuzione di pacchetti SSIS. La località del runtime di integrazione può essere diversa dalla località della data factory a cui appartiene. 

### <a name="azure-ir-location"></a>Località del runtime di integrazione di Azure
È possibile impostare una determinata località per un runtime di integrazione di Azure. In questo caso, lo spostamento dei dati o l'invio dell'attività verrà eseguito in quella specifica area. 

>[!TIP]
>Se sono presenti requisiti di conformità rigorosi per i dati e si deve garantire che i dati non lascino una certa area geografica, è possibile creare esplicitamente un runtime di integrazione di Azure in una determinata area e puntare il servizio collegato a questo runtime di integrazione tramite la proprietà ConnectVia. Se ad esempio si vogliono copiare i dati da un BLOB nel Regno Unito meridionale a un'istanza di SQL Data Warehouse nel Regno Unito meridionale e si vuole garantire che i dati non lascino il Regno Unito, creare un runtime di integrazione di Azure nel Regno Unito meridionale e collegare entrambi i servizi collegati a questo runtime di integrazione.

Se si sceglie di usare il sistema di risoluzione automatico del sistema di recapito **di Azure,** che è l'impostazione predefinita, 

- Per l'attività di copia, ADF farà del suo meglio per rilevare automaticamente la posizione dell'archivio dati sink, quindi utilizzare il flusso di accesso video nella stessa area, se disponibile o in quella più vicina nella stessa area geografica; se l'area dell'archivio dati sink non è rilevabile, viene utilizzato il flusso di controllo di messaggità a capo nell'area della data factory in alternativa.

  Per esempio, hai creato la tua fabbrica negli Stati Uniti orientali, 
  
  - Quando si copiano dati in BLOB di Azure negli Stati Uniti occidentali, se ADF ha rilevato correttamente che il BLOB si trova negli Stati Uniti occidentali, l'attività di copia viene eseguita nel sistema di gestione delle operazioni di messaggistica istantanea negli Stati Uniti occidentali. se il rilevamento dell'area non riesce, l'attività di copia viene eseguita sul iR negli Stati Uniti orientali.
  - Quando si copiano i dati in Salesforce di cui la regione non è rilevabile, l'attività di copia viene eseguita su IR negli Stati Uniti orientali.

- Per l'esecuzione di attività Lookup/GetMetadata/Delete (note anche come attività della pipeline), l'invio di attività di trasformazione (noto anche come attività esterne) e le operazioni di creazione e modifica (connessione di test, elenco cartelle di ricerca ed elenco di tabelle, dati di anteprima), ADF utilizzerà il runtime di accesso nell'area data factory.

- Per il flusso di dati, ADF utilizzerà il flusso di proprietà nell'area della data factory. 

  > [!TIP] 
  > È consigliabile garantire che il flusso di dati venga eseguito nella stessa area degli archivi dati corrispondenti (se possibile). È possibile ottenere questo risultato risolvendo automaticamente il livello di accesso di Azure (se la posizione dell'archivio dati corrisponde alla posizione di Data Factory) oppure creando una nuova istanza di livello di controllo di accesso di azure nella stessa area degli archivi dati e quindi eseguire il flusso di dati su di esso. 

È possibile monitorare quale località del runtime di integrazione viene applicata durante l'esecuzione di attività nella vista di monitoraggio delle attività della pipeline nell'interfaccia utente o nel payload di monitoraggio delle attività.

### <a name="self-hosted-ir-location"></a>Località del runtime di integrazione self-hosted
Il runtime di integrazione self-hosted è registrato in modo logico al servizio Data Factory e il calcolo usato per supportare le relative funzionalità viene fornito dall'utente. Pertanto non esiste una proprietà location esplicita per il runtime di integrazione self-hosted. 

Quando viene usato per eseguire lo spostamento di dati, il runtime di integrazione self-hosted estrae i dati dall'origine e li scrive nella destinazione.

### <a name="azure-ssis-ir-location"></a>Località del runtime di integrazione Azure-SSIS
Selezionando la località corretta per il runtime di integrazione Azure-SSIS è fondamentale ottenere prestazioni elevate per ei flussi di lavoro di estrazione, trasformazione e caricamento (ETL).

- Non è necessario che il percorso del sistema di oggetti di archiviazione Azure-SSIS sia uguale al percorso della data factory, ma deve essere uguale al percorso del database SQL di Azure o del server di istanza gestita in cui deve essere ospitato SSISDB. In questo modo il runtime di integrazione Azure-SSIS può accedere facilmente a SSISDB senza incorrere in traffico eccessivo tra le diverse località.
- Se non si dispone di un database SQL di Azure esistente o di un server di istanza gestita per ospitare SSISDB, ma si dispone di origini/destinazioni dati locali, è necessario creare un nuovo database SQL di Azure o un nuovo server di istanza gestita nello stesso percorso di una rete virtuale connessa alla rete locale.  In questo modo, è possibile creare il livello di accesso Azure-SSIS usando il nuovo database SQL di Azure o il nuovo server dell'istanza gestita e partecipando a tale rete virtuale, il tutto nella stessa posizione, riducendo al minimo gli movimenti dei dati in posizioni diverse.
- Se il percorso del database SQL di Azure esistente o del server dell'istanza gestita in cui è ospitato SSISDB non corrisponde al percorso di una rete virtuale connessa alla rete locale, creare innanzitutto il componente di accesso Azure-SSIS usando un database SQL di Azure esistente o Server dell'istanza gestita e aggiunta a un'altra rete virtuale nello stesso percorso, quindi configurare una rete virtuale per la connessione di rete virtuale tra percorsi diversi.

Il diagramma seguente mostra le impostazioni relative alla località di Data Factory e dei relativi runtime di integrazione:

![Località del runtime di integrazione](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Determinazione del runtime di integrazione da usare

### <a name="copy-activity"></a>Attività di copia

Per l'attività di copia sono necessari i servizi collegati di origine e sink per definire la direzione del flusso di dati. Per determinare l'istanza del runtime di integrazione usata per eseguire la copia, viene usata la logica seguente: 

- **Copia tra due origini dati cloud**: quando entrambi i servizi collegati (origine e sink) usano il runtime di integrazione di Azure, Azure Data Factory usa il runtime di integrazione di Azure dell'area, se specificato, oppure determina automaticamente una località del runtime di integrazione di Azure se si sceglie l'opzione di risoluzione automatica del runtime di integrazione (impostazione predefinita), come descritto nella sezione [Località del runtime di integrazione](#integration-runtime-location).
- **Copia dei dati tra un'origine dati cloud e un'origine dati nella rete privata**: se il servizio collegato di origine o sink punta a un runtime di integrazione self-hosted, l'attività di copia viene eseguita su questo runtime di integrazione self-hosted.
- **Copia tra due origini dati nella rete privata:** sia il servizio collegato di origine che quello del sink devono puntare alla stessa istanza del runtime di integrazione e tale runtime di integrazione viene usato per eseguire l'attività di copia.

### <a name="lookup-and-getmetadata-activity"></a>Attività Lookup e GetMetadata

L'attività Lookup e GetMetadata viene eseguita sul runtime di integrazione associato al servizio collegato dell'archivio dati.

### <a name="external-transformation-activity"></a>Attività di trasformazione esterna

Ogni attività di trasformazione esterna che utilizza un motore di calcolo esterno dispone di un servizio collegato di calcolo di destinazione, che punta a un runtime di integrazione. Questa istanza del runtime di integrazione determina la posizione da cui viene inviata l'attività di trasformazione esterna codificata a mano.

### <a name="data-flow-activity"></a>Attività flusso di datiData Flow activity

Le attività del flusso di dati vengono eseguite nel runtime di integrazione di Azure associato. Il calcolo Spark utilizzato dai flussi di dati è determinato dalle proprietà del flusso di dati nel runtime di integrazione di Azure e sono completamente gestite da ADF.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare il runtime di integrazione di AzureCreate Azure integration runtime](create-azure-integration-runtime.md)
- [Creare un runtime di integrazione self-hostedCreate self-hosted integration runtime](create-self-hosted-integration-runtime.md)
- Creare un runtime di [integrazione Azure-SSIS](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e offre istruzioni sull'uso dell'Istanza gestita di database SQL di Azure e sull'aggiunta del runtime di integrazione a una rete virtuale. 

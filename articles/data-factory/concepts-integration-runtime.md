---
title: Runtime di integrazione in Azure Data Factory | Microsoft Docs
description: Informazioni sul runtime di integrazione in Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: 5c78f61074258c734cefade28d6aca1055519758
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="integration-runtime-in-azure-data-factory"></a>Runtime di integrazione in Azure Data Factory
Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le seguenti funzionalità di integrazione di dati in diversi ambienti di rete:

- **Spostamento dei dati**: spostare dati tra gli archivi dati nella rete pubblica e gli archivi dati nella rete privata (locale o VPN). Fornisce il supporto per i connettori predefiniti, la conversione dei formati, il mapping di colonne e il trasferimento di dati scalabile e ad alte prestazioni.
- **Invio di attività**: inviare e monitorare le attività di trasformazione in esecuzione in diversi servizi di calcolo, come Azure HDInsight, Azure Machine Learning, il database SQL di Azure, SQL Server e altro ancora.
- **Esecuzione di pacchetti SSIS**: eseguire in modo nativo i pacchetti SQL Server Integration Services (SSIS) in un ambiente di calcolo Azure gestito.


> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale (GA), vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).

In Data Factory, un'attività definisce l'azione da eseguire. Un servizio collegato definisce un archivio dati o un servizio di calcolo di destinazione. Un runtime di integrazione funge da ponte tra l'attività e i servizi collegati.  Viene referenziato dal servizio collegato e fornisce l'ambiente di calcolo in cui l'attività viene eseguita o da cui viene inviata.  In questo modo, l'attività può essere eseguita nell'area più vicina possibile all'archivio dati o al servizio di calcolo di destinazione nel modo più efficiente soddisfacendo al contempo le esigenze di sicurezza e conformità.

## <a name="integration-runtime-types"></a>Tipi di runtime di integrazione
Il servizio Data Factory offre tre tipi di runtime di integrazione; è consigliabile scegliere il tipo più adatto alle funzionalità di integrazione e ai requisiti dell'ambiente di rete desiderato.  Questi tre tipi sono:

- Azure
- Self-hosted
- Azure-SSIS

Nella tabella seguente vengono descritte le funzionalità e il supporto di rete per ogni tipo di runtime di integrazione:

Tipo di runtime di integrazione | Rete pubblica | Rete privata
------- | -------------- | ---------------
Azure | Spostamento dati<br/>Invio di attività | &nbsp;
Self-hosted | Spostamento dati<br/>Invio di attività | Spostamento dati<br/>Invio di attività
Azure-SSIS | Esecuzione di pacchetti SSIS | Esecuzione di pacchetti SSIS

Il diagramma seguente mostra come è possibile usare runtime di integrazione diversi in combinazione per offrire funzionalità avanzate di integrazione dei dati e supporto di rete:

![Tipi diversi di runtime di integrazione](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Runtime di integrazione di Azure
Un runtime di integrazione di Azure è in grado di eseguire queste operazioni:

- Eseguire attività di copia tra archivi dati cloud
- Invio delle seguenti attività di trasformazione nella rete pubblica: attività Hive di HDInsight, attività Pig di HDInsight, attività MapReduce di HDInsight, attività Spark di HDInsight , attività di streaming di HDInsight, attività di esecuzione batch di Machine Learning, attività della risorsa di aggiornamento di Machine Learning, attività stored procedure, attività U-SQL di Data Lake Analytics, attività personalizzate .Net, attività Web, attività di ricerca e attività GetMetadata.

### <a name="network-environment"></a>Ambiente di rete
Il runtime di integrazione di Azure supporta la connessione agli archivi dati e ai servizi di calcolo nella rete pubblica con endpoint accessibili pubblici. Usare un runtime di integrazione self-hosted per l'ambiente di rete virtuale Azure.

### <a name="compute-resource-and-scaling"></a>Risorsa di calcolo e ridimensionamento
Il runtime di integrazione di Azure fornisce un calcolo senza server completamente gestito in Azure.  Non è necessario preoccuparsi di eseguire il provisioning dell'infrastruttura, l'installazione del software, l'applicazione di patch o il ridimensionamento della capacità.  Inoltre si paga solo per la durata dell'utilizzo effettivo.

Il runtime di integrazione di Azure fornisce il calcolo nativo per spostare i dati tra gli archivi dati cloud in modo sicuro, affidabile e ad alte prestazioni.  È possibile impostare il numero di unità di spostamento dati da usare per l'attività di copia; le dimensioni di calcolo del runtime di integrazione di Azure vengono aumentate di conseguenza in modo elastico senza che sia necessario modificare in modo esplicito le dimensioni del runtime di integrazione di Azure.

L'invio di attività è un'operazione semplice per indirizzare l'attività al servizio di calcolo di destinazione e quindi non è necessario aumentare le dimensioni di calcolo per questo scenario.

Per maggiori dettagli, vedere le informazioni su come creare e configurare il runtime di integrazione nelle guide alle procedure. 

## <a name="self-hosted-integration-runtime"></a>Runtime di integrazione self-hosted
Un runtime di integrazione self-hosted è in grado di eseguire queste operazioni:

- Eseguire attività di copia tra gli archivi dati cloud e un archivio dati in una rete privata.
- Invio delle seguenti attività di trasformazione a risorse di calcolo in locale o nella rete virtuale di Azure: attività Hive di HDInsight (BYOC), attività Pig di HDInsight (BYOC), attività MapReduce di HDInsight (BYOC), attività di HDInsight Spark (BYOC), attività di streaming di HDInsight (BYOC), attività di esecuzione batch di Machine Learning, attività della risorsa di aggiornamento di Machine Learning, attività stored procedure, attività U-SQL di Data Lake Analytics, attività personalizzate .Net, attività di ricerca e attività GetMetadata.

> [!NOTE] 
> Usare il runtime di integrazione self-hosted per supportare gli archivi dati che necessitano dell'approccio Bring-Your-Own-Driver, ad esempio SAP Hana, MySQL e così via.  Per altre informazioni, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="network-environment"></a>Ambiente di rete
Per eseguire l'integrazione dei dati in modo sicuro in un ambiente di rete privata, che non ha una visione diretta dall'ambiente cloud pubblico, è possibile installare un runtime di integrazione self-hosted nell'ambiente locale protetto dal firewall aziendale o all'interno un rete privata virtuale.  Il runtime di integrazione self-hosted stabilisce solo connessioni basate su HTTP in uscita per accedere a Internet.

### <a name="compute-resource-and-scaling"></a>Risorsa di calcolo e ridimensionamento
Il runtime di integrazione self-hosted deve essere installato su un computer locale o una macchina virtuale all'interno di una rete privata. Attualmente in un sistema operativo Windows è supportato solo il runtime di integrazione self-hosted.  

In termini di disponibilità elevata e scalabilità è possibile scalare orizzontalmente il runtime di integrazione self-hosted associando l'istanza logica con più computer locali in modalità attivo-attivo.  Per altre informazioni dettagliate, vedere l'articolo su come creare e configurare il runtime di integrazione self-hosted nelle guide alle procedure.

## <a name="azure-ssis-integration-runtime"></a>Runtime di integrazione Azure-SSIS
Per eseguire in modalità lift-and-shift il carico di lavoro SSIS esistente, è possibile creare un runtime di integrazione Azure-SSIS per l'esecuzione di pacchetti SSIS in modo nativo.

### <a name="network-environment"></a>Ambiente di rete
È possibile eseguire il provisioning del runtime di integrazione Azure-SSIS nella rete pubblica o privata.  L'accesso ai dati locali è supportata aggiungendo il runtime di integrazione Azure-SSIS a una rete virtuale connessa alla rete locale. Attualmente è supportata solo la reti virtuale classica. 

### <a name="compute-resource-and-scaling"></a>Risorsa di calcolo e ridimensionamento
Il runtime di integrazione Azure-SSIS è un cluster completamente gestito di macchine virtuali Azure dedicato all'esecuzione di pacchetti SSIS. È possibile usare il proprio server di database SQL o Istanza gestita (anteprima privata) per eseguire l'hosting del catalogo di progetti/pacchetti SSIS (SSISDB) da associare ad esso. È possibile aumentare la potenza di calcolo specificando la dimensione del nodo e scalare orizzontalmente specificando il numero di nodi nel cluster. È possibile gestire il costo di esecuzione del runtime di integrazione Azure-SSIS interrompendolo e avviandolo in base alle necessità.

Per altre informazioni, vedere l'articolo su come creare e configurare il runtime di integrazione SSIS di Azure nelle guide alle procedure.  Dopo la creazione è possibile distribuire e gestire i pacchetti SSIS esistenti con poche o nessuna modifica usando strumenti familiari, ad esempio SQL Server Data Tools (SSDT) e SQL Server Management Studio (SSMS), come si usa SSIS in locale.

Per altre informazioni sul runtime SSIS di Azure, vedere gli articoli seguenti: 

- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-deploy-ssis-packages-azure.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database Azure SQL per ospitare il catalogo SSIS. 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'utilizzo dell’istanza gestita di database SQL di Azure (anteprima privata) e sull’aggiunta del runtime di integrazione a un VNet. 
- [Monitorare un runtime di integrazione SSIS di Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo viene illustrato come recuperare informazioni su un runtime di integrazione SSIS di Azure e le descrizioni degli stati nelle informazioni restituite. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione. 
- [Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo fornisce informazioni sull'aggiunta di un runtime di integrazione SSIS di Azure a una rete virtuale di Azure (VNet). Indica inoltre i passaggi per usare il portale di Azure per configurare VNet in modo che il runtime di integrazione SSIS di Azure possa essere aggiunto alla rete virtuale. 

## <a name="determining-which-ir-to-use"></a>Determinazione del runtime di integrazione da usare
Ogni attività di trasformazione ha un servizio collegato di calcolo di destinazione che punta a un runtime di integrazione. Questa istanza del runtime di integrazione è la posizione da cui viene inviata l'attività di trasformazione.

Per l'attività di copia sono necessari i servizi collegati di origine e sink per definire la direzione del flusso di dati. Per determinare l'istanza del runtime di integrazione usata per eseguire la copia, viene usata la logica seguente: 

- **Copia tra due origini dati cloud**: quando entrambi i servizi collegati, di origine e sink, usano il runtime di integrazione di Azure, l'attività di copia viene eseguita sul runtime di integrazione usato dal servizio collegato sink.
- **Copia dei dati tra un'origine dati cloud e un'origine dati nella rete privata**: se il servizio collegato di origine o sink punta a un runtime di integrazione self-hosted, l'attività di copia viene eseguita su questo runtime di integrazione self-hosted.
- **Copia tra due origini dati nella rete privata**: sia il servizio collegato di origine che quello sink devono puntare alla stessa istanza del runtime di integrazione e questo runtime di integrazione viene usato per eseguire l'attività di copia.

Il diagramma seguente mostra due esempi di attività di copia:

- Per l'attività di copia 1, l'origine è un servizio collegato di Microsoft SQL Server che referenzia a un runtime di integrazione A self-hosted e il sink è un servizio collegato di Archiviazione di Azure che referenzia a un runtime di integrazione B di Azure. L'attività di copia viene eseguita sul runtime di integrazione A self-hosted.
- Per attività di copia 2, l'origine è un servizio collegato del database SQL di Azure che referenzia a un runtime di integrazione C di Azure e il sink è un servizio collegato di Archiviazione di Azure che referenzia a un runtime di integrazione B di Azure. L'attività di copia viene eseguita sul runtime di integrazione B di Azure perché è il runtime di integrazione usato dal servizio collegato sink.

![Runtime di integrazione da usare](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>Località del runtime di integrazione
La località di Data Factory è il punto in cui vengono archiviati i metadati di data factory e il punto da cui viene avviata l'attivazione della pipeline. Attualmente le località Data Factory supportate sono: Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Una data factory può accedere ad archivi dati e servizi di calcolo in altre aree di Azure per spostare i dati tra archivi dati o elaborare i dati usando i servizi di calcolo. Questo comportamento viene attuato tramite il runtime di integrazione disponibile a livello globale in più aree per garantire la conformità dei dati, l'efficienza e costi in uscita della rete inferiori.

La località del runtime di integrazione definisce la località del calcolo back-end ed essenzialmente la località in cui vengono eseguiti lo spostamento dei dati, l'invio di attività e l'esecuzione di pacchetti SSIS. La località del runtime di integrazione può essere diversa dalla località della data factory a cui appartiene. Il diagramma seguente mostra le impostazioni relative alla località di Data Factory e dei relativi runtime di integrazione:

![Località del runtime di integrazione](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Runtime di integrazione di Azure
Data Factory usa un runtime di integrazione di Azure nell'area più vicina al sink nella stessa area geografica per spostare i dati. Consultare la tabella seguente per il mapping:

Geografia dell'archivio dati sink | Località dell'archivio dati sink | Località usata dal runtime di integrazione di Azure
-------------------------------| ----------------| ------------------
Stati Uniti | Stati Uniti orientali | Stati Uniti orientali
&nbsp; | Stati Uniti orientali 2 | Stati Uniti orientali 2
&nbsp; | Stati Uniti centrali | Stati Uniti centrali
&nbsp; | Stati Uniti centro-settentrionali | Stati Uniti centro-settentrionali
&nbsp; | Stati Uniti centro-meridionali | Stati Uniti centro-meridionali
&nbsp; | Stati Uniti centro-occidentali | Stati Uniti centro-occidentali
&nbsp; | Stati Uniti occidentali | Stati Uniti occidentali
&nbsp; | Stati Uniti occidentali 2 | Stati Uniti occidentali 2
Canada | Canada orientale | Canada centrale
&nbsp; | Canada centrale | Canada centrale
Brasile | Brasile meridionale | Brasile meridionale
Europa | Europa settentrionale | Europa settentrionale
&nbsp; | Europa occidentale | Europa occidentale
Regno Unito | Regno Unito occidentale | Regno Unito meridionale
&nbsp; | Regno Unito meridionale | Regno Unito meridionale
Asia/Pacifico | Asia sudorientale | Asia sudorientale
&nbsp; | Asia orientale | Asia sudorientale
Australia | Australia orientale | Australia orientale
&nbsp; | Australia sudorientale | Australia sudorientale
Giappone | Giappone orientale | Giappone orientale
&nbsp; | Giappone occidentale | Giappone orientale
Corea | Corea centrale | Corea centrale
&nbsp; | Corea meridionale | Corea centrale
India | India centrale | India centrale
&nbsp; | India occidentale | India centrale
&nbsp; | India meridionale | India centrale

È possibile anche impostare la località di un runtime di integrazione di Azure per la risoluzione automatica, ovvero Data Factory esegue il massimo sforzo per rilevare automaticamente la località migliore da usare in base alla definizione del servizio collegato.

> [!NOTE] 
> Se l'area dell'archivio dati di destinazione non è nell'elenco o non è rilevabile, l'attività ha esito negativo invece di passare attraverso un'area alternativa per motivi di conformità. In questo caso indicare in modo esplicito la località alternativa da usare per eseguire la copia.
 
La figura seguente illustra un esempio di località effettiva quando la località del runtime di integrazione di Azure viene impostato come risoluzione automatica. Quando viene eseguita un'attività di copia, rileva la località di destinazione dei dati, in questo esempio il Giappone occidentale.  In base alla tabella viene usato un runtime di integrazione in Giappone orientale per eseguire la copia dei dati effettiva. Quando lo stesso runtime di integrazione viene usato per connettersi ad HDInsight per un'attività Spark, si verifica l'invio dell'applicazione Spark dalla località di Data Factory: in questo esempio la località è gli Stati Uniti orientali e l'esecuzione effettiva dell'applicazione Spark avviene nella località del server HDInsight. 

![Località effettiva](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>Runtime di integrazione self-hosted
Il runtime di integrazione self-hosted è registrato in modo logico al servizio Data Factory e il calcolo usato per supportare le relative funzionalità viene fornito dall'utente. Pertanto non esiste una proprietà location esplicita per il runtime di integrazione self-hosted. 

Quando viene usato per eseguire lo spostamento di dati, il runtime di integrazione self-hosted estrae i dati dall'origine e li scrive nella destinazione.

### <a name="azure-ssis-ir"></a>Runtime di integrazione Azure-SSIS
Selezionando la località corretta per il runtime di integrazione Azure-SSIS è fondamentale ottenere prestazioni elevate per ei flussi di lavoro di estrazione, trasformazione e caricamento (ETL).  Due località sono inizialmente disponibili per l'anteprima (Stati Uniti orientali ed Europa settentrionale).

- La località del runtime di integrazione Azure-SSIS non deve essere necessariamente uguale a quella della data factory, ma deve essere uguale a quella del server del database SQL/Istanza gestita di Azure (anteprima privata) in cui verrà eseguito l'hosting di SSISDB. In questo modo il runtime di integrazione Azure-SSIS può accedere facilmente a SSISDB senza incorrere in traffico eccessivo tra le diverse località.
- Se non si dispone di un server del database SQL/Istanza gestita di Azure (anteprima privata) esistente per eseguire l'hosting di SSISDB, ma si dispone di origini/destinazioni di dati locali, è opportuno creare un nuovo server del database SQL/Istanza gestita di Azure (anteprima privata) nella stessa località di una rete virtuale connessa alla rete locale.  In questo modo è possibile creare il runtime di integrazione Azure-SSIS usando il nuovo server del database SQL/Istanza gestita di Azure (anteprima privata) e aggiungendo tale rete virtuale, tutto nella stessa località, riducendo al minimo gli spostamenti di dati in località diverse in modo efficace.
- Se la località del server del database SQL/Istanza gestita di Azure (anteprima privata) esistente in cui è stato eseguito l'hosting di SSISDB non è la stessa località di una rete virtuale connessa alla rete locale, creare prima il runtime di integrazione Azure-SSIS usando un server del database SQL/Istanza gestita di Azure (anteprima privata) e aggiungendo un'altra rete virtuale nella stessa località e quindi configurare una connessione da rete virtuale a rete virtuale tra località diverse.


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare il runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md)
- [Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'utilizzo dell’istanza gestita di database SQL di Azure (anteprima privata) e sull’aggiunta del runtime di integrazione a un VNet. 

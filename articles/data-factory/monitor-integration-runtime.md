---
title: Monitorare il runtime di integrazione in Azure Data Factory
description: Informazioni su come monitorare diversi tipi di runtime di integrazione in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/11/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 4a0c2813a45fab497173d0101f87b30288e93884
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568899"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitoraggio di un runtime di integrazione in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integration Runtime** è l'infrastruttura di calcolo usata da Azure Data Factory (ADF) per fornire diverse funzionalità di integrazione dei dati in diversi ambienti di rete. Esistono tre tipi di runtime di integrazione offerti da Data Factory:

- Runtime di integrazione di Azure
- Runtime di integrazione self-hosted
- Runtime di integrazione di Azure-SQL Server Integration Services (SSIS)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per ottenere lo stato di un'istanza di runtime di integrazione (IR), eseguire il comando PowerShell seguente: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Il cmdlet restituisce informazioni diverse per diversi tipi di runtime di integrazione. In questo articolo vengono illustrate le proprietà e gli stati per ogni tipo di runtime di integrazione.  

## <a name="azure-integration-runtime"></a>Runtime di integrazione di Azure

La risorsa di calcolo per un runtime di integrazione di Azure è completamente gestita in modo elastico in Azure. La tabella seguente fornisce le descrizioni per le proprietà restituite dal comando **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Proprietà

Nella tabella seguente vengono fornite descrizioni per le proprietà restituite dal cmdlet per un runtime di integrazione Azure:

| Proprietà | Descrizione |
-------- | ------------- | 
| Nome | Nome del runtime di integrazione di Azure. |  
| State | Stato del runtime di integrazione di Azure. | 
| Località | Percorso del runtime di integrazione di Azure. Per altri dettagli sul percorso di un runtime di integrazione di Azure, vedere [Introduzione al runtime di integrazione](concepts-integration-runtime.md). |
| DataFactoryName | Nome della data factory a cui appartiene il runtime di integrazione di Azure. | 
| ResourceGroupName | Nome del gruppo di risorse a cui appartiene la data factory.  |
| Descrizione | Descrizione del runtime di integrazione di Azure.  |

### <a name="status"></a>Stato

La tabella seguente indica i possibili stati di un runtime di integrazione di Azure:

| Stato | Commenti/Scenari | 
| ------ | ------------------ |
| Online | Il runtime di integrazione di Azure è online e pronto per essere utilizzato. | 
| Offline | Il runtime di integrazione di Azure è offline a causa di un errore interno. |

## <a name="self-hosted-integration-runtime"></a>Runtime di integrazione self-hosted

In questa sezione vengono fornite le descrizioni per le proprietà restituite dal cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> La proprietà restituite e lo stato contengono informazioni sul runtime di integrazione self-hosted generale e su ogni nodo nel runtime.  

### <a name="properties"></a>Proprietà

La tabella seguente fornisce le descrizioni delle proprietà di monitoraggio per **ogni nodo**:

| Proprietà | Descrizione | 
| -------- | ----------- | 
| Nome | Nome del runtime di integrazione self-hosted e dei nodi associati. Il nodo è un computer Windows locale su cui è installato il runtime di integrazione self-hosted. |  
| Stato | Lo stato del runtime di integrazione self-hosted generale e di ogni nodo. Esempio: online/offline/Limited/ecc. Per informazioni su questi Stati, vedere la sezione successiva. | 
| Versione | La versione del runtime di integrazione self-hosted e di ogni nodo. La versione del runtime di integrazione self-hosted viene determinata in base alla versione della maggior parte dei nodi del gruppo. Se nella configurazione del runtime di integrazione self-hosted sono presenti nodi con versioni diverse, solo i nodi con lo stesso numero di versione del runtime di integrazione self-hosted funzionano correttamente. Gli altri sono in modalità limitata e devono essere aggiornati manualmente (solo se l'aggiornamento automatico non riesce). | 
| Memoria disponibile | Memoria disponibile in un nodo di runtime di integrazione self-hosted. Questo valore è uno snapshot in tempo quasi reale. | 
| Uso della CPU | Utilizzo della CPU da parte di un nodo di runtime di integrazione self-hosted. Questo valore è uno snapshot in tempo quasi reale. |
| Rete (in/out) | Utilizzo del network da parte di un nodo di runtime di integrazione self-hosted. Questo valore è uno snapshot in tempo quasi reale. | 
| Processi simultanei (in esecuzione/limite) | **In esecuzione**. Numero di processi o di attività in esecuzione in ogni nodo. Questo valore è uno snapshot in tempo quasi reale. <br/><br/>**Limite**. Per limite si intende il numero massimo di processi simultanei per ogni nodo. Questo valore viene definito in base alle dimensioni del computer. È possibile aumentare il limite per incrementare il numero di processi simultanei in esecuzione negli scenari avanzati, in cui si verifica il timeout delle attività anche in caso di sottoutilizzo di CPU, memoria o rete. Questa capacità è disponibile anche in un runtime di integrazione self-hosted a nodo singolo. |
| Ruolo | Esistono due tipi di ruoli in un runtime di integrazione self-hosted a più nodi: dispatcher e ruolo di lavoro. Tutti i nodi sono ruoli di lavoro e quindi possono essere tutti usati per eseguire i processi. Esiste un solo nodo dispatcher, che viene usato per eseguire il pull di attività/processi dai servizi cloud e inviarli a nodi ruolo di lavoro diversi. Il nodo dispatcher è anche un nodo di lavoro. |

Alcune impostazioni delle proprietà sono più appropriate quando sono presenti due o più nodi nel runtime di integrazione self-hosted, ovvero in uno scenario in cui viene incrementato il numero di istanze.

#### <a name="concurrent-jobs-limit"></a>Limite di processi simultanei

Il valore predefinito del limite di processi simultanei è impostato in base alle dimensioni del computer. I fattori usati per calcolare questo valore variano in base alla quantità di RAM e al numero di core di CPU del computer. Pertanto, quanto maggiori sono il numero di core e la quantità di memoria, tanto più elevato sarà il valore predefinito del limite di processi simultanei.

Per aumentare questo limite è necessario incrementare il numero di nodi. In questo modo, infatti, il limite di processi simultanei corrisponde alla somma dei valori limite di processi simultanei di tutti i nodi disponibili.  Se ad esempio un nodo consente di eseguire un massimo di 12 processi simultanei, aggiungendo altri tre nodi simili sarà possibile eseguire un massimo di 48 processi simultanei, ovvero 4 x 12. È consigliabile aumentare il limite di processi simultanei solo quando si nota uno scarso utilizzo delle risorse con i valori predefiniti su ogni nodo.

È possibile sostituire il valore predefinito calcolato nel portale di Azure. Selezionare Autore > Connessioni > Runtime di integrazione > Modifica > Nodi > modificare il limite di processi simultanei per ogni nodo. È anche possibile usare il comando PowerShell [Update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) .
  
### <a name="status-per-node"></a>Stato (per ogni nodo)

La tabella seguente indica i possibili stati di un nodo di runtime di integrazione self-hosted:

| Stato | Descrizione |
| ------ | ------------------ | 
| Online | Il nodo è connesso al servizio Data Factory. |
| Offline | Il nodo è offline. |
| Aggiornamento | È in corso l'aggiornamento automatico del nodo. |
| Limitato | La causa è un problema di connettività, ad esempio un problema della porta HTTP 8050, di connettività del bus di servizio o di sincronizzazione delle credenziali. |
| Inactive | Il nodo è in una configurazione diversa da quella della maggior parte degli altri nodi. |

Un nodo può essere inattivo quando non riesce a connettersi agli altri nodi.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stato (runtime di integrazione self-hosted generale)

La tabella seguente indica i possibili stati di un runtime di integrazione self-hosted. Questo stato varia a seconda degli stati di tutti i nodi che appartengono al runtime. 

| Stato | Descrizione |
| ------ | ----------- | 
| Need Registration | Nessun nodo è ancora registrato per questo runtime di integrazione self-hosted. |
| Online | Tutti i nodi sono online. |
| Offline | Nessun nodo è online. |
| Limitato | Non tutti i nodi in questo runtime di integrazione self-hosted sono in uno stato integro. Questo stato è un avviso indicante che qualche nodo potrebbe essere inattivo. La causa di questo stato potrebbe essere un problema di sincronizzazione delle credenziali nel nodo dispatcher/ruolo di lavoro. |

Usare il cmdlet **Get-AzDataFactoryV2IntegrationRuntimeMetric** per recuperare il payload JSON contenente le proprietà dettagliate del runtime di integrazione self-hosted e i relativi valori di snapshot durante l'esecuzione del cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Esempio di output (presuppone che siano presenti due nodi associati a questo runtime di integrazione self-hosted):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```

## <a name="azure-ssis-integration-runtime"></a>Runtime di integrazione SSIS di Azure

Azure-SSIS IR è un cluster completamente gestito di macchine virtuali (VM o nodi) di Azure dedicato all'esecuzione dei pacchetti SSIS. È possibile richiamare le esecuzioni di pacchetti SSIS in Azure-SSIS IR usando diversi metodi, ad esempio tramite SQL Server Data Tools abilitati per Azure (SSDT), l'utilità della riga di comando AzureDTExec, T-SQL su SQL Server Management Studio (SSMS)/SQL Server Agent ed eseguire le attività del pacchetto SSIS nelle pipeline di ADF. Azure-SSIS IR non esegue altre attività di ADF. Una volta effettuato il provisioning, è possibile monitorarne le proprietà complessive/specifiche del nodo tramite Azure PowerShell, portale di Azure e monitoraggio di Azure.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Monitorare il runtime di integrazione Azure-SSIS con Azure PowerShell

Usare il cmdlet di Azure PowerShell seguente per monitorare le proprietà complessive/specifiche del nodo e gli Stati di Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Proprietà

Nella tabella seguente vengono fornite le descrizioni delle proprietà restituite dal cmdlet precedente per un Azure-SSIS IR.

| Proprietà/stato              | Descrizione                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | Ora UTC in cui è stata creata la Azure-SSIS IR. |
| Nodi                        | I nodi allocati/disponibili della Azure-SSIS IR con stati specifici del nodo (avvio/disponibile/riciclo/non disponibile) ed errori eseguibili. |
| OtherErrors                  | Errori interoperabili non specifici del nodo nel Azure-SSIS IR. |
| LastOperation                | Il risultato dell'ultima operazione di avvio/arresto sul Azure-SSIS IR con errori di utilità pratica se non è riuscito. |
| State                        | Stato complessivo (iniziale/iniziale/avviata/arrestata/arrestata) del Azure-SSIS IR. |
| Località                     | Percorso della Azure-SSIS IR. |
| NodeSize                     | Dimensioni di ogni nodo nella Azure-SSIS IR. |
| NodeCount                    | Il numero di nodi nell'Azure-SSIS IR. |
| MaxParallelExecutionsPerNode | Numero massimo di esecuzioni parallele per nodo nel Azure-SSIS IR. |
| CatalogServerEndpoint        | Endpoint del server di database SQL di Azure o istanza gestita esistente per l'hosting del catalogo SSIS (SSISDB). |
| CatalogAdminUserName         | Nome utente amministratore per il server di database SQL di Azure o istanza gestita esistente. ADF utilizza queste informazioni per preparare e gestire SSISDB per conto dell'utente. |
| CatalogAdminPassword         | Password amministratore per il server di database SQL di Azure o istanza gestita esistente. |
| CatalogPricingTier           | Piano tariffario per SSISDB ospitato dal server del database SQL di Azure.  Non applicabile all’istanza gestita SQL di Azure che ospita il database SSIS. |
| VNetId                       | ID della risorsa di rete virtuale per la Azure-SSIS IR da unire. |
| Subnet                       | Nome della subnet per il Azure-SSIS IR da unire. |
| ID                           | ID risorsa della Azure-SSIS IR. |
| Tipo                         | Tipo IR (gestito/self-hosted) del Azure-SSIS IR. |
| ResourceGroupName            | Nome del gruppo di risorse di Azure in cui sono stati creati i file ADF e Azure-SSIS IR. |
| DataFactoryName              | Nome del file ADF. |
| Nome                         | Nome del Azure-SSIS IR. |
| Descrizione                  | Descrizione della Azure-SSIS IR. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>Stato (per nodo Azure-SSIS IR)

Nella tabella seguente vengono indicati gli stati possibili di un nodo Azure-SSIS IR:

| Stato specifico del nodo | Descrizione |
| -------------------- | ----------- | 
| Avvio in corso             | Questo nodo è in corso di preparazione. |
| Disponibile            | Questo nodo è pronto per la distribuzione/esecuzione di pacchetti SSIS. |
| Riciclo            | Questo nodo è in corso di riparazione/riavvio. |
| Non disponibile          | Questo nodo non è pronto per la distribuzione/esecuzione di pacchetti SSIS e presenta errori o problemi di utilità pratica che è possibile risolvere. |

#### <a name="status-overall-azure-ssis-ir"></a>Stato (generale Azure-SSIS IR)

Nella tabella seguente sono riportati i possibili stati complessivi di un Azure-SSIS IR. Lo stato complessivo a sua volta dipende dagli stati combinati di tutti i nodi appartenenti al Azure-SSIS IR. 

| Stato generale | Descrizione | 
| -------------- | ----------- | 
| Initial        | I nodi del Azure-SSIS IR non sono stati allocati/preparati. | 
| Avvio in corso       | È in corso l'allocazione/preparazione dei nodi del Azure-SSIS IR e la fatturazione è stata avviata. |
| Avviato        | I nodi del Azure-SSIS IR sono stati allocati/preparati e sono pronti per la distribuzione/esecuzione di pacchetti SSIS. |
| Stopping       | Verranno rilasciati i nodi del Azure-SSIS IR. |
| Arrestato        | I nodi del Azure-SSIS IR sono stati rilasciati e la fatturazione è stata interrotta. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Monitorare il runtime di integrazione SSIS di Azure in portale di Azure

Per monitorare le Azure-SSIS IR in portale di Azure, passare alla pagina **runtime di integrazione** di hub **monitoraggio** nell'interfaccia utente di ADF, in cui è possibile visualizzare tutti i runtime di integrazione.

![Monitora tutti i runtime di integrazione](media/monitor-integration-runtime/monitor-integration-runtimes.png)

Selezionare quindi il nome della Azure-SSIS IR per aprire la relativa pagina di monitoraggio, in cui è possibile visualizzare le proprietà complessive e specifiche del nodo e gli Stati. In questa pagina, a seconda di come vengono configurate le impostazioni generali, di distribuzione e avanzate del Azure-SSIS IR, sono disponibili vari riquadri informativi/funzionali.  I riquadri informazioni sul **tipo** e sull' **area** indicano rispettivamente il tipo e l'area dell'Azure-SSIS IR. Il riquadro informazioni **dimensioni nodo** Mostra lo SKU (SSIS edition_VM tier_VM Series), il numero di core CPU e le dimensioni della RAM per nodo per il Azure-SSIS IR. Il riquadro informativo dei nodi **in esecuzione/richiesto** confronta il numero di nodi attualmente in esecuzione con il numero totale di nodi precedentemente richiesti per la Azure-SSIS IR. I riquadri funzionali sono descritti in dettaglio di seguito.

![Monitorare la Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>Riquadro stato

Nel riquadro **stato** della pagina Monitoraggio Azure-SSIS IR è possibile visualizzare lo stato generale, ad esempio **in esecuzione** o **arrestato**. Se si seleziona lo stato **in esecuzione** , viene visualizzata una finestra con pulsante **Arresta** live per arrestare la Azure-SSIS IR. Se si seleziona lo stato **arrestato** , viene visualizzata una finestra con il pulsante Live **Start** per avviare il Azure-SSIS IR. Nella finestra popup è inoltre disponibile un pulsante **Esegui pacchetto SSIS** per generare automaticamente una pipeline ADF con l'attività Esegui pacchetto SSIS eseguita nel Azure-SSIS IR (vedere [esecuzione di pacchetti SSIS come attività Esegui pacchetto SSIS in pipeline ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)) e una casella di testo **ID risorsa** , da cui è possibile copiare l'ID risorsa Azure-SSIS IR ( `/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR` ). Il suffisso dell'ID di risorsa del Azure-SSIS IR che contiene i nomi di ADF e Azure-SSIS IR costituisce un ID cluster che può essere usato per acquistare componenti SSIS Premium/con licenza aggiuntivi da fornitori di software indipendenti (ISV) e associarli ai Azure-SSIS IR (vedere [installazione di componenti Premium/con licenza nel Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components)).

![Monitorare il riquadro Stato Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>Riquadro ENDPOINT SERVER SSISDB

Se si usa il modello di distribuzione del progetto in cui i pacchetti vengono archiviati in SSISDB ospitato dal server di database SQL di Azure o dall'istanza gestita, verrà visualizzato il riquadro **endpoint server SSISDB** nella pagina Monitoraggio Azure-SSIS IR (vedere [configurazione delle impostazioni di distribuzione Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure#deployment-settings-page)). In questo riquadro è possibile selezionare un collegamento che designa il server di database SQL di Azure o l'istanza gestita per visualizzare una finestra, in cui è possibile copiare l'endpoint server da una casella di testo e usarlo quando ci si connette da SSMS per distribuire, configurare, eseguire e gestire i pacchetti. Nella finestra popup è possibile anche selezionare il collegamento **vedere le impostazioni del database SQL di Azure o dell'istanza gestita** per riconfigurare/ridimensionare il database SSISDB in portale di Azure.

![Monitorare il riquadro Azure-SSIS IR-SSISDB](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>Riquadro PROXY/gestione temporanea

Se si scarica, si installa e si configura il runtime di integrazione self-hosted come proxy per la Azure-SSIS IR di accedere ai dati in locale, il riquadro **proxy/staging** verrà visualizzato nella pagina di monitoraggio Azure-SSIS IR (vedere [configurazione di un proxy per il Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)). In questo riquadro è possibile selezionare un collegamento che designa il problema per aprire la relativa pagina di monitoraggio. È anche possibile selezionare un altro collegamento che designa l'archivio BLOB di Azure per la gestione temporanea per riconfigurare il servizio collegato.

#### <a name="validate-vnet--subnet-tile"></a>Riquadro convalida VNET/SUBNET

Se si aggiunge il Azure-SSIS IR a una VNet, verrà visualizzato il riquadro **convalida VNet/subnet** nella pagina di monitoraggio Azure-SSIS IR (vedere [aggiunta della Azure-SSIS IR a un VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)). In questo riquadro è possibile selezionare un collegamento che designa la VNet e la subnet per visualizzare una finestra, in cui è possibile copiare l'ID di risorsa VNet ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet` ) e il nome della subnet dalle caselle di testo, nonché convalidare le configurazioni di VNet e subnet per assicurarsi che i traffico di rete in ingresso/in uscita e la gestione del Azure-SSIS IR non siano ostruiti.

![Monitorare il riquadro Azure-SSIS IR-VALIDATE](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>Riquadro diagnostica connettività

Nel riquadro **diagnostica connettività** della pagina Monitoraggio Azure-SSIS IR è possibile selezionare il collegamento **Test connessione** per visualizzare una finestra, in cui è possibile controllare le connessioni tra l'Azure-SSIS IR e gli archivi di pacchetti/configurazione/dati rilevanti, nonché i servizi di gestione, tramite il nome di dominio completo (FQDN)/IP e la porta designata (vedere [test delle connessioni dal Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-diagnose-connectivity-faq)).

![Screenshot che mostra dove è possibile testare le connessioni tra il Azure-SSIS IR e gli archivi di pacchetti/configurazione/dati pertinenti.](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>Riquadro indirizzi IP pubblici statici

Se si usano indirizzi IP pubblici statici per Azure-SSIS IR, il riquadro **indirizzi IP pubblici statici** verrà visualizzato nella pagina di monitoraggio Azure-SSIS IR (vedere l' [introduzione di indirizzi IP pubblici statici per Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#publicIP)). In questo riquadro è possibile selezionare i collegamenti che definiscono il primo/secondo indirizzo IP pubblico statico per Azure-SSIS IR per visualizzare una finestra, in cui è possibile copiare l'ID risorsa ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress` ) da una casella di testo. Nella finestra popup è possibile anche selezionare il collegamento **vedere il primo/secondo indirizzo IP pubblico statico** per gestire il primo/secondo indirizzo IP pubblico statico in portale di Azure.

![Screenshot che mostra dove è possibile designare il primo/secondo indirizzo IP pubblico statico.](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>Riquadro archivi pacchetti

Se si usa il modello di distribuzione del pacchetto in cui i pacchetti vengono archiviati nel database file system/File di Azure/SQL Server (MSDB) ospitato dal Istanza gestita SQL di Azure e gestiti tramite gli archivi di pacchetti Azure-SSIS IR, il riquadro **archivi pacchetti** verrà visualizzato nella pagina di monitoraggio Azure-SSIS IR (vedere [configurazione delle impostazioni di distribuzione di Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure#deployment-settings-page)). In questo riquadro è possibile selezionare un collegamento che designa il numero di archivi pacchetti collegati alla Azure-SSIS IR per visualizzare una finestra, in cui è possibile riconfigurare i servizi collegati pertinenti per gli archivi di pacchetti di Azure-SSIS IR in file system/File di Azure/MSDB ospitati dal Istanza gestita SQL di Azure.

![Monitorare il riquadro del pacchetto di Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>Riquadro Errori

Se si verificano problemi durante l'avvio, l'arresto, la manutenzione o l'aggiornamento del Azure-SSIS IR, verrà visualizzato un ulteriore riquadro **errori** nella pagina di monitoraggio Azure-SSIS IR. In questo riquadro è possibile selezionare un collegamento che designa il numero di errori generati dal Azure-SSIS IR per visualizzare una finestra, in cui è possibile visualizzare tali errori in altri dettagli e copiarli per trovare le soluzioni consigliate nella Guida alla risoluzione dei problemi (vedere la pagina relativa alla [risoluzione dei problemi di Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-management-troubleshoot)).

![Monitorare il riquadro Azure-SSIS IR-diagnosi](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Monitorare il runtime di integrazione Azure-SSIS con monitoraggio di Azure

Per monitorare la Azure-SSIS IR con monitoraggio di Azure, vedere [monitoraggio delle operazioni SSIS con monitoraggio di Azure](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#monitor-ssis-operations-with-azure-monitor).

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Altre informazioni sul runtime di integrazione di Azure-SSIS

Vedere gli articoli seguenti per ulteriori informazioni sul runtime di integrazione SSIS di Azure:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Questo articolo offre informazioni sui runtime di integrazione in generale, incluso il runtime di integrazione Azure-SSIS. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). Questo articolo fornisce istruzioni dettagliate per creare il Azure-SSIS IR e usare il database SQL di Azure per ospitare il catalogo SSIS (SSISDB). 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'uso di Istanza gestita SQL di Azure per ospitare il database SSISDB. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). Questo articolo illustra come avviare, arrestare o eliminare i Azure-SSIS IR. Viene inoltre illustrato come scalare il piano di distribuzione aggiungendo altri nodi. 
- [Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo fornisce istruzioni per l'aggiunta del Azure-SSIS IR a una rete virtuale.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per il monitoraggio di pipeline in modi diversi: 

- [Guida introduttiva: creare un data factory](quickstart-create-data-factory-dot-net.md).
- [Utilizzare Monitoraggio di Azure per monitorare le pipeline di Data Factory](monitor-using-azure-monitor.md)
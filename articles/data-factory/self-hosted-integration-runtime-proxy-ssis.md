---
title: Configurare il runtime di integrazione self-hosted come proxy per SSIS in Azure Data Factory | Microsoft Docs
description: Informazioni su come configurare il Integration Runtime self-hosted come proxy per Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2ade270011ad5c1e1e5f5940ca305687e52bba86
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200310"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Configurare il runtime di integrazione self-hosted come proxy per Azure-SSIS IR in ADF
Questo articolo descrive come eseguire pacchetti SQL Server Integration Services (SSIS) in Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) con il runtime di integrazione self-hosted configurato come proxy.  Questa funzionalità consente di accedere ai dati in locale senza [aggiungerli alla Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Questa operazione è utile quando la rete aziendale ha un criterio eccessivamente complesso di configurazione/restrittiva per inserire i Azure-SSIS IR al suo interno.

Questa funzionalità suddividerà il pacchetto che contiene un'attività flusso di dati con origine dati locale in due attività di gestione temporanea: la prima in esecuzione sul runtime di integrazione self-hosted sposta i dati dall'origine dati locale in un'area di gestione temporanea nell'archivio BLOB di Azure, mentre il secondo in esecuzione sul Azure-SSIS IR sposta i dati dall'area di gestione temporanea alla destinazione dei dati desiderata.

Questa funzionalità offre anche altri vantaggi e funzionalità che consentono di effettuare il provisioning del runtime di integrazione self-hosted in aree non ancora supportate da Azure-SSIS IR, di consentire l'indirizzo IP statico pubblico del runtime di integrazione self-hosted nel firewall delle origini dati e così via.

## <a name="prepare-self-hosted-ir"></a>Preparare il runtime di integrazione self-hosted
Per usare questa funzionalità, è necessario prima creare un ADF ed effettuare il provisioning del Azure-SSIS IR al suo interno, se non è già stato fatto, seguendo le [procedure per il provisioning di un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

Sarà quindi necessario effettuare il provisioning del runtime di integrazione Self-Hosted nello stesso ADF in cui viene eseguito il provisioning del Azure-SSIS IR seguendo l'articolo [come creare un runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Infine, sarà necessario scaricare e installare la versione più recente del runtime di integrazione self-hosted, nonché i driver e il runtime aggiuntivi, nel computer locale o nella macchina virtuale di Azure (VM) come indicato di seguito:
- Scaricare e installare la versione più recente del runtime di integrazione self-hosted da [qui](https://www.microsoft.com/download/details.aspx?id=39717).
- Se si usano connettori OLEDB nei pacchetti, scaricare e installare i relativi driver OLEDB nello stesso computer in cui è installato il runtime di integrazione self-hosted, se non è già stato fatto.  Se si usa la versione precedente di OLEDB driver for SQL Server (SQLNCLI), è possibile scaricare la versione a 64 bit da [qui](https://www.microsoft.com/download/details.aspx?id=50402).  Se si usa la versione più recente del driver OLEDB per SQL Server (MSOLEDBSQL), è possibile scaricare la versione a 64 bit da [qui](https://www.microsoft.com/download/details.aspx?id=56730).  Se si usano driver OLEDB per altri sistemi di database, ad esempio PostgreSQL, MySQL, Oracle e così via, è possibile scaricare la versione a 64 bit dai rispettivi siti Web.
- Scaricare e installare il runtime C++ di Visual (VC) nello stesso computer in cui è installato il runtime di integrazione self-hosted, se non è già stato fatto.  È possibile scaricare la versione a 64 bit da [qui](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Preparare il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea
Creare un servizio collegato di archiviazione BLOB di Azure nello stesso ADF in cui viene eseguito il provisioning del Azure-SSIS IR, se non è già stato fatto, seguendo l'articolo [come creare un servizio collegato di ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Verificare quanto segue:
- **Archiviazione BLOB di Azure** è selezionata per l' **archivio dati**
- **AutoResolveIntegrationRuntime** è selezionato per la **connessione tramite il runtime di integrazione**
- L'**entità servizio** dell'**URI**/di firma di **accesso condiviso chiave**/account è selezionata per il **metodo di autenticazione**

![Preparare il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Configurare Azure-SSIS IR con il runtime di integrazione self-hosted come proxy
Dopo aver preparato il runtime di integrazione self-hosted e il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea, è ora possibile configurare il Azure-SSIS IR nuovo/esistente con il runtime di integrazione self-hosted come proxy nel portale o nell'app ADF.  Se il Azure-SSIS IR esistente è in esecuzione, arrestarlo prima di eseguire questa operazione e riavviarlo in seguito.

Nella pagina **Impostazioni avanzate** selezionare la casella di controllo **Configura Integration Runtime self-hosted come proxy per il Azure-SSIS Integration Runtime** , selezionare il runtime di integrazione self-hosted e il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea e specificare un BLOB nome del contenitore per il **percorso di gestione temporanea** , se lo si desidera.

![Configurare Azure-SSIS IR con il runtime di integrazione self-hosted come proxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Abilitare la connessione dei pacchetti SSIS per proxy
Usando la versione più recente di SSDT con l'estensione di progetti SSIS per Visual Studio che può essere scaricata da [qui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) o come programma di installazione autonomo che può essere scaricato da [qui](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), è possibile trovare una nuova proprietà **ConnectByProxy** che è stata aggiunta in OLEDB/ Gestioni connessioni file flat.  

Quando si progettano nuovi pacchetti contenenti attività flusso di dati con origini file OLEDB/flat per accedere a database o file in locale, è possibile abilitare questa proprietà impostando il valore su **true** nel pannello Proprietà delle gestioni connessioni pertinenti.

![Abilita proprietà ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

È anche possibile abilitare questa proprietà quando si eseguono i pacchetti esistenti senza doverle modificare manualmente una alla volta.  Sono disponibili due opzioni:
- Aprire, ricompilare e ridistribuire il progetto contenente i pacchetti con la versione più recente di SSDT da eseguire nel Azure-SSIS IR: La proprietà può quindi essere abilitata impostando il valore su **true** per le gestioni connessioni pertinenti visualizzate nella scheda **gestioni connessioni** della finestra popup Esegui pacchetto durante l'esecuzione di pacchetti da SSMS.

  ![Abilita Property2 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  La proprietà può inoltre essere abilitata impostando il valore su **true** per le gestioni connessioni pertinenti visualizzate nella scheda **gestioni connessioni** dell' [attività Esegui pacchetto SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) durante l'esecuzione di pacchetti nelle pipeline di ADF.
  
  ![Abilita property3 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Ridistribuzione del progetto contenente i pacchetti da eseguire sul runtime di integrazione SSIS: La proprietà può quindi essere abilitata specificando il percorso `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`della proprietà e impostando il valore su **true** come override della proprietà nella scheda **Avanzate** della finestra popup Esegui pacchetto durante l'esecuzione di pacchetti da SSMS.

  ![Abilita Property4 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  La proprietà `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`può inoltre essere abilitata specificando il percorso della proprietà e impostando il valore su **true** come override della proprietà nella scheda **override proprietà** dell' [attività Esegui pacchetto SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) durante l'esecuzione di pacchetti nelle pipeline di ADF.
  
  ![Abilita property5 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Eseguire il debug della prima e della seconda attività di gestione temporanea
Nel runtime di integrazione self-hosted è possibile trovare i log di `C:\ProgramData\SSISTelemetry` runtime nella cartella e i log di esecuzione delle prime `C:\ProgramData\SSISTelemetry\ExecutionLog` attività di staging nella cartella.  I log di esecuzione delle seconde attività di gestione temporanea sono reperibili nei percorsi di registrazione SSISDB o specificati, a seconda che i pacchetti vengano archiviati rispettivamente in SSISDB o file system/condivisioni file/File di Azure.  Gli ID univoci delle prime attività di staging sono disponibili anche nei log di esecuzione delle seconde attività di gestione temporanea, ad esempio 

![ID univoco della prima attività di staging](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Fatturazione per la prima e la seconda attività di gestione temporanea
Le prime attività di staging in esecuzione sul runtime di integrazione self-hosted verranno fatturate separatamente nello stesso modo in cui le attività di spostamento dei dati in esecuzione sul runtime di integrazione self-hosted vengono fatturate come specificato nell'articolo [sui prezzi della pipeline di dati di ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Le seconde attività di gestione temporanea in esecuzione nel Azure-SSIS IR non verranno fatturate separatamente, ma la Azure-SSIS IR in esecuzione verrà fatturata come specificato nell'articolo [sui prezzi di Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Limitazioni correnti

- Attualmente sono supportate solo le gestioni connessioni file flat e OLEDB o le origini file flat. 
- Attualmente sono supportati solo i servizi collegati di archiviazione BLOB di Azure configurati con l'autenticazione dell'**entità servizio** **URI**/della **chiave**/dell'account.
- È attualmente supportato solo il runtime di integrazione self-hosted con provisioning nello stesso ADF in cui è stato eseguito il provisioning del Azure-SSIS IR.
- Non è supportato l'uso di parametri/variabili SSIS all'interno delle proprietà delle origini file e delle gestioni connessioni di OLEDB/flat.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato il runtime di integrazione self-hosted come proxy per la Azure-SSIS IR, è possibile distribuire ed eseguire i pacchetti per accedere ai dati locali come attività Esegui pacchetto SSIS nelle pipeline di ADF, vedere [eseguire pacchetti SSIS come attività Esegui pacchetto SSIS nelle pipeline di ADF ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
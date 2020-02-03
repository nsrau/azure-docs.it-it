---
title: Configurare il runtime di integrazione self-hosted come proxy per SSIS
description: Informazioni su come configurare il Integration Runtime self-hosted come proxy per Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 12/23/2019
ms.openlocfilehash: 48d4df5684c84e195810439912dd610f5af364d4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964482"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Configurare il runtime di integrazione self-hosted come proxy per Azure-SSIS IR in ADF

Questo articolo descrive come eseguire pacchetti SQL Server Integration Services (SSIS) in Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) con il runtime di integrazione self-hosted configurato come proxy.  Questa funzionalità consente di accedere ai dati in locale senza [aggiungerli alla Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  È utile quando la rete aziendale ha un criterio eccessivamente complesso di configurazione/restrittiva per inserire i Azure-SSIS IR al suo interno.

Questa funzionalità suddividerà il pacchetto che contiene un'attività flusso di dati con origine dati locale in due attività di gestione temporanea: la prima in esecuzione sul runtime di integrazione self-hosted sposta i dati dall'origine dati locale in un'area di gestione temporanea nell'archivio BLOB di Azure, mentre il secondo in esecuzione sul Azure-SSIS IR sposta i dati dall'area di gestione temporanea alla destinazione dei dati desiderata.

Questa funzionalità offre anche altri vantaggi e funzionalità che consentono di effettuare il provisioning del runtime di integrazione self-hosted in aree non ancora supportate da Azure-SSIS IR, di consentire l'indirizzo IP statico pubblico del runtime di integrazione self-hosted nel firewall delle origini dati e così via.

## <a name="prepare-self-hosted-ir"></a>Preparare il runtime di integrazione self-hosted

Per usare questa funzionalità, è necessario prima creare un ADF ed effettuare il provisioning del Azure-SSIS IR al suo interno, se non è già stato fatto, seguendo le [procedure per il provisioning di un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

Sarà quindi necessario effettuare il provisioning del runtime di integrazione Self-Hosted nello stesso ADF in cui viene eseguito il provisioning del Azure-SSIS IR seguendo l'articolo [come creare un runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Infine, sarà necessario scaricare e installare la versione più recente del runtime di integrazione self-hosted, nonché i driver e il runtime aggiuntivi, nel computer locale o nella macchina virtuale di Azure (VM) come indicato di seguito:
- Scaricare e installare la versione più recente del runtime di integrazione self-hosted da [qui](https://www.microsoft.com/download/details.aspx?id=39717).
- Se si usano connettori OLEDB nei pacchetti, scaricare e installare i relativi driver OLEDB nello stesso computer in cui è installato il runtime di integrazione self-hosted, se non è già stato fatto.  Se si usa la versione precedente di OLEDB driver for SQL Server (SQLNCLI), è possibile scaricare la versione a 64 bit da [qui](https://www.microsoft.com/download/details.aspx?id=50402).  Se si usa la versione più recente del driver OLEDB per SQL Server (MSOLEDBSQL), è possibile scaricare la versione a 64 bit da [qui](https://www.microsoft.com/download/details.aspx?id=56730).  Se si usano driver OLEDB per altri sistemi di database, ad esempio PostgreSQL, MySQL, Oracle e così via, è possibile scaricare la versione a 64 bit dai rispettivi siti Web.
- Scaricare e installare il C++ runtime di Visual (VC) nello stesso computer in cui è installato il runtime di integrazione self-hosted, se non è già stato fatto.  È possibile scaricare la versione a 64 bit da [qui](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Preparare il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea

Creare un servizio collegato di archiviazione BLOB di Azure nello stesso ADF in cui viene eseguito il provisioning del Azure-SSIS IR, se non è già stato fatto, seguendo l'articolo [come creare un servizio collegato di ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Verificare quanto segue:
- **Archiviazione BLOB di Azure** è selezionata per l' **archivio dati**
- **AutoResolveIntegrationRuntime** è selezionato per la **connessione tramite il runtime di integrazione**
- **Chiave Account**/**URI SAS**/**entità servizio** è selezionata per il **metodo di autenticazione**

>[!TIP]
>Quando si seleziona l' **entità servizio** , concedere almeno il **ruolo di collaboratore dati BLOB di archiviazione**. Per altre informazioni, vedere [connettore di archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties).

![Preparare il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Configurare Azure-SSIS IR con il runtime di integrazione self-hosted come proxy

Dopo aver preparato il runtime di integrazione self-hosted e il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea, è ora possibile configurare il Azure-SSIS IR nuovo/esistente con il runtime di integrazione self-hosted come proxy nel portale o nell'app ADF.  Se il Azure-SSIS IR esistente è in esecuzione, arrestarlo prima di eseguire questa operazione e riavviarlo in seguito.

1. Nel pannello di installazione di Integration runtime passare alle sezioni **Impostazioni generali** e **Impostazioni SQL** selezionando il pulsante **Avanti** . 

1. Nella sezione **Impostazioni avanzate** :

   1. Selezionare la casella di controllo **configura Integration Runtime self-hosted come proxy per il Azure-SSIS Integration Runtime** . 

   1. Per **Integration runtime indipendenti**, selezionare il runtime di integrazione self-hosted esistente come proxy per Azure-SSIS IR.

   1. Per **servizio collegato archiviazione di staging**selezionare il servizio collegato di archiviazione BLOB di Azure esistente o crearne uno nuovo per la gestione temporanea.

   1. Per **percorso di gestione temporanea**specificare un contenitore BLOB nell'account di archiviazione BLOB di Azure selezionato o lasciarlo vuoto per usare uno predefinito per la gestione temporanea.

   1. Selezionare **Continua**.

   ![Impostazioni avanzate con un runtime di integrazione self-hosted](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

È anche possibile configurare il Azure-SSIS IR nuovo/esistente con il runtime di integrazione self-hosted come proxy usando PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Abilitare la connessione dei pacchetti SSIS per proxy

Usando la versione più recente di SSDT con l'estensione di progetti SSIS per Visual Studio che può essere scaricata da [qui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) o come programma di installazione autonomo che può essere scaricato da [qui](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), è possibile trovare una nuova proprietà **ConnectByProxy** aggiunta nelle gestioni connessioni file flat e OLEDB.  

Quando si progettano nuovi pacchetti contenenti attività flusso di dati con origini file OLEDB/flat per accedere a database o file in locale, è possibile abilitare questa proprietà impostando il valore su **true** nel pannello Proprietà delle gestioni connessioni pertinenti.

![Abilita proprietà ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

È anche possibile abilitare questa proprietà quando si eseguono i pacchetti esistenti senza doverle modificare manualmente una alla volta.  Sono disponibili due opzioni:
- Aprire, ricompilare e ridistribuire il progetto contenente i pacchetti con la versione più recente di SSDT da eseguire nel Azure-SSIS IR: la proprietà può quindi essere abilitata impostando il valore su **true** per le gestioni connessioni pertinenti visualizzate nella scheda **gestioni connessioni** della finestra popup Esegui pacchetto durante l'esecuzione di pacchetti da SSMS.

  ![Abilita Property2 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  La proprietà può inoltre essere abilitata impostando il valore su **true** per le gestioni connessioni pertinenti visualizzate nella scheda **gestioni connessioni** dell' [attività Esegui pacchetto SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) durante l'esecuzione di pacchetti nelle pipeline di ADF.
  
  ![Abilita property3 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Ridistribuzione del progetto contenente i pacchetti da eseguire sul runtime di integrazione SSIS: la proprietà può quindi essere abilitata specificando il percorso della proprietà, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`e impostando il valore su **true** come override di proprietà nella scheda **Avanzate** della finestra popup Esegui pacchetto quando si eseguono pacchetti da SSMS.

  ![Abilita Property4 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  La proprietà può inoltre essere abilitata specificando il percorso della proprietà, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`e impostando il valore su **true** come override della proprietà nella scheda **override della proprietà** dell' [attività Esegui pacchetto SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) durante l'esecuzione di pacchetti nelle pipeline di ADF.
  
  ![Abilita property5 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Eseguire il debug della prima e della seconda attività di gestione temporanea

Nel runtime di integrazione self-hosted è possibile trovare i log di runtime in `C:\ProgramData\SSISTelemetry` cartella e i log di esecuzione delle prime attività di gestione temporanea in `C:\ProgramData\SSISTelemetry\ExecutionLog` cartella.  I log di esecuzione delle seconde attività di gestione temporanea sono reperibili nei percorsi di registrazione SSISDB o specificati, a seconda che i pacchetti vengano archiviati rispettivamente in SSISDB o file system/condivisioni file/File di Azure.  Gli ID univoci delle prime attività di staging sono disponibili anche nei log di esecuzione delle seconde attività di gestione temporanea, ad esempio 

![ID univoco della prima attività di staging](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>Utilizzo dell'autenticazione di Windows nelle attività di gestione temporanea

Se le attività di gestione temporanea sul runtime di integrazione self-hosted richiedono l'autenticazione di Windows, è necessario [configurare i pacchetti SSIS in modo da usare la stessa autenticazione di Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). Le attività di gestione temporanea verranno richiamate con l'account del servizio IR self-hosted (`NT SERVICE\DIAHostService` per impostazione predefinita) e l'accesso agli archivi dati verrà eseguito con l'account di autenticazione di Windows. Per entrambi gli account è necessario assegnare determinati criteri di sicurezza. Quindi, nel computer IR indipendente, aprire `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` e completare i passaggi seguenti.
- Assegnare i criteri di **regolazione delle quote di memoria per un processo** e sostituire i criteri del **token a livello di processo** con l'account del servizio IR self-hosted. Questa operazione deve essere eseguita automaticamente quando si installa il runtime di integrazione self-hosted con l'account del servizio predefinito. Se si utilizza un account del servizio diverso, assegnare gli stessi criteri.
- Assegnare i criteri **Accedi come servizio** all'account di autenticazione di Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Fatturazione per la prima e la seconda attività di gestione temporanea

Le prime attività di staging in esecuzione sul runtime di integrazione self-hosted verranno fatturate separatamente nello stesso modo in cui le attività di spostamento dei dati in esecuzione sul runtime di integrazione self-hosted vengono fatturate come specificato nell'articolo [sui prezzi della pipeline di dati di ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Le seconde attività di gestione temporanea in esecuzione nel Azure-SSIS IR non verranno fatturate separatamente, ma la Azure-SSIS IR in esecuzione verrà fatturata come specificato nell'articolo [sui prezzi di Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Limitazioni correnti

- Attualmente sono supportate solo le attività flusso di dati con gestioni connessioni ODBC/OLEDB/flat file e origini file ODBC/OLEDB/flat. 
- Attualmente sono supportati solo i servizi collegati di archiviazione BLOB di Azure configurati con la **chiave dell'Account**/**URI SAS**/l'autenticazione dell' **entità servizio** .

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il runtime di integrazione self-hosted come proxy per la Azure-SSIS IR, è possibile distribuire ed eseguire i pacchetti per accedere ai dati locali come attività Esegui pacchetto SSIS nelle pipeline di ADF, vedere [eseguire pacchetti SSIS come attività Esegui pacchetto SSIS nelle pipeline di ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
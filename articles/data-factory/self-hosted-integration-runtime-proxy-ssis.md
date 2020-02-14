---
title: Configurare un runtime di integrazione self-hosted come proxy per SSIS
description: Informazioni su come configurare un runtime di integrazione self-hosted come proxy per un Azure-SSIS Integration Runtime.
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
ms.date: 02/06/2020
ms.openlocfilehash: 5f9e15b83c36c6c19fbe93c5f1df365f6f763c81
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187676"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Configurare un runtime di integrazione self-hosted come proxy per un Azure-SSIS IR in Azure Data Factory

Questo articolo descrive come eseguire pacchetti SQL Server Integration Services (SSIS) in un Azure-SSIS Integration Runtime (Azure-SSIS IR) in Azure Data Factory con un runtime di integrazione self-hosted (IR self-hosted) configurato come proxy. 

Con questa funzionalità è possibile accedere ai dati in locale senza dover [aggiungere la Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Questa funzionalità è utile quando la rete aziendale presenta una configurazione troppo complessa o un criterio troppo restrittivo per poter inserire il Azure-SSIS IR.

Questa funzionalità suddivide i pacchetti che contengono un'attività flusso di dati con un'origine dati locale in due attività di gestione temporanea: 
* La prima attività, eseguita sul runtime di integrazione self-hosted, sposta innanzitutto i dati dall'origine dati locale in un'area di gestione temporanea nell'archivio BLOB di Azure.
* La seconda attività, che viene eseguita nel Azure-SSIS IR, sposta i dati dall'area di gestione temporanea alla destinazione dati desiderata.

Altri vantaggi e funzionalità di questa funzionalità consentono, ad esempio, di configurare il runtime di integrazione self-hosted in aree non ancora supportate da un Azure-SSIS IR e di consentire l'indirizzo IP statico pubblico del runtime di integrazione self-hosted nel firewall delle origini dati.

## <a name="prepare-the-self-hosted-ir"></a>Preparare il runtime di integrazione self-hosted

Per usare questa funzionalità, creare prima di tutto un data factory e configurarne uno Azure-SSIS IR. Se non è già stato fatto, seguire le istruzioni riportate in [configurare un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Si configura quindi il runtime di integrazione Self-Hosted nello stesso data factory in cui è configurata la Azure-SSIS IR. A tale scopo, vedere [creare un runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Infine, scaricare e installare la versione più recente del runtime di integrazione self-hosted, nonché i driver e il runtime aggiuntivi, nel computer locale o nella macchina virtuale di Azure (VM), come indicato di seguito:
- Scaricare e installare la versione più recente del runtime di integrazione [self-hosted](https://www.microsoft.com/download/details.aspx?id=39717).
- Se si usano connettori di oggetti di collegamento e di incorporamento dei database (OLEDB) nei pacchetti, scaricare e installare i driver OLEDB pertinenti nello stesso computer in cui è installato il runtime di integrazione self-hosted, se non è già stato fatto.  

  Se si utilizza la versione precedente del driver OLEDB per SQL Server (SQL Server Native Client [SQLNCLI]), [scaricare la versione a 64 bit](https://www.microsoft.com/download/details.aspx?id=50402).  

  Se si utilizza la versione più recente del driver OLEDB per SQL Server (MSOLEDBSQL), [scaricare la versione a 64 bit](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Se si usano driver OLEDB per altri sistemi di database, ad esempio PostgreSQL, MySQL, Oracle e così via, è possibile scaricare le versioni a 64 bit dai rispettivi siti Web.
- Se non è già stato fatto, [scaricare e installare la versione a 64 bit del runtime C++ di Visual (VC)](https://www.microsoft.com/download/details.aspx?id=40784) nello stesso computer in cui è installato il runtime di integrazione self-hosted.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Preparare il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea

Se non è già stato fatto, creare un servizio collegato di archiviazione BLOB di Azure nello stesso data factory in cui è configurata la Azure-SSIS IR. A tale scopo, vedere [creare un servizio collegato di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Assicurarsi di eseguire le operazioni seguenti:
- Per **archivio dati**selezionare **archiviazione BLOB di Azure**.  
- Per **Connetti tramite Integration Runtime**, selezionare **AutoResolveIntegrationRuntime**.  
- In **metodo di autenticazione**selezionare **chiave account**, **URI SAS**o **entità servizio**.  

    >[!TIP]
    >Se si seleziona **entità servizio**, concedere almeno il ruolo *collaboratore dati Blob di archiviazione* . Per altre informazioni, vedere [connettore di archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties).

![Preparare il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Configurare un Azure-SSIS IR con il runtime di integrazione self-hosted come proxy

Dopo aver preparato il runtime di integrazione self-hosted e il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea, è ora possibile configurare la Azure-SSIS IR nuova o esistente con il runtime di integrazione self-hosted come proxy nel portale data factory o nell'app. Prima di procedere, tuttavia, se il Azure-SSIS IR esistente è già in esecuzione, arrestarlo e riavviarlo.

1. Nel riquadro **installazione di Integration Runtime** , ignorare le sezioni **Impostazioni generali** e **Impostazioni SQL** selezionando **Avanti**. 

1. Nella sezione **Impostazioni avanzate** eseguire le operazioni seguenti:

   1. Selezionare la casella di controllo **configura Integration Runtime self-hosted come proxy per il Azure-SSIS Integration Runtime** . 

   1. Nell'elenco a discesa **Integration Runtime self-hosted** selezionare il runtime di integrazione self-hosted esistente come proxy per il Azure-SSIS IR.

   1. Nell'elenco a discesa **servizio collegato archiviazione di staging** selezionare il servizio collegato di archiviazione BLOB di Azure esistente o crearne uno nuovo per la gestione temporanea.

   1. Nella casella **percorso di gestione temporanea** specificare un contenitore BLOB nell'account di archiviazione BLOB di Azure selezionato o lasciarlo vuoto per utilizzarne uno predefinito per la gestione temporanea.

   1. Selezionare **Continua**.

   ![Impostazioni avanzate con un runtime di integrazione self-hosted](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

È anche possibile configurare la Azure-SSIS IR nuova o esistente con il runtime di integrazione self-hosted come proxy usando PowerShell.

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

Utilizzando la versione più recente di SSDT con estensione progetti SSIS per Visual Studio o un programma di installazione autonomo, è possibile trovare una nuova proprietà `ConnectByProxy` aggiunta nelle gestioni connessioni file flat o OLEDB.
* [Scaricare l'estensione SSDT con i progetti SSIS per Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Scaricare il programma di installazione autonomo](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Quando si progettano nuovi pacchetti che contengono attività flusso di dati con OLEDB o origini file flat che consentono di accedere a database o file in locale, è possibile abilitare questa proprietà impostandola su *true* nel riquadro **Proprietà** delle gestioni connessioni pertinenti.

![Abilita proprietà ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

È anche possibile abilitare questa proprietà quando si eseguono i pacchetti esistenti senza doverli modificare manualmente uno alla volta.  Sono disponibili due opzioni:
- **Opzione a**: aprire, ricompilare e ridistribuire il progetto contenente i pacchetti con la versione più recente di SSDT da eseguire nel Azure-SSIS IR. È quindi possibile abilitare la proprietà impostandola su *true* per le gestioni connessioni pertinenti. Quando eseguono pacchetti da SSMS, queste gestioni connessioni vengono visualizzate nella scheda **gestioni connessioni** della finestra popup **Esegui pacchetto** .

  ![Abilita Property2 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  È inoltre possibile abilitare la proprietà impostandola su *true* per le gestioni connessioni pertinenti visualizzate nella scheda **gestioni connessioni** dell' [attività Esegui pacchetto SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando eseguono pacchetti in data factory pipeline.
  
  ![Abilita property3 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opzione B:** Ridistribuire il progetto contenente i pacchetti da eseguire sul runtime di integrazione SSIS. È quindi possibile abilitare la proprietà specificando il percorso della proprietà, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`e impostando il valore su *true* come override di proprietà nella scheda **Avanzate** della finestra popup **Esegui pacchetto** quando si eseguono pacchetti da SSMS.

  ![Abilita Property4 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  È anche possibile abilitare la proprietà specificando il percorso della proprietà, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`e impostando il valore su *true* come override della proprietà nella scheda **override proprietà** dell' [attività Esegui pacchetto SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando si eseguono pacchetti in pipeline Data Factory.
  
  ![Abilita property5 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Eseguire il debug della prima e della seconda attività di gestione temporanea

Nel runtime di integrazione self-hosted è possibile trovare i log di runtime nella cartella *C:\ProgramData\SSISTelemetry* e i log di esecuzione delle prime attività di staging nella cartella *C:\ProgramData\SSISTelemetry\ExecutionLog* .  È possibile trovare i log di esecuzione delle seconde attività di staging nei percorsi di registrazione SSISDB o specificati, a seconda che i pacchetti vengano archiviati in SSISDB o file system, condivisioni file o File di Azure. È anche possibile trovare gli ID univoci delle prime attività di staging nei log di esecuzione delle seconde attività di gestione temporanea. 

![ID univoco della prima attività di staging](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Usa autenticazione di Windows nelle attività di gestione temporanea

Se le attività di gestione temporanea sul runtime di integrazione self-hosted richiedono l'autenticazione di Windows, [configurare i pacchetti SSIS in modo da usare la stessa autenticazione di Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Le attività di gestione temporanea verranno richiamate con l'account del servizio IR self-hosted (*NT SERVICE\DIAHostService*, per impostazione predefinita) e gli archivi dati saranno accessibili con l'account di autenticazione di Windows. Per entrambi gli account è necessario assegnare determinati criteri di sicurezza. Nel computer IR indipendente, passare a **criteri di sicurezza locali** > **criteri locali** > **assegnazione diritti utente**e quindi eseguire le operazioni seguenti:

1. Assegnare i criteri di *regolazione delle quote di memoria per un processo* e sostituire i criteri del *token a livello di processo* con l'account del servizio IR self-hosted. Questa operazione deve essere eseguita automaticamente quando si installa il runtime di integrazione self-hosted con l'account del servizio predefinito. Se si utilizza un account del servizio diverso, assegnare gli stessi criteri.

1. Assegnare i criteri *Accedi come servizio* all'account di autenticazione di Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Fatturazione per la prima e la seconda attività di gestione temporanea

Le prime attività di staging eseguite nel runtime di integrazione self-hosted vengono fatturate separatamente, così come vengono fatturate le attività di spostamento dei dati eseguite in un runtime di integrazione self-hosted. Questo è specificato nell'articolo [sui prezzi della pipeline di dati Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

La seconda attività di gestione temporanea eseguita nel Azure-SSIS IR non viene fatturata separatamente, ma la Azure-SSIS IR in esecuzione viene fatturata come specificato nell'articolo [sui prezzi di Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Limitazioni correnti

- Attualmente sono supportate solo le attività flusso di dati con Open Database Connectivity (ODBC), OLEDB o le gestioni connessioni file flat e ODBC, OLEDB e origini file flat o la destinazione OLEDB. 
- Attualmente sono supportati solo i servizi collegati di archiviazione BLOB di Azure configurati con la *chiave dell'account*, *l'URI della firma di accesso condiviso*o l'autenticazione dell' *entità servizio* .

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il runtime di integrazione self-hosted come proxy per la Azure-SSIS IR, è possibile distribuire ed eseguire i pacchetti per accedere ai dati in locale come eseguire le attività del pacchetto SSIS in Data Factory pipeline. Per informazioni, vedere [esecuzione di pacchetti SSIS come attività Esegui pacchetto SSIS in pipeline Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

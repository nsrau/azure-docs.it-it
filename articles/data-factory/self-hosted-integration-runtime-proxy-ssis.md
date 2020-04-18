---
title: Configurare un runtime di integrazione self-hosted come proxy per SSISConfigure a self-hosted integration runtime as a proxy for SSIS
description: Informazioni su come configurare un runtime di integrazione self-hosted come proxy per un runtime di integrazione Azure-SSIS.Learn how to configure a self-hosted integration runtime as a proxy for an Azure-SSIS Integration Runtime.
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
ms.date: 04/15/2020
ms.openlocfilehash: 4cb5b84f3889dcf4e0f28d525afb42cfeac5b54c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605502"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Configurare un componente di ri-/o self-hosted come proxy per un componente di gestione Azure-SSIS in Azure Data FactoryConfigure a self-hosted IR as a proxy for an Azure-SSIS IR in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive come eseguire pacchetti di SQL Server Integration Services (SSIS) in un runtime di integrazione Azure-SSIS (Azure-SSIS IR) in Azure Data Factory con un runtime di integrazione self-hosted (iR self-hosted) configurato come proxy. 

Con questa funzionalità è possibile accedere ai dati in locale senza dover [aggiungere il componente di integrazione Azure-SSIS a una rete virtuale.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) La funzionalità è utile quando la rete aziendale ha una configurazione troppo complessa o un criterio troppo restrittivo per inserirvi il componente di ricambio Azure-SSIS.The feature is useful when your corporate network has a configuration too complex or a policy toto to you to inject your Azure-SSIS IR into it.

Questa funzionalità suddivide qualsiasi attività Flusso di dati SSIS con un'origine dati locale in due attività di gestione temporanea:This feature splits any SSIS data flow task that has an on-premises data source into two staging tasks: 
* La prima attività, che viene eseguita nel componente di integrazione self-hosted, sposta innanzitutto i dati dall'origine dati locale in un'area di gestione temporanea nell'archiviazione BLOB di Azure.The first task, which runs on your self-hosted IR, first moves data from the on-premises data source into a staging area in your Azure Blob storage.
* La seconda attività, che viene eseguita nel raggio di ir Azure-SSIS, quindi sposta i dati dall'area di gestione temporanea nella destinazione dati desiderata.

Altri vantaggi e funzionalità di questa funzionalità consentono, ad esempio, di configurare il flusso di accesso alternativo in aree non ancora supportate da un catalogo mentiutenti di Azure-SSIS e di consentire l'indirizzo IP statico pubblico del componente di accesso autonomo nel client di archiviazione.

## <a name="prepare-the-self-hosted-ir"></a>Preparare il sistema di gestione delle applicazioni autonomo

Per usare questa funzionalità, è innanzitutto necessario creare una data factory e configurarvi un oggetto di proprietà Azure-SSIS. Se non è già stato fatto, seguire le istruzioni in Configurare un oggetto Accesso [porGore azure.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

È quindi possibile configurare il componente di accesso a tempo in-utente nella stessa data factory in cui è configurato il componente di rior di archiviazione Azure-SSIS. A tale scopo, vedere [Creare un iR indipendente.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

Infine, si scarica e si installa la versione più recente del runtime di integrazione self-hosted, nonché i driver e il runtime aggiuntivi nella macchina locale o nella macchina virtuale di Azure, come indicato di seguito:
- Scaricare e installare la versione più recente del [file IR self-hosted](https://www.microsoft.com/download/details.aspx?id=39717).
- Se si utilizzano connettori OLEDB (Object Linking and Embedding Database) nei pacchetti, scaricare e installare i driver OLEDB pertinenti nello stesso computer in cui è installato il componente di integrazione autonomo, se non è già stato fatto.  

  Se si utilizza la versione precedente del driver OLEDB per SQL Server (SQL Server Native Client [SQLNCLI]), scaricare la versione a [64 bit](https://www.microsoft.com/download/details.aspx?id=50402).  

  Se si utilizza la versione più recente del driver OLEDB per SQL Server (MSOLEDBSQL), scaricare la versione a [64 bit](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Se si utilizzano driver OLEDB per altri sistemi di database, ad esempio PostgreSQL, MySQL, Oracle e così via, è possibile scaricare le versioni a 64 bit dai propri siti Web.
- Se non è già stato fatto, [scaricare e installare la versione a 64 bit del runtime di Visual C, (VC)](https://www.microsoft.com/download/details.aspx?id=40784) nello stesso computer in cui è installato il runtime di accesso client.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Preparare il servizio collegato all'archiviazione BLOB di Azure per la gestione temporaneaPrepare the Azure Blob storage-linked service for staging

Se non è già stato fatto, creare un servizio collegato all'archiviazione BLOB di Azure nella stessa data factory in cui è configurato il componente di accesso Azure-SSIS. A tale scopo, vedere [Creare un servizio collegato a Azure data factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Assicurarsi di eseguire le operazioni seguenti:
- Per **Archivio dati**selezionare **Archiviazione BLOB**di Azure .  
- Per Connetti tramite runtime di **integrazione,** selezionare AutoResolveIntegrationRuntime (non il runtime di integrazione Azure-SSIS né il runtime di integrazione self-hosted), perché viene usato il runtime di accesso di Azure predefinito per recuperare le credenziali di accesso per l'archiviazione BLOB di Azure.For Connect via integration runtime , select **AutoResolveIntegrationRuntime** (not your Azure-SSIS IR nor nor your self-hosted IR), because we use the default Azure IR to fetch access credentials for your Azure Blob Storage.
- Per **Metodo di autenticazione**, selezionare **Chiave account**, URI server di chiamata di **accesso**condiviso o **Entità servizio**.  

    >[!TIP]
    >Se si seleziona il metodo **dell'entità servizio,** concedere all'entità servizio almeno un ruolo *Collaboratore* dati BLOB di archiviazione. Per altre informazioni, vedere [Connettore di archiviazione BLOB](connector-azure-blob-storage.md#linked-service-properties)di Azure .For more information, refer to Azure Blob storage connector .

![Preparare il servizio collegato all'archiviazione BLOB di Azure per la gestione temporaneaPrepare the Azure Blob storage-linked service for staging](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Configurare un codice a/r Azure-SSIS con il componente di accesso client autonomo come proxyConfigure an Azure-SSIS IR with your self-hosted IR as a proxy

Dopo aver preparato il servizio di archiviazione iR e il blob di Azure per la gestione temporanea, è ora possibile configurare il provider di oggetti di vendita Azure-SSIS nuovo o esistente con il provider di disponibilità automatico self-hosted come proxy nel portale o nell'app della data factory. Prima di eseguire questa operazione, tuttavia, se il componente di accesso a ir Azure-SSIS esistente è già in esecuzione, arrestarlo e riavviarlo.

1. Nel riquadro **Installazione runtime di integrazione** ignorare le sezioni **Impostazioni generali** e **Impostazioni SQL** selezionando **Avanti**. 

1. Nella sezione **Impostazioni avanzate** eseguire le operazioni seguenti:

   1. Selezionare la casella di controllo **Configura runtime di integrazione self-hosted come proxy per il runtime di integrazione Azure-SSIS.** 

   1. Nell'elenco a discesa **Self-Hosted Integration Runtime** selezionare il runtime di integrazione self-hosted esistente come proxy per il runtime di integrazione Azure-SSIS.

   1. Nell'elenco a discesa **Servizio collegato Archiviazione di gestione temporanea** selezionare il servizio collegato all'archiviazione BLOB di Azure esistente o crearne uno nuovo per la gestione temporanea.

   1. Nella casella **Percorso di gestione temporanea** specificare un contenitore BLOB nell'account di archiviazione BLOB di Azure selezionato o lasciarlo vuoto per usare uno predefinito per la gestione temporanea.

   1. Selezionare **Continua**.

   ![Impostazioni avanzate con un iR auto-ospitato](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

È anche possibile configurare il componente di accesso Azure-SSIS nuovo o esistente con il codice a mano self-hosted come proxy tramite PowerShell.You can also configure your new or existing Azure-SSIS IR with the self-hosted IR as a proxy by using PowerShell.

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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Abilitare i pacchetti SSIS per la connessione tramite proxyEnable SSIS packages to connect by proxy

Utilizzando l'estensione SSDT più recente con progetti SSIS per Visual Studio `ConnectByProxy` o un programma di installazione autonomo, è possibile trovare una nuova proprietà che è stata aggiunta in OLEDB o gestione connessione file flat.
* [Scaricare l'estensione SSDT con progetti SSIS per Visual StudioDownload the SSDT with SSIS Projects extension for Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Scaricare il programma di installazione autonomo](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Quando si progettano nuovi pacchetti contenenti attività del flusso di dati con origini OLEDB o File flat, che consentono di accedere a database o file in locale, è possibile abilitare questa proprietà impostandola su *True* nel riquadro **Proprietà** delle gestioni connessioni pertinenti.

![Abilitare la proprietà ConnectByProxyEnable ConnectByProxy property](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

È inoltre possibile abilitare questa proprietà quando si eseguono pacchetti esistenti, senza doverli modificare manualmente uno alla volta.  Sono disponibili due opzioni:
- **Opzione A**: Aprire, ricompilare e ridistribuire il progetto contenente i pacchetti con l'unità SSDT più recente per l'esecuzione nel runtime di gli sci Azure-SSIS. È quindi possibile abilitare la proprietà impostandola su *True* per le gestioni connessioni pertinenti. Quando eseguono pacchetti da SSMS, queste gestioni connessioni vengono visualizzate nella scheda **Gestioni connessioni** della finestra popup **Esegui pacchetto.**

  ![Abilitare la proprietà ConnectByProxy2Enable ConnectByProxy property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  È anche possibile abilitare la proprietà impostandola su True per le gestioni connessioni pertinenti visualizzate nella scheda **Gestioni connessioni** dell'attività Esegui pacchetto SSIS quando eseguono pacchetti nelle pipeline di Data Factory.You can also enable the property by setting it to *True* for the relevant connection managers that appear on the Connection Managers tab of [Execute SSIS Package activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) when they're running packages in Data Factory pipelines.
  
  ![Abilita proprietà ConnectByProxy3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opzione B:** Ridistribuire il progetto contenente i pacchetti da eseguire nel runtime di gestione SSIS. È quindi possibile abilitare la proprietà `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`fornendo il relativo percorso della proprietà, e impostandola su *True* come override della proprietà nella scheda **Avanzate** della finestra popup **Esegui pacchetto** quando si eseguono pacchetti da SSMS.

  ![Abilitare la proprietà ConnectByProxy4Enable ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  È inoltre possibile abilitare la proprietà `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`fornendo il relativo percorso della proprietà, e impostandola su *True* come override della proprietà nella scheda **Override proprietà** dell'attività [Esegui pacchetto SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando si eseguono pacchetti nelle pipeline di Data Factory.
  
  ![Abilita proprietà ConnectByProxy5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Eseguire il debug della prima e della seconda attività di gestione temporaneaDebug the first and second staging tasks

Nel runtime di accesso self-hosted, è possibile trovare i registri di runtime nella cartella *C:* *C:\ProgramData\SSISTelemetry\ExecutionLog*  You can find the execution logs of second staging tasks in your SSISDB or specified logging paths, depending on whether you store your packages in SSISDB or file system, file shares, or Azure Files. È inoltre possibile trovare gli ID univoci delle prime attività di gestione temporanea nei log di esecuzione delle seconde attività di gestione temporanea. 

![ID univoco della prima attività di gestione temporanea](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Usare l'autenticazione di Windows nelle attività di gestione temporaneaUse Windows authentication in staging tasks

Se le attività di gestione temporanea nel componente di gestione utilizzo automatico richiedono l'autenticazione di Windows, [configurare i pacchetti SSIS per l'utilizzo della stessa autenticazione](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)di Windows. 

Per impostazione predefinita, le attività di gestione temporanea verranno richiamate con l'account del servizio IR self-hosted (*NT SERVICE -DIAHostService*) e per impostazione predefinita si accederà agli archivi dati con l'account di autenticazione di Windows. Entrambi gli account richiedono l'assegnazione di determinati criteri di sicurezza. Nel computer a ricreazione autonomo passare a **Assegnazione** > diritti utente criteri di sicurezza**locali** > **criteri**di sicurezza locali e quindi eseguire le operazioni seguenti:

1. Assegnare i criteri *Regola quote di memoria per un processo* e Sostituire un token a livello di *processo* all'account del servizio IR self-hosted. Questa operazione dovrebbe essere eseguita automaticamente quando si installa il runtime di gestione delle applicazioni autonomo con l'account di servizio predefinito. In caso contrario, assegnarli manualmente. Se si utilizza un account di servizio diverso, assegnargli gli stessi criteri.

1. Assegnare il criterio di accesso come servizio all'account di autenticazione di Windows.Assign the *Log on as a service* policy to the Windows Authentication account.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Fatturazione per la prima e la seconda attività di gestione temporanea

Le prime attività di gestione temporanea eseguite nel runtime di irrigazione self-hosted vengono fatturate separatamente, così come vengono fatturate tutte le attività di spostamento dei dati eseguite in un runtime di gestione dei prodotti popup. Questo è specificato nell'articolo Sui prezzi della pipeline di dati di Azure Data Factory.This is specified in [the Azure Data Factory data pipeline pricing](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) article.

Le seconde attività di gestione temporanea eseguite nel runtime di accesso Azure-SSIS non vengono fatturate separatamente, ma il runtime di accesso Azure-SSIS in esecuzione viene fatturato come specificato nell'articolo sui prezzi dei runtime di [accesso Azure-SSIS.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enabling-tls-12"></a>Abilitazione di TLS 1.2

Se è necessario utilizzare la crittografia avanzata/protocollo di rete più sicuro (TLS 1.2) e disabilitare le versioni SSL/TLS meno recenti nel runtime di gestione di messaggistica istantanea self-hosted, è possibile scaricare ed eseguire lo script *main.cmd* che si trova nella cartella *CustomSetupScript/UserScenarios/TLS 1.2* del contenitore di anteprima pubblica.  Usando [Azure Storage Explorer,](https://storageexplorer.com/)è possibile connettersi al contenitore di anteprima pubblica immettendo l'URI di accesso condiviso seguente:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Limitazioni correnti

- Attualmente sono supportate solo le attività del flusso di dati con origini ODBC (Open Database Connectivity)/OLEDB/Flat File o destinazione OLEDB. 
- Solo i servizi di archiviazione BLOB di Azure configurati con *la chiave account,* l'URI della firma di *accesso condiviso*o l'autenticazione *dell'entità servizio* sono attualmente supportati.
- *ParameterMapping* nell'origine OLEDB non è ancora supportato. Come soluzione alternativa, utilizzare *il comando SQL da variabile* come *AccessMode* e utilizzare *Expression* per inserire le variabili/parametri in un comando SQL. Come illustrazione, vedere il pacchetto *ParameterMappingSample.dtsx* che è disponibile nella cartella *SelfHostedIRProxy/Limitations* del contenitore di anteprima pubblica. Usando Azure Storage Explorer, è possibile connettersi al contenitore di anteprima pubblica immettendo l'URI di accesso condiviso precedente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il runtime di integrazione self-hosted come proxy per il runtime di integrazione Azure-SSIS, è possibile distribuire ed eseguire i pacchetti per accedere ai dati in locale come attività Esegui pacchetto SSIS nelle pipeline di Data Factory.After you've configured your self-hosted IR as a proxy for your Azure-SSIS IR, you can deploy and run your packages to access data on-premises as Execute SSIS Package activities in Data Factory pipelines. Per informazioni, vedere [Eseguire pacchetti SSIS come eseguire attività di pacchetto SSIS nelle pipeline di Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

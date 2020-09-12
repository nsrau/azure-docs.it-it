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
ms.date: 09/09/2020
ms.openlocfilehash: d135320d8dd9f86fbc313b17b8b55ed3c609e9dc
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595021"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Configurare un runtime di integrazione self-hosted come proxy per un Azure-SSIS IR in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive come eseguire pacchetti SQL Server Integration Services (SSIS) in un Azure-SSIS Integration Runtime (Azure-SSIS IR) in Azure Data Factory con un runtime di integrazione self-hosted (IR self-hosted) configurato come proxy. 

Con questa funzionalità è possibile accedere ai dati in locale senza dover [aggiungere la Azure-SSIS IR a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Questa funzionalità è utile quando la rete aziendale presenta una configurazione troppo complessa o un criterio troppo restrittivo per poter inserire il Azure-SSIS IR.

Questa funzionalità suddivide l'attività flusso di dati SSIS in due attività di gestione temporanea quando applicabile: 
* **Attività di gestione temporanea locale**: questa attività esegue il componente flusso di dati che si connette a un archivio dati locale nel runtime di integrazione self-hosted. Sposta i dati dall'archivio dati locale in un'area di gestione temporanea nell'archivio BLOB di Azure o viceversa.
* **Attività di gestione temporanea cloud**: questa attività esegue il componente flusso di dati che non si connette a un archivio dati locale nella Azure-SSIS IR. Sposta i dati dall'area di gestione temporanea nell'archivio BLOB di Azure a un archivio dati cloud o viceversa.

Se l'attività flusso di dati sposta i dati da locale a cloud, la prima e la seconda attività di gestione temporanea saranno rispettivamente attività di staging locali e cloud. Se l'attività flusso di dati sposta i dati dal cloud in locale, la prima e la seconda attività di gestione temporanea saranno rispettivamente attività di gestione temporanea cloud e locali. Se l'attività flusso di dati sposta i dati da un ambiente locale a un ambiente locale, la prima e la seconda attività di gestione temporanea saranno entrambe attività di gestione temporanea locali. Se l'attività flusso di dati sposta i dati dal cloud al cloud, questa funzionalità non è applicabile.

Altri vantaggi e funzionalità di questa funzionalità consentono, ad esempio, di configurare il runtime di integrazione self-hosted in aree non ancora supportate da un Azure-SSIS IR e di consentire l'indirizzo IP statico pubblico del runtime di integrazione self-hosted nel firewall delle origini dati.

## <a name="prepare-the-self-hosted-ir"></a>Preparare il runtime di integrazione self-hosted

Per usare questa funzionalità, creare prima di tutto un data factory e configurarne uno Azure-SSIS IR. Se non è già stato fatto, seguire le istruzioni riportate in [configurare un Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Si configura quindi il runtime di integrazione Self-Hosted nello stesso data factory in cui è configurata la Azure-SSIS IR. A tale scopo, vedere [creare un runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Infine, scaricare e installare la versione più recente del runtime di integrazione self-hosted, nonché i driver e il runtime aggiuntivi, nel computer locale o nella macchina virtuale di Azure (VM), come indicato di seguito:
- Scaricare e installare la versione più recente del runtime di integrazione [self-hosted](https://www.microsoft.com/download/details.aspx?id=39717).
- Se si usano gli oggetti collegamento e incorporamento dei connettori ODBC (/Open Database Connectivity) nei pacchetti, scaricare e installare i driver pertinenti nello stesso computer in cui è installato il runtime di integrazione self-hosted, se non è già stato fatto.  

  Se si utilizza la versione precedente del driver OLEDB per SQL Server (SQL Server Native Client [SQLNCLI]), [scaricare la versione a 64 bit](https://www.microsoft.com/download/details.aspx?id=50402).  

  Se si utilizza la versione più recente del driver OLEDB per SQL Server (MSOLEDBSQL), [scaricare la versione a 64 bit](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Se si usano driver OLEDB/ODBC per altri sistemi di database, ad esempio PostgreSQL, MySQL, Oracle e così via, è possibile scaricare le versioni a 64 bit dai rispettivi siti Web.
- Se non è già stato fatto, [scaricare e installare la versione a 64 bit del runtime di Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) nello stesso computer in cui è installato il runtime di integrazione self-hosted.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Preparare il servizio collegato di archiviazione BLOB di Azure per la gestione temporanea

Se non è già stato fatto, creare un servizio collegato di archiviazione BLOB di Azure nello stesso data factory in cui è configurata la Azure-SSIS IR. A tale scopo, vedere [creare un servizio collegato di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Assicurarsi di eseguire le operazioni seguenti:
- Per **archivio dati**selezionare **archiviazione BLOB di Azure**.  
- Per **Connetti tramite Integration Runtime**, selezionare **AutoResolveIntegrationRuntime** (non il Azure-SSIS IR né il runtime di integrazione self-hosted), perché si usa il Azure IR predefinito per recuperare le credenziali di accesso per l'archiviazione BLOB di Azure.
- In **metodo di autenticazione**selezionare **chiave account**, **URI SAS**o **entità servizio**.  

    >[!TIP]
    >Se si seleziona il metodo dell' **entità servizio** , concedere all'entità servizio almeno un ruolo di *collaboratore dati BLOB di archiviazione*   . Per altre informazioni, vedere [connettore di archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties).

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

Utilizzando la versione più recente di SSDT come estensione di progetti SSIS per Visual Studio o un programma di installazione autonomo, è possibile trovare una nuova `ConnectByProxy` proprietà aggiunta nelle gestioni connessioni per i componenti del flusso di dati supportati.
* [Scaricare l'estensione di progetti SSIS per Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Scaricare il programma di installazione autonomo](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Quando si progettano nuovi pacchetti contenenti attività flusso di dati con componenti che accedono ai dati in locale, è possibile abilitare questa proprietà impostandola su *true* nel riquadro **Proprietà** delle gestioni connessioni pertinenti.

![Abilita proprietà ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

È anche possibile abilitare questa proprietà quando si eseguono i pacchetti esistenti senza doverli modificare manualmente uno alla volta.  Sono disponibili due opzioni:
- **Opzione a**: aprire, ricompilare e ridistribuire il progetto contenente i pacchetti con la versione più recente di SSDT da eseguire nel Azure-SSIS IR. È quindi possibile abilitare la proprietà impostandola su *true* per le gestioni connessioni pertinenti. Quando si eseguono pacchetti da SSMS, queste gestioni connessioni vengono visualizzate nella scheda **gestioni connessioni** della finestra popup **Esegui pacchetto** .

  ![Abilita Property2 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Per abilitare la proprietà, è inoltre possibile impostarla su *true* per le gestioni connessioni pertinenti visualizzate nella scheda **gestioni connessioni** dell' [attività Esegui pacchetto SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando si eseguono pacchetti in data factory pipeline.
  
  ![Abilita property3 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opzione B:** Ridistribuire il progetto contenente i pacchetti da eseguire sul runtime di integrazione SSIS. È quindi possibile abilitare la proprietà specificando il percorso della proprietà `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` e impostando il valore su *true* come override della proprietà nella scheda **Avanzate** della finestra popup **Esegui pacchetto** quando si eseguono pacchetti da SSMS.

  ![Abilita Property4 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  È anche possibile abilitare la proprietà specificando il percorso della proprietà `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` e impostando il valore su *true* come override della proprietà nella scheda **override proprietà** dell' [attività Esegui pacchetto SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando si eseguono pacchetti in data factory pipeline.
  
  ![Abilita property5 ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Eseguire il debug delle attività di gestione temporanea in locale e nel cloud

Nel runtime di integrazione self-hosted è possibile trovare i log di runtime nella cartella *C:\ProgramData\SSISTelemetry* e i log di esecuzione delle attività di gestione temporanea locali nella cartella *C:\ProgramData\SSISTelemetry\ExecutionLog* .  È possibile trovare i log di esecuzione delle attività di gestione temporanea nel database SSISDB o nei percorsi di registrazione specificati, a seconda che i pacchetti vengano archiviati in SSISDB o meno. È anche possibile trovare gli ID univoci delle attività di staging locali nei log di esecuzione delle attività di gestione temporanea del cloud. 

![ID univoco della prima attività di staging](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-on-premises-staging-tasks"></a>Usare l'autenticazione di Windows nelle attività di gestione temporanea locali

Se per le attività di gestione temporanea locali nel runtime di integrazione self-hosted è richiesta l'autenticazione di Windows, [configurare i pacchetti SSIS in modo da usare la stessa autenticazione di Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Le attività di gestione temporanea locali verranno richiamate con l'account del servizio IR self-hosted (*NT SERVICE\DIAHostService*, per impostazione predefinita) e l'accesso agli archivi dati verrà eseguito con l'account di autenticazione di Windows. Per entrambi gli account è necessario assegnare determinati criteri di sicurezza. Nel computer IR indipendente, passare a **criteri di sicurezza**  >  **locali**  >  **assegnazione diritti utente**e quindi eseguire le operazioni seguenti:

1. Assegnare i criteri di *regolazione delle quote di memoria per un processo* e sostituire i criteri del *token a livello di processo* con l'account del servizio IR self-hosted. Questa operazione deve essere eseguita automaticamente quando si installa il runtime di integrazione self-hosted con l'account del servizio predefinito. In caso contrario, assegnare questi criteri manualmente. Se si utilizza un account del servizio diverso, assegnare gli stessi criteri.

1. Assegnare i criteri *Accedi come servizio* all'account di autenticazione di Windows.

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>Fatturazione per le attività locali e di gestione temporanea cloud

Le attività di gestione temporanea locali eseguite nel runtime di integrazione self-hosted vengono fatturate separatamente, così come vengono fatturate tutte le attività di spostamento dei dati eseguite in un runtime di integrazione self-hosted. Questo è specificato nell'articolo [sui prezzi della pipeline di dati Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Le attività di staging cloud eseguite nel Azure-SSIS IR non vengono fatturate separatamente, ma la Azure-SSIS IR in esecuzione viene fatturata come specificato nell'articolo relativo ai [prezzi di Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="enabling-tls-12"></a>Abilitazione di TLS 1.2

Se è necessario usare il protocollo di rete Secure Cryptography/più sicuro (TLS 1,2) e disabilitare le versioni precedenti di SSL/TLS nel runtime di integrazione self-hosted, è possibile scaricare ed eseguire lo script *Main. cmd* disponibile nella cartella *CustomSetupScript/UserScenarios/TLS 1,2* del contenitore di anteprima pubblica.  Con [Azure Storage Explorer](https://storageexplorer.com/)è possibile connettersi al contenitore di anteprima pubblica immettendo il seguente URI SAS:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Limitazioni correnti

- Attualmente sono supportate solo le attività flusso di dati con OLE DB/ODBC/origini file flat o destinazione OLEDB.
- Attualmente sono supportati solo i servizi collegati di archiviazione BLOB di Azure configurati con la *chiave dell'account*, *l'URI della firma di accesso condiviso*o l'autenticazione dell' *entità servizio* .
- *ParameterMapping* nell'origine OLEDB non è attualmente supportato. Come soluzione alternativa, usare il *comando SQL dalla variabile* come *AccessMode* e usare *Expression* per inserire le variabili o i parametri in un comando SQL. Per un'illustrazione, vedere il pacchetto *ParameterMappingSample. dtsx* disponibile nella cartella *SelfHostedIRProxy/limitazioni* del contenitore di anteprima pubblica. Con Azure Storage Explorer è possibile connettersi al contenitore di anteprima pubblica immettendo l'URI SAS precedente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il runtime di integrazione self-hosted come proxy per la Azure-SSIS IR, è possibile distribuire ed eseguire i pacchetti per accedere ai dati in locale come eseguire le attività del pacchetto SSIS in Data Factory pipeline. Per informazioni, vedere [esecuzione di pacchetti SSIS come attività Esegui pacchetto SSIS in pipeline Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

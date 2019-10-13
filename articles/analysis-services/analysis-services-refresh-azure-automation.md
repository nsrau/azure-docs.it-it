---
title: Aggiornare i modelli di Azure Analysis Services con automazione di Azure | Microsoft Docs
description: Informazioni su come codificare gli aggiornamenti del modello usando automazione di Azure.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: ed1634ef1009149dc2937174b20248eab9cd335f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294786"
---
# <a name="refresh-with-azure-automation"></a>Eseguire l'aggiornamento con Automazione di Azure

Usando automazione di Azure e manuali operativi di PowerShell, è possibile eseguire operazioni automatiche di aggiornamento dei dati nei modelli tabulari di Azure Analysis.  

L'esempio in questo articolo usa i [moduli di PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Un esempio di Runbook di PowerShell, che illustra l'aggiornamento di un modello, è disponibile più avanti in questo articolo.  

## <a name="authentication"></a>Authentication

Tutte le chiamate devono essere autenticate con un token di Azure Active Directory (OAuth 2) valido.  Nell'esempio riportato in questo articolo verrà usata un'entità servizio (SPN) per l'autenticazione Azure Analysis Services.

Per altre informazioni sulla creazione di un'entità servizio, vedere [creare un'entità servizio usando portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Nell'esempio seguente si presuppone che Azure Analysis Services firewall sia disabilitato. Se il firewall è abilitato, l'indirizzo IP pubblico dell'iniziatore della richiesta deve essere inserito nell'elenco elementi consentiti nel firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installare i moduli SqlServer da PowerShell Gallery.

1. Nell'account di automazione di Azure fare clic su **moduli**, quindi su **Esplora raccolta**.

2. Nella barra di ricerca cercare **SqlServer**.

    ![Cerca moduli](./media/analysis-services-refresh-azure-automation/1.png)

3. Selezionare SqlServer, quindi fare clic su **Importa**.
 
    ![Importa modulo](./media/analysis-services-refresh-azure-automation/2.png)

4. Fare clic su **OK**.
 
### <a name="create-a-service-principal-spn"></a>Creare un'entità servizio (SPN)

Per informazioni sulla creazione di un'entità servizio, vedere [creare un'entità servizio usando portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configurare le autorizzazioni in Azure Analysis Services
 
L'entità servizio creata deve avere le autorizzazioni di amministratore del server nel server. Per altre informazioni, vedere [Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Progettare il Runbook di automazione di Azure

1. Nell'account di automazione creare una risorsa **credenziali** che verrà usata per archiviare in modo sicuro l'entità servizio.

    ![Creazione di credenziali](./media/analysis-services-refresh-azure-automation/6.png)

2. Immettere i dettagli per la credenziale.  Per **nome utente**, immettere il nome dell'entità servizio ( **SPN**) per la **password**, immettere il **segreto SPN**.

    ![Creazione di credenziali](./media/analysis-services-refresh-azure-automation/7.png)

3. Importare il Runbook di automazione

    ![Importare Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Individuare il file **Refresh-Model. ps1** , specificare un **nome** e una **Descrizione**, quindi fare clic su **Crea**.

    ![Importare Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Quando è stato creato il Runbook, questo passa automaticamente alla modalità di modifica.  Selezionare **Pubblica**.

    ![Pubblica runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > La risorsa di credenziali creata in precedenza viene recuperata da Runbook usando il comando **Get-AutomationPSCredential** .  Questo comando viene quindi passato al comando PowerShell **Invoke-ProcessASADatabase** per eseguire l'autenticazione Azure Analysis Services.

6. Testare il Runbook facendo clic su **Avvia**.

    ![Avviare il Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Compilare i parametri **DatabaseName**, **ANALYSISSERVER**e **REFRESHTYPE** e quindi fare clic su **OK**. Il parametro **WEBHOOKDATA** non è obbligatorio quando il Runbook viene eseguito manualmente.

    ![Avviare il Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Se il Runbook viene eseguito correttamente, si riceverà un output simile al seguente:

![Esecuzione riuscita](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Usare un Runbook di automazione di Azure indipendente

Runbook può essere configurato in modo da attivare l'aggiornamento del modello di Azure Analysis Services in base a una pianificazione.

Questa configurazione può essere configurata nel modo seguente:

1. Nel runbook di automazione fare clic su **pianificazioni**, quindi **aggiungere una pianificazione**.
 
    ![Crea pianificazione](./media/analysis-services-refresh-azure-automation/14.png)

2. Fare clic su **pianifica** >  per**creare una nuova pianificazione**e quindi specificare i dettagli.

    ![Configura pianificazione](./media/analysis-services-refresh-azure-automation/15.png)

3. Fare clic su **Create**(Crea).

4. Immettere i parametri per la pianificazione. Questi verranno usati ogni volta che il Runbook viene attivato. Il parametro **WEBHOOKDATA** deve essere lasciato vuoto durante l'esecuzione tramite una pianificazione.

    ![Configurare i parametri](./media/analysis-services-refresh-azure-automation/16.png)

5. Fare clic su **OK**.

## <a name="consume-with-data-factory"></a>Utilizzare con Data Factory

Per utilizzare il Runbook usando Azure Data Factory, creare prima un **webhook** per il Runbook. Il **webhook** fornirà un URL che può essere chiamato tramite un Azure Data Factory attività Web.

> [!IMPORTANT]
> Per creare un **webhook**, è necessario **pubblicare**lo stato del Runbook.

1. Nel runbook di automazione fare clic su **webhook**, quindi fare clic su **Aggiungi webhook**.

   ![Aggiungi webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Assegnare al webhook un nome e una scadenza.  Il nome identifica solo il webhook all'interno del Runbook di automazione, ma non fa parte dell'URL.

   >[!CAUTION]
   >Assicurarsi di copiare l'URL prima di chiudere la procedura guidata, perché non è possibile ottenerlo di nuovo una volta chiuso.
    
   ![Configurare il webhook](./media/analysis-services-refresh-azure-automation/18.png)

    I parametri per il webhook possono rimanere vuoti.  Quando si configura l'attività Web di Azure Data Factory, è possibile passare i parametri nel corpo della chiamata Web.

3. In Data Factory configurare un' **attività Web**

### <a name="example"></a>Esempio

   ![Attività Web di esempio](./media/analysis-services-refresh-azure-automation/19.png)

L' **URL** è l'URL creato dal webhook.

Il **corpo** è un documento JSON che deve contenere le proprietà seguenti:


|Proprietà  |Value  |
|---------|---------|
|**AnalysisServicesDatabase**     |Nome del database di Azure Analysis Services <br/> Esempio: AdventureWorksDB         |
|**AnalysisServicesServer**     |Nome del server Azure Analysis Services. <br/> Esempio: https: \//westus. asazure. Windows. NET/Servers/MyServer/Models/AdventureWorks/         |
|**DatabaseRefreshType**     |Tipo di aggiornamento da eseguire. <br/> Esempio: Completa         |

Corpo JSON di esempio:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Questi parametri sono definiti nello script di PowerShell Runbook.  Quando viene eseguita l'attività Web, il payload JSON passato è WEBHOOKDATA.

Questa operazione viene deserializzata e archiviata come parametri PowerShell, che vengono quindi usati dal comando di PowerShell Invoke-ProcesASDatabase.

![Webhook deserializzato](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Usare un ruolo di lavoro ibrido con Azure Analysis Services

Una macchina virtuale di Azure con un indirizzo IP pubblico statico può essere usata come ruolo di lavoro ibrido per runbook di Automazione di Azure.  Questo indirizzo IP pubblico può quindi essere aggiunto al firewall Azure Analysis Services.

> [!IMPORTANT]
> Verificare che l'indirizzo IP pubblico della macchina virtuale sia configurato come statico.
>
>Per altre informazioni sulla configurazione di ruoli di lavoro ibridi di automazione di Azure, vedere [automatizzare le risorse nel Data Center o nel cloud usando il ruolo di lavoro ibrido per Runbook](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Una volta configurato un ruolo di lavoro ibrido, creare un webhook, come descritto nella sezione [utilizzare con data factory](#consume-with-data-factory).  L'unica differenza consiste nel selezionare l'opzione **Esegui in**un ruolo di**lavoro ibrido**  >  durante la configurazione del webhook.

Webhook di esempio con ruolo di lavoro ibrido:

![Webhook di esempio di lavoro ibrido](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Runbook di PowerShell di esempio

Il frammento di codice seguente è un esempio di come eseguire l'aggiornamento del modello di Azure Analysis Services usando un Runbook di PowerShell.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Passaggi successivi

[Esempi](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)

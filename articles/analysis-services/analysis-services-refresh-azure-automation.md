---
title: Aggiornare i modelli di Azure Analysis Services con Automazione di Azure Documenti Microsoft
description: Questo articolo descrive come codificare gli aggiornamenti del modello per Azure Analysis Services usando Automazione di Azure.This article describes how to code model refreshes for Azure Analysis Services by using Azure Automation.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572360"
---
# <a name="refresh-with-azure-automation"></a>Eseguire l'aggiornamento con Automazione di Azure

Usando Automazione di Azure e runbook di PowerShell, è possibile eseguire operazioni di aggiornamento automatico dei dati nei modelli tabulari di Azure Analysis.By using Azure Automation and PowerShell Runbooks, you can perform automated data refresh operations on your Azure Analysis tabular models.  

Nell'esempio riportato in questo articolo vengono utilizzati i [moduli SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)di PowerShell .

Un Runbook di PowerShell di esempio, che illustra l'aggiornamento di un modello viene fornito più avanti in questo articolo.  

## <a name="authentication"></a>Authentication

Tutte le chiamate devono essere autenticate con un token Azure Active Directory (OAuth 2) valido.  L'esempio in questo articolo utilizzerà un'entità servizio (SPN) per l'autenticazione in Azure Analysis Services.The example in this article will use a Service Principal (SPN) to authenticate to Azure Analysis Services.

Per altre informazioni sulla creazione di un'entità servizio, vedere [Creare un'entità servizio tramite](../active-directory/develop/howto-create-service-principal-portal.md)il portale di Azure.To learn more about creating a Service Principal, see Create a service principal by using Azure portal.

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Nell'esempio seguente si presuppone che il firewall di Azure Analysis Services sia disabilitato. Se il firewall è abilitato, l'indirizzo IP pubblico dell'initiator della richiesta dovrà essere inserito nella whitelist nel firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installare i moduli SqlServer dalla raccolta di PowerShell.Install SqlServer modules from PowerShell gallery.

1. Nell'account di automazione di Azure fare clic su **Moduli**, quindi **su Sfoglia raccolta**.

2. Nella barra di ricerca cercare **SqlServer**.

    ![Moduli di ricerca](./media/analysis-services-refresh-azure-automation/1.png)

3. Selezionare SqlServer, quindi fare clic su **Importa**.
 
    ![Modulo di importazione](./media/analysis-services-refresh-azure-automation/2.png)

4. Fare clic su **OK**.
 
### <a name="create-a-service-principal-spn"></a>Creare un'entità servizio (SPN)Create a Service Principal (SPN)

Per altre informazioni sulla creazione di un'entità servizio, vedere [Creare un'entità servizio tramite](../active-directory/develop/howto-create-service-principal-portal.md)il portale di Azure.To learn about creating a Service Principal, see Create a service principal by using Azure portal.

### <a name="configure-permissions-in-azure-analysis-services"></a>Configurare le autorizzazioni in Azure Analysis ServicesConfigure permissions in Azure Analysis Services
 
L'entità servizio creata deve disporre delle autorizzazioni di amministratore del server sul server. Per altre informazioni, vedere [Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Progettare il runbook di automazione di AzureDesign the Azure Automation Runbook

1. Nell'account di automazione creare una risorsa **Credenziali** che verrà utilizzata per archiviare in modo sicuro l'entità servizio.

    ![Creare credenziali](./media/analysis-services-refresh-azure-automation/6.png)

2. Immettere i dettagli per la credenziale.  Per **Nome utente**immettere l'SPN **ClientId**, per La **Password**, immettere il **segreto SPN**.

    ![Creare credenziali](./media/analysis-services-refresh-azure-automation/7.png)

3. Importare il Runbook di automazione

    ![Importare Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Individuare il file **Refresh-Model.ps1,** specificare un **Nome** e una **descrizione**, quindi fare clic su **Crea**.

    ![Importare Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Una volta creato, runbook passerà automaticamente alla modalità di modifica.  Selezionare **Pubblica**.

    ![Pubblica runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > La risorsa credenziali creata in precedenza viene recuperata dal runbook tramite il comando **Get-AutomationPSCredential.**  Questo comando viene quindi passato al comando **PowerShell Invoke-ProcessASADatabase** per eseguire l'autenticazione in Azure Analysis Services.This command is then passed to the Invoke-ProcessASADatabase PowerShell command to perform the authentication to Azure Analysis Services.

6. Testare il runbook facendo clic su **Start**.

    ![Avviare il Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Compilare i parametri **DATABASENAME**, **ANALYSISSERVER**e **REFRESHTYPE** , quindi scegliere **OK**. Il parametro **WEBHOOKDATA** non è necessario quando il Runbook viene eseguito manualmente.

    ![Avviare il Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Se il Runbook è stato eseguito correttamente, si riceverà un output simile al seguente:

![Esecuzione riuscita](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Usare un Runbook di automazione di Azure autonomoUse a self-contained Azure Automation Runbook

Il Runbook può essere configurato per attivare l'aggiornamento del modello di Azure Analysis Services in base a una pianificazione.

Questo può essere configurato come segue:

1. Nel Runbook di automazione fare clic su **Pianificazioni**, quindi **su Aggiungi pianificazione**.
 
    ![Crea pianificazione](./media/analysis-services-refresh-azure-automation/14.png)

2. Fare clic su **Pianificazione** > **Creare una nuova pianificazione**e quindi immettere i dettagli.

    ![Configurare la pianificazione](./media/analysis-services-refresh-azure-automation/15.png)

3. Fare clic su **Crea**.

4. Compilare i parametri per l'abaco. Questi verranno utilizzati ogni volta che il Runbook si attiva. Il parametro **WEBHOOKDATA** deve essere lasciato vuoto quando viene eseguito tramite una pianificazione.

    ![Configurare i parametri](./media/analysis-services-refresh-azure-automation/16.png)

5. Fare clic su **OK**.

## <a name="consume-with-data-factory"></a>Consumare con Data FactoryConsume with Data Factory

Per usare il runbook usando Azure Data Factory, creare innanzitutto un **Webhook** per il runbook. Il Webhook fornirà un URL che può essere chiamato tramite un'attività Web di Azure Data Factory.The **Webhook** will provide a URL which can be called via an Azure Data Factory web activity.

> [!IMPORTANT]
> Per creare un **Webhook**, è necessario che lo stato del Runbook sia **Published**.

1. Nel Runbook di automazione fare clic su **Webhook**, quindi su **Aggiungi Webhook**.

   ![Aggiungi Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Assegnare al Webhook un nome e una scadenza.  Il nome identifica solo il Webhook all'interno del Runbook di automazione, non fa parte dell'URL.

   >[!CAUTION]
   >Assicurarsi di copiare l'URL prima di chiudere la procedura guidata in quanto non è possibile riaverlo una volta chiuso.
    
   ![Configurare Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    I parametri per il webhook possono rimanere vuoti.  Quando si configura l'attività Web di Azure Data Factory, i parametri possono essere passati nel corpo della chiamata Web.

3. In Data Factory configurare **un'attività Web**

### <a name="example"></a>Esempio

   ![Esempio di attività Web](./media/analysis-services-refresh-azure-automation/19.png)

**L'URL** è l'URL creato dal Webhook.

Il corpo è un documento JSON che deve contenere le proprietà seguenti:The **body** is a JSON document which should contain the following properties:


|Proprietà  |valore  |
|---------|---------|
|**Database di AnalysisServices**     |Nome del database di Azure Analysis Services <br/> Esempio: AdventureWorksDBExample: AdventureWorksDB         |
|**ServerAnalysisServicesServer (ServerAnalysisServices)**     |Nome del server di Azure Analysis Services. <br/> Esempio: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Tipo di aggiornamento da eseguire. <br/> Esempio: Completo         |

Corpo JSON di esempio:Example JSON body:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Questi parametri sono definiti nello script di PowerShell del runbook.  Quando viene eseguita l'attività web, il payload JSON passato è WEBHOOKDATA.

Viene deserializzato e archiviato come parametri di PowerShell, che vengono quindi utilizzati dal comando di PowerShell Invoke-ProcesASDatabase.This is deserialized and stored as PowerShell parameters, which are then used by the Invoke-ProcesASDatabase PowerShell command.

![Webhook deserializzato](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Usare un ruolo di lavoro ibrido con Azure Analysis ServicesUse a Hybrid Worker with Azure Analysis Services

An Azure Virtual Machine with a static public IP address can be used as an Azure Automation Hybrid Worker.  Questo indirizzo IP pubblico può quindi essere aggiunto al firewall di Azure Analysis Services.This public IP address can then be added to the Azure Analysis Services firewall.

> [!IMPORTANT]
> Verificare che l'indirizzo IP pubblico della macchina virtuale sia configurato come statico.
>
>Per altre informazioni sulla configurazione di ruoli di lavoro ibridi di automazione di Azure, vedere Automatizzare le risorse nel data center o nel [cloud usando Hybrid Runbook Worker.](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)

Dopo aver configurato un ruolo di lavoro ibrido, creare un webhook come descritto nella sezione [Consume with Data Factory](#consume-with-data-factory).  L'unica differenza consiste nel selezionare l'opzione **Esegui su** > **lavoro ibrido** durante la configurazione del Webhook.

Webhook di esempio con Hybrid Worker:Example webhook using Hybrid Worker:

![Esempio di webhook di lavoro ibrido](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Runbook di PowerShell di esempio

The following code snippet is an example of how to perform the Azure Analysis Services model refresh using a PowerShell Runbook.

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
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)

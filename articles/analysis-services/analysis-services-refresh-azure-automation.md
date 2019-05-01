---
title: Aggiornare i modelli di Azure Analysis Services con automazione di Azure | Microsoft Docs
description: Informazioni su come gli aggiornamenti del modello di codice usando automazione di Azure.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 1897193f0ae781029a7303c42ca8eeaa51389892
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920583"
---
# <a name="refresh-with-azure-automation"></a>Aggiornamento con automazione di Azure

Usando automazione di Azure e PowerShell Runbooks, è possibile eseguire operazioni di aggiornamento dati automatico nei modelli tabulari di analisi di Azure.  

L'esempio in questo articolo usa il [moduli di PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Più avanti in questo articolo viene fornito un esempio di Runbook di PowerShell, che illustra l'aggiornamento di un modello.  

## <a name="authentication"></a>Authentication

Tutte le chiamate devono essere autenticate con un token di Azure Active Directory (OAuth 2) valido.  Nell'esempio riportato in questo articolo utilizzerà dell'entità servizio (SPN) per l'autenticazione ad Azure Analysis Services.

Per altre informazioni sulla creazione di un'entità servizio, vedere]

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Nell'esempio seguente si presuppone che il firewall di Azure Analysis Services è stato disabilitato. Se il firewall è abilitato, sarà necessario l'indirizzo IP pubblico dell'iniziatore richiesta siano consentiti nel firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installare SQL Server moduli da PowerShell gallery.

1. Nell'Account di automazione di Azure, fare clic su **moduli**, quindi **Esplora raccolta**.

2. Nella barra di ricerca, cercare **SqlServer**.

    ![Cerca moduli](./media/analysis-services-refresh-azure-automation/1.png)

3. Selezionare SQL Server, quindi fare clic su **importazione**.
 
    ![Importa modulo](./media/analysis-services-refresh-azure-automation/2.png)

4. Fare clic su **OK**.
 
### <a name="create-a-service-principal-spn"></a>Creare un'entità servizio (SPN)

Per altre informazioni sulla creazione di un'entità servizio, vedere [creare un'entità servizio usando il portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configurare le autorizzazioni in Azure Analysis Services
 
L'entità servizio si crea deve disporre delle autorizzazioni di amministratore di server nel server. Per altre informazioni, vedere [Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Progettare il Runbook di automazione di Azure

1. Nell'Account di automazione, creare un **credenziali** risorsa che verrà usato per archiviare in modo sicuro l'entità servizio.

    ![creare le credenziali](./media/analysis-services-refresh-azure-automation/6.png)

2. Immettere i dettagli per le credenziali.  Per il **nome utente**, immettere il **SPN ClientId**, per il **Password**, immettere il **SPN segreto**.

    ![creare le credenziali](./media/analysis-services-refresh-azure-automation/7.png)

3. Importare il Runbook di automazione

    ![Importare Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Cerca il **aggiornamento Model.ps1** del file, fornire un **nome** e **descrizione**e quindi fare clic su **Create**.

    ![Importare Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Dopo aver creato il Runbook, passerà automaticamente alla modalità di modifica.  Selezionare **Pubblica**.

    ![Pubblica runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > La credenziale risorsa che è stata creata in precedenza viene recuperata dal runbook usando il **Get-AutomationPSCredential** comando.  Questo comando viene quindi passato per il **Invoke-ProcessASADatabase** comando di PowerShell per eseguire l'autenticazione ad Azure Analysis Services.

6. Testare il runbook facendo clic **avviare**.

    ![Avviare il Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Compilare il **NOMEDATABASE**, **ANALYSISSERVER**, e **REFRESHTYPE** parametri e quindi fare clic su **OK**. Il **WEBHOOKDATA** parametro non è obbligatorio quando il Runbook viene eseguito manualmente.

    ![Avviare il Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Se il Runbook è stata eseguita correttamente, verrà visualizzato un output simile al seguente:

![Esecuzione riuscita](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Usare un Runbook di automazione di Azure autonomo

Il Runbook può essere configurato per attivare il modello di Azure Analysis Services Aggiorna in base a una pianificazione.

Ciò può essere configurato come segue:

1. Il runbook di automazione, fare clic su **pianificazioni**, quindi **Aggiungi pianificazione**.
 
    ![Creare pianificazione](./media/analysis-services-refresh-azure-automation/14.png)

2. Fare clic su **pianificazione** > **creare una nuova pianificazione**e quindi immettere i dettagli.

    ![Configura pianificazione](./media/analysis-services-refresh-azure-automation/15.png)

3. Fare clic su **Create**(Crea).

4. Specificare i parametri per la pianificazione. Questi verranno usati ogni volta che attiva il Runbook. Il **WEBHOOKDATA** parametro deve essere lasciato vuoto quando si esegue tramite una pianificazione.

    ![Configurare i parametri](./media/analysis-services-refresh-azure-automation/16.png)

5. Fare clic su **OK**.

## <a name="consume-with-data-factory"></a>Utilizzo con Data Factory

Per usare i runbook usando Azure Data Factory, creare prima di tutto una **Webhook** per il runbook. Il **Webhook** fornirà un URL che può essere chiamato tramite un'attività web di Azure Data Factory.

> [!IMPORTANT]
> Per creare un **Webhook**, lo stato del Runbook deve essere **Published**.

1. Nel Runbook di automazione, fare clic su **Webhooks**, quindi fare clic su **Aggiungi Webhook**.

   ![Aggiungi Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Assegnare al Webhook un nome e una scadenza.  Il nome identifica solo il Webhook all'interno del Runbook di automazione, non fanno parte dell'URL.

   >[!CAUTION]
   >Assicurarsi di che copiare l'URL prima di chiudere la procedura guidata, come è possibile ottenerlo di nuovo una volta chiusa.
    
   ![Configurare Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    I parametri del webhook possono rimanere vuoti.  Quando si configura l'attività web di Azure Data Factory, i parametri possono essere passati nel corpo della chiamata web.

3. In Data Factory, configurare un **attività web**

### <a name="example"></a>Esempio

   ![Attività Web di esempio](./media/analysis-services-refresh-azure-automation/19.png)

Il **URL** è l'URL creato dal Webhook.

Il **corpo** è un documento JSON che deve contenere le proprietà seguenti:


|Proprietà  |Value  |
|---------|---------|
|**AnalysisServicesDatabase**     |Il nome del database di Azure Analysis Services <br/> Esempio: AdventureWorksDB         |
|**AnalysisServicesServer**     |Il nome del server Azure Analysis Services. <br/> Esempio: https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Il tipo di aggiornamento per l'esecuzione. <br/> Esempio: Completa         |

Corpo JSON di esempio:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Questi parametri sono definiti nel runbook script di PowerShell.  Quando viene eseguita l'attività web, il payload JSON passato è WEBHOOKDATA.

Questo viene deserializzato e archiviato come parametri di PowerShell, che vengono quindi utilizzati dal comando Invoke-ProcesASDatabase PowerShell.

![Webhook deserializzati](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Usare un ruolo di lavoro ibrido con Azure Analysis Services

Una macchina virtuale di Azure con un indirizzo IP pubblico statico è utilizzabile come un ruolo di lavoro ibrido automazione di Azure.  Questo indirizzo IP pubblico può quindi essere aggiunto al firewall di Azure Analysis Services.

> [!IMPORTANT]
> Verificare che l'indirizzo IP pubblico di macchina virtuale sia configurato come statico.
>
>Per altre informazioni sulla configurazione dei ruoli di lavoro ibridi automazione di Azure, vedere [automatizzare le risorse nel Data Center o cloud con Hybrid Runbook Workers](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Dopo aver configurato un ruolo di lavoro ibrido, creare un Webhook come descritto nella sezione [Consume Data factory](#consume-with-data-factory).  L'unica differenza consiste nel selezionare i **eseguirvi** > **ruolo di lavoro ibrido** opzione durante la configurazione del Webhook.

Webhook di esempio con ruolo di lavoro ibrido:

![Webhook di ruolo di lavoro ibrido di esempio](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>PowerShell Runbook di esempio

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

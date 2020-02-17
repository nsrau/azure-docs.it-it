---
title: Distribuire uno strumento di gestione per desktop virtuale Windows usando un'entità servizio-Azure
description: Come distribuire lo strumento di gestione per desktop virtuale Windows tramite PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
ms.openlocfilehash: e55641457e1ed27928e6bf380701b646b672cae8
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368839"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Distribuire uno strumento di gestione con PowerShell

In questo articolo viene illustrato come distribuire lo strumento di gestione tramite PowerShell.

## <a name="important-considerations"></a>Considerazioni importanti

Ogni sottoscrizione del tenant di Azure Active Directory (Azure AD) necessita di una propria distribuzione separata dello strumento di gestione. Questo strumento non supporta Azure AD scenari business to business (B2B). 

Questo strumento di gestione è un esempio. Microsoft fornirà importanti aggiornamenti di qualità e sicurezza. [Il codice sorgente è disponibile in GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Che tu sia un cliente o un partner, ti invitiamo a personalizzare lo strumento per soddisfare le tue esigenze aziendali.

I browser seguenti sono compatibili con lo strumento di gestione di:

- Google Chrome 68 o versione successiva
- Microsoft Edge 40.15063 o versione successiva
- Mozilla Firefox 52.0 o versione successiva
- Safari 10 o versione successiva (solo macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Requisiti per la distribuzione dello strumento di gestione

Prima di distribuire lo strumento di gestione, è necessario che un utente di Azure Active Directory (Azure AD) crei una registrazione dell'app e distribuisca l'interfaccia utente di gestione. Questo utente dovrà avere:

- L'autorizzazione per creare risorse nella sottoscrizione di Azure
- L'autorizzazione per creare un'applicazione di Azure AD. Seguire questa procedura per verificare se l'utente ha le autorizzazioni necessarie in base alle istruzioni indicate in [Autorizzazioni necessarie](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Per distribuire e configurare correttamente lo strumento di gestione, è prima di tutto necessario scaricare gli script di PowerShell seguenti dal [repository GitHub dei modelli RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) e salvarli nella stessa cartella del computer locale.

  - createWvdMgmtUxAppRegistration. ps1
  - updateWvdMgmtUxApiUrl. ps1

Dopo aver distribuito e configurato lo strumento di gestione, è consigliabile chiedere a un utente di avviare l'interfaccia utente di gestione per verificarne il corretto funzionamento. L'utente che avvia l'interfaccia utente di gestione deve avere un'assegnazione di ruolo che gli consenta di visualizzare o modificare il tenant di Desktop virtuale Windows.

## <a name="set-up-powershell"></a>Configurare PowerShell

Per iniziare, accedere ai moduli AZ e Azure AD PowerShell. Ecco come eseguire l'accesso:

1. Aprire PowerShell come amministratore e passare alla directory in cui sono stati salvati gli script di PowerShell.
2. Accedere ad Azure con un account con autorizzazioni di proprietario o collaboratore nella sottoscrizione di Azure che si intende usare per creare lo strumento di gestione eseguendo il cmdlet seguente:

    ```powershell
    Login-AzAccount
    ```

3. Eseguire il cmdlet seguente per accedere a Azure AD con lo stesso account usato per il modulo AZ PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Successivamente, passare alla cartella in cui sono stati salvati i due script di PowerShell dal repository GitHub dei modelli RDS.

Per eseguire i cmdlet di PowerShell aggiuntivi durante l'accesso, lasciare aperta la finestra di PowerShell usata per l'accesso.

## <a name="create-an-azure-active-directory-app-registration"></a>Creare una registrazione dell'app Azure Active Directory

Eseguire i comandi seguenti per creare la registrazione dell'app con le autorizzazioni API necessarie:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Ora che è stata completata la registrazione dell'app Azure AD, è possibile distribuire lo strumento di gestione di.

## <a name="deploy-the-management-tool"></a>Distribuire lo strumento di gestione

Eseguire i comandi di PowerShell seguenti per distribuire lo strumento di gestione e associarlo all'entità servizio appena creata:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Dopo aver creato l'app Web, è necessario aggiungere un URI di reindirizzamento all'applicazione Azure AD per eseguire correttamente l'accesso degli utenti.

## <a name="set-the-redirect-uri"></a>Impostare l'URI di Reindirizzamento

Eseguire i comandi di PowerShell seguenti per recuperare l'URL dell'app Web e impostarlo come URI di reindirizzamento dell'autenticazione (detto anche URL di risposta):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

A questo punto, dopo aver aggiunto un URI di reindirizzamento, è necessario aggiornare l'URL dell'API in modo che lo strumento di gestione possa interagire con il servizio back-end dell'API.

## <a name="update-the-api-url-for-the-web-application"></a>Aggiornare l'URL dell'API per l'applicazione Web

Eseguire lo script seguente per aggiornare la configurazione dell'URL dell'API nel front-end dell'applicazione Web:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Ora che è stata completata la configurazione dell'app Web dello strumento di gestione, è possibile verificare l'applicazione Azure AD e fornire il consenso.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Verificare l'applicazione Azure AD e fornire il consenso

Per verificare la configurazione dell'applicazione Azure AD e fornire il consenso:

1. Aprire il browser Internet e accedere al [portale di Azure](https://portal.azure.com/) con l'account amministrativo.
2. Nella barra di ricerca nella parte superiore della portale di Azure cercare **registrazioni app** e selezionare l'elemento in **Servizi**.
3. Selezionare **tutte le applicazioni** e cercare il nome univoco dell'app fornito per lo script di PowerShell in [creare una Azure Active Directory registrazione dell'app](#create-an-azure-active-directory-app-registration).
4. Nel pannello sul lato sinistro del browser selezionare **autenticazione** e assicurarsi che l'URI di reindirizzamento corrisponda all'URL dell'app Web per lo strumento di gestione, come illustrato nella figura seguente.
   
   [![la pagina di autenticazione con l'URI di reindirizzamento immesso](media/management-ui-redirect-uri-inline.png)](media/management-ui-redirect-uri-expanded.png#lightbox)

5. Nel pannello sinistro selezionare **autorizzazioni API** per verificare che siano state aggiunte le autorizzazioni. Se si è un amministratore globale, selezionare il pulsante **concedi il consenso dell'amministratore per `tenantname`** e seguire le istruzioni della finestra di dialogo per fornire il consenso dell'amministratore per l'organizzazione.
    
    [![pagina autorizzazioni API](media/management-ui-permissions-inline.png)](media/management-ui-permissions-expanded.png#lightbox)

È ora possibile iniziare a usare lo strumento di gestione di.

## <a name="use-the-management-tool"></a>Usare lo strumento di gestione

Ora che lo strumento di gestione è stato configurato in qualsiasi momento, è possibile avviarlo in qualsiasi momento, ovunque. Ecco come avviare lo strumento:

1. Aprire l'URL dell'app Web in un Web browser. Se non si ricorda l'URL, è possibile accedere ad Azure, trovare il servizio app distribuito per lo strumento di gestione, quindi selezionare l'URL.
2. Accedere con le credenziali di Desktop virtuale Windows.
   
   > [!NOTE]
   > Se non è stato concesso il consenso dell'amministratore durante la configurazione dello strumento di gestione, ogni utente che esegue l'accesso dovrà fornire il proprio consenso dell'utente per poter usare lo strumento.

3. Quando viene richiesto di scegliere un gruppo di tenant, selezionare **gruppo tenant predefinito** dall'elenco a discesa.
4. Quando si seleziona **Default Tenant Group** (Gruppo di tenant predefinito), sul lato destro della finestra viene visualizzato un menu. In questo menu trovare il nome del gruppo di tenant e selezionarlo.
   
   > [!NOTE]
   > Se è disponibile un gruppo di tenant personalizzato, immettere il nome manualmente invece di scegliere una voce dell'elenco a discesa.

## <a name="report-issues"></a>Segnalare i problemi

Se si verificano problemi con lo strumento di gestione o altri strumenti di Desktop virtuale Windows, seguire le istruzioni nell'articolo relativo ai [modelli di Azure Resource Manager per Servizi Desktop remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md) per segnalarli in GitHub.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come distribuire lo strumento di gestione e come connettersi, è possibile scoprire come usare Integrità dei servizi di Azure per monitorare i problemi dei servizi e gli avvisi di integrità. Per altre informazioni, vedere l'esercitazione [Configurare gli avvisi dei servizi](./set-up-service-alerts.md).

---
title: Distribuire uno strumento di gestione per Desktop virtuale di Windows usando l'entità servizio - AzureDeploy a management tool for Windows Virtual Desktop using service principal - Azure
description: Come distribuire lo strumento di gestione per Windows Virtual Desktop utilizzando PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127787"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Distribuire uno strumento di gestione con PowerShellDeploy a management tool with PowerShell

Questo articolo illustra come distribuire lo strumento di gestione usando PowerShell.This article will show you how to deploy the management tool using PowerShell.

## <a name="important-considerations"></a>Considerazioni importanti

La sottoscrizione di ogni tenant di Azure Active Directory (Azure AD) richiede una distribuzione separata dello strumento di gestione. Questo strumento non supporta gli scenari Business-to-Business (B2B) di Azure AD. 

Questo strumento di gestione è un esempio. Microsoft fornirà importanti aggiornamenti di qualità e sicurezza. [Il codice sorgente è disponibile in GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Che tu sia un cliente o un partner, ti invitiamo a personalizzare lo strumento per soddisfare le tue esigenze aziendali.

I seguenti browser sono compatibili con lo strumento di gestione:

- Google Chrome 68 o versione successiva
- Microsoft Edge 40.15063 o versione successiva
- Mozilla Firefox 52.0 o versione successiva
- Safari 10 o versione successiva (solo macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Requisiti per la distribuzione dello strumento di gestione

Prima di distribuire lo strumento di gestione, è necessario che un utente di Azure Active Directory (Azure AD) crei una registrazione dell'app e distribuisca l'interfaccia utente di gestione. Questo utente dovrà avere:

- L'autorizzazione per creare risorse nella sottoscrizione di Azure
- L'autorizzazione per creare un'applicazione di Azure AD. Seguire questa procedura per verificare se l'utente ha le autorizzazioni necessarie in base alle istruzioni indicate in [Autorizzazioni necessarie](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Per distribuire e configurare correttamente lo strumento di gestione, è necessario innanzitutto scaricare i seguenti script di PowerShell dal [repository GitHub di RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) e salvarli nella stessa cartella nel computer locale.

  - createWvdMgmtUxAppRegistration.ps1
  - updateWvdMgmtUxApiUrl.ps1

Dopo aver distribuito e configurato lo strumento di gestione, è consigliabile chiedere a un utente di avviare l'interfaccia utente di gestione per verificarne il corretto funzionamento. L'utente che avvia l'interfaccia utente di gestione deve avere un'assegnazione di ruolo che gli consenta di visualizzare o modificare il tenant di Desktop virtuale Windows.

## <a name="set-up-powershell"></a>Configurare PowerShell

Iniziare accedendo a entrambi i moduli di Azure e Azure AD PowerShell.Get started by signing in to both the Az and Azure AD PowerShell modules. Ecco come eseguire l'accesso:

1. Aprire PowerShell come amministratore e passare alla directory in cui sono stati salvati gli script di PowerShell.Open PowerShell as an Administrator and navigate to the directory where you saved the PowerShell scripts.
2. Accedere ad Azure con un account con autorizzazioni proprietario o collaboratore per la sottoscrizione di Azure che si prevede di usare per creare lo strumento di gestione eseguendo il cmdlet seguente:

    ```powershell
    Login-AzAccount
    ```

3. Eseguire il cmdlet seguente per accedere ad Azure AD con lo stesso account usato per il modulo Az PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Successivamente, passare alla cartella in cui sono stati salvati i due script di PowerShell dal repository GitHub di RDS-Templates.

Mantenere aperta la finestra di PowerShell usata per l'accesso per l'esecuzione di cmdlet PowerShell aggiuntivi durante l'accesso.

## <a name="create-an-azure-active-directory-app-registration"></a>Creare una registrazione dell'app Azure Active DirectoryCreate an Azure Active Directory app registration

Eseguire i comandi seguenti per creare la registrazione dell'app con le autorizzazioni API necessarie:Run the following commands to create the app registration with required API permissions:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Dopo aver completato la registrazione dell'app Azure AD, è possibile distribuire lo strumento di gestione.

## <a name="deploy-the-management-tool"></a>Distribuire lo strumento di gestione

Eseguire i comandi di PowerShell seguenti per distribuire lo strumento di gestione e associarlo all'entità servizio appena creata:Run the following PowerShell commands to deploy the management tool and associate it with the service principal you just created:
     
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

Dopo aver creato l'app Web, è necessario aggiungere un URI di reindirizzamento all'applicazione Azure AD per accedere correttamente agli utenti.

## <a name="set-the-redirect-uri"></a>Impostare l'URI di reindirizzamento

Eseguire i comandi di PowerShell seguenti per recuperare l'URL dell'app Web e impostarlo come URI di reindirizzamento dell'autenticazione (denominato anche URL di risposta):Run the following PowerShell commands to retrieve the web app URL and set it as the authentication redirect URI (also called a reply URL):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Dopo aver aggiunto un URI di reindirizzamento, è necessario aggiornare l'URL dell'API in modo che lo strumento di gestione possa interagire con il servizio di back-end API.

## <a name="update-the-api-url-for-the-web-application"></a>Aggiornare l'URL dell'API per l'applicazione Web

Eseguire lo script seguente per aggiornare la configurazione dell'URL API nel front-end dell'applicazione Web:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Dopo aver configurato completamente l'app Web dello strumento di gestione, è necessario verificare l'applicazione Azure AD e fornire il consenso.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Verificare l'applicazione Azure AD e fornire il consensoVerify the Azure AD application and provide consent

Per verificare la configurazione dell'applicazione Azure AD e fornire il consenso:To verify the Azure AD application configuration and provide consent:

1. Aprire il browser Internet e accedere al portale di [Azure](https://portal.azure.com/) con l'account amministrativo.
2. Dalla barra di ricerca nella parte superiore del portale di Azure cercare **Registrazioni app** e selezionare l'elemento in **Servizi**.
3. Selezionare **Tutte le applicazioni** e cercare il nome univoco dell'app fornito per lo script di PowerShell in Creare una registrazione [dell'app Azure Active Directory.](#create-an-azure-active-directory-app-registration)
4. Nel riquadro sul lato sinistro del browser selezionare **Autenticazione** e assicurarsi che l'URI di reindirizzamento sia lo stesso dell'URL dell'app Web per lo strumento di gestione, come illustrato nell'immagine seguente.
   
   [![La pagina di autenticazione](media/management-ui-redirect-uri-inline.png) con l'URI di reindirizzamento immesso](media/management-ui-redirect-uri-expanded.png#lightbox)

5. Nel riquadro sinistro selezionare **Autorizzazioni API** per verificare che siano state aggiunte le autorizzazioni. Se sei un amministratore globale, seleziona il pulsante **Concedi `tenantname` il consenso dell'amministratore** e segui le istruzioni della finestra di dialogo per fornire il consenso dell'amministratore per la tua organizzazione.
    
    [![Pagina delle](media/management-ui-permissions-inline.png) autorizzazioni dell'API](media/management-ui-permissions-expanded.png#lightbox)

È ora possibile iniziare a utilizzare lo strumento di gestione.

## <a name="use-the-management-tool"></a>Usare lo strumento di gestione

Ora che hai configurato lo strumento di gestione in qualsiasi momento, puoi avviarlo in qualsiasi momento, ovunque. Ecco come avviare lo strumento:

1. Aprire l'URL dell'app Web in un Web browser. Se non si ricorda l'URL, è possibile accedere ad Azure, trovare il servizio app distribuito per lo strumento di gestione e quindi selezionare l'URL.
2. Accedere con le credenziali di Desktop virtuale Windows.
   
   > [!NOTE]
   > Se non hai concesso il consenso dell'amministratore durante la configurazione dello strumento di gestione, ogni utente che accede dovrà fornire il proprio consenso utente per poter utilizzare lo strumento.

3. Quando viene richiesto di scegliere un gruppo di tenant, selezionare **Gruppo tenant predefinito** dall'elenco a discesa.
4. Quando si seleziona **Default Tenant Group** (Gruppo di tenant predefinito), sul lato destro della finestra viene visualizzato un menu. In questo menu trovare il nome del gruppo di tenant e selezionarlo.
   
   > [!NOTE]
   > Se è disponibile un gruppo di tenant personalizzato, immettere il nome manualmente invece di scegliere una voce dell'elenco a discesa.

## <a name="report-issues"></a>Segnalare i problemi

Se si verificano problemi con lo strumento di gestione o altri strumenti di Desktop virtuale Windows, seguire le istruzioni nell'articolo relativo ai [modelli di Azure Resource Manager per Servizi Desktop remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md) per segnalarli in GitHub.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come distribuire lo strumento di gestione e come connettersi, è possibile scoprire come usare Integrità dei servizi di Azure per monitorare i problemi dei servizi e gli avvisi di integrità. Per altre informazioni, vedere l'esercitazione [Configurare gli avvisi dei servizi](./set-up-service-alerts.md).

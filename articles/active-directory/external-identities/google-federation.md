---
title: Aggiungere Google come provider di identità per B2B - Azure AD
description: Federazione con Google per consentire agli utenti guest di accedere alle app Azure AD con i propri account Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926236"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Aggiungere Google come provider di identità per utenti guest B2B

Impostando la Federazione con Google, è possibile consentire agli utenti invitati di accedere alle app e alle risorse condivise con i propri account Gmail, senza dover creare account Microsoft. 

> [!NOTE]
> Federazione Google è appositamente progettato per gli utenti di Gmail. Per attuare la Federazione con i domini G Suite, usare [Federazione diretta](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Qual è l'esperienza per l'utente di Google?
Quando si invia un invito a utenti di Google Gmail, gli utenti Guest devono accedere alle app o alle risorse condivise usando un collegamento che include il contesto del tenant. L'esperienza varia a seconda che sia stato già eseguito o meno l'accesso a Google:
  - Agli utenti guest che non hanno eseguito l'accesso a Google verrà richiesto di farlo.
  - Agli utenti Guest a cui è già stato effettuato l'accesso a Google verrà richiesto di scegliere l'account che si desidera utilizzare. L'utente deve scegliere l'account usato nell'invito.

Gli utenti guest che visualizzano un errore di "intestazione troppo lungo" possono cancellare i cookie o aprire una finestra privata o in incognito e provare ad accedere di nuovo.

![Screenshot che mostra la pagina di accesso di Google.](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Limitazioni

Teams supporta completamente gli utenti guest di Google su tutti i dispositivi. Gli utenti di Google possono accedere a Teams da un endpoint comune come `https://teams.microsoft.com`.

Gli endpoint comuni di altre applicazioni potrebbero non supportare gli utenti di Google. Gli utenti guest di Google devono accedere usando un collegamento che include le informazioni del tenant. Di seguito sono riportati alcuni esempi:
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Se gli utenti guest di Google tentano di usare un collegamento come `https://myapps.microsoft.com` o, riceveranno `https://portal.azure.com` un errore.

È anche possibile fornire agli utenti guest di Google un collegamento diretto a un'applicazione o a una risorsa, purché il collegamento includa le informazioni sul tenant. Ad esempio `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Passaggio 1: Configurare un progetto di Google Developers
Per prima cosa, creare un nuovo progetto nella console di sviluppatori di Google per ottenere un ID client e un segreto client che è possibile aggiungere in un secondo momento al Azure Active Directory (Azure AD). 
1. Passare alle API di Google all'indirizzo https://console.developers.google.com e accedere con l'account Google. È consigliabile usare l'account condiviso di un team Google.
2. Se richiesto, accettare le condizioni del servizio.
3. Creare un nuovo progetto: nel Dashboard selezionare **Crea progetto** , assegnare un nome al progetto, ad esempio **Azure ad B2B** , quindi selezionare **Crea** : 
   
   ![Screenshot che mostra una nuova pagina del progetto.](media/google-federation/google-new-project.png)

4. Nella pagina **api & Services** selezionare **Visualizza** nel nuovo progetto.

5. Selezionare **Vai a API Panoramica** nella scheda API. Selezionare la **schermata di consenso OAuth** .

6. Selezionare **External** (Esterno), quindi **Create** (Crea). 

7. Nella **schermata di consenso OAuth** immettere il nome di un' **applicazione** :

   ![Screenshot che mostra la schermata di consenso di Google OAuth.](media/google-federation/google-oauth-consent-screen.png)

8. Scorrere fino alla sezione **domini autorizzati** e immettere **microsoftonline.com** :

   ![Screenshot che mostra la sezione domini autorizzati.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Selezionare **Salva** .

10. Selezionare **Credentials** (Credenziali). Scegliere **ID client OAuth** dal menu **Crea credenziali** :

    ![Screenshot che mostra il menu di creazione delle credenziali di Google APIs.](media/google-federation/google-api-credentials.png)

11. In **Tipo di applicazione** selezionare **Applicazione Web** . Assegnare all'applicazione un nome appropriato, ad esempio **Azure ad B2B** . In **URI di reindirizzamento autorizzati** immettere gli URI seguenti:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(dove `<tenant ID>` è l'ID tenant)
   
    > [!NOTE]
    > Per trovare l'ID tenant, passare alla [portale di Azure](https://portal.azure.com). In **Azure Active Directory** selezionare **Proprietà** e copiare l' **ID tenant** .

    ![Screenshot che mostra la sezione relativa agli URI di reindirizzamento autorizzati.](media/google-federation/google-create-oauth-client-id.png)

12. Selezionare **Crea** . Copiare l'ID client e il segreto client. Verranno usati quando si aggiunge il provider di identità nel portale di Azure.

    ![Screenshot che mostra l'ID client OAuth e il segreto client.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Passaggio 2: Configurare la federazione con Google in Azure AD 
È ora possibile impostare l'ID client e il segreto client di Google. A tale scopo, è possibile usare il portale di Azure o PowerShell. Assicurarsi di testare la configurazione di Google Federation invitandolo. Usare un indirizzo Gmail e provare a riscattare l'invito con l'account Google invitato. 

**Per configurare Google Federation nella portale di Azure** 
1. Accedere al [portale di Azure](https://portal.azure.com). Nel riquadro sinistro selezionare **Azure Active Directory** . 
2. Selezionare **Identità esterne** .
3. Selezionare **tutti i provider di identità** e quindi fare clic sul pulsante **Google** .
4. Immettere l'ID client e il segreto client ottenuti in precedenza. Selezionare **Salva** : 

   ![Screenshot che mostra la pagina Aggiungi provider di identità Google.](media/google-federation/google-identity-provider.png)

**Per configurare la federazione con Google tramite PowerShell**
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire questo comando: `Connect-AzureAD`
3. Al prompt di accesso, accedere con l'account di amministratore globale gestito.  
4. Eseguire il comando seguente: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Usare l'ID client e il segreto client dall'app creata in "passaggio 1: configurare un progetto per sviluppatori Google". Per ulteriori informazioni, vedere [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Come si rimuove la federazione con Google?
È possibile rimuovere la configurazione della federazione con Google. In tal caso, gli utenti guest di Google che hanno già riscattato l'invito non saranno in grado di eseguire l'accesso. È tuttavia possibile concedere loro l'accesso alle risorse tramite l'eliminazione dalla directory e la loro reinserimento. 
 
**Per eliminare Google Federation nel portale di Azure AD**
1. Accedere al [portale di Azure](https://portal.azure.com). Nel riquadro sinistro selezionare **Azure Active Directory** . 
2. Selezionare **Identità esterne** .
3. Selezionare **Tutti i provider di identità** .
4. Nella riga di **Google** , selezionare il pulsante con i puntini di sospensione ( **...** ) e quindi selezionare **Elimina** . 
   
   ![Screenshot che mostra il pulsante Elimina per il provider di identità basato su social network.](media/google-federation/google-social-identity-providers.png)

1. Selezionare **Sì** per confermare l'eliminazione. 

**Per eliminare Google Federation usando PowerShell** 
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire `Connect-AzureAD`.  
4. Nella richiesta di accesso accedere con l'account amministratore globale gestito.  
5. Immettere il comando seguente:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Per altre informazioni, vedere l'articolo relativo al cmdlet [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).

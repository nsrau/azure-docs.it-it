---
title: Aggiungere Facebook come provider di identità - Azure AD
description: Aggiungere la federazione con Facebook per consentire a utenti esterni (guest) di accedere alle app Azure AD con i propri account Facebook.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b5e1db2c86f6118c3cd333974c9cfd64f747128
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551442"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Aggiungere Facebook come provider di identità per le identità esterne

È possibile aggiungere Facebook ai flussi utente di iscrizione self-service (anteprima) per consentire agli utenti di accedere alle applicazioni usando i propri account Facebook. Per consentire agli utenti di accedere con Facebook, è prima necessario [abilitare l'iscrizione self-service](self-service-sign-up-user-flow.md) per il tenant. Dopo aver aggiunto Facebook come provider di identità, configurare un flusso utente per l'applicazione e selezionare Facebook come una delle opzioni di accesso.

> [!NOTE]
> Gli utenti possono usare i propri account Facebook solo per iscriversi alle app usando l'iscrizione self-service e i flussi utente. Gli utenti non possono essere inviati e riscattare l'invito usando un account Facebook.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Creare un'app nella console per sviluppatori di Facebook

Per usare un account Facebook come [provider di identità](identity-providers.md), è necessario creare un'applicazione nella console per sviluppatori di Facebook. Se non si possiede già un account Facebook, è possibile crearlo sul sito [https://www.facebook.com/](https://www.facebook.com).

> [!NOTE]  
> Usare gli URL seguenti nei passaggi 9 e 16 riportati sotto.
> - Per **URL sito** immettere l'indirizzo dell'applicazione, ad esempio `https://contoso.com` .
> - In **URI di reindirizzamento OAuth validi** immettere `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp`. È possibile trovare il valore di `<tenant-ID>` corrente nel pannello Panoramica di Azure Active Directory.


1. Accedere al [sito Web Facebook for developers](https://developers.facebook.com/) con le credenziali dell'account Facebook.
2. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo, selezionare **Accedi** nell'angolo in alto a destra della pagina, accettare le condizioni di Facebook e completare la procedura di registrazione.
3. Selezionare **Le mie app** e quindi **Creazione di un'app**.
4. Inserire un **nome visualizzato** e una **email di contatto** valida.
5. Selezionare **Crea ID app**. Potrebbe essere necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
6. Selezionare **Impostazioni** > **Base**.
7. Scegliere una **Categoria**, ad esempio Aziende e Pagine. Questo valore è obbligatorio in Facebook, ma non è usato per Azure AD.
8. Nella parte inferiore della pagina, selezionare **Aggiungi piattaforma**, quindi selezionare **Sito Web**.
9. In **URL del sito** immettere l'URL appropriato (indicato in precedenza).
10. In **URL della Normativa sulla privacy** immettere l'URL della pagina in cui vengono gestite le informazioni sulla privacy per l'applicazione, ad esempio `http://www.contoso.com`.
11. Selezionare **Save changes** (Salva modifiche).
12. Nella parte superiore della pagina copiare l'**ID app**.
13. Fare clic su **Mostra** e copiare il valore di **Chiave segreta**. Sono necessari entrambi per configurare Facebook come provider di identità nel tenant. **App Segreta** è una credenziale di sicurezza importante.
14. Selezionare il segno + accanto a **PRODOTTI**, quindi selezionare **Configura** sotto **Facebook Login** (Accesso a Facebook).
15. Selezionare **Impostazioni** sotto **Facebook Login** (Accesso a Facebook).
16. In **URI di reindirizzamento OAuth validi** immettere l'URL appropriato (indicato in precedenza).
17. Fare clic su **Salva le modifiche** nella parte inferiore della pagina.
18. Per rendere disponibile l'applicazione Facebook per Azure AD, fare clic sul selettore di stato nella parte superiore destra della pagina, impostarlo su **Sì** per rendere pubblica l'applicazione e quindi fare clic su **Switch Mode** (Cambia modalità). A questo punto lo stato dovrebbe cambiare da **Sviluppo** a **Live**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurare un account Facebook come provider di identità
A questo punto, si procederà all'impostazione dell'ID client e del segreto client di Facebook immettendoli nel portale di Azure AD o tramite PowerShell. Per testare la configurazione di Facebook, è possibile eseguire l'iscrizione tramite un flusso utente in un'app abilitata per l'iscrizione self-service.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Per configurare la federazione con Facebook nel portale di Azure AD
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale del tenant di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **Identità esterne**.
4. Selezionare **Tutti i provider di identità** e quindi selezionare **Facebook**.
5. Per **ID client**, immettere l'**ID app** dell'applicazione Facebook creata in precedenza.
6. Per **Segreto client** immettere il valore di **Chiave segreta** annotato in precedenza.

   ![Screenshot che mostra la pagina Aggiungi un provider di identità basato su social network](media/facebook-federation/add-social-identity-provider-page.png)

7. Selezionare **Salva**.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>Per configurare la federazione con Facebook tramite PowerShell
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire questo comando: `Connect-AzureAD`.
3. Al prompt di accesso, accedere con l'account di amministratore globale gestito.  
4. Eseguire il comando seguente: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Usare l'ID client e il segreto client dall'app creata in precedenza nella console per sviluppatori di Facebook. Per altre informazioni, vedere l'articolo relativo al cmdlet [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 

## <a name="how-do-i-remove-facebook-federation"></a>Come si rimuove la federazione con Facebook?
È possibile rimuovere la configurazione della federazione con Facebook. In tal caso, tutti gli utenti che hanno effettuato l'iscrizione tramite flussi utente con i propri account Facebook non riusciranno più a eseguire l'accesso. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Per eliminare la federazione con Facebook nel portale di Azure AD: 
1. Accedere al [portale di Azure](https://portal.azure.com). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Identità esterne**.
3. Selezionare **Tutti i provider di identità**.
4. Nella riga **Facebook** selezionare il menu di scelta rapida ( **...** ), quindi scegliere **Elimina**. 
5. Selezionare **Sì** per confermare l'eliminazione.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>Per eliminare la federazione con Facebook tramite PowerShell: 
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire `Connect-AzureAD`.  
4. Nella richiesta di accesso accedere con l'account amministratore globale gestito.  
5. Immettere il comando seguente:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > Per altre informazioni, vedere l'articolo relativo al cmdlet [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere l'iscrizione self-service a un'app](self-service-sign-up-user-flow.md)

---
title: Uso della modalità dispositivo condiviso con MSAL per Android | Azure
description: Informazioni su come preparare un dispositivo Android per l'esecuzione in modalità condivisa ed eseguire un'app per operatori sul campo.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 98882ad115ff977cfd8222c6055a436855f50c04
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701247"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Esercitazione: Usare la modalità dispositivo condiviso nell'app Android

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="developer-guide"></a>Guida per sviluppatori

Questa guida fornisce agli sviluppatori indicazioni per implementare la modalità dispositivo condiviso in un'applicazione Android usando Microsoft Authentication Library (MSAL). Vedere l'[esercitazione su MSAL per Android](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) per informazioni su come integrare MSAL con l'app Android, connettere un utente, chiamare Microsoft Graph e disconnettere l'utente.

### <a name="download-the-sample"></a>Scaricare l'esempio

Clonare l'[applicazione di esempio](https://github.com/Azure-Samples/ms-identity-android-java/) da GitHub. L'esempio consente di lavorare in [modalità per account singolo o per account multipli](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Aggiungere MSAL SDK al repository Maven locale

Se non si usa l'app di esempio, aggiungere la libreria MSAL come dipendenza nel file build.gradle, come segue:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Configurare l'app per l'uso della modalità dispositivo condiviso

Per altre informazioni sull'impostazione del file di configurazione, vedere la [documentazione sulla configurazione](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration).

Impostare `"shared_device_mode_supported"` su `true` nel file di configurazione MSAL.

Se non si prevede di supportare la modalità per account multipli, come nel caso in cui non si usa un dispositivo condiviso e l'utente può accedere all'app con più account nello stesso momento, impostare `"account_mode"` su `"SINGLE"`. Ciò garantisce che l'app ottenga sempre `ISingleAccountPublicClientApplication`, semplificando notevolmente l'integrazione di MSAL. Il valore predefinito di `"account_mode"` è `"MULTIPLE"`, quindi è importante cambiarlo nel file di configurazione se si usa la modalità `"single account"`.

Ecco un esempio del file auth_config.json incluso nella directory **app**>**main**>**res**>**raw** dell'app di esempio:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Rilevare la modalità dispositivo condiviso

La modalità dispositivo condiviso consente di configurare i dispositivi Android in modo che vengano condivisi da più dipendenti, fornendo al contempo la gestione del dispositivo supportata da Microsoft Identity. I dipendenti possono accedere ai propri dispositivi e visualizzare rapidamente le informazioni dei clienti. Al termine del turno o dell'attività, potranno disconnettersi da tutte le app nel dispositivo condiviso con un solo clic e il dispositivo sarà immediatamente pronto per il prossimo dipendente.

Usare `isSharedDevice()` per determinare se un'app è in esecuzione in un dispositivo che è in modalità dispositivo condiviso. L'app può usare questo flag per determinare se l'esperienza utente deve essere modificata di conseguenza.

Ecco un frammento di codice che mostra come è possibile usare `isSharedDevice()`.  È tratto dalla classe `SingleAccountModeFragment` nell'app di esempio:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inizializzare l'oggetto PublicClientApplication

Se si imposta `"account_mode":"SINGLE"` nel file di configurazione MSAL, è possibile eseguire il cast sicuro dell'oggetto applicazione restituito come `ISingleAccountPublicCLientApplication`.

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/ 
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config, 
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});  
```

### <a name="detect-single-vs-multiple-account-mode"></a>Rilevare la modalità per account singolo o per account multipli

Se si vuole scrivere un'app che verrà usata solo per operatori sul campo su un dispositivo condiviso, è consigliabile scriverla in modo che supporti solo la modalità per account singolo. Questo vale per la maggior parte delle applicazioni incentrate su attività, come le app medicali, le app di fatturazione e quasi tutte le app line-of-business. In questo modo si semplifica lo sviluppo, in quanto molte funzionalità dell'SDK non saranno necessarie.

Se l'app supporta più account, oltre alla modalità dispositivo condiviso, è necessario eseguire un controllo del tipo ed eseguire il cast all'interfaccia appropriata, come illustrato di seguito.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Ottenere l'utente connesso e determinare se un utente è cambiato nel dispositivo

Il metodo `loadAccount` recupera l'account dell'utente che ha eseguito l'accesso. Il metodo `onAccountChanged` determina se l'utente connesso è cambiato e, in tal caso, esegue la pulizia:

```java 
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override 
    public void onError(@NonNull Exception exception) 
    {
    }
  }
}  
```

### <a name="globally-sign-in-a-user"></a>Connettere un utente a livello globale

Il codice seguente connette un utente attraverso il dispositivo ad altre app che usano MSAL con l'app Authenticator:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Disconnettere un utente a livello globale

Il codice seguente rimuove l'account connesso e cancella i token memorizzati nella cache non solo dall'app, ma anche dal dispositivo in modalità dispositivo condiviso:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Guida dell'amministratore

I passaggi seguenti descrivono come configurare l'applicazione nel portale di Azure e impostare il dispositivo in modalità dispositivo condiviso.

### <a name="register-your-application-in-azure-active-directory"></a>Registrare l'applicazione in Azure Active Directory

Come prima cosa, registrare l'applicazione nel tenant dell'organizzazione. Quindi fornire i valori indicati di seguito nel file auth_config.json per consentire la corretta esecuzione dell'applicazione.

Per informazioni sula procedura, vedere [Registrare l'applicazione](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application).

> [!NOTE]
> Per registrare l'app, usare la guida di avvio rapido sul lato sinistro e quindi selezionare **Android**. Si aprirà una pagina in cui si dovranno compilare i campi **Nome pacchetto** e **Hash della firma** per l'app. Questi valori sono molto importanti per assicurare la corretta configurazione dell'app. Si riceverà quindi un oggetto di configurazione per l'app da tagliare e incollare nel file auth_config.json.

![Schermata di registrazione dell'app](media/tutorial-v2-shared-device-mode/register-app.png) Selezionare **Apporta questa modifica per me** e quindi specificare i valori richiesti dalla guida di avvio rapido nel portale di Azure. Al termine verranno generati tutti i file di configurazione necessari.

![Schermata di informazioni sulla configurazione dell'app](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Configurare un tenant

A scopo di test, configurare nel tenant almeno due dipendenti, un amministratore dispositivo cloud e un amministratore globale. Nel portale di Azure impostare l'amministratore dispositivo cloud modificando i ruoli aziendali. Nel portale di Azure accedere ai ruoli aziendali selezionando **Azure Active Directory** > **Ruoli e amministratori** > **Amministratore dispositivo cloud**. Aggiungere gli utenti autorizzati a impostare un dispositivo in modalità condivisa.

## <a name="set-up-an-android-device-in-shared-mode"></a>Configurare un dispositivo Android in modalità condivisa

### <a name="download-the-authenticator-app"></a>Scaricare l'app Authenticator

Scaricare l'app Microsoft Authenticator da Google Play Store. Se l'app è già stata scaricata, assicurarsi che sia della versione più recente.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Impostazioni dell'app Authenticator e registrazione del dispositivo nel cloud

Avviare l'app Authenticator e passare alla pagina dell'account principale. Quando viene visualizzata la pagina **Aggiungi account**, si è pronti per impostare il dispositivo in modalità condivisa.

![Schermata Aggiungi account dell'app Authenticator](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Passare al riquadro **Impostazioni** usando la barra dei menu a destra. Selezionare **Registrazione del dispositivo** in **Account aziendale o dell'istituto di istruzione**.
 
 ![Schermata Aggiungi account dell'app Authenticator](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Quando si fa clic su questo pulsante, viene chiesto di autorizzare l'accesso ai contatti del dispositivo. Questa richiesta è dovuta all'integrazione dell'account di Android nel dispositivo. Scegliere **Consenti**.

 ![Schermata Aggiungi account dell'app Authenticator](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

L'amministratore dispositivo cloud deve immettere il proprio indirizzo di posta elettronica aziendale in **In alternativa, eseguire la registrazione come dispositivo condiviso**. Fare quindi clic sul pulsante **Registra come dispositivo condiviso** e immettere le credenziali.

![Schermata di registrazione del dispositivo](media/tutorial-v2-shared-device-mode/register-device.png)

![accesso](media/tutorial-v2-shared-device-mode/sign-in.png)

Il dispositivo è ora in modalità condivisa.

![Schermata di registrazione del dispositivo](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Tutti gli accessi e le disconnessioni nel dispositivo avvengono a livello globale, ossia si applicano a tutte le app integrate con MSAL e Microsoft Authenticator nel dispositivo. È ora possibile distribuire nel dispositivo applicazioni che usano funzionalità in modalità dispositivo condiviso.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Visualizzare il dispositivo condiviso nel portale di Azure

Dopo che un dispositivo è stato messo in modalità condivisa, diventa noto all'organizzazione e ne viene tenuta traccia nel tenant dell'organizzazione. È possibile visualizzare i dispositivi condivisi controllando il valore di **Tipo di aggiunta** nel pannello Azure Active Directory del portale di Azure.

![Pannello Tutti i dispositivi nel portale di Azure](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>Esecuzione dell'app di esempio

L'applicazione di esempio è un'app semplice che chiama l'API Graph dell'organizzazione. Alla prima esecuzione verrà chiesto di fornire il consenso, in quanto l'applicazione è nuova per l'account del dipendente.

![Schermata di informazioni sulla configurazione dell'app](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla modalità condivisa, vedere [Modalità dispositivo condiviso per dispositivi Android](shared-device-mode.md)
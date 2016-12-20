---
title: Introduzione all&quot;autenticazione per app per dispositivi mobili in Xamarin Android
description: "Informazioni su come usare le app per dispositivi mobili per autenticare gli utenti dell&quot;app per Xamarin Android tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: dwrede
editor: 
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4c0ba82ca010f1ee571424fa3d650718e5acdd8b


---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Aggiungere l'autenticazione all'app per Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Questo argomento descrive come autenticare gli utenti di un'app per dispositivi mobili dall'applicazione client. In questa esercitazione si aggiungerà l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato dal servizio App per dispositivi mobili di Azure. Una volta completate l'autenticazione e l'autorizzazione nell'app per dispositivi mobili, verrà visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva dell'app mobile. È anche necessario completare prima l'esercitazione [Creare un'app per Xamarin.Android]. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di autenticazione al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all' [utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-app-services"></a><a name="register"></a>Registrare l'app per l'autenticazione e configurare i servizi app
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Visual Studio o Xamarin Studio, eseguire il progetto client su un dispositivo o un emulatore. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato). L'eccezione non gestita viene generata perché l'app prova ad accedere al back-end dell'app per dispositivi mobili come utente non autenticato. La tabella *TodoItem* richiede ora l'autenticazione.

Si aggiornerà quindi l'app client per richiedere le risorse dal back-end dell'app per dispositivi mobili con un utente autenticato.

## <a name="a-nameadd-authenticationaadd-authentication-to-the-app"></a><a name="add-authentication"></a>Aggiungere l'autenticazione all'app
L'applicazione viene aggiornata per richiedere agli utenti di toccare il pulsante **Accedi** ed eseguire l'autenticazione prima della visualizzazione dei dati.

1. Aggiungere il codice seguente alla classe **TodoActivity** :
   
        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Verrà creato un nuovo metodo per autenticare un utente e un gestore del metodo per un nuovo pulsante **Accedi** . Nell'esempio di codice precedente, l'utente viene autenticato tramite un account di accesso di Facebook. Viene usata una finestra di dialogo per visualizzare l'ID utente una volta completata l'autenticazione.
   
   > [!NOTE]
   > Se si usa un provider di identità diverso da Google, sostituire il valore passato a **LoginAsync** riportato in precedenza con uno dei seguenti: *MicrosoftAccount*, *Twitter*, *Google* o *WindowsAzureActiveDirectory*.
   > 
   > 
2. Nel metodo **OnCreate** , eliminare o impostare come commento la riga di codice seguente:
   
        OnRefreshItemsSelected ();
3. Nel file Activity_To_Do.axml aggiungere la definizione del pulsante *LoginUser* prima del pulsante *AddItem* esistente:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Aggiungere l'elemento seguente al file di risorse Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. In Visual Studio o Xamarin Studio eseguire il progetto client in un dispositivo o un emulatore ed eseguire l'accesso tramite il provider di identità scelto.
   
       When you are successfully logged-in, the app will display your login ID and the list of todo items, and you can make updates to the data.

<!-- URLs. -->
[Creare un'app per Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md



<!--HONumber=Nov16_HO3-->



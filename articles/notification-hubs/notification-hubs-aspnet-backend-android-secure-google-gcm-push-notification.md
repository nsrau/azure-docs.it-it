---
title: Inviare notifiche push sicure con hub di notifica di Azure
description: Informazioni su come inviare notifiche push sicure a un'app per Android da Azure. Gli esempi di codice sono scritti in Java e C#.
documentationcenter: android
keywords: notifica push, notifiche push, push dei messaggi, notifiche push di android
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: f2d5d618fabbe7400ce825f984ace1622a524f05
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88004019"
---
# <a name="send-secure-push-notifications-with-azure-notification-hubs"></a>Inviare notifiche push sicure con hub di notifica di Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Panoramica

> [!IMPORTANT]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Il supporto per le notifiche push in Microsoft Azure consente di accedere a un'infrastruttura di messaggistica push di facile utilizzo, multipiattaforma con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili.

A causa di vincoli normativi o di sicurezza, un'applicazione potrebbe talvolta includere nella notifica informazioni che non è possibile trasmettere attraverso l'infrastruttura di notifiche push standard. Questa esercitazione descrive come conseguire la stessa esperienza inviando informazioni sensibili attraverso una connessione autenticata e sicura tra il dispositivo client Android e il back-end dell'app.

A livello generale, il flusso è il seguente:

- Il back-end dell'app:
  * Archivia il payload sicuro nel database back-end.
  * Invia l'ID di questa notifica al dispositivo Android (non vengono inviate informazioni sicure).
- L'app sul dispositivo, quando riceve la notifica:
  * Il dispositivo Android contatta il back-end richiedendo il payload sicuro.
  * L'app può indicare il payload come una notifica sul dispositivo.

È importante notare che nel flusso precedente e in questa esercitazione si presuppone che il dispositivo archivi un token di autenticazione nella memoria locale, dopo l'accesso dell'utente. Questo approccio garantisce un'esperienza senza problemi, in quanto il dispositivo può recuperare il payload protetto della notifica usando questo token. Se l'applicazione non archivia i token di autenticazione nel dispositivo o se questi token possono essere scaduti, l'app per dispositivo, alla ricezione della notifica push, dovrebbe visualizzare una notifica generica che richiede all'utente di avviare l'app. L'app autentica quindi l'utente e mostra il payload di notifica.

Questa esercitazione descrive come inviare notifiche push sicure. Si basa sull'esercitazione [Notify Users](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , quindi è necessario completare prima di tutto la procedura descritta in questa esercitazione.

> [!NOTE]
> Questa esercitazione presuppone che sia stato creato e configurato l'hub di notifica come descritto in [Introduzione ad hub di notifica (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modificare il progetto Android

Ora che il back-end dell'app è stato modificato per inviare solo l'ID di una notifica push, è necessario modificare l'app per Android per gestire tale notifica e richiamare il back-end per recuperare il messaggio sicuro da visualizzare.
Per conseguire questo obiettivo, è necessario assicurarsi che l'app per Android sia in grado di eseguire l'autenticazione con il back-end quando riceve le notifiche push.

Ora si modifica il flusso di accesso per salvare il valore dell'intestazione di autenticazione nelle preferenze condivise dell'app. Un meccanismo analogo può essere usato per archiviare eventuali token di autenticazione (ad esempio token OAuth) che l'app deve usare senza richiedere le credenziali dell'utente.

1. Nel progetto di app per Android, aggiungere le costanti seguenti all'inizio della classe `MainActivity`:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```

2. Sempre nella classe `MainActivity` aggiornare il metodo `getAuthorizationHeader()` per includere il codice seguente:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```

3. Aggiungere le istruzioni `import` seguenti all'inizio del file `MainActivity`:

    ```java
    import android.content.SharedPreferences;
    ```

A questo punto, modificare il gestore chiamato quando si riceve la notifica.

1. Nella classe `MyHandler` modificare il metodo `OnReceive()` in modo che contenga:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```

2. Aggiungere quindi il metodo `retrieveNotification()`, sostituendo il segnaposto `{back-end endpoint}` con l'endpoint del back-end ottenuto durante la distribuzione del back-end:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Questo metodo chiama il back-end dell'app per recuperare il contenuto della notifica usando le credenziali memorizzate nelle preferenze condivise e lo visualizza come una normale notifica. L'utente dell'app vedrà la notifica esattamente come qualsiasi altra notifica push.

È preferibile gestire i casi in cui manca la proprietà dell'intestazione di autenticazione o di rifiuto da parte del back-end. La gestione specifica di questi casi dipende in larga misura dall'esperienza dell'utente di destinazione. Una delle opzioni consiste nel visualizzare una notifica con un prompt generico affinché l'utente possa autenticarsi per recuperare la notifica effettiva.

## <a name="run-the-application"></a>Eseguire l'applicazione

Per eseguire l'applicazione, eseguire queste azioni:

1. Assicurarsi che il progetto **AppBackend** sia distribuito in Azure. Se si usa Visual Studio, eseguire l'applicazione API Web **AppBackend** . Verrà visualizzata una pagina Web ASP.NET.
2. In Eclipse eseguire l'app su un dispositivo Android fisico o sull'emulatore.
3. Nell'interfaccia utente dell'app per Android immettere un nome utente e una password. Può trattarsi di qualsiasi stringa, ma devono avere lo stesso valore.
4. Nell'interfaccia utente dell'app per Android fare clic su **Log in**. Fare clic su **Send push**.

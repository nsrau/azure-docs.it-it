<properties title="Azure Notification Hubs Secure Push" pageTitle="Azure Notification Hubs Secure Push" metaKeywords="Azure push notifications, Azure notification hubs, secure push" description="Learn how to send secure push notifications to an Android app from Azure. Code samples written in Java and C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"/>

# Push sicuro degli hub di notifica di Azure

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/it-it/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS">iOS</a>
        <a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android" class="current">Android</a>
</div>

Il supporto per le notifiche push in Microsoft Azure consente di accedere a un'infrastruttura push di facile utilizzo, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili.

A causa di vincoli normativi o di sicurezza, un'applicazione potrebbe talvolta includere nella notifica informazioni che non è possibile trasmettere attraverso l'infrastruttura di notifiche push standard. In questa esercitazione viene descritto come conseguire la stessa esperienza inviando informazioni sensibili attraverso una connessione autenticata e sicura tra il dispositivo client e il back-end dell'app.

A livello generale, il flusso è il seguente:

1.  Il back-end dell'app:

    -   Archivia il payload sicuro nel database back-end.
    -   Invia l'ID di questa notifica al dispositivo (non vengono inviate informazioni sicure).

2.  L'app sul dispositivo, quando riceve la notifica:

    -   Il dispositivo contatta il back-end richiedendo il payload sicuro.
    -   L'app può indicare il payload come una notifica sul dispositivo.

È importante notare che nel flusso precedente (e in questa esercitazione) si presume che il dispositivo archivi un token di autenticazione nell'archiviazione locale, dopo che l'utente ha eseguito l'accesso. Ciò garantisce un'esperienza del tutto uniforme, in quanto il dispositivo può recuperare il payload sicuro della notifica usando questo token. Se invece l'applicazione non archivia i token di autenticazione nel dispositivo o se questi hanno una scadenza, l'app per dispositivo, alla ricezione della notifica, dovrà visualizzare una notifica generica in cui si richiede all'utente di avviare l'app. L'app autentica quindi l'utente e mostra il payload di notifica.

In questa esercitazione sul push sicuro viene illustrato come inviare una notifica push in modo sicuro. Poiché i passaggi qui descritti si basano sull'esercitazione **Utilizzo di Hub di notifica per inviare notifiche agli utenti**, sarà prima necessario completare i passaggi di quest'ultima.

> [AZURE.NOTE] In questa esercitazione si presuppone che l'utente abbia creato e configurato l'hub di notifica come descritto in [Introduzione ad Hub di notifica (Android)][Introduzione ad Hub di notifica (Android)].

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Modificare il progetto Android

Dopo aver modificato il back-end dell'app in modo che invii solo l'*ID* di una notifica, occorre modificare l'app per Android in modo che gestisca tale notifica e richiami il back-end per recuperare il messaggio sicuro da visualizzare.
A questo scopo, è necessario verificare che l'app per Android sia in grado di autenticarsi con il back-end quando riceve le notifiche push.

Ora si modificherà il flusso di *accesso* per salvare il valore dell'intestazione di autenticazione nelle preferenze condivise dell'app. Un meccanismo analogo può essere usato per archiviare eventuali token di autenticazione (ad esempio token OAuth) che l'app dovrà usare senza richiedere le credenziali dell'utente.

1.  Nel progetto di app per Android, aggiungere le costanti seguenti all'inizio della classe **MainActivity**:

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2.  Sempre nella classe **MainActivity**, aggiornare il metodo `getAuthorizationHeader()` in modo che contenga il codice seguente:

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3.  Aggiungere le istruzioni `import` seguenti all'inizio del file **MainActivity**:

        import android.content.SharedPreferences;

A questo punto, modificare il gestore chiamato quando si riceve la notifica.

1.  Nella classe **MyHandler** modificare il metodo `OnReceive()` in modo che contenga:

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;   
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

2.  Aggiungere quindi il metodo `retrieveNotification()`, sostituendo il segnaposto `{back-end endpoint}` con l'endpoint del back-end ottenuto durante la distribuzione del back-end:

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

Questo metodo chiama il back-end dell'app per recuperare il contenuto della notifica usando le credenziali memorizzate nelle preferenze condivise e lo visualizza come una normale notifica. L'utente dell'app vedrà la notifica esattamente come qualsiasi altra notifica push.

Notare che è preferibile gestire i casi in cui manca la proprietà dell'intestazione di autenticazione o di rifiuto da parte del back-end. La gestione specifica di questi casi dipende in larga misura dall'esperienza dell'utente di destinazione. Una delle opzioni consiste nel visualizzare una notifica con un prompt generico affinché l'utente possa autenticarsi per recuperare la notifica effettiva.

## Esecuzione dell'applicazione

Per eseguire l'applicazione, eseguire le operazioni seguenti:

1.  Assicurarsi che il progetto **AppBackend** sia distribuito in Azure. Se si usa Visual Studio, eseguire l'applicazione API Web **AppBackend**. Verrà visualizzata una pagina Web ASP.NET.

2.  In Eclipse eseguire l'app su un dispositivo Android fisico o sull'emulatore.

3.  Nell'interfaccia utente dell'app per Android immettere un nome utente e una password. Può trattarsi di qualsiasi stringa, ma devono avere lo stesso valore.

4.  Nell'interfaccia utente dell'app per Android fare clic su **Log in**. Fare clic su **Send push**.


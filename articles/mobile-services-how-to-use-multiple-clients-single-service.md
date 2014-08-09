<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerations for supporting multiple clients from a single mobile service" authors="krisragh" solutions="" manager="" editor="" />

Considerazioni relative al supporto di più client da un unico servizio mobile
=============================================================================

Uno dei principali vantaggi dell'utilizzo di Servizi mobili di Azure per supportare lo sviluppo di app per dispositivi mobili è la possibilità di utilizzare un unico servizio back-end che supporta un'app su più piattaforme client. Servizi mobili fornisce librerie client native per le principali piattaforme per dispositivi. Per ulteriori informazioni, vedere le [esercitazioni e risorse](/en-us/develop/mobile/resources/) disponibili.

Con Servizi mobili è facile eseguire la migrazione dell'app nativa su più piattaforme client con un unico servizio back-end, tuttavia occorre tenere presenti alcune considerazioni. In questo argomento vengono fornite indicazioni su come abilitare l'utilizzo delle notifiche push in tutte le piattaforme client in uso. Viene inoltre descritto come risolvere un problema di utilizzo del servizio Single-Sign-On guidato dal client tramite un account Microsoft nelle app per Windows Store e in quelle per Windows Phone. In questo argomento vengono infine indicate alcune procedure consigliate per riutilizzare il codice nei progetti di Visual Studio.

Notifiche push
--------------

In questa sezione vengono descritti due approcci per l'invio di notifiche push dal servizio mobile alle app client su più piattaforme.

### Utilizzo di Hub di notifica

Hub di notifica di Azure è un servizio di Azure che offre una soluzione scalabile per l'invio di notifiche push dal servizio mobile o da qualsiasi back-end alle app client su tutte le principali piattaforme per dispositivi. Per ulteriori informazioni, vedere [Hub di notifica di Azure](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/).

Hub di notifica fornisce un'infrastruttura coerente e unificata per la creazione e la gestione delle registrazioni dei dispositivi e per l'invio di notifiche push ai dispositivi in cui vengono eseguite le principali piattaforme per dispositivi. Per ulteriori informazioni, vedere [Introduzione ad Hub di notifica](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/). Hub di notifica supporta la registrazione di modelli specifici della piattaforma, per consentire l'utilizzo di un'unica chiamata dell'API per inviare una notifica all'app in esecuzione su qualsiasi piattaforma registrata. Per ulteriori informazioni, vedere [Utilizzo di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/).

Hub di notifica è la soluzione consigliata per l'invio di notifiche dal servizio mobile a più piattaforme client.

### Utilizzo di una tabella di registrazione comune e di un identificatore di piattaforma

Se si sceglie di non utilizzare Hub di notifica, è comunque possibile supportare le notifiche push per più client dal servizio mobile definendo un meccanismo di registrazione dei dispositivi comune. Utilizzare un'unica tabella per archiviare le informazioni specifiche del dispositivo utilizzate dai servizi delle notifiche push per la piattaforma supportata. Nelle esercitazioni del gruppo **Introduzione alle notifiche push** ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/)) viene utilizzata una tabella **Registrations** e l'URI del canale (Windows), il token del dispositivo (iOS) o il gestore (Android) viene registrato in una colonna denominata *handle*.

**Nota**

Quando si utilizza la procedura guidata Aggiungi notifica Push in Visual Studio 2013 per aggiungere notifiche push a un'app di Windows Store, viene creata automaticamente una tabella denominata **Channel** in cui memorizzare gli URI dei canali. Nell'esercitazione **Introduzione alle notifiche push in Visual Studio 2012** ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012)/[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012)) viene descritto come abilitare le notifiche push mediante la tabella **Registrations**.

Per supportare più client in un'unica tabella di registrazione, includere una colonna *platform* e impostare il campo sulla piattaforma del client inserendo una riga durante la registrazione. La definizione della classe **Registrations** seguente, ad esempio, appartenente a un'app C\# per Windows Store o Windows Phone, esegue il mapping tra il campo *ChannelUri* del client e la colonna *handle* della tabella Registrations:

     public class Registrations
        {
            [JsonProperty(PropertyName = "platform")]         
            public string Platform { 
                get
                {
                    return "windowsstore";
                    // return "windowsphone";
                }
                set {}
            }
            
            public string Id { get; set; }
        
            [JsonProperty(PropertyName = "handle")]
            public string ChannelUri { get; set; }
        }

Si noti che in questo dispositivo Windows il campo *Platform* restituisce sempre `windowsstore` (o `windowsphone`). Con lo schema dinamico abilitato (impostazione predefinita), Servizi mobili aggiunge una colonna relativa alla piattaforma alla tabella Registrations, se non è ancora presente. Per ulteriori informazioni, vedere [Schema dinamico](http://msdn.microsoft.com/it-it/library/windowsazure/jj193175.aspx).

Nello script del lato server che invia le notifiche, utilizzare il campo della piattaforma per determinare quale funzione specifica della piattaforma chiamare sull'[oggetto push](http://msdn.microsoft.com/it-it/library/windowsazure/jj554217.aspx). Lo script seguente è una versione modificata dello script del server delle esercitazioni del gruppo **Introduzione alle notifiche push** ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/)) per abilitare più piattaforme client:

     function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });
        
            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            if (registration.platform === 'winstore') {
                                push.wns.sendToastText04(registration.handle, {
                                    text1: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'winphone') {
                                push.mpns.sendFlipTile(registration.handle, {
                                    title: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'ios') {
                                push.apns.send(registration.handle, {
                                    alert: "Toast: " + item.text,
                                    payload: {
                                        inAppMessage: item.text
                                    }
                                });
                            } else if (registration.platform === 'android') {
                                push.gcm.send(registration.handle, item.text, {
                                    success: pushCompleted
                                });
                            } else {
                                console.error("Unknown push notification platform");
                            }
                        });
                    }
                });
            }
        
            function pushCompleted(pushResponse) {
                console.log("Sent push:", pushResponse);
            }
        }

Registrazione dell'app di Windows
---------------------------------

Per utilizzare l'autenticazione client Single-Sign-On tramite un account Microsoft nelle applicazioni di Windows Store e Windows Phone, è necessario innanzitutto registrare l'app di Windows Store nel dashboard di Windows Store. Dopo aver creato una registrazione Live Connect per Windows Phone non è infatti più possibile crearne una per Windows Store. Per ulteriori informazioni su come eseguire questa operazione, vedere l'argomento **Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect** ([Windows Store](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)).

Procedure consigliate per il riutilizzo di codice in progetti di Visual Studio
------------------------------------------------------------------------------

Le librerie di classi portabili consentono di scrivere e compilare assembly gestiti che funzionano su più piattaforme, come Windows Store e Windows Phone. È possibile creare classi contenenti codice da condividere tra vari progetti, quindi fare riferimento a tali classi da tipi di progetto diversi.

La libreria di classi portabile di .NET Framework consente di implementare il modello MVVM (Model-View-View Model) e condividere assembly su più piattaforme. È possibile implementare le classi di questo modello in un progetto di libreria di classi portabile in Visual Studio 2012, quindi creare viste personalizzate per piattaforme diverse. Il codice del modello, comune per tutte le piattaforme, può ad esempio recuperare i dati da un'origine come Servizi mobili di Azure in modo indipendente dalla piattaforma. MSDN Library include una [panoramica e un esempio](http://msdn.microsoft.com/it-it/library/gg597391(v=vs.110)), una descrizione delle [differenze tra le API](http://msdn.microsoft.com/it-it/library/gg597392(v=vs.110)), un esempio di [utilizzo della libreria di classi portabile con MVVM (Model-View-View Model)](http://msdn.microsoft.com/it-it/library/hh563947(v=vs.110)), ulteriori [istruzioni](http://msdn.microsoft.com/it-it/library/windowsphone/develop/jj714086(v=vs.105).aspx) e informazioni sulla [gestione delle risorse](http://msdn.microsoft.com/it-it/library/hh871422(v=vs.110)) nei progetti di libreria di classi portabile.


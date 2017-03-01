---
title: Come usare l&quot;API di Engagement in Windows Phone Silverlight
description: Come usare l&quot;API di Engagement in Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ae2ba2e8-f75b-4dee-a164-a7dd65d35a23
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8ed91048ecfb1e7d19bb000216618b4e0c4b381
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Come usare l'API di Engagement in Windows Phone Silverlight
Questo documento è complementare all'articolo [Come integrare Mobile Engagement in un'app per Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). Fornisce informazioni approfondite su come usare l'API di Engagement per segnalare le statistiche dell'applicazione.

Se si vuole impostare Engagement in modo che segnali solo le sessioni, le attività, gli arresti anomali e i dati tecnici dell'applicazione, la soluzione più semplice consiste nel fare in modo che tutte le sottoclassi `PhoneApplicationPage` ereditino dalla classe `EngagementPage`.

Se invece si hanno esigenze più complesse, ad esempio se è necessario segnalare eventi, errori e processi specifici dell'applicazione o presentare le attività dell'applicazione in modo diverso rispetto a quello implementato nelle classi `EngagementPage`, è necessario usare l'API di Engagement.

L'API di Engagement viene fornita dalla classe `EngagementAgent` . È possibile accedere a questi metodi tramite `EngagementAgent.Instance`.

Anche se il modulo dell'agente non è stato inizializzato, ogni chiamata all'API viene posticipata e verrà eseguita nuovamente quando l'agente è disponibile.

## <a name="engagement-concepts"></a>Concetti relativi a Mobile Engagement
Le parti seguenti approfondiscono le informazioni contenute nell'articolo "Concetti relativi ad Azure Mobile Engagement" per la piattaforma Windows Phone.

### <a name="session-and-activity"></a>`Session` e `Activity`
Un'*attività* è in genere associata a una pagina dell'applicazione, ovvero l'*attività* inizia quando la pagina viene visualizzata e si arresta quando la pagina viene chiusa. Questo avviene quando Engagement SDK è integrato mediante la classe `EngagementPage`.

Le *attività* possono tuttavia essere controllate anche manualmente usando l'API di Engagement. Ciò consente di dividere una determinata pagina in più sottoparti per ottenere maggiori dettagli sull'utilizzo della pagina (ad esempio per conoscere la frequenza e la durata dell'uso delle finestre di dialogo all'interno della pagina).

## <a name="reporting-activities"></a>Segnalazione di attività
### <a name="user-starts-a-new-activity"></a>L'utente inizia una nuova attività
#### <a name="reference"></a>riferimento
            void StartActivity(string name, Dictionary<object, object> extras = null)

È necessario chiamare `StartActivity()` ogni volta che l'attività dell'utente cambia. La prima chiamata a questa funzione avvia una nuova sessione utente.

> [!IMPORTANT]
> L'SDK chiama automaticamente il metodo EndActivity quando viene chiusa l'applicazione. Di conseguenza, è ALTAMENTE consigliato chiamare il metodo StartActivity ogni volta che l'attività dell'utente cambia e non chiamare MAI il metodo EndActivity poiché la chiamata di questo metodo forza la chiusura della sessione corrente.
> 
> 

#### <a name="example"></a>Esempio
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>L'utente termina l'attività corrente
#### <a name="reference"></a>riferimento
            void EndActivity()

È necessario chiamare `EndActivity()` almeno una volta quando l'utente termina la sua ultima attività. In questo modo, si indica all'SDK di Engagement che l'utente è attualmente inattivo e che la sessione utente deve essere chiusa allo scadere del timeout. Se si chiama `StartActivity()` prima dello scadere del timeout, la sessione rimane semplicemente attiva.

#### <a name="example"></a>Esempio
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>Segnalazione di processi
### <a name="start-a-job"></a>Avviare un processo
#### <a name="reference"></a>riferimento
            void StartJob(string name, Dictionary<object, object> extras = null)

È possibile utilizzare il processo per tenere traccia delle attività in un determinato periodo di tempo.

#### <a name="example"></a>Esempio
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Terminare un processo
#### <a name="reference"></a>riferimento
            void EndJob(string name)

Appena terminata un'attività di cui un processo tiene traccia, è necessario chiamare il metodo EndJob per questo processo, fornendo il nome del processo.

#### <a name="example"></a>Esempio
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>Segnalazione di eventi
Esistono tre tipi di eventi:

* Eventi autonomi
* Eventi di sessione
* Eventi di processo

### <a name="standalone-events"></a>Eventi autonomi
#### <a name="reference"></a>riferimento
            void SendEvent(string name, Dictionary<object, object> extras = null)

Gli eventi autonomi possono verificarsi all'esterno del contesto di una sessione.

#### <a name="example"></a>Esempio
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Eventi di sessione
#### <a name="reference"></a>riferimento
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Gli eventi di sessione vengono in genere usati per segnalare le azioni eseguite da un utente durante la sua sessione.

#### <a name="example"></a>Esempio
**Senza dati:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Con dati:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Eventi di processo
#### <a name="reference"></a>riferimento
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Gli eventi di processo in genere vengono utilizzati per segnalare le azioni eseguite da un utente durante un processo.

#### <a name="example"></a>Esempio
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>Segnalazione di errori
Esistono tre tipi di errori:

* Errori autonomi
* Errori di sessione
* Errori di processo

### <a name="standalone-errors"></a>Errori autonomi
#### <a name="reference"></a>riferimento
            void SendError(string name, Dictionary<object, object> extras = null)

Diversamente dagli errori di sessione, gli errori autonomi possono verificarsi all'esterno del contesto di una sessione.

#### <a name="example"></a>Esempio
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Errori di sessione
#### <a name="reference"></a>riferimento
            void SendSessionError(string name, Dictionary<object, object> extras = null)

Gli errori di sessione vengono in genere usati per segnalare gli errori che hanno impatto sull'utente durante la sua sessione.

#### <a name="example"></a>Esempio
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Errori di processo
#### <a name="reference"></a>riferimento
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Gli errori possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente.

#### <a name="example"></a>Esempio
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>Segnalazione di arresti anomali
L'agente fornisce due metodi per gestire gli arresti anomali.

### <a name="send-an-exception"></a>Inviare un'eccezione
#### <a name="reference"></a>riferimento
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Esempio
È possibile inviare un'eccezione in qualsiasi momento chiamando:

            EngagementAgent.Instance.SendCrash(aCatchedException);

È inoltre possibile utilizzare un parametro facoltativo per terminare la sessione di Engagement contemporaneamente all'invio dell'arresto anomalo. A tale scopo, chiamare:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

In questo caso la sessione e i processi verranno chiusi solo dopo l'invio dell'arresto anomalo.

### <a name="send-an-unhandled-exception"></a>Inviare un'eccezione non gestita
#### <a name="reference"></a>riferimento
            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement fornisce inoltre un metodo per inviare le eccezioni non gestite. Questa possibilità è particolarmente utile se utilizzata all'interno del gestore eventi UnhandledException Silverlight.

Questo metodo terminerà **SEMPRE** la sessione di Engagement e i processi dopo essere stato chiamato.

#### <a name="example"></a>Esempio
È possibile utilizzarlo per implementare il proprio gestore UnhandledException (specialmente se è stata disattivata la funzione di segnalazione automatica degli arresti anomali di Engagement). Ad esempio, nel metodo `Application_UnhandledException` del file `App.xaml.cs`:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code

              EngagementAgent.Instance.SendCrash(e);
            }

## <a name="onactivated"></a>OnActivated
### <a name="reference"></a>riferimento
            void OnActivated(ActivatedEventArgs e)

Quando l'utente procede con la navigazione, lontano da un'applicazione, una volta generato l'evento di disattivazione il sistema operativo tenterà di mettere l'applicazione in stato di inattività. Quindi, l'applicazione viene rimossa definitivamente. In questo processo viene terminata un'applicazione, ma alcuni dati relativi allo stato dell'applicazione e alle singole pagine all'interno dell'applicazione vengono mantenute.

È necessario inserire `EngagementAgent.Instance.OnActivated(e)` nel metodo `Application_Activated` dal file App.xaml.cs per reimpostare l'agente di Engagement quando l'applicazione è stata rimossa definitivamente.

### <a name="example"></a>Esempio
            // Inside your App.xaml.cs file

            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

## <a name="device-id"></a>Id dispositivo
            String GetDeviceId()

È possibile ottenere l'id del dispositivo Engagement chiamando questo metodo.

## <a name="extras-parameters"></a>Parametri aggiuntivi
Dati arbitrari possono essere collegati a un evento, un errore, un'attività o un processo. Tali dati possono essere strutturati utilizzando un dizionario. Chiavi e valori possono essere di qualsiasi tipo.

I dati aggiuntivi vengono serializzati, per cui se si desidera inserirvi il proprio tipo è necessario aggiungere un contratto dati per questo tipo.

### <a name="example"></a>Esempio
Si crea una nuova classe "Person".

            using System.Runtime.Serialization;

            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Quindi, si include un'istanza di `Person` per un dato aggiuntivo.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> Se si inseriscono altri tipi di oggetti, assicurarsi che il relativo metodo ToString() venga implementato per restituire una stringa leggibile.
> 
> 

### <a name="limits"></a>Limiti
#### <a name="keys"></a>Chiavi
Ogni chiave dell'oggetto deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensione
I dati aggiuntivi sono limitati a **1024** caratteri per chiamata.

## <a name="reporting-application-information"></a>Segnalazione di informazioni sull'applicazione
### <a name="reference"></a>riferimento
            void SendAppInfo(Dictionary<object, object> appInfos)

È possibile segnalare manualmente le informazioni di traccia o qualsiasi altra informazione specifica dell'applicazione mediante la funzione SendAppInfo().

Queste informazioni possono essere inviate in modo incrementale: viene mantenuto solo l'ultimo valore per una determinata chiave per ogni dispositivo specifico. Come per i dati aggiuntivi degli eventi, usare Dictionary\<object, object\> per allegare informazioni.

### <a name="example"></a>Esempio
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limiti
#### <a name="keys"></a>Chiavi
Ogni chiave dell'oggetto deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensione
Le informazioni sull'applicazione sono limitate a **1024** caratteri per chiamata.

Nell'esempio precedente il codice JSON inviato al server è lungo 44 caratteri:

            {"subscription":"2013-12-07","premium":"true"}

## <a name="logging"></a>Registrazione
### <a name="enable-logging"></a>Abilitare la registrazione
È possibile configurare SDK per generare log di test nella console IDE.
Questi log non sono abilitati per impostazione predefinita. Per eseguire una personalizzazione, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` scegliendo uno dei valori disponibili nell'enumerazione `EngagementTestLogLevel`, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();


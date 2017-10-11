---
title: Come usare l'API di Engagement in un'app di Windows universale
description: Come usare l'API di Engagement in un'app di Windows universale
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: bb501fca-9cfe-4495-81df-b5efd6e0137b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 75fc134a5535e6113331470cf61df9c06eb8e2ab
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-the-engagement-api-on-windows-universal"></a>Come usare l'API di Engagement in un'app di Windows universale
Questo documento è complementare all'articolo [Come integrare Engagement in un'app di Windows universale](mobile-engagement-windows-store-integrate-engagement.md)e fornisce informazioni approfondite su come usare l'API di Engagement per segnalare le statistiche dell'applicazione.

Tenere presente che, se si vuole impostare Engagement in modo che segnali solo le sessioni, le attività, gli arresti anomali e i dati tecnici dell'applicazione, la soluzione più semplice consiste nel fare in modo che tutte le sottoclassi `Page` ereditino dalla classe `EngagementPage`.

Se invece si hanno esigenze più complesse, ad esempio se è necessario segnalare eventi, errori e processi specifici dell'applicazione o presentare le attività dell'applicazione in modo diverso rispetto a quello implementato nelle classi `EngagementPage`, è necessario usare l'API di Engagement.

L'API di Engagement viene fornita dalla classe `EngagementAgent` . È possibile accedere a questi metodi tramite `EngagementAgent.Instance`.

Anche se il modulo dell'agente non è stato inizializzato, ogni chiamata all'API viene posticipata e verrà eseguita nuovamente quando l'agente è disponibile.

## <a name="engagement-concepts"></a>Concetti relativi a Mobile Engagement
Le parti seguenti approfondiscono le informazioni contenute nell'articolo [Concetti relativi ad Azure Mobile Engagement](mobile-engagement-concepts.md) per la piattaforma Windows universale.

### <a name="session-and-activity"></a>`Session` e `Activity`
Un'*attività* è in genere associata a una pagina dell'applicazione, ovvero l'*attività* inizia quando la pagina viene visualizzata e si arresta quando la pagina viene chiusa. Questo avviene quando Engagement SDK è integrato mediante la classe `EngagementPage`.

Le *attività* possono tuttavia essere controllate anche manualmente usando l'API di Engagement. Ciò consente di dividere una determinata pagina in più sottoparti per ottenere maggiori dettagli sull'uso della pagina (ad esempio per conoscere la frequenza e la durata dell'uso delle finestre di dialogo all'interno della pagina).

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

Vengono terminate l'attività e la sessione. Chiamare questo metodo solo se si è perfettamente consapevoli delle conseguenze.

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
Esistono tre tipi di errore:

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
            void SendCrash(Exception e)

Engagement fornisce anche un metodo per inviare le eccezioni non gestite, se è stata **DISABILITATA** la segnalazione automatica degli **arresti anomali** in Engagement. Questa possibilità è particolarmente utile se utilizzata all'interno del gestore eventi UnhandledException.

Questo metodo terminerà **SEMPRE** la sessione di Engagement e i processi dopo essere stato chiamato.

#### <a name="example"></a>Esempio
Consente di implementare un proprio gestore UnhandledExceptionEventArgs. Ad esempio, aggiungere il metodo `Current_UnhandledException` del file `App.xaml.cs`:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
            {
               EngagementAgent.Instance.SendCrash(e.Exception,false);
            }

In App.xaml.cs in "Public App(){}" aggiungere:

            Application.Current.UnhandledException += Current_UnhandledException;

## <a name="device-id"></a>Id dispositivo
            String EngagementAgent.Instance.GetDeviceId()

È possibile ottenere l'id del dispositivo Engagement chiamando questo metodo.

## <a name="extras-parameters"></a>Parametri aggiuntivi
Dati arbitrari possono essere collegati a un evento, un errore, un'attività o un processo. Tali dati possono essere strutturati utilizzando un dizionario. Chiavi e valori possono essere di qualsiasi tipo.

I dati aggiuntivi vengono serializzati, per cui se si desidera inserirvi il proprio tipo è necessario aggiungere un contratto dati per questo tipo.

### <a name="example"></a>Esempio
Si crea una nuova classe "Person".

            using System.Runtime.Serialization;

            namespace Microsoft.Azure.Engagement
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

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, numeri o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensione
I dati aggiuntivi sono limitati a **1024** caratteri per chiamata.

## <a name="reporting-application-information"></a>Segnalazione di informazioni sull'applicazione
### <a name="reference"></a>riferimento
            void SendAppInfo(Dictionary<object, object> appInfos)

È possibile segnalare manualmente le informazioni di traccia o qualsiasi altra informazione specifica dell'applicazione mediante la funzione SendAppInfo().

Questi dati possono essere inviati in modo incrementale: viene mantenuto solo l'ultimo valore per una determinata chiave per ogni dispositivo specifico. Come per le informazioni aggiuntive degli eventi, usare Dictionary\<object, object\> per allegare dati.

### <a name="example"></a>Esempio
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
              {
                {"birthdate", "1983-12-07"},
                {"gender", "female"}
              };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limiti
#### <a name="keys"></a>Chiavi
Ogni chiave dell'oggetto deve corrispondere all'espressione regolare seguente:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, numeri o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensione
Le informazioni sull'applicazione sono limitate a **1024** caratteri per chiamata.

Nell'esempio precedente il codice JSON inviato al server è lungo 44 caratteri:

            {"birthdate":"1983-12-07","gender":"female"}

## <a name="logging"></a>Registrazione
### <a name="enable-logging"></a>Abilitare la registrazione
È possibile configurare SDK per generare log di test nella console IDE.
Questi log non sono abilitati per impostazione predefinita. Per eseguire una personalizzazione, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` scegliendo uno dei valori disponibili nell'enumerazione `EngagementTestLogLevel`, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();


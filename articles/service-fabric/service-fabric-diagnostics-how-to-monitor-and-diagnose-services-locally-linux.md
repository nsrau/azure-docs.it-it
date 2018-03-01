---
title: Eseguire il debug di microservizi di Azure in Linux | Documentazione Microsoft
description: Informazioni su come eseguire il monitoraggio e la diagnosi dei servizi scritti usando Microsoft Azure Service Fabric in un computer di sviluppo locale.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 23222353e47128b213273cbf5bc570c937d5d9f7
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Le operazioni di monitoraggio, rilevamento, diagnosi e risoluzione dei problemi consentono ai servizi di continuare a funzionare con un'interruzione minima dell'esperienza utente. Il monitoraggio e la diagnostica sono essenziali in un ambiente di produzione distribuito reale. L'adozione di un modello simile durante lo sviluppo dei servizi garantisce il funzionamento della pipeline di diagnostica anche in un ambiente di produzione. Service Fabric consente agli sviluppatori di servizi di implementare facilmente un sistema di diagnostica in grado di operare senza problemi sia in ambienti di sviluppo costituiti da un unico computer locale sia in configurazioni con cluster di produzione veri e propri.


## <a name="debugging-service-fabric-java-applications"></a>Debug delle applicazioni Java di Service Fabric

Per le applicazioni Java sono disponibili [più framework di registrazione](http://en.wikipedia.org/wiki/Java_logging_framework) . Dato che `java.util.logging` è l'opzione predefinita con JRE, viene usato anche per gli [esempi di codice in GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  Di seguito viene illustrato come configurare il framework `java.util.logging` .

Usando java.util.logging è possibile reindirizzare i log dell'applicazione a memoria, flussi di output, file di console o socket. Nel framework sono disponibili gestori predefiniti per ognuna di queste opzioni. È possibile creare un file `app.properties` per configurare il gestore di file per l'applicazione in modo da reindirizzare tutti i log a un file locale.

Il frammento di codice seguente contiene una configurazione di esempio:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

La cartella a cui fa riferimento il file `app.properties` deve essere presente. Dopo la creazione del file `app.properties`, è anche necessario modificare lo script del punto di ingresso`entrypoint.sh`, nella cartella `<applicationfolder>/<servicePkg>/Code/` in modo da impostare la proprietà `java.util.logging.config.file` sul file `app.propertes`. La voce dovrebbe essere simile al frammento seguente:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Con questa configurazione, i log verranno raccolti a rotazione in `/tmp/servicefabric/logs/`. Il questo caso il file di log è denominato mysfapp%u.%g.log, dove:
* **%u** è un numero univoco per risolvere i conflitti tra processi simultanei di Java.
* **%g** è il numero di generazione per distinguere tra i log di rotazione.

Se non viene configurato in modo esplicito alcun gestore, per impostazione predefinita viene registrato il gestore della console. È possibile visualizzare i log in syslog, in /var/log/syslog.

Per altre informazioni, vedere gli [GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  


## <a name="debugging-service-fabric-c-applications"></a>Debug di applicazioni C# di Service Fabric


Sono disponibili vari framework per il tracciamento delle applicazioni CoreCLR in Linux. Per altre informazioni, vedere [GitHub: logging](http:/github.com/aspnet/logging) (Accesso a GitHub).  In questo articolo viene usato EventSource, già noto agli sviluppatori C#, per la traccia negli esempi CoreCLR in Linux.

Il primo passaggio consiste nell'includere System.Diagnostics.Tracing in modo da poter scrivere i log in m in memoria, flussi di output o file di console.  Per la registrazione tramite EventSource, aggiungere il seguente progetto a project.json:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

È possibile usare un EventListener personalizzato per l'ascolto dell'evento di servizio e quindi eseguire il reindirizzamento appropriato ai file di traccia. Il frammento di codice seguente mostra un esempio di implementazione della registrazione tramite EventSource e un EventListener personalizzato:


```csharp

 public class ServiceEventSource : EventSource
 {
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
   internal class ServiceEventListener : EventListener
   {

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
            using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))           
        { 
                 // report all event information               
         Out.Write(" {0} ",  Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                if (eventData.Message != null)              
            Out.WriteLine(eventData.Message, eventData.Payload.ToArray());              
            else             
        { 
                    string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                    Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");             
        }
           }
        }
    }
```


Il frammento di codice precedente restituisce i log a un file in `/tmp/MyServiceLog.txt`. Il nome del file deve essere aggiornato in modo appropriato. Se si desidera reindirizzare i log alla console, usare il frammento di codice seguente nella classe EventListener personalizzata:

```csharp
public static TextWriter Out = Console.Out;
```

Gli esempi in [C# Samples](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) (Esempio C#) usano EventSource e un EventListener personalizzato per registrare eventi in un file.



## <a name="next-steps"></a>Passaggi successivi
Lo stesso codice di traccia aggiunto all'applicazione potrà essere usato per la diagnostica dell'applicazione in un cluster di Azure. Consultare questi articoli che illustrano le diverse opzioni per gli strumenti e descrivono come configurarli.
* [Come raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-lad.md)

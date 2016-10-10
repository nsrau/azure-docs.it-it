<properties
   pageTitle="Monitorare e diagnosticare localmente servizi scritti con Azure Service Fabric | Microsoft Azure"
   description="Informazioni su come eseguire il monitoraggio e la diagnosi dei servizi scritti usando Microsoft Azure Service Fabric in un computer di sviluppo locale."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>  

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>  


# Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Le operazioni di monitoraggio, rilevamento, diagnosi e risoluzione dei problemi consentono ai servizi di continuare a funzionare con un'interruzione minima dell'esperienza utente. Il monitoraggio e la diagnostica sono essenziali in un ambiente di produzione distribuito reale. L'adozione di un modello simile durante lo sviluppo dei servizi garantisce il funzionamento della pipeline di diagnostica anche in un ambiente di produzione. Service Fabric consente agli sviluppatori di servizi di implementare facilmente un sistema di diagnostica in grado di operare senza problemi sia in ambienti di sviluppo costituiti da un unico computer locale sia in configurazioni con cluster di produzione veri e propri.


## Debug delle applicazioni Java di Service Fabric

Per le applicazioni Java sono disponibili [più framework di registrazione](http://en.wikipedia.org/wiki/Java_logging_framework). Dato che `java.util.logging` è l'opzione predefinita con JRE, viene usato anche per gli [esempi di codice in GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started). Di seguito viene illustrato come configurare il framework `java.util.logging`.
 
Con java.util.logging è possibile reindirizzare i log delle applicazioni a flussi di output, memoria, file di console o socket. Nel framework sono disponibili gestori predefiniti per ognuna di queste opzioni. È possibile creare un file `app.properties` per configurare il gestore di file per l'applicazione in modo da reindirizzare tutti i log a un file locale.

Il frammento di codice seguente contiene una configurazione di esempio:

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

La cartella a cui fa riferimento il file `app.properties` deve essere presente. Dopo la creazione del file `app.properties`, è anche necessario modificare lo script del punto di ingresso, `entrypoint.sh`, nella cartella `<applicationfolder>/<servicePkg>/Code/` in modo da impostare la proprietà `java.util.logging.config.file` sul file `app.propertes`. La voce dovrebbe essere simile al frammento seguente:

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
Con questa configurazione, i log verranno raccolti a rotazione in `/tmp/servicefabric/logs/`. **%u** e **%g** consentono la creazione di più file, con i nomi file mysfapp0.log, mysfapp1.log e così via e mysfapplog0.log, mysfapp1.log e così via. Se non viene configurato in modo esplicito alcun gestore, per impostazione predefinita viene registrato il gestore della console. È possibile visualizzare i log in syslog, in /var/log/syslog.
 
Per altre informazioni, vedere gli [GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).



## Passaggi successivi
Lo stesso codice di traccia aggiunto all'applicazione potrà essere usato per la diagnostica dell'applicazione in un cluster di Azure. Leggere questi articoli che illustrano le diverse opzioni per gli strumenti e descrivono come configurarle.
* [Come raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-lad.md)

<!---HONumber=AcomDC_0928_2016-->
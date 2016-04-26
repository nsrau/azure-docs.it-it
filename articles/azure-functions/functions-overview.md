<properties
   pageTitle="Panoramica di Funzioni di Azure | Microsoft Azure"
   description="Informazioni su come Funzioni di Azure possa ottimizzare carichi di lavoro asincroni creando semplici funzioni che possono essere scritte in pochi minuti."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/09/2016"
   ms.author="cfowler;mahender"/>
   
   
# Panoramica di Funzioni di Azure

## Creazione rapida di funzioni

Scrivere una funzione in meno di un minuto, ad esempio quando è necessario un processo semplice per pulire un database o per compilare una funzionalità che elabori milioni di messaggi provenienti dai dispositivi connessi. Usare il linguaggio di sviluppo preferito (C#, Node.JS, Python e altri). Pagare solo per il tempo in cui il codice viene eseguito ed essere certi che Azure venga ridimensionato in base alle esigenze.

Funzioni di Azure è una soluzione per eseguire facilmente piccole parti di codice, o "funzioni", nel cloud. È possibile scrivere solo il codice necessario per il problema effettivo, senza preoccuparsi di un'intera applicazione o dell'infrastruttura necessaria per eseguirlo. In questo modo lo sviluppo può essere ancora più produttivo ed è possibile [iniziare a usare la prima funzione](functions-create-first-azure-function.md) in pochi minuti.

## Quali operazioni si possono eseguire con Funzioni?

Funzioni di Azure è un'ottima soluzione per l'elaborazione di dati e l'integrazione di sistemi, l'uso di IoT (Internet delle cose) e la compilazione di semplici API e microservizi.

Funzioni include una raccolta di modelli per scenari comuni, tra cui:

* Risposta a una richiesta di un webhook GitHub
* Ridimensionamento di un'immagine caricata in Archiviazione di Azure
* Uso di code di elaborazione ordini
* E altro ancora 

Per informazioni più approfondite su Funzioni e per alcune configurazioni di esempio, vedere [Guida di riferimento per gli sviluppatori](functions-reference.md).

## Funzionalità

Funzioni di Azure è una piattaforma aziendale completa, dotata di funzionalità che semplificano anche le attività di integrazione e connettività più complesse. Con questo set di funzionalità di base, gli sviluppatori, usando Funzioni di Azure, possono aumentare ulteriormente la produttività concentrandosi solo sul vero obiettivo invece di assemblare un'intera infrastruttura.

In Funzioni di Azure sono incluse le funzionalità seguenti:
    
* **Scelta del linguaggio**: scrivere funzioni con C#, Node.js, Python, F#, PHP, batch, bash, Java o un eseguibile.  
* **Modello di determinazione prezzi in base al consumo**: pagare solo per il tempo impiegato per l'esecuzione del codice. Vedere più avanti l'opzione relativa al piano di servizio app dinamico nella [sezione sui prezzi](#pricing).  
* **Trasferimento delle dipendenze**: poiché Funzioni supporta NuGet e NPM, è possibile usare le proprie librarie preferite.  
* **Sicurezza integrata**: proteggere le funzioni attivate da HTTP con provider OAuth, ad esempio Azure Active Directory, Facebook, Google, Twitter e account Microsoft.  
* **Integrazione senza codice**: sfruttare facilmente i servizi di Azure e le offerte di software come un servizio (SaaS). Per alcuni esempi, vedere più avanti la [sezione sulle integrazioni](#integrations).  
* **Sviluppo flessibile**: modificare le funzioni con un editor nel portale o configurare l'integrazione continua e distribuire il codice con GitHub, Visual Studio Team Services e altro.  
* **Open source**: Funzioni è open source e [disponibile su GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

### <a name="integrations"></a>Integrazioni

Azure Functions supporta svariate integrazioni con servizi di Azure e di terze parti. È possibile usarli per attivare la funzione e avviare l'esecuzione o come input e output per il codice. La tabella seguente illustra alcune integrazioni di esempio supportate da Funzioni di Azure.

[AZURE.INCLUDE [Calcolo dinamico](../../includes/functions-bindings.md)]

## <a name="pricing"></a>Quanto costa Funzioni?

È possibile eseguire Funzioni di Azure in due modi: con un piano di servizio app dinamico e con un piano di servizio app classico.

In un **piano di servizio app dinamico** non ci si dovrà preoccupare della gestione risorse. Quando la funzione viene eseguita, Azure fornisce sempre tutte le risorse di calcolo necessarie. Si paga solo per il tempo impiegato per l'esecuzione del codice. I dettagli completi sui prezzi sono disponibili nella [pagina Prezzi di Funzioni](/pricing/details/functions).

Un **piano di servizio app classico** consente di eseguire funzioni esattamente come le app Web, per dispositivi mobili e per le API. È un'ottima soluzione se si usa già il servizio app per altre applicazioni, perché le funzioni possono essere eseguite nello stesso piano senza costi aggiuntivi. I dettagli completi sono disponibili nella pagina [Prezzi di Servizio app](/pricing/details/app-service/).

## Segnalazione di problemi

[AZURE.INCLUDE [Segnalazione di problemi](../../includes/functions-reporting-issues.md)]

<!---HONumber=AcomDC_0420_2016-->
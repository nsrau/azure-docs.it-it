<properties
    pageTitle="Elenco dei connettori e delle app per le API disponibili | Microsoft Azure App Service"
    description="Informazioni sui connettori e le app per le API in Azure App Service"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>



# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Elenco di connettori e app per le API da usare nelle app per la logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica. Per la versione di disponibilità generale delle app per la logica, vedere [Elenco di connettori](../connectors/apis-list.md).

Informazioni su tutti i connettori disponibili e le app per le API create da Microsoft che è possibile usare nelle app per la logica.

Per informazioni sui prezzi e un elenco di ciò che è incluso in ogni livello di servizio, vedere le [Informazioni sui prezzi di Azure App Service](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Per iniziare a provare le app per la logica di Azure prima di iscriversi per ottenere un account Azure, visitare [Crea un'app del Servizio app di Azure](https://tryappservice.azure.com/?appservice=logic). È possibile creare immediatamente un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## <a name="core-connectors"></a>Connettori Core
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft, disponibili come Connettori Core:

Nome | Description
--- | ---
[Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Utilizzare Bing per tradurre il testo in un'altra lingua.
[HTTP](app-service-logic-connector-http.md) | Il listener HTTP apre un endpoint che funge da server HTTP e resta in ascolto delle richieste HTTP o HTTPS in ingresso. L'azione HTTP non richiede un'app per le API ed è supportata in modo nativo all'interno delle app per la logica.
[Slack](app-service-logic-connector-slack.md) | Si connette a Slack e invia messaggi ai canali Slack.


## <a name="enterprise-integration-connectors"></a>Connettori di integrazione aziendale
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft disponibili come Connettori di integrazione aziendale:

Nome  | Descrizione
------------- | -------------
[BizTalk Rules](app-service-logic-use-biztalk-rules.md) | Usa le regole BizTalk per definire e controllare la logica di business all'interno di un'organizzazione. I criteri di business possono essere aggiornati senza ricompilare e ridistribuire le applicazioni associate.
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | Cerca ed estrae dati dal contenuto XML in base a un'espressione XPath specificata.
[DB2](app-service-logic-connector-db2.md) | Si connette a un database IBM DB2, in locale e in una macchina virtuale di Azure che esegue un sistema operativo Windows. Può mappare le operazioni API Web e API OData ai comandi Informix Structured Query Language. <br/><br/>Nessun trigger. Le azioni includono la selezione, l'inserimento, l'aggiornamento e l'eliminazione di tabelle, nonché istruzioni personalizzate<br/><br/>Questo connettore include anche il client Microsoft per DRDA per la connessione a un server Informix con una rete TCP/IP.
[File](app-service-logic-connector-file.md) | Usando questo connettore, è possibile connettersi al file system o rete locale e completare varie attività sui file, come ad esempio, caricare, eliminare, elencare file e molto altro.
[Informix](app-service-logic-connector-informix.md) | Si connette a un database IBM Informix, in locale e in una macchina virtuale di Azure che esegue un sistema operativo Windows. Può mappare le operazioni API Web e API OData ai comandi Informix Structured Query Language.<br/><br/>Nessun trigger. Le azioni includono la selezione, l'inserimento, l'aggiornamento e l'eliminazione di tabelle, nonché istruzioni personalizzate.<br/><br/>Se l'uso è in locale, è possibile usare VPN o Azure ExpressRoute. Questo connettore include anche un client Microsoft per DRDA per la connessione a un server Informix mediante una rete TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Si connette a un database di SQL Server locale o a un database SQL di Azure. È possibile creare, aggiornare, ottenere ed eliminare le voci in una tabella di database SQL.
MQ | Si connette a un database IBM WebSphere MQ Server versione 8, in locale e in una macchina virtuale di Azure che esegue un sistema operativo Windows. Se l'uso è in locale, è possibile usare VPN o Azure ExpressRoute. Il connettore include anche il client Microsoft per MQ.<br/><br/>Nessun trigger. Nessuna azione.<br/><br/>**Nota** : non può essere attualmente usato con app per la logica.

## <a name="connectors-as-triggers"></a>Connettori come trigger
Diversi connettori forniscono i trigger per le app per la logica. Tali trigger sono di due tipi:

1. Trigger di polling: questi trigger eseguono il polling del servizio a una frequenza specificata per verificare la disponibilità di nuovi dati. Quando sono disponibili nuovi dati, viene eseguita una nuova istanza dell'app per la logica con i dati come input. Per evitare che gli stessi dati vengano usati più volte, il trigger può eseguire pulire i dati letti e passati all'app per la logica. Esempi di tali connettori sono File, SQL e Archiviazione di Azure.
2. Trigger di push: tali trigger restano in ascolto di dati su un endpoint oppure di un evento, quindi attivano una nuova istanza dell'app per la logica. Esempi di tali connettori sono il listener HTTP e Twitter.

## <a name="connectors-as-actions"></a>Connettori come azioni
È anche possibile usare i connettori come azioni nell'ambito dell'app per la logica. Le azioni sono utili per cercare nell'app per la logica i dati da usare nell'esecuzione. Può essere necessario ad esempio cercare in un database SQL altre informazioni su un cliente quando si elabora un ordine. In alternativa, potrebbe essere necessario scrivere, aggiornare o eliminare dati in una destinazione. A tale scopo, usare le azioni fornite dai connettori. Le azioni vengono mappate a operazioni nelle app per le API (come definito dai relativi metadati Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Creare connettori e app per le API personalizzati
[Connectors and API Apps Reference](http://aka.ms/appservicesconnectorreference) (Informazioni di riferimento su connettori e app per le API)  
[Trigger delle app per le API del servizio app di Azure](../app-service-api/app-service-api-dotnet-triggers.md)  
[Informazioni di riferimento sulle app per la logica](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Altre informazioni su connettori e app per le API
[Informazioni sui connettori e sulle app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)  
[Uso di Gestione connessione ibrida nel servizio app di Azure](app-service-logic-hybrid-connection-manager.md)  
[Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md)



<!--HONumber=Oct16_HO2-->



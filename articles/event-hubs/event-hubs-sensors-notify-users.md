<properties 
   pageTitle="Inviare notifiche agli utenti riguardo ai dati ricevuti da sensori o da altri sistemi | Microsoft Azure"
   description="Descrive come usare Hub eventi per inviare notifiche agli utenti riguardo ai dati ricevuti da sensori."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# Inviare notifiche agli utenti riguardo ai dati ricevuti da sensori o da altri sistemi

Si supponga di disporre di un'applicazione che consente di monitorare dati in tempo reale o di generare report in base a una pianificazione. Se si osserva il sito Web in cui vengono visualizzati i grafici o i report in tempo reale, si potrebbe notare una situazione che richiede un intervento da parte dell'utente. Potrebbe essere utile ricevere un avviso nel caso in cui si verifichino tali situazioni, anziché doversi ricordare di controllare il sito Web. Si supponga di avere una serra con una lampada da coltivazione e che sia necessario sapere immediatamente se la luce della lampada si spegne. A tale scopo è possibile ad esempio collocare un sensore di luminosità nella serra e fare in modo che venga inviato un messaggio di posta elettronica se la luce si spegne.

![][1]

In un altro scenario, si supponga di gestire una pensione per animali domestici e di dover essere informati quando le scorte stanno per esaurirsi. Ad esempio, si potrebbe fare in modo che venga inviato un SMS dal sistema ERP se le scorte di cibo per cani raggiungono un livello critico.

![][2]

Il problema è come ottenere informazioni importanti quando vengono soddisfatte determinate condizioni e non quando si ha la possibilità di controllare un report statico. Se si usa un [hub eventi di Azure][] o un [hub IoT di Azure][] per ricevere dati da dispositivi o applicazioni aziendali come [Dynamics AX][], sono disponibili diverse opzioni per l'elaborazione di tali informazioni. È possibile visualizzarle in un sito Web, analizzarle, archiviarle e usarle per attivare i comandi per eseguire un'operazione. A tale scopo è possibile usare strumenti efficaci come [Siti Web di Azure][], [SQL Azure][], [HDInsight][], [Cortana Intelligence Suite][], [IoT Suite][], [App per la logica][] o [Hub di notifica di Azure][]. A volte tuttavia si desidera solo inviare i dati a un utente con un overhead minimo. Per effettuare questa operazione semplicemente con un codice, è disponibile un nuovo esempio, [AppToNotifyUsers][]. Le opzioni incluse sono messaggi di posta elettronica (SMTP), SMS e telefono.

## Struttura dell'applicazione

L'applicazione viene scritta in C# e il file leggimi dell'esempio contiene tutte le informazioni necessarie per modificare, compilare e pubblicare l'applicazione. Le sezioni seguenti contengono una panoramica generale delle funzionalità dell'applicazione.

Si presuppone che siano presenti eventi critici di cui viene eseguito il push a un Hub eventi di Azure o a un hub IoT. È possibile usare qualsiasi hub, purché si abbia accesso ad esso e si conosca la stringa di connessione.

Se non si ha già un Hub eventi o un hub IoT, è possibile impostare facilmente un ambiente di prova con una scheda Arduino e un Raspberry Pi, seguendo le istruzioni nel progetto [Connect The Dots](https://github.com/Azure/connectthedots). Il sensore di luminosità nella scheda Arduino invia i dati sui livelli di illuminazione tramite il Pi a un [Hub eventi di Azure][] \(**ehdevices**) e un processo di [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/) esegue il push degli avvisi a un secondo hub eventi (**ehalerts**), se i livelli di luminosità ricevuti scendono al di sotto di un determinato livello.

All'avvio, **AppToNotify** legge un file di configurazione (App.config) per ottenere l'URL e le credenziali dell'Hub eventi che riceve gli avvisi. Genera quindi un processo per monitorare costantemente quell'Hub eventi per ogni messaggio che arriva. Se si ha accesso all'URL dell'Hub eventi o dell'hub IoT e si dispone di credenziali valide, il codice di lettura dell'Hub eventi leggerà continuamente le informazioni in arrivo. Durante l'avvio, l'applicazione legge anche l'URL e le credenziali del servizio di messaggistica (posta elettronica, SMS, telefono) che si vuole usare e il nome o l'indirizzo del mittente, nonché un elenco di destinatari.

Quando il monitoraggio di Hub eventi rileva un messaggio, attiva un processo che invia il messaggio usando il metodo specificato nel file di configurazione. Si noti che viene inviato ogni messaggio rilevato. Se si imposta il monitoraggio in modo che punti a un Hub eventi che riceve dieci messaggi al secondo, il mittente invierà dieci messaggi al secondo, ovvero dieci messaggi di posta elettronica, dieci SMS o dieci chiamate al secondo. Per questo motivo, è necessario assicurarsi di monitorare un Hub eventi che riceve solo gli avvisi che devono essere inviati e non un Hub eventi che riceve tutti i dati non elaborati dai sensori o dalle applicazioni.

## Applicabilità

Il codice in questo esempio illustra solo come monitorare l'Hub eventi e come chiamare i servizi di messaggistica esterni se si vuole aggiungere questa funzionalità all'applicazione. Si noti che si tratta di una soluzione fai da te, destinata solo agli sviluppatori. La soluzione non soddisfa i requisiti aziendali, ad esempio ridondanza, failover, riavvio in caso di errore e così via. Per soluzioni di produzione più complete, vedere quanto segue:

- Uso dei connettori o delle notifiche push tramite il servizio [App per la logica di Azure](../app-service-logic/app-service-logic-connectors-list.md).
- Uso di [Hub di notifica di Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx), come descritto nel blog relativo alla [trasmissione di notifiche push a milioni di dispositivi mobili tramite Hub di notifica di Azure](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs).

## Passaggi successivi

La creazione di un semplice servizio di notifica che invii messaggi di posta elettronica o SMS o che chiami i destinatari per inoltrare i dati ricevuti da un Hub eventi o da un hub IoT è un'operazione piuttosto semplice. Per distribuire la soluzione per l'invio di notifiche agli utenti in base ai dati ricevuti dagli hub, visitare [AppToNotifyUsers][].

Per altre informazioni sugli hub, vedere gli articoli seguenti:

- [Hub eventi di Azure]
- [Hub IoT Azure]
- Iniziare con un’[esercitazione di Hub eventi].
- Un'[applicazione di esempio completa che usa Hub eventi].
- Una [soluzione di messaggistica accodata] che usa le code di Bus di servizio.

Per distribuire la soluzione per l'invio di notifiche agli utenti in base ai dati ricevuti dagli hub, visitare:

- [AppToNotifyUsers][]

[esercitazione di Hub eventi]: event-hubs-csharp-ephcs-getstarted.md
[Hub IoT Azure]: https://azure.microsoft.com/services/iot-hub/
[hub IoT di Azure]: https://azure.microsoft.com/services/iot-hub/
[Hub eventi di Azure]: https://azure.microsoft.com/services/event-hubs/
[hub eventi di Azure]: https://azure.microsoft.com/services/event-hubs/
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[soluzione di messaggistica accodata]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Siti Web di Azure]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Intelligence Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[App per la logica]: https://azure.microsoft.com/services/app-service/logic/
[Hub di notifica di Azure]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png

<!---HONumber=AcomDC_0831_2016-->
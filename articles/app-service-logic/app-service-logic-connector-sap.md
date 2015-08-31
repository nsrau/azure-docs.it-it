<properties
   pageTitle="Connettore SAP"
   description="Come usare il connettore SAP"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/19/2015"
   ms.author="sameerch"/>


# Connettore SAP #

I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un flusso. Esistono scenari in cui può essere necessario usare un sistema SAP installato in locale e protetto da un firewall. Inserendo SAP Connector nel flusso è possibile ottenere un'ampia gamma di scenari. Alcuni esempi:

1.	Esporre una sezione dei dati presenti nell'ambiente SAP tramite un front-end utente mobile o Web.
2.	Pubblicare i dati in SAP dopo l'elaborazione
3.	Estrarre dati da SAP per l'uso in un processo aziendale

Per questi scenari è necessario quanto segue:

1. Creare un'istanza dell'app per le API SAP Connector
2. Stabilire la connettività ibrida per la comunicazione tra l'app per le API e il sistema SAP locale
3. Usare l'app per le API creata in un'app per la logica per ottenere il processo aziendale desiderato

## Creare un'istanza dell'app per le API SAP Connector ##

Per usare il connettore SAP è necessario creare un'istanza dell'app per le API 'SAP Connector'. A questo scopo, procedere come segue:

1. Aprire Azure Marketplace con l'opzione '+ NUOVO' in basso a sinistra nel portale di Azure.
2. Andare a "Web e dispositivi mobili > App per le API" e cercare "SAP Connector".
3. Configurare l'app come segue:
	1. Inserire i dettagli generici, come nome, piano di servizio app e così via nel primo pannello

	2. In Impostazioni pacchetto inserire le credenziali SAP. Fornire inoltre una stringa di connessione per il bus di servizio di Azure, che verrà usata per stabilire la connettività ibrida con il sistema SAP locale.

	3. RFC, TRFC, BAPI e IDOC vanno configurati in base allo scenario desiderato. Se è necessario specificare più valori, separarli con virgole

![][1]

## Configurare l'app per le API SAP Connector creata ##

Individuare l'applicazione API creata scegliendo Sfoglia -> App per le API -> <Name of the API App just created>. Verrà visualizzato il comportamento seguente. La configurazione è incompleta, in quanto la connessione ibrida non è stata ancora stabilita.

![][2]

Il connettore SAP richiede la connettività ibrida per la connessione a *qualsiasi* endpoint SAP. Per stabilire la connettività ibrida eseguire le operazioni seguenti:

1. Copiare la stringa di connessione primaria
2. Fare clic sul collegamento 'Scarica e configura'
3. Seguire il processo di installazione che viene avviato e, quando viene richiesto, fornire la stringa di connessione primaria
4. Al termine del processo di installazione verrà visualizzata una finestra di dialogo simile alla seguente

![][3]

A questo punto, quando si passa nuovamente all'app per le API creata, lo stato della connessione ibrida visualizzato sarà Connected.

![][4]

Nota: per passare alla stringa di connessione secondaria è sufficiente ripetere la configurazione della connessione ibrida e inserire la stringa di connessione secondaria al posto della stringa di connessione primaria.

## Utilizzo in un'app per la logica ##

Il connettore SAP può essere usato come azione/passaggio solo in un'app per la logica.

Durante la creazione/modifica di un'app per la logica, scegliere l'app per le API SAP Connector creata in precedenza. Verranno elencate tutte le azioni consentite tra cui scegliere.

![][5]

Selezionando un'azione verranno elencati i relativi parametri di input. Fornire i valori appropriati e fare clic sull'icona con il segno di spunta.

![][6]

L'azione o il passaggio verrà ora visualizzato come configurato nell'app per la logica. Verranno visualizzati gli output dell'operazione, che possono essere usati come input in un passaggio successivo.

![][7]

Completare l'app per la logica per definire il processo aziendale e quindi eseguirla per raggiungere lo scopo desiderato.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=August15_HO8-->
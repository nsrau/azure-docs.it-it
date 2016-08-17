<properties
   pageTitle="Uso del connettore SAP nelle app per la logica | Servizio app di Microsoft Azure"
   description="Come creare e configurare l'app per le API o il connettore SAP e usarlo in un'app per la logica nel servizio app di Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="sameerch"/>


# Iniziare a usare il connettore SAP e aggiungerlo all'app per la logica

>[AZURE.NOTE] Il connettore è in corso di aggiornamento sulla base del feedback dei clienti. Questa pagina verrà aggiornata quando il connettore sarà pronto. Non è consigliabile usare il connettore SAP prima che sia disponibile la nuova versione. Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

Connettersi a SAP locale per chiamare RFC oppure ottenere i metadati. Esistono scenari in cui può essere necessario usare un sistema SAP installato in locale e protetto da un firewall. Inserendo il connettore SAP nel flusso è possibile ottenere un'ampia gamma di scenari. Alcuni esempi:

1.	Esporre una sezione dei dati presenti nell'ambiente SAP tramite un front-end utente mobile o Web.
2.	Pubblicare i dati in SAP dopo l'elaborazione
3.	Estrarre dati da SAP per l'uso in un processo aziendale

I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un flusso. È possibile aggiungere il connettore SAP al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.


Per questi scenari è necessario quanto segue:

1. Creare un'istanza dell'app per le API del connettore SAP
2. Stabilire la connettività ibrida per la comunicazione tra l'app per le API e il sistema SAP locale
3. Usare l'app per le API creata in un'app per la logica per ottenere il processo aziendale desiderato


## Creare un'istanza dell'app per le API del connettore SAP ##

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "SAP Connector", selezionarlo e fare clic su **Crea**.
3. Configurare l'app come segue:
	1. Inserire i dettagli generici, come nome, piano di servizio app e così via nel primo pannello

	2. In Impostazioni pacchetto inserire le credenziali SAP. Fornire inoltre una stringa di connessione per il bus di servizio di Azure, che viene usata per stabilire la connettività ibrida con il sistema SAP locale.

	3. RFC, TRFC, BAPI e IDOC vanno configurati in base allo scenario desiderato. Se è necessario specificare più valori, separarli con virgole

![][1]

## Configurare l'app per le API del connettore SAP creata ##

Trovare l'app per le API creata scegliendo Sfoglia -> App per le API -> Nome dell'app per le API appena creata. Viene visualizzato il comportamento seguente. La configurazione è incompleta, in quanto la connessione ibrida non è stata ancora stabilita:  
![][2]

Il connettore SAP richiede la connettività ibrida per la connessione a *qualsiasi* endpoint SAP. Per stabilire la connettività ibrida seguire questa procedura:

1. Copiare la stringa di connessione primaria
2. Fare clic sul collegamento 'Scarica e configura'
3. Seguire il processo di installazione che viene avviato e, quando viene richiesto, fornire la stringa di connessione primaria
4. Dopo aver completato il processo di installazione viene visualizzata una finestra di dialogo simile alla seguente:  
![][3]

Per altre informazioni, vedere [Integrazione con un server SAP locale](app-service-logic-integrate-with-an-on-premise-sap-server.md).

A questo punto, quando si passa nuovamente all'app per le API creata, lo stato della connessione ibrida visualizzato sarà Connected:  
![][4]

Nota: per passare alla stringa di connessione secondaria è sufficiente ripetere la configurazione della connessione ibrida e inserire la stringa di connessione secondaria al posto della stringa di connessione primaria.

## Utilizzo in un'app per la logica ##

Il connettore SAP può essere usato come azione/passaggio solo in un'app per la logica.

Durante la creazione/modifica di un'app per la logica, scegliere l'app per le API del connettore SAP creata in precedenza. Vengono elencate tutte le azioni consentite tra cui scegliere:  
![][5]

Selezionando un'azione vengono elencati i relativi parametri di input. Fornire i valori appropriati e fare clic sull'icona con il segno di spunta:  
![][6]

L'azione/passaggio ora viene visualizzato come configurato nell'app per la logica. Verranno visualizzati gli output dell'operazione, che possono essere usati come input in un passaggio successivo:  
![][7]

Completare l'app per la logica per definire il processo aziendale e quindi eseguirla per raggiungere lo scopo desiderato.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare le app per la logica di Azure prima di registrarsi per ottenere un account Azure, passare a [Prova l'app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare immediatamente un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

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

<!---HONumber=AcomDC_0803_2016-->
<properties
   pageTitle="Uso del listener e del connettore HTTP nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore del listener e dell'azione HTTP e usarlo in un'app per la logica in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="prkumar"/>


# Uso del listener e dell'azione HTTP e aggiunta all'app per la logica
Connettersi direttamente alle risorse HTTP per rimanere in attesa delle richieste HTTP e configurare le richieste Web HTTP. In alcuni scenari può essere necessario usare connessioni HTTP dirette, ad esempio:

1.	Per sviluppare un'app per la logica che supporta un front-end interattivo utente mobile o Web.
2.	Per ottenere ed elaborare i dati da un servizio Web privo di un connettore predefinito.
3.	Per eseguire azioni già esposte come servizio Web, ma non disponibili come app per le API.

Per questi scenari, sono disponibili due opzioni:

1. **Listener HTTP**: questo connettore funge da trigger ed è in ascolto delle richieste HTTP su un endpoint configurato. Quando viene ricevuta una chiamata sull'endpoint configurato, attiva una nuova istanza del flusso e passa i dati ricevuti nella richiesta al flusso per l'elaborazione. Può anche essere configurato per rispondere automaticamente alla richiesta in arrivo quando il flusso è stato avviato o per consentire la costruzione di una risposta in base all'esecuzione del flusso e l'invio di una risposta al chiamante.
2. **Azione HTTP**: consente di configurare una richiesta Web a un endpoint disponibile su Internet, ottiene una risposta e la rende disponibile per le azioni aggiuntive nel flusso per l'utilizzo.

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. È possibile aggiungere il connettore HTTP al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Creazione di un listener HTTP per l'app per la logica
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "HTTP", selezionare Listener HTTP, quindi fare clic su **Crea**.
3.	Configurare il listener HTTP nel modo seguente: ![][1]

4.	Quando si configurano le impostazioni del pacchetto, verrà visualizzata l'opzione seguente che indica se il listener deve rispondere automaticamente o richiedere all'utente di inviare una risposta esplicita. Impostarla su **False** per inviare la propria risposta: ![][2]

5.	Fare clic su **OK** per crearla.
6.	Dopo avere creato l'istanza dell'app per le API, aprire le impostazioni per configurare la sicurezza. Il listener HTTP supporta attualmente l'autenticazione di base. È possibile configurarla usando l'opzione Sicurezza quando si apre il listener HTTP: ![][3]
  
	**Problema noto** *Le impostazioni di sicurezza mostrano "Nessuno" come valore predefinito, ma il valore non è stato definito. È necessario modificare l'impostazione in Base e quindi di nuovo in Nessuno prima di salvarla, in modo da assicurare che il listener HTTP sia configurato correttamente.*

7. Configurare infine le impostazioni di sicurezza dell'app per le API su Pubblico (anonimo) per consentire ai client esterni di accedere all'endpoint. Questa impostazione è disponibile in "Tutte le impostazioni > Impostazioni applicazione" dell'app per le API del listener HTTP: ![][10]

A questo punto è possibile creare un'app per la logica per usare il listener HTTP.

## Uso del listener HTTP nell'app per la logica
Dopo aver creato l'app per le API, è possibile usare il listener HTTP come trigger per l'app per la logica. A questo scopo, è necessario:

4.	Creare una nuova app per la logica.
5.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso. Il listener HTTP è elencato nella raccolta. Selezionarlo.
6.	A questo punto, è possibile impostare il metodo HTTP e il relativo URL per richiedere al listener di attivare il flusso: ![][4] ![][5]

7.	Per ottenere l'URI completo, fare doppio clic sul listener HTTP per visualizzarne le impostazioni di configurazione e copiare l'URL dell'"Host" dell'app per le API: ![][6]
8.	A questo punto, è possibile usare i dati ricevuti nella richiesta HTTP in altre azioni nel flusso, nel modo seguente: ![][7] ![][8]
9.	Infine, per inviare una risposta, aggiungere un altro listener HTTP e selezionare l'azione di invio della risposta HTTP. Impostare l'ID richiesta sull'ID ottenuto dal listener HTTP e immettere il corpo della risposta e lo stato HTTP che dovranno essere restituiti: ![][9]

## Uso dell'azione HTTP
L'azione HTTP è supportata in modo nativo dalle app per la logica e non è necessario creare prima un'app per le API per poterla usare. È possibile inserire un'azione HTTP in qualsiasi punto dell'app per la logica e scegliere l'URI, le intestazioni e il corpo per la chiamata. L'azione HTTP supporta diverse opzioni per la sicurezza lato client. Per usarle, vedere l'articolo disponibile [qui](http://aka.ms/logicapphttpauth).

L'output dell'azione HTTP sono le intestazioni e il corpo, che possono essere usati nel flusso downstream in modo simile a come viene usato l'output di altre azioni e altri connettori.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png

<!---HONumber=Oct15_HO1-->
<properties
   pageTitle="Listener e connettore HTTP"
   description="Uso del listener HTTP e dell'azione HTTP nell'app per la logica"
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
   ms.date="07/02/2015"
   ms.author="prkumar"/>


#Uso del listener HTTP e dell'azione HTTP nell'app per la logica#

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. Tuttavia, in alcuni scenari potrebbe essere necessario usare connessioni HTTP dirette, ovvero:

1.	Per sviluppare un'app per la logica che supporta un front-end interattivo utente mobile o Web.
2.	Per ottenere ed elaborare i dati da un servizio Web privo di un connettore predefinito.
3.	Per eseguire azioni già esposte come servizio Web, ma non disponibili come app per le API.


Per questi scenari, la raccolta fornisce due opzioni

1. Listener HTTP: questo connettore funge da trigger ed è in ascolto delle richieste HTTP su un endpoint configurato. Quando viene ricevuta una chiamata sull'endpoint configurato, attiva una nuova istanza del flusso e passa i dati ricevuti nella richiesta al flusso per l'elaborazione. Può anche essere configurato per rispondere automaticamente alla richiesta in arrivo quando il flusso è stato avviato o per consentire la costruzione di una risposta in base all'esecuzione del flusso e l'invio di una risposta al chiamante.
2.	Azione HTTP: consente di configurare una richiesta Web a un endpoint disponibile su Internet, ottiene una risposta e la rende disponibile per le azioni nel flusso per l'utilizzo.

##Creazione di un listener HTTP per l'app per la logica
Per usare il listener HTTP, prima è necessario creare un'istanza dell'app per le API HTTP Listener: Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a destra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "HTTP Listener".
3.	Configurare il listener HTTP nel modo seguente:

	![][1]

4.	Quando si configurano le impostazioni del pacchetto, verrà visualizzata l'opzione seguente che indica se il listener deve rispondere automaticamente o richiedere all'utente di inviare una risposta esplicita. Impostarla su False per poter inviare la propria risposta.

	![][2]

5.	Fare clic su OK per creare.
6.	Una volta creata l'istanza dell'app per le API, aprire le impostazioni per configurare la sicurezza. Il listener HTTP supporta attualmente l'autenticazione di base. È possibile configurarla usando l'opzione Sicurezza quando si apre il listener HTTP.

	![][3] <br> <br> <b><u>Problema noto</u></b><br> *Le impostazioni di sicurezza mostrano "Nessuno" come valore predefinito, ma il valore non è stato definito. È necessario modificare l'impostazione in Base e quindi di nuovo in Nessuno prima di salvarla, in modo da assicurare che il listener HTTP sia configurato correttamente.*

7. Sarà infine necessario configurare le impostazioni di sicurezza dell'app per le API su Pubblico (anonimo) per consentire ai client esterni di accedere all'endpoint. Questa impostazione è disponibile in "Tutte le impostazioni > Impostazioni applicazione" dell'app per le API del listener HTTP.

	![][10]

A questo punto è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il listener HTTP.

##Uso del listener HTTP nell'app per la logica
Una volta creata l'app per le API, si può usare il listener HTTP come trigger per l'app per la logica. A questo scopo, è necessario:

4.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del listener HTTP.
5.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso. Il listener HTTP apparirà nella sezione "Usati di recente" nella raccolta sulla destra. Selezionarlo.
6.	Ora è possibile impostare il metodo HTTP e l'URL relativo per richiedere al listener di attivare il flusso.<br>

	![][4] ![][5]

7.	Per ottenere l'URI completo, fare doppio clic sul listener HTTP per visualizzarne le impostazioni di configurazione e copiare l'URL dell'"Host" dell'app per le API.


	![][6]
8.	Ora è possibile usare i dati ricevuti nella richiesta HTTP in altre azioni nel flusso, nel modo seguente:<br> ![][7] ![][8]
9.	Infine, per inviare una risposta, aggiungere un altro listener HTTP e selezionare l'azione di invio della risposta HTTP. Impostare l'ID richiesta sull'ID ottenuto dal listener HTTP e immettere il corpo della risposta e lo stato HTTP che dovranno essere restituiti![][9]

##Uso dell'azione HTTP
L'azione HTTP è supportata in modo nativo dalle app per la logica e non è necessario creare prima un'app per le API per poterla usare. È possibile inserire un'azione HTTP in qualsiasi punto dell'app per la logica e scegliere l'URI, le intestazioni e il corpo per la chiamata. L'azione HTTP supporta diverse opzioni per la sicurezza lato client. Per usarle, vedere l'articolo disponibile [qui](http://aka.ms/logicapphttpauth).

L'output dell'azione HTTP sono le intestazioni e il corpo, che possono essere usati nel flusso downstream in modo simile a come viene usato l'output di altre azioni e altri connettori.

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

<!---HONumber=August15_HO6-->
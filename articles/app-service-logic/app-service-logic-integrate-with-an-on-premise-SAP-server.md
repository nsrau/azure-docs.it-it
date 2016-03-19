<properties
	pageTitle="Integrazione con un server SAP locale in Servizio app di Azure | Microsoft Azure"
	description="Informazioni su come eseguire l'integrazione con un server SAP locale"
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="sameerch"/>


# Integrazione con un server SAP locale

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

Usando il [connettore SAP](app-service-logic-connector-sap.md) è possibile connettere le app Web, mobili e per la logica dei Servizi app di Azure al server SAP esistente. In questo modo è possibile richiamare RFC, BAPI, tRFC nonché inviare IDoc al server SAP, anche se si trova dietro al firewall locale.

Se si dispone di un server SAP locale, utilizzare un listener ibrido per stabilire la connessione con il connettore SAP, come illustrato:

![Flusso di connettività ibrida][1]

Sebbene un connettore SAP nel cloud non possa connettersi direttamente a un server SAP dietro un firewall locale, è possibile utilizzare il listener ibrido per colmare il divario. Si imposta questa funzionalità ospitando un endpoint di inoltro che consente al connettore di stabilire in modo sicuro la connettività al server SAP.


## Diverse modalità di integrazione con SAP
Sono supportate le azioni seguenti:

- Chiamare RFC
- Chiamare tRFC
- Chiamare BAPI
- Inviare IDoc

## Prerequisiti
Le librerie client specifiche di SAP sono richieste sul computer client su cui è installato e in esecuzione il listener ibrido. Informazioni dettagliate sono contenute nella [Guida all’installazione di BizTalk Adapter Pack][9] alla sezione **Per l’adattatore SAP**.


## Creare un nuovo connettore SAP
1. Accedere al portale di Azure.
2. Selezionare **Nuovo**.
3. Nel pannello di creazione, selezionare **Calcolo ** > **Azure Marketplace**.
4. Nel pannello Marketplace selezionare **App per le API** e quindi eseguire la ricerca di SAP nella barra di ricerca: ![App per le API del connettore SAP][2]
5. Selezionare il **connettore SAP** pubblicato da Microsoft.
6. Nel pannello Connettore SAP selezionare **Crea**.
7. Nel nuovo pannello che viene visualizzato, immettere le informazioni seguenti:  
	1. **Località**: scegliere la località geografica in cui si vuole distribuire il connettore
	2. **Sottoscrizione**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	3. **Gruppo di risorse**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	4. **Piano di hosting Web**: selezionare o creare un piano di hosting Web
	5. **Piano tariffario**: scegliere un piano tariffario per il connettore
	6. **Nome**: assegnare un nome al connettore SAP
	7. **Impostazioni pacchetto**
		- **Nome server**: immettere il nome del server SAP. Esempio: "ServerSAP" o "ServerSAP.nomedominio.com".
		- **Nome utente**: immettere un nome utente valido per connettersi al server SAP.
		- **Password**: immettere una password valida per connettersi al server SAP.
		- **Numero sistema**: immettere il numero sistema del server applicazioni SAP.
		- **Lingua**: immettere la lingua di accesso, ad esempio "IT". Se non viene immesso alcun valore, l'impostazione predefinita sarà "IT".
		- **Locale**: indicare se il server SAP locale è protetto da firewall o no. Se impostato su TRUE, è necessario installare un agente listener in un server che può accedere al server SAP. Per installare l'agente, passare alla pagina di riepilogo dell'app per le API e fare clic su 'Connessione ibrida'.
		- **Stringa di connessione per il bus di servizio**: immettere questo parametro se il server SAP è locale. Deve trattarsi di una stringa di connessione valida allo spazio dei nomi del bus di servizio.
		- **RFC**: immettere in SAP gli RFC che il connettore può richiamare.
		- **TRFC**: immettere in SAP i TRFC che il connettore può richiamare.
		- **BAPI**: immettere in SAP i BAPI che il connettore può richiamare.
		- **IDOC**: immettere in SAP gli IDOC che il connettore può richiamare.
	8. Scegliere Seleziona. Il connettore SAP verrà creato in pochi minuti.


## Installare il listener ibrido
Per passare al connettore SAP creato, scegliere **Sfoglia** > **App per le API** > *nome del connettore*

Nel pannello del connettore, notare che lo stato di connessione ibrida è in sospeso. Selezionare Connessione ibrida. Viene visualizzato il pannello Connessione ibrida.

![Pannello Connessione ibrida][3]

Copiare la stringa di configurazione del gateway primario. Verrà utilizzata in seguito come parte della configurazione del listener ibrido.

Selezionare il collegamento **Scarica e configura**. Fare clic una volta aperto il programma di installazione:

![Programma di installazione Click Once di Connessione ibrida][4]

Selezionare **Installa** e quindi immettere la stringa di configurazione del gateway primario copiata in precedenza:

![Stringa di connessione del listener di inoltro][5]

Selezionare **Installa** per completare la configurazione della Gestione connessioni ibride:

![Installazione della gestione della connessione ibrida in corso][6]

![Installazione della gestione della connessione ibrida completata][7]

## Convalidare la connessione ibrida
Per passare al connettore SAP creato, scegliere **Sfoglia** > **App per le API** > *nome del connettore*

Nel pannello del connettore, notare che lo stato di connessione ibrida è *Connesso*:

![Stato di Connessione ibrida - connesso][8]


## Uso del connettore SAP nelle app per la logica
Dopo aver creato il connettore SAP è possibile usarlo all'interno del flusso di lavoro delle app per la logica. A tale scopo, creare una nuova app per la logica scegliendo **Nuovo** > **App per la logica** > **Crea**. Immettere i metadati relativi all'app per la logica, incluso il gruppo di risorse.

Selezionare **Trigger e azioni**. Viene visualizzata la finestra di progettazione del flusso di lavoro delle app per la logica.

Selezionare il connettore SAP dal riquadro a destra e selezionare un'azione dalla scheda Azioni.

> [AZURE.NOTE] L'elenco di azioni è basato sulla configurazione immessa quando è stato creato il connettore SAP.

Per l'azione selezionata verranno visualizzati i parametri di input e output. È possibile immettere gli input per l'azione e usare l'output dell'azione corrente in altre app per le API, possibilmente per ulteriori processi decisionali nell'ambito del flusso di lavoro.

<!--Image references-->
[1]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectivityFlow.PNG
[2]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.APIApp.PNG
[3]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.PNG
[4]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.PNG
[5]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.RelayInformation.PNG
[6]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.InProgress.PNG
[7]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.Completed.PNG
[8]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.HybridConnection.Connected.PNG
[9]: http://www.microsoft.com/download/details.aspx?id=35552

<!---HONumber=AcomDC_0224_2016-->
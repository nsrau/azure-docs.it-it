<properties 
	pageTitle="Connettori Business to Business e app per le API nel servizio app di Microsoft Azure | Azure" 
	description="Informazioni su come creare e configurare connettori EDI, EDIFACT, AS2 e TPM; architettura dei microservizi" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="mandia"/>

# Connettori Business to Business e app per le API nel servizio app di Microsoft Azure
Il servizio app di Microsoft Azure (o servizio app) include numerose app per le API BizTalk, estremamente importanti per gli ambienti di integrazione. Queste app per le API sono basate sui concetti e gli strumenti usati all'interno di BizTalk Server, ma sono ora disponibili come parte del servizio app di Azure.

Una categoria di queste app sono le app per le API Business to Business (B2B), che consentono di aggiungere partner, creare contratti ed eseguire facilmente tutte le operazioni possibili in locale mediante EDI, AS2 ed EDIFACT.

Le app per le API B2B offrono funzionalità "Trigger" e "Azione". Un trigger avvia una nuova istanza in base a un evento specifico, ad esempio l'arrivo di un messaggio X12 da un partner. Un'azione è il risultato, ad esempio, dopo la ricezione di un messaggio X12, invio del messaggio mediante AS2.


## Informazioni sui connettori Business to Business e le app per le API
La funzionalità Business to Business (B2B) include app per le API precompilate esistenti, che consentono ad aziende, reparti o applicazioni diverse di comunicare usando AS2, EDI ed EDIFACT.

Le app per le API B2B includono:

Connettore o app per le API | Descrizione
--- | ---
BizTalk Trading Partner Management | Un'app per le API che consente di creare relazioni Business to Business (B2B) mediante partner e contratti. Queste relazioni usano il protocollo AS2, EDIFACT e X12. <br/><br/>L'app per le API TPM è il requisito di base del connettore AS2 e delle app per le API X12 o EDIFACT. 
AS2 Connector | Un connettore che riceve e invia messaggi mediante il protocollo di trasporto AS2. Il connettore trasferisce i dati in modo sicuro e affidabile tramite Internet.
BizTalk EDIFACT | Un'app per le API che riceve e invia i messaggi usando EDIFACT. EDIFACT è anche noto come UN/EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport) ed è ampiamente usato in vari settori.
BizTalk X12 | Un'app per le API che riceve e invia i messaggi usando il protocollo X12. X12 è anche noto come ASC X12 (Accredited Standards Committee X12) ed è ampiamente usato in vari settori. 


Usando queste app per le API è possibile completare svariate attività di messaggistica EDI. Ad esempio, mediante il connettore AS2 è possibile ricevere e inviare in modo sicuro diversi tipi di messaggi (EDI, XML, file flat e così via) a un cliente, un reparto all'interno dell'azienda, ad esempio le risorse umane o a qualsiasi utente che usa AS2.

È possibile creare il numero desiderato di app per le API in maniera molto facile. È anche possibile riutilizzare una singola app per le API nell'ambito di più scenari o flussi di lavoro,

senza bisogno di scrivere codice. Di seguito sono riportati i requisiti iniziali.


## Requisiti iniziali
Quando si crea un'app per le API B2B sono necessarie alcune risorse. L'utente deve creare questi elementi prima che sia possibile usarli in altre app per le API. Tra i requisiti sono inclusi i seguenti:

Requisito | Descrizione
--- | ---
Database SQL di Azure | Archivia elementi B2B, tra cui partner, schemi, certificati e contratti. Ogni app per le API B2B richiede un proprio database SQL di Azure. <br/><br/>**Nota** Copiare la stringa di connessione al database.<br/><br/>[Creare un database SQL di Azure](../sql-database-create-configure.md)
Contenitore dell'archiviazione BLOB di Azure | Archivia le proprietà dei messaggi quando è abilitata l'archiviazione AS2. Se l'archiviazione dei messaggi AS2 non è necessaria, il contenitore di archiviazione non è necessario. <br/><br/>**Nota** Se si sta abilitando l'archiviazione, copiare la stringa di connessione all'archivio BLOB.<br/><br/>[Informazioni sugli account di archiviazione di Azure](../storage-create-storage-account.md)
Spazio dei nomi del bus di servizio e i relativi valori chiave | Archivia i dati di invio in batch X12 ed EDIFACT. Se non è necessario l'invio in batch, lo spazio dei nomi del bus di servizio non è necessario.<br/><br/>**Nota** Se si abilita l'invio in batch, copiare questi valori.<br/><br/>[Creare uno spazio dei nomi del bus di servizio](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Istanza di TPM | Per creare un connettore AS2 e un'app per le API X12 o EDIFACT è necessaria un'istanza di BizTalk Trading Partner Management (TPM). Quando si crea l'app per le API TPM, si crea l'istanza di TPM. <br/><br/>**Nota** Prendere nota del nome dell'app per le API TPM. 


## Creare le app per le API
Le app per le API B2B possono essere create nel portale di Azure oppure usando le API REST.


### Creare le app per le API mediante le API REST
[Vedere la documentazione sull'uso delle API REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### Creare le app per le API B2B nel portale di Azure
Nel portale di Azure è possibile creare app per le API B2B durante la creazione di app per la logica, app Web o app per dispositivi mobili. In alternativa, è possibile crearne una usando il relativo pannello. Entrambe le modalità sono facili, perciò tutto dipende dalle proprie necessità o preferenze. Alcuni utenti preferiscono creare prima tutte le app per le API B2B con le loro specifiche proprietà, quindi creare le app per la logica, Web o per dispositivi mobili e infine aggiungere le app per le API B2B create.

La procedura seguente consente di creare le app per le API B2B tramite il pannello App per le API.


#### Creare app per le API BizTalk Trading Partner Management (TPM)

> [AZURE.NOTE]Per creare un connettore AS2 e un'app per le API X12 o EDIFACT è necessaria un'istanza di BizTalk Trading Partner Management (TPM). Quando si crea l'app per le API TPM, si crea l'istanza di TPM.

La procedura seguente consente di creare l'istanza di TPM:

1. Nella schermata iniziale del portale di Azure (la home page) selezionare **Marketplace**. **App per le API** elenca tutte le app per le API e i connettori esistenti. È anche possibile **cercare** una specifica app per le API B2B.
2. Selezionare **BizTalk Trading Partner Management**. Nel nuovo pannello selezionare **Crea**. 
3. Immettere le proprietà: 

	Proprietà | Descrizione
--- | ---
Nome | Immettere un nome per l'istanza di TPM, ad esempio, *AccountsPayableTPM*.
Impostazioni pacchetto | Immettere la **Stringa di connessione a database** ADO.NET per il database SQL di Azure creato. <br/><br/>Quando si copia la stringa di connessione, la password non viene aggiunta alla stringa di connessione. Assicurarsi di immettere la password nella stringa di connessione.
Piano di servizio app | Elenca il piano di pagamento. È possibile modificarlo se sono necessarie più o meno risorse.
Piano tariffario | Proprietà di sola lettura che elenca la categoria di prezzo nella sottoscrizione di Azure. 
Gruppo di risorse | Creare un nuovo gruppo di risorse o usare un gruppo esistente. Tutte le app per le API e i connettori per app per la logica, app Web e app per dispositivi mobili devono trovarsi nello stesso gruppo di risorse. L'articolo <br/><br/>[Uso dei gruppi di risorse](../resource-group-overview.md) illustra questa proprietà. 
Sottoscrizione | Proprietà di sola lettura che elenca la sottoscrizione corrente.
Location | La località geografica che ospita il servizio di Azure. 
Aggiungi a schermata iniziale | Selezionare questa opzione per aggiungere l'app per le API B2B alla schermata iniziale (home page).

4. Selezionare **Crea**.

Dopo aver creato l'app per le API TPM (istanza di TPM), è possibile creare il connettore AS2 e/o le app per le API X12 o EDIFACT.


#### Creare il connettore AS2

1. Nella schermata iniziale del portale di Azure (la home page) selezionare **Marketplace**. **App per le API** elenca tutte le app per le API e i connettori esistenti. È anche possibile **cercare** una specifica app per le API B2B.
2. Selezionare **AS2 Connector**. Nel nuovo pannello selezionare **Crea**. 
3. Immettere le proprietà: 

	Proprietà | Descrizione
--- | ---
Nome | Immettere un nome per il connettore AS2, ad esempio, *AS2Connector*.
Impostazioni pacchetto | Immettere le impostazioni specifiche per l'app per le API, ad esempio il nome dell'istanza di TPM. <br/><br/>Vedere [Impostazioni pacchetto del connettore AS2](#AddAS2Conn) in questo argomento per informazioni sulle specifiche proprietà. 
Piano di servizio app | Elenca il piano di pagamento. È possibile modificarlo se sono necessarie più o meno risorse.
Piano tariffario | Proprietà di sola lettura che elenca la categoria di prezzo nella sottoscrizione di Azure. 
Gruppo di risorse | Creare un nuovo gruppo di risorse o usare un gruppo esistente. L'articolo [Uso dei gruppi di risorse](../resource-group-overview.md) illustra questa proprietà. 
Sottoscrizione | Proprietà di sola lettura che elenca la sottoscrizione corrente.
Location | La località geografica che ospita il servizio di Azure. 
Aggiungi a schermata iniziale | Selezionare questa opzione per aggiungere l'app per le API B2B alla schermata iniziale (home page).

	**<a name="AddAS2Conn"></a>Impostazioni pacchetto del connettore AS2**

	Proprietà | Descrizione
--- | --- 
Stringa di connessione a database | Immettere la stringa di connessione ADO.NET al database SQL di Azure creato. Quando si copia la stringa di connessione, la password non viene aggiunta alla stringa di connessione. Assicurarsi di immettere la password nella stringa di connessione prima di incollare.
Abilita archiviazione per messaggi in arrivo | Facoltativa. Abilitare questa proprietà per archiviare le proprietà di un messaggio AS2 in arrivo ricevuto da un partner. 
Stringa di connessione ad archiviazione BLOB di Azure | Immettere la stringa di connessione per il contenitore di archiviazione BLOB di Azure creato. Quando è abilitata l'archiviazione, i messaggi codificati e decodificati vengono archiviati nel contenitore di archiviazione.
Nome istanza TPM | Immettere il nome dell'app per le API **BizTalk Trading Partner Management** creata in precedenza. Quando si crea il connettore AS2, il connettore esegue solo i contratti AS2 all'interno di questa specifica istanza di TPM.

4. Selezionare **Crea**.


#### Creare le app per le API X12 o EDIFACT

1. Nella schermata iniziale del portale di Azure (la home page) selezionare **Marketplace**. **App per le API** elenca tutte le app per le API e i connettori esistenti. È anche possibile **cercare** una specifica app per le API B2B.
2. Selezionare** BizTalk X12** o **BizTalk EDIFACT**. Nel nuovo pannello selezionare **Crea**. 
3. Immettere le proprietà: 

	Proprietà | Descrizione
--- | ---
Nome | Immettere un nome per l'app per le API B2B, ad esempio, *EDI850APIApp*.
Impostazioni pacchetto | Immettere le impostazioni specifiche per l'app per le API, ad esempio il nome dell'istanza di TPM. <br/><br/>Vedere [Impostazioni pacchetto delle app per le API X12 o EDIFACT](#AddX12) in questo argomento per informazioni sulle specifiche proprietà. 
Piano di servizio app | Elenca il piano di pagamento. È possibile modificarlo se sono necessarie più o meno risorse.
Piano tariffario | Proprietà di sola lettura che elenca la categoria di prezzo nella sottoscrizione di Azure. 
Gruppo di risorse | Creare un nuovo gruppo di risorse o usare un gruppo esistente. L'articolo [Uso dei gruppi di risorse](../resource-group-overview.md) illustra questa proprietà. 
Sottoscrizione | Proprietà di sola lettura che elenca la sottoscrizione corrente.
Location | La località geografica che ospita il servizio di Azure. 
Aggiungi a schermata iniziale | Selezionare questa opzione per aggiungere l'app per le API B2B alla schermata iniziale (home page).

	**<a name="AddX12"></a>Impostazioni pacchetto delle app per le API X12 o EDIFACT**

	Proprietà | Descrizione
--- | --- 
Stringa di connessione a database | Immettere la stringa di connessione ADO.NET al database SQL di Azure creato. Quando si copia la stringa di connessione, la password non viene aggiunta alla stringa di connessione. Assicurarsi di immettere la password nella stringa di connessione prima di incollare.
Spazio dei nomi del bus di servizio | Immettere lo spazio dei nomi del bus di servizio creato. Necessario solo quando è abilitato l'invio in batch. 
Nome chiave di accesso condivisa per spazio dei nomi del bus di servizio | Immettere il nome della chiave di accesso per lo spazio dei nomi del bus di servizio creata. Necessario solo quando è abilitato l'invio in batch. 
Valore chiave di accesso condivisa per spazio dei nomi del bus di servizio | Immettere il valore della chiave di accesso per lo spazio dei nomi del bus di servizio creata. Necessario solo quando è abilitato l'invio in batch. 
Nome istanza TPM | Immettere il nome dell'app per le API **BizTalk Trading Partner Management** creata in precedenza. Quando si creano app per le API X12 o EDIFACT, questa app per le API esegue solo i contratti X12/EDIFACT all'interno di questa specifica istanza di TPM.

4. Selezionare **Crea**.


## Aggiungere partner, contratti, certificati e schemi 
Aprire l'app per le API TPM nel portale di gestione di Azure. Nella sezione **Componenti** aggiungere partner, contratti, certificati e schemi.

È anche possibile aggiungere contratti ai connettori AS2 e alle app per le API X12 ed EDIFACT.


## Monitorare le app per le API
Aprire l'app per le API TPM nel portale di gestione di Azure. Nella sezione **Operazioni** è possibile visualizzare le varie operazioni di gestione. Ad esempio, è possibile:

- Visualizzare eventi informativi e di errore
- Visualizzare l'utilizzo della memoria e il conteggio dei thread del processo di lavoro (w3wp)
- Visualizzare i log dell'applicazione e del server Web

Per altre informazioni, vedere [Monitorare le app per la logica](app-service-logic-monitor-your-logic-apps.md).


## Aggiungere le app per le API all'applicazione 
Il servizio app di Microsoft Azure espone diversi tipi di applicazione che possono usare queste app per le API B2B. È possibile creare una nuova app per le API B2B o aggiungere quelle esistenti alle app per la logica, per dispositivi mobili o Web.

All'interno dell'app, selezionare semplicemente le app per le API B2B dalla raccolta per aggiungerle automaticamente all'app.

> [AZURE.IMPORTANT]Per aggiungere connettori e app per le API create in precedenza, creare le app per la logica, mobili o Web nello stesso gruppo di risorse.

La procedura seguente consente di aggiungere le app per le API B2B ad app per la logica, app per dispositivi mobili o Web:

1. Nella schermata iniziale del portale di Azure (la home page) passare a **Marketplace** e cercare le proprie app per la logica, per dispositivi mobili o Web. 

	Se si sta creando una nuova app, cercare app per la logica, app per dispositivi mobili o app Web. Selezionare l'app e nel nuovo pannello selezionare **Crea**. La procedura è descritta in [Creare un'app per la logica](app-service-logic-create-a-logic-app.md).

2. Aprire l'app e selezionare **Trigger e azioni**.

3. In **Raccolta** selezionare l'app per le API B2B, che verrà aggiunta automaticamente all'app. È anche possibile creare una nuova app per le API B2B.

	> [AZURE.IMPORTANT]Il connettore AS2 e le app per le API X12 ed EDIFACT richiedono un'istanza di TPM. Pertanto, quando si creano nuove app per le API B2B, creare prima l'app per le API TPM e quindi creare il connettore AS2 e l'app per le API X12 o EDIFACT.

4. Selezionare **OK** per salvare le modifiche.


## Altre risorse B2B

[Creazione di un processo B2B](app-service-logic-create-a-b2b-process.md)<br/> [Creazione di un accordo tra partner commerciali](app-service-logic-create-a-trading-partner-agreement.md)<br/> [Informazioni sui connettori e sulle app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## Informazioni sulle app per la logica e le app Web
[Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)<br/> [Siti Web e app Web in Azure App Service](../app-service-web/app-service-web-overview.md)


## Altri connettori

[Elenco di connettori e app per le API](app-service-logic-connectors-list.md)<br/><br/> [Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=July15_HO3-->
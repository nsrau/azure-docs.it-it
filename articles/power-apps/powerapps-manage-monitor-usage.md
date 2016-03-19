<properties
	pageTitle="Controllare l'uso delle app in PowerApps Enterprise | Microsoft Azure"
	description="Visualizzare tutte le informazioni su app, API, utenti, richieste di app e autorizzazioni di aggiornamento nel portale di Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# Gestire e proteggere PowerApps
È possibile creare l'ambiente del servizio app e aggiungere le API e le relative connessioni. Ora gli utenti dell'organizzazione possono iniziare a usare queste API e connessioni. È inoltre possibile gestire tutte le app create nell'organizzazione. Tali opzioni includono:

- Vedere le diverse app all'interno dell'ambiente del servizio app, tra cui PowerApps, app Web, app per la logica, app per dispositivi mobili e altre ancora.
- Vedere tutte le API usate da app specifiche.
- Visualizzare e gestire l'accesso utente per le app all'interno dell'ambiente del servizio app. 
- Visualizzare e gestire l'accesso utente per le API e le relative connessioni. 

Tenere presente che nell'ambiente del servizio app è possibile aggiungere altre app, tra cui app Web e app per la logica. È quindi possibile aprire PowerApps Enterprise per vedere e gestire queste app.


## Aggiungere gli amministratori di PowerApps
Una volta che PowerApps Enterprise è abilitato e pronto per essere usato, è possibile aggiungere gli amministratori e monitorare altre app all'interno dell'ambiente del servizio app.

1. Nel [portale di Azure](https://portal.azure.com/) aprire **PowerApps**.
2. Selezionare **Impostazioni**.
3. In **Impostazioni** selezionare **Amministratore**: ![][1]  
4. In **Utenti** selezionare **Aggiungi**.
5. Selezionare il ruolo **Proprietario**: ![][2]  

	> [AZURE.IMPORTANT]Assicurarsi di selezionare il ruolo **Proprietario** se si sta assegnando un utente come un amministratore di PowerApps. Gli altri ruoli elencati non forniscono agli utenti l'accesso completo per la gestione PowerApps.

6. Selezionare gli utenti o i gruppi.
7. Selezionare **OK** per completare i passaggi.

Quando si aggiungono gli amministratori a PowerApps Enterprise, gli utenti e i gruppi aggiunti come amministratori:

- Possono aggiungere altri utenti come amministratori di PowerApps.
- Possono gestire tutte le app, nonché il relativo accesso utente.
- Non possono modificare la fatturazione.

> [AZURE.IMPORTANT]Gli amministratori di PowerApps non possono apportare modifiche all'ambiente del servizio app finché non dispongono del ruolo Proprietario per il gruppo di risorse dell'ambiente del servizio app. A tale scopo, vedere [questo articolo](powerapps-get-started-azure-portal.md).

Dopo avere assegnato il ruolo Proprietario per il gruppo di risorse dell'ambiente del servizio app, gli amministratori di PowerApps possono anche:

- Creare e configurare le API e le relative connessioni.
- Apportare le modifiche alle impostazioni di PowerApps, incluso l'ambiente del servizio app.
- Aggiungere altri utenti e gruppi e assegnare loro ruoli e autorizzazioni per le API, le relative connessioni e l'ambiente del servizio app. 


## Gestire PowerApps e altri tipi di app
Dopo avere abilitato PowerApps e l'ambiente del servizio app, è possibile aggiungere altre app, ad esempio app Web e app per la logica, allo stesso ambiente del servizio app. Al termine, le app sono elencate in *Tutte le app* insieme alle app create in PowerApps. È possibile fare clic su ogni tipo di app per esplorare le app.

### Visualizzare e gestire PowerApps

1. Nel [portale di Azure](https://portal.azure.com/) aprire **PowerApps**.
2. Nel riquadro **Tutte le app** selezionare **PowerApps**: ![][3]  
3. Selezionare un'app per visualizzare i relativi dettagli, tra cui:  
	- Le API usate dall'app
	- Gli utenti e i gruppi che hanno accesso all'app 
	- Le analisi dell'app (presto disponibili)

#### Aggiungere un'app

Non è possibile aggiungere un'app tramite il portale di Azure. Attualmente, è necessario passare al [portale di PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715583).

#### Eliminare le app create in PowerApps
In qualità di amministratore di PowerApps, è possibile eliminare qualsiasi app, incluse quelle create in PowerApps e altri tipi di app nell'ambiente del servizio app. Per eliminare l'app, selezionare il riquadro **Tutte le app**, selezionare l'app e quindi selezionare **Elimina**: ![][4]


#### Concedere a utenti o gruppi l'accesso per l'uso di un'app
In qualità di amministratore di PowerApps, è possibile aggiungere o rimuovere utenti e gruppi in PowerApps.

1. Nel [portale di Azure](https://portal.azure.com/) aprire **PowerApps**.
2. Nel riquadro **Tutte le app** selezionare **PowerApps**: ![][3]  
3. Selezionare un'app, ad esempio **Service Desk**. 
4. In **Impostazioni** selezionare **Accesso utente all'app**: ![][5]  
5. Selezionare **Aggiungi** per aggiungere un nuovo utente o gruppo. 
6. Selezionare un ruolo:  
	- Modifica consentita
	- Visualizzazione consentita
7. Selezionare gli utenti o i gruppi.
8. Selezionare **OK** per completare i passaggi.

### Visualizzare e gestire le app per la logica

1. Nel [portale di Azure](https://portal.azure.com/) aprire **PowerApps**.
2. Nel riquadro **Tutte le app** selezionare **App per la logica**: ![][8]  
3. Selezionare un'app per la logica per visualizzare i relativi dettagli. Assicurarsi di selezionare la sottoscrizione appropriata per visualizzare in PowerApps un elenco delle app per la logica corrette: ![][7]  

	> [AZURE.IMPORTANT]Nell'anteprima pubblica si potrebbero osservare alcune incoerenze nel numero delle app per la logica indicato nel pannello di esplorazione rispetto al numero visualizzato nel pannello principale di PowerApps. Si tratta di un comportamento previsto. Il portale visualizza tutte le app per la logica in tutti i piani di hosting e non filtra le app per la logica nell'ambiente del servizio app distribuito per PowerApps. Questo problema verrà risolto in un futuro aggiornamento.

	**Per altre informazioni sulle app per la logica e su come gestirle, vedere [queste istruzioni](https://azure.microsoft.com/documentation/services/app-service/logic/).**

### Visualizzare e gestire le app Web

1. Nel [portale di Azure](https://portal.azure.com/) aprire **PowerApps**.
2. Nel riquadro **Tutte le app** selezionare **App Web**: ![][9]  

	**Per altre informazioni sulle app Web e su come gestirle, vedere [queste istruzioni](https://azure.microsoft.com/documentation/services/app-service/web/).**

### Visualizzare e gestire le app per dispositivi mobili

1. Nel [portale di Azure](https://portal.azure.com/) aprire **PowerApps**.
2. Nel riquadro **Tutte le app** selezionare **App per dispositivi mobili**: ![][10]  

	**Per altre informazioni sulle app per dispositivi mobili e su come gestirle, vedere [queste istruzioni](https://azure.microsoft.com/documentation/services/app-service/mobile/).**


## Esaminare le opzioni di sicurezza
Vengono usati diversi metodi di sicurezza, a seconda dell'operazione che si sta eseguendo. Ecco le informazioni che è necessario conoscere:

- **Amministratore della sottoscrizione**: questi amministratori controllano la fatturazione e sono responsabili per l'iscrizione dell'azienda a PowerApps Enterprise. Solo gli amministratori della sottoscrizione possono richiedere di abilitare PowerApps nella sottoscrizione di Azure dell'azienda. 

- **Accesso utente al runtime**: sono disponibili tre diversi tipi di accesso utente al runtime:
	- **Accesso utente all'app**: questa autorizzazione consente di controllare se l'utente dispone del ruolo *Modifica consentita* o *Visualizzazione consentita* per l'app.
	- **Accesso utente all'API**: questa autorizzazione consente di controllare l'accesso al runtime. Se l'utente dispone di questa autorizzazione, può usare l'API nella propria app. Gli utenti possono essere autorizzati o meno a usare l'API in fase di esecuzione. 
	- **Accesso utente alla connessione**: *Visualizzazione consentita* e *Modifica consentita* sono le autorizzazioni utente di runtime disponibili per una connessione. Quando si aggiunge un'API (o un profilo di connessione) e si crea la relativa connessione, vengono concessi a utenti e gruppi queste autorizzazioni specifiche: ![][6]  

		Ad esempio, è possibile assegnare al gruppo Vendite all'interno dell'azienda l'autorizzazione *Modifica consentita* per una connessione di un'API SQL Connector. Un utente con l'autorizzazione *Modifica consentita* sarà in grado di usare la connessione nelle proprie app, nonché di modificare la configurazione della connessione. Un utente con l'autorizzazione *Visualizzazione consentita* sarà in grado di usare la connessione nelle proprie app, ma non potrà modificare la configurazione della connessione come una stringa di connessione.

- **Controllo degli accessi in base al ruolo**: molte offerte Azure usano il controllo degli accessi in base al ruolo per determinare chi è autorizzato a eseguire le varie operazioni. In PowerApps, il controllo degli accessi in base al ruolo viene usato in due posizioni:
	- Quando si accede al portale di PowerApps per la prima volta, è possibile aggiungere e gestire gli utenti che devono essere amministratori di PowerApps. 
	- Quando si crea l'ambiente del servizio app, è possibile aggiungere utenti o gruppi a PowerApps e rimuovere utenti o gruppi da PowerApps. Ad esempio, è possibile aggiungere specifici gruppi di amministratori all'interno dell'azienda al ruolo *Proprietari*, che consente loro di creare le API e le connessioni. Queste API e connessioni vengono quindi aggiunte alle app create in PowerApps.
	- Quando si aggiungono utenti ad app come app Web, app per la logica o app per dispositivi mobili. È possibile scegliere il ruolo per questi utenti.  
		
		L'aggiunta degli utenti e l'assegnazione dei ruoli è molto simile all'uso del [controllo degli accessi in base al ruolo](../role-based-access-control-configure.md) all'interno di Azure. I ruoli includono:

		Ruolo | Descrizione
		--- | ---
		Collaboratore | Gestisce tutti gli elementi, tranne la concessione dell'accesso agli utenti.
		Reader | È in grado di visualizzare tutti gli elementi, ma non può apportare modifiche.
		Proprietario | È in grado di gestire tutti gli elementi e di concedere l'accesso agli utenti.

Usando questi ruoli, è possibile concedere all'utente A l'autorizzazione **Visualizzazione consentita** per un'app giornaliera Twitter e all'utente B l'autorizzazione **Modifica consentita** per l'app ShuttleBus. All'utente B può essere concesso l'accesso a tutte le API. È possibile ottenere un controllo molto granulare grazie a questi diritti o aggiungere tutti gli utenti con un ruolo specifico. Tutto dipende dalle specifiche esigenze aziendali.


## Riepilogo e passaggi successivi
In questo argomento sono state fornite informazioni sulle diverse opzioni per gestire PowerApps e sui metodi di sicurezza implementati in PowerApps.

Dopo avere configurato le funzionalità del portale di Azure, è possibile iniziare a creare le app. Ecco alcuni suggerimenti per iniziare:

- [Creare un'app da un modello in PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715536) 
- [Creare un'app dai dati in PowerApps](http://go.microsoft.com/fwlink/?LinkId=715539) 
- [Creare un'app da zero in PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715538)


[1]: ./media/powerapps-manage-monitor-usage/addadmin.png
[2]: ./media/powerapps-manage-monitor-usage/selectrole.png
[3]: ./media/powerapps-manage-monitor-usage/PowerApps.png
[4]: ./media/powerapps-manage-monitor-usage/deleteapp.png
[5]: ./media/powerapps-manage-monitor-usage/appuseraccess.png
[6]: ./media/powerapps-manage-monitor-usage/selectpermission.png
[7]: ./media/powerapps-manage-monitor-usage/alllogicapps.png
[8]: ./media/powerapps-manage-monitor-usage/logicapps.png
[9]: ./media/powerapps-manage-monitor-usage/webapps.png
[10]: ./media/powerapps-manage-monitor-usage/mobileapps.png

<!---HONumber=AcomDC_1203_2015-->
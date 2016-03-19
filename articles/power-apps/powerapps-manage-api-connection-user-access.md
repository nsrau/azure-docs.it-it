<properties
	pageTitle="Aggiungere o creare una nuova API e concedere autorizzazioni agli utenti in PowerApps | Microsoft Azure"
	description="Aggiungere, creare e configurare una nuova API, una connessione o un profilo di connessione e assegnare autorizzazioni e diritti con l’accesso degli utenti nel portale di Azure"
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


# Aggiungere una nuova API, una connessione e fornire l'accesso agli utenti

Le API esistono all'interno di un [ambiente del servizio app](powerapps-get-started-azure-portal.md). È possibile creare API dalle API disponibili per PowerApps, da app per le API ospitate nell'ambiente del servizio app o da Swagger 2.0. Esistono molte API precompilate disponibili che possono essere aggiunte facilmente a PowerApps. Inoltre è possibile caricare la propria API in formato JSON o Swagger 2.0.

Questo argomento:

- Elenca i passaggi per aggiungere un'API a PowerApps e fornire agli utenti all'interno della società le autorizzazioni per utilizzare l'API, ad esempio modificandone le proprietà.
- Elenca i passaggi per aggiungere una connessione all'API e fornire agli utenti all'interno della società le autorizzazioni per utilizzare la connessione.


#### Prerequisiti per iniziare

- Abilitare [PowerApps nella sottoscrizione di Azure](powerapps-get-started-azure-portal.md).
- Creare un [ambiente di servizio app](powerapps-get-started-azure-portal.md).
- Creare un'API utilizzando uno dei metodi seguenti:  
	- Creare un'[API gestita da Microsoft o gestita dal reparto IT](powerapps-register-from-available-apis.md).
	- Creare un'API ospitata all'interno dell'[ambiente del servizio app](powerapps-register-api-hosted-in-app-service.md).
	- Creazione mediante una [definizione API Swagger 2.0](powerapps-register-existing-api-from-api-definition.md)


## Fornire agli utenti l’accesso all’API
Ora che l'API è stata creata e aggiunta all'ambiente del servizio app, è necessario concedere le autorizzazioni per utilizzarla agli utenti all'interno della società.

1. In PowerApps selezionare **Gestione API** e selezionare l'API. Ad esempio, se è stata creata un’API *MS Power BI*, selezionarla per aprire il pannello. Selezionare **Accesso degli utenti all’API**: ![][1]  

2. Selezionare **Aggiungi** per aggiungere gli utenti e selezionare i diritti. Al termine, selezionare **Aggiungi** per salvare le modifiche. Il conteggio degli utenti o gruppi aumenta nella finestra **Accesso degli utenti all’API**.


## Aggiungere una nuova connessione all'API
Il passaggio successivo consiste nel creare la "connessione" all'API, che è molto simile a una stringa di connessione. In questo modo l'API può connettersi al sistema "back-end". Per l'anteprima pubblica di PowerApps Enterprise, è possibile aggiungere e configurare solo la connessione a SQL Server. Altre verranno aggiunte.

- [Creare una connessione a SQL Server](powerapps-create-api-sqlserver.md)

## Concedere agli utenti l'accesso al runtime per la connessione
Ora fornire agli utenti all'interno della società l'autorizzazione a utilizzare la connessione.

1. Aprire l'API, selezionare **Connessioni**, quindi selezionare la connessione specifica. Verrà aperto un nuovo pannello che elenca il nome della connessione nella parte superiore. 
2. In questo nuovo pannello selezionare **Accesso degli utenti alla connessione**. Nell'esempio seguente, è selezionata la connessione **Tunnel ibrida**. Viene visualizzato il nuovo pannello in cui è possibile specificare l’**Accesso degli utenti alla connessione**: ![][2]
  
3. In **Accesso degli utenti alla connessione** selezionare **Aggiungi**, e quindi selezionare l'autorizzazione da assegnare: ![][3]
  
4. Aggiungere l'utente o il gruppo. Selezionare **Aggiungi** per salvare le modifiche.

Ora che gli utenti dispongono delle autorizzazioni per l’API e la relativa connessione, possono aggiungere queste API alle proprie create in PowerApps. In particolare:

- Gli utenti possono visualizzare l’API elencata in **Connessioni disponibili** in PowerApps.
- Gli utenti possono visualizzare la connessione in **connessioni** in PowerApps.


## Eliminare un’API
È inoltre possibile eliminare un'API aggiunta in precedenza. In PowerApps, selezionare **Gestione API**, selezionare l'API e selezionare **Elimina**: ![][4]


## Riepilogo e passaggi successivi
In questo argomento:

- È stata aggiunta un'API e sono stati assegnati agli utenti all'interno della società i diritti per utilizzarla. È inoltre possibile utilizzare questi passaggi per gestire l'accesso al runtime in qualsiasi momento. Ad esempio, se l'utente A lascia la società, è possibile utilizzare il portale di Azure per rimuovere facilmente le autorizzazioni dell'utente. Stesso scenario, se un utente B entra a far parte della società.
- È stata aggiunta una connessione (simile a una stringa di connessione). Questo passaggio consente all’API ospitata in Azure di connettersi al sistema, ad esempio un Server SQL locale. È stata fornita agli utenti all'interno della società l'autorizzazione a utilizzare la connessione. 
- Si è lavorato con diversi pannelli, a seconda dell'attività. Per aggiungere una connessione, aprire l'API e utilizzare il relativo pannello. Per concedere l'accesso agli utenti, aprire l'API o la connessione, a seconda di ciò a cui si sta fornendo l’accesso. 
- È inoltre possibile eliminare una delle API create all'interno dell'ambiente del servizio app.

Successivamente, è possibile [gestire e monitorare PowerApps](powerapps-manage-monitor-usage.md).

[1]: ./media/powerapps-manage-api-connection-user-access/apiuseraccess.png
[2]: ./media/powerapps-manage-api-connection-user-access/connectionuseraccess.png
[3]: ./media/powerapps-manage-api-connection-user-access/selectpermission.png
[4]: ./media/powerapps-manage-api-connection-user-access/deleteapi.png

<!---HONumber=AcomDC_1203_2015-->
<properties
	pageTitle="Aggiungere l'API di Twitter a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di Twitter nell’ambiente del servizio app dell'organizzazione"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
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

# Creare una nuova API di Twitter nell’ambiente del servizio app dell'organizzazione

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società. 

2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][14]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][15]

4. In **PowerApps** selezionare **Gestione API**: ![Sfogliare le API registrate][1]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API: ![Add API][2]

6. Immettere un **nome** descrittivo per l'API.
	
7. In **Origine** selezionare **API disponibili** per selezionare le API predefinite e selezionare **Twitter**: ![selezionare l'API di Twitter][3]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**: ![configurare le impostazioni dell’API di Twitter][4]

9. Immettere *Chiave utente* e *Segreto utente* dell'applicazione Twitter. Se non è disponibile, vedere la sezione "Registrare un'app di Twitter per l'utilizzo con PowerApps" in questo argomento per creare i valori chiave e segreto necessari.

	> [AZURE.IMPORTANT]Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.

10. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di Twitter all'ambiente del servizio app.


## Facoltativo: Registrare un'app per Twitter per l'utilizzo con PowerApps

Se non si dispone di un'app Twitter esistente con i valori chiave e segreto, utilizzare i passaggi seguenti per creare l'applicazione e ottenere i valori necessari.

1. Passare a [https://apps.twitter.com/](https://apps.twitter.com) e accedere con l'account twitter.

2. Fare clic su **Crea nuova app**: ![Pagina app di Twitter][6]

3. In **Creare un'applicazione**:
   
	a) Immettere un valore per **Nome**. b) Immettere un valore per **Descrizione**. c) Immettere un valore per **Sito Web**. d) Impostare l’**Url di callback** sull'URL di reindirizzamento ricevuto quando è stata aggiunta la nuova API di Twitter al portale di Azure (in questo argomento). e) Accettare il contratto per gli sviluppatori e selezionare **Crea l'applicazione Twitter**.

	![Creare app di Twitter][7]

4. Al completamento della creazione dell’app, si verrà reindirizzati alla pagina dell'app.

Viene creata una nuova applicazione Twitter. È possibile utilizzare questa app nella configurazione dell'API di Twitter nel portale di Azure.

## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Twitter alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)


<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_1203_2015-->
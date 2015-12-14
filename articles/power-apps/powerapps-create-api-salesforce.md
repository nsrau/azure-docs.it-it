<properties
	pageTitle="Aggiungere l'API di Salesforce a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di Salesforce nell’ambiente del servizio app dell'organizzazione"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwerde"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Creare una nuova API Salsesforce nell’ambiente del servizio app dell'organizzazione

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][14]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][15]

4. In **PowerApps** selezionare **Gestione API**: ![Sfogliare le API registrate][1]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API: ![Add API][2]

6. Immettere un **nome** descrittivo per l'API.
	
7. In **Origine** selezionare **API disponibili** per selezionare le API predefinite e selezionare **Salesforce**: ![selezionare l'API di Salesforce][3]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**: ![configurare le impostazioni dell'API di Dropbox.][7]

9. Immettere *Chiave app* e *Segreto app* dell'applicazione Salesforce. Se non è disponibile, vedere la sezione "Registrare un'app di Salesforce per l'utilizzo con PowerApps" in questo argomento per creare i valori chiave e segreto necessari.

	> [AZURE.IMPORTANT]Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.

10. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di Salesforce all'ambiente del servizio app.


## Facoltativo: Registrare un'app di Salesforce per l'utilizzo con PowerApps

Se non si dispone di un'app Salesforce esistente con i valori chiave e segreto, utilizzare i passaggi seguenti per creare l'applicazione e ottenere i valori necessari.

1. Aprire la [home page per sviluppatori Salesforce][5] e accedere con l'account Salesforce. 

2. Nella home page selezionare il profilo e selezionare **Configurazione**: ![Home page di Salesforce][6]

3. Selezionare **Crea** e selezionare **App**. Nella pagina **App** selezionare **Nuova** in **App connesse**: ![Creazione app Salesforce][7]

4. In **Nuova App connessa**:

	a) Immettere il valore per **Nome app connessa**. b) Immettere il valore per **Nome API**. c) Immettere il valore per **E-mail di contatto**. d) In _API (Attiva impostazioni OAuth)_ selezionare **Attiva impostazioni OAuth** e impostare l’**URL di callback** sull'URL di reindirizzamento ricevuto quando è stata aggiunta la nuova API di Salesforce nel portale di Azure (in questo argomento).

5. In _Ambiti OAuth selezionati_ aggiungere i seguenti ambiti agli **Ambiti OAuth selezionati**:

	- Accedere e gestire i dati Chatter (chatter\_api)
	- Accedere e gestire i dati (api)
	- Consentire l'accesso all'identificatore univoco (openid)
	- Eseguire richieste per conto dell’utente in qualsiasi momento (refresh\_token, offline\_access)

6. **Salvare** le modifiche: ![Nuova app di Salesforce][8]

Viene creata una nuova app Salesforce. È possibile utilizzare questa app nella configurazione dell'API di Salesforce nel portale di Azure.

## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Salesforce alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-salesforce/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-salesforce/add-api.PNG
[3]: ./media/powerapps-create-api-salesforce/select-salesforce-api.PNG
[4]: ./media/powerapps-create-api-salesforce/configure-salesforce-api.PNG
[5]: https://developer.salesforce.com
[6]: ./media/powerapps-create-api-salesforce/salesforce-developer-homepage.PNG
[7]: ./media/powerapps-create-api-salesforce/salesforce-create-app.PNG
[8]: ./media/powerapps-create-api-salesforce/salesforce-new-app.PNG
[14]: ./media/powerapps-create-api-salesforce/browseall.png
[15]: ./media/powerapps-create-api-salesforce/allresources.png

<!---HONumber=AcomDC_1203_2015-->
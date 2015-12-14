<properties
	pageTitle="Aggiungere l'API di Google Drive a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di Google Drive nell’ambiente del servizio app dell'organizzazione"
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

# Creare una nuova API di Google Drive nell’ambiente del servizio app dell'organizzazione

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][15]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][16]

4. In **PowerApps** selezionare **Gestione API**: ![Sfogliare le API registrate][1]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API: ![Add API][2]

6. Immettere un **nome** descrittivo per l'API.
	
7. In **Origine**, selezionare **API disponibili** per selezionare le API predefinite e selezionare **Google Drive**: ![selezionare API Google Drive][3]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**: ![configurare le impostazioni API di Google Drive][4]

9. Immettere *Chiave app* e *Segreto app* dell'applicazione Google Drive. Se non è disponibile, vedere la sezione "Registrare un'app di Google Drive per l'utilizzo con PowerApps" in questo argomento per creare i valori chiave e segreto necessari.

	> [AZURE.IMPORTANT]Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.

10. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di Google Drive all'ambiente del servizio app.


## Facoltativo: Registrare un'app per Google Drive per l'utilizzo con PowerApps

Se non si dispone di un'app Google Drive esistente con i valori chiave e segreto, utilizzare i passaggi seguenti per creare l'applicazione e ottenere i valori necessari.

1. Accedere a [Google Developers Console][5]\: ![Google Developers Console][6]

2. Selezionare **Crea un progetto vuoto**.

3. Immettere un nome per l'applicazione e accettare i termini e condizioni, quindi selezionare **Crea**: ![creare un nuovo progetto di Google Drive][7]

4. Dopo la creazione del nuovo progetto, selezionare **Utilizza API Google**: ![Usare le API Google][8]

5. Nella pagina della panoramica, selezionare **API Drive**: ![Panoramica dell'API di Google Drive][9]

6. Selezionare **Abilita API**: ![Abilitare le API di Google Drive][10]

7. All’abilitazione delle API Drive, scegliere **Credenziali** e selezionare **OAuth 2.0 Client ID**: ![Aggiungere credenziali][12]

8. Selezionare **Configura schermata di consenso**.

9. Nella scheda **Schermata di consenso OAuth** immettere un **Nome prodotto** e selezionare **Salva**: ![Configurare la schermata di consenso][13]

10. Nella pagina di creazione dell’id client:

	a) In **Tipo di applicazione** selezionare **Applicazione Web**. b) Immettere un nome per il client. c) Impostare l'URL di reindirizzamento sull'URL di reindirizzamento ricevuto quando è stata aggiunta la nuova API di Google Drive nel portale di Azure (in questo argomento). d) Selezionare **Crea**.

	![Creare l’id client][14]

11. Vengono visualizzati l'id client e il segreto client dell'applicazione registrata.

Viene creata una nuova app di Google Drive. È possibile utilizzare questa app nella configurazione dell'API di Google Drive nel portale di Azure.

## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Google Drive alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

<!---HONumber=AcomDC_1203_2015-->
<properties
	pageTitle="Aggiungere l'API di Dropbox a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di Dropbox nell’ambiente del servizio app dell'organizzazione"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
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

# Creare una nuova API di Dropbox nell’ambiente del servizio app dell'organizzazione

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni:  
![][12]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*:  
![][13]

4. In **PowerApps** selezionare **Gestione API**:  
![Sfogliare le API registrate][4]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API:  
![Add API][5]

6. Immettere un **nome** descrittivo per l'API.
	
7. In **Origine** selezionare **API disponibili** per visualizzare le API predefinite e selezionare **Dropbox**:  
![selezionare API di Dropbox][6]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**:  
![configurare le impostazioni dell'API di Dropbox][7]

9. Immettere i valori **Chiave app** e **Segreto app** dell'applicazione Dropbox. Se non è ancora disponibile, vedere la sezione "Registrare un'app di Dropbox per l'utilizzo con PowerApps" in questo argomento per creare i valori chiave e segreto necessari.

	> [AZURE.IMPORTANT]Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.

10. Selezionare **OK** per completare i passaggi.


Al termine, verrà aggiunta una nuova API di Dropbox all'ambiente del servizio app.


## Facoltativo: Registrare un'app per Dropbox per l'utilizzo con PowerApps

Se non si dispone di un'app Dropbox esistente con i valori chiave e segreto, utilizzare i passaggi seguenti per creare l'applicazione e ottenere i valori necessari.

1. Passare a [Dropbox][1] e accedere con il proprio account.

2. Visitare il sito per sviluppatori Dropbox e selezionare **My Apps**:  
![Sito per sviluppatori Dropbox][8]

3. Selezionare **Crea app**:  
![Creare app Dropbox][9]

4. In **Crea una nuova app sulla piattaforma Dropbox**:

	a) In **Scegliere l’API** selezionare **API Dropbox**. b) In **Scegliere il tipo di accesso necessario** selezionare **Dropbox completo...**. c) Immettere un nome per l'app.

	![Creare app Dropbox pagina 1][10]

5. Nella pagina delle impostazioni dell’app:

	a) In **OAuth 2** impostare l’**URL di reindirizzamento** sull'URL di reindirizzamento ricevuto quando è stata aggiunta la nuova API Dropbox nel portale di Azure (in questo argomento). Selezionare **Aggiungi**. b) Selezionare il collegamento **Mostra** per visualizzare il **segreto app**:

	![Creare app Dropbox pagina 2][11]

Viene creata una nuova app Dropbox. È possibile utilizzare questa app nella configurazione dell'API di Dropbox nel portale di Azure.


## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Dropbox alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[4]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[5]: ./media/powerapps-create-api-dropbox/add-api.PNG
[6]: ./media/powerapps-create-api-dropbox/select-dropbox-api.PNG
[7]: ./media/powerapps-create-api-dropbox/configure-dropbox-api.PNG
[8]: ./media/powerapps-create-api-dropbox/dropbox-developer-site.PNG
[9]: ./media/powerapps-create-api-dropbox/dropbox-create-app.PNG
[10]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page1.PNG
[11]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page2.PNG


[12]: ./media/powerapps-create-api-dropbox/browseall.png
[13]: ./media/powerapps-create-api-dropbox/allresources.png

<!----HONumber=AcomDC_1203_2015-->
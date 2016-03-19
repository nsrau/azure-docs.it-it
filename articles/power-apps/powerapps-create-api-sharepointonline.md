<properties
	pageTitle="Aggiungere l'API di SharePoint Online a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di SharePoint Online nell’ambiente del servizio app dell'organizzazione"
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

# Creare una nuova API di SharePoint Online nell’ambiente del servizio app dell'organizzazione

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][14]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][15]

4. In **PowerApps** selezionare **Gestione API**: ![Sfogliare le API registrate][1]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API: ![Add API][2]

6. Immettere un **nome** descrittivo per l'API.
	
7. In **Origine** selezionare **API disponibili** per selezionare le API predefinite e selezionare **SharePoint Online**: ![selezionare API SharePoint Online][3]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**: ![configurare le impostazioni dell’API SharePoint Online][4]

9. Immettere l’*Id client* e il *segreto app* dell'applicazione di SharePoint Online Azure Active Directory (ADD). Se non è disponibile, vedere la sezione "Registrare un'app di ADD per l'utilizzo con PowerApps" in questo argomento per creare i valori ID e segreto necessari.

	> [AZURE.IMPORTANT]Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.

10. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di SharePoint Online all'ambiente del servizio app.


## Registrare un'app di ADD per l'utilizzo con API di PowerApps SharePoint Online

1. Aprire il [portale di Azure][5].

2. Selezionare **Sfoglia** e quindi selezionare **Active Directory**:

	> [AZURE.NOTE]Questo consente di aprire Active Directory nel portale di Azure classico.

3. Selezionare il nome tenant dell'organizzazione: ![Avviare Azure Active Directory][6]

4. Selezionare la scheda **Applicazioni** e selezionare **Aggiungi**: ![Applicazioni tenant ADD][7]

5. In **Aggiungi applicazione**:

	a) immettere un **Nome** per l'applicazione. b) Lasciare il tipo di applicazione come **Web**. c) Selezionare **Avanti**.


	![Aggiungere l'applicazione ADD - info app][8]

6. In **Proprietà app**:

	a) Immettere l’**URL DI ACCESSO** dell'applicazione. Poiché si intende eseguire l'autenticazione con ADD per PowerApps, impostare l'url di accesso su \__https://login.windows.net_. b) Immettere un **URI ID APP** valido per l’app. c) Selezionare **OK**.

	![Aggiungere l'applicazione ADD - proprietà app][9]

7. Al termine, si verrà reindirizzati alla nuova app ADD. Selezionare **Configura**: ![App ADD Contoso][10]

8. Impostare l’**URL di risposta** nella sezione _OAuth 2_ sull'URL di reindirizzamento ricevuto quando è stata aggiunta la nuova API di SharePoint Online nel portale di Azure (in questo argomento). Selezionare **Aggiungi applicazione**: ![Configurare l'applicazione ADD Contoso][11]

9. Nella finestra **Autorizzazioni per altre applicazioni** selezionare **Office 365 Exchange Online** e selezionare **OK**: ![Delegato app Contoso][12]

10. Nella pagina di configurazione, notare che _Office 365 Exchange Online_ viene aggiunto all’elenco _Autorizzazioni per altre applicazioni_.

11. Selezionare **Autorizzazioni delegate** per _Office 365 Exchange Online_ e quindi selezionare le autorizzazioni seguenti:

	- Leggere e scrivere elementi nelle raccolte di tutti i siti
	- Leggere e scrivere elementi ed elenchi nelle raccolte di tutti i siti

	![Autorizzazioni delegate app Contoso][13]

Viene creata una nuova applicazione Azure Active Directory È possibile utilizzare questa app nella configurazione dell'API di SharePoint Online nel portale di Azure.

## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di SharePoint Online alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-sharepointonline/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-sharepointonline/add-api.PNG
[3]: ./media/powerapps-create-api-sharepointonline/select-sharepointonline-api.PNG
[4]: ./media/powerapps-create-api-sharepointonline/configure-sharepointonline-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-sharepointonline/launch-aad.PNG
[7]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline.PNG
[13]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline-permissions.PNG
[14]: ./media/powerapps-create-api-sharepointonline/browseall.png
[15]: ./media/powerapps-create-api-sharepointonline/allresources.png

<!---HONumber=AcomDC_1203_2015-->
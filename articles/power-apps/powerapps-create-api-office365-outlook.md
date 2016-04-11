<properties
	pageTitle="Aggiungere l'API di Office 365 Outlook a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di Office 365 Outlook nell’ambiente del servizio app dell'organizzazione"
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
   ms.date="03/29/2016"
   ms.author="litran"/>

# Creare una nuova API Office 365 Outlook in PowerApps Enterprise

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/connectors-create-api-office365-outlook.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-outlook.md)

Aggiungere l'API Office 365 Outlook all'ambiente del servizio app dell'organizzazione (tenant).

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][14]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][15]

4. In **Servizi PowerApps** selezionare **Gestione API**: ![Sfogliare le API registrate][1]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API: ![Add API][2]

6. Immettere un **nome** descrittivo per l'API.
	
7. In **Origine** selezionare **API disponibili** per selezionare le API predefinite e selezionare **Office 365 Outlook**: ![selezionare l'API di Office 365 Outlook][3]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**: ![configurare le impostazioni dell’API di Office 365 Outlook][4]

9. Immettere il valore *Chiave app* e *Segreto app* dell’applicazione di Office 365 Azure Active Directory (ADD). Se non è disponibile, vedere la sezione "Registrare un'app di ADD per l'utilizzo con PowerApps" in questo argomento per creare i valori chiave e segreto necessari.
 
	> [AZURE.IMPORTANT] Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.

10. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di Office 365 Outlook all'ambiente del servizio app.


## Facoltativo: registrare un'app di ADD per l'utilizzo con API di PowerApps Office 365

Se non si dispone di un'app ADD esistente con i valori chiave e segreto, utilizzare i passaggi seguenti per creare l'applicazione e ottenere i valori necessari.

1. Aprire il [portale di Azure][5].

2. Selezionare **Sfoglia** e quindi selezionare **Active Directory**.

	>[AZURE.NOTE] Questo consente di aprire Active Directory nel portale di Azure classico.

3. Selezionare il nome tenant dell'organizzazione: ![Avviare Azure Active Directory][6]

4. Selezionare la scheda **Applicazioni** e selezionare **Aggiungi**: ![Applicazioni tenant ADD][7]

5. In **Aggiungi applicazione**:

	1. Immettere un **Nome** per l'applicazione.  
	3. Lasciare il tipo di applicazione come **Web**.  
	3. Selezionare **Avanti**.  

	![Aggiungere l'applicazione ADD - info app][8]

6. In **Proprietà app**:

	1. Immettere l'**URL DI ACCESSO** dell'applicazione. Poiché si intende eseguire l'autenticazione con AAD per PowerApps, impostare l'URL di accesso su \__https://login.windows.net_.
2. Immettere un valore valido di **URI ID APP** per l'app.  
	3. Selezionare **OK**.  

	![Aggiungere l'applicazione ADD - proprietà app][9]

7. Al termine, si verrà reindirizzati alla nuova app ADD. Selezionare **Configura**: ![App ADD Contoso][10]

8. Impostare l’**URL di risposta** nella sezione _OAuth 2_ sull'URL di reindirizzamento ricevuto quando è stata aggiunta la nuova API di Office 365 Outlook nel portale di Azure (in questo argomento). Quindi selezionare **Aggiungi applicazione**: ![Configurare l'applicazione ADD Contoso][11]

9. Nella finestra **Autorizzazioni per altre applicazioni** selezionare **Office 365 Exchange Online** e selezionare **OK**: ![Delegato app Contoso][12]

10. Nella pagina di configurazione, notare che _Office 365 Exchange Online_ viene aggiunto all’elenco _Autorizzazioni per altre applicazioni_.

11. Selezionare **Autorizzazioni delegate** per _Office 365 Exchange Online_ e quindi selezionare le autorizzazioni seguenti:

	- Lettura e scrittura dei contatti dell'utente
	- Lettura dei contatti dell'utente
	- Lettura e scrittura dei calendari dell'utente
	- Lettura dei calendari dell’utente
	- Invio di messaggi di posta elettronica come utente
	- Lettura e scrittura della posta dell'utente
	- Lettura della posta utente

	![Autorizzazioni delegate app Contoso][13]

Viene creata una nuova applicazione Azure Active Directory È possibile utilizzare questa app nella configurazione dell'API di Office 365 Outlook nel portale di Azure.

Per informazioni utili sulle applicazioni AAD, vedere [Come vengono aggiunte le applicazioni in Azure AD e perché](../active-directory/active-directory-how-applications-are-added.md).

## Vedere le API REST

Informazioni di riferimento sull'[API REST Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md).


## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Office 365 Outlook alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-office365-outlook/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-outlook/add-api.PNG
[3]: ./media/powerapps-create-api-office365-outlook/select-office365-outlook-api.PNG
[4]: ./media/powerapps-create-api-office365-outlook/configure-office365-outlook-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-outlook/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.PNG
[13]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_0330_2016-->
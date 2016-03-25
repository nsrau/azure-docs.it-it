<properties
	pageTitle="Aggiungere l'API di Dynamics CRM Online a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di Dynamics CRM Online nell’ambiente del servizio app dell'organizzazione"
	services=""
    suite="powerapps"
	documentationCenter=""
	authors="schabungbam"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="sameerch"/>

# Creare una nuova API Dynamics CRM in PowerApps Enterprise

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

Aggiungere l'API Dynamics CRM all'ambiente del servizio app dell'organizzazione (tenant).

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.

2. Selezionare **Sfoglia** nella barra delle applicazioni:  
![][1]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*:  
![][2]

4. In **PowerApps** selezionare **Gestione API**:  
![Sfogliare le API registrate][3]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API:  
![Add API][4]

6. Immettere un **nome** descrittivo per l'API.

7. In **Origine** selezionare **API disponibili** per selezionare le API predefinite e selezionare **Dynamics CRM Online**:  
![Selezionare API di Dynamics CRM Online][5]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**:  
![Configurare le impostazioni dell’API di Dynamics CRM Online][6]

9. Immettere l’**Id client** e la **Chiave app** dell'applicazione di Dynamics CRM Online Azure Active Directory (ADD). Se non è disponibile, vedere la sezione "Registrare un'app di ADD per l'utilizzo con PowerApps" in questo argomento per creare i valori ID e segreto necessari.

	> [AZURE.IMPORTANT] Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.

10. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di Dynamics CRM Online all'ambiente del servizio app.

## Registrare un'app di ADD per l'utilizzo con API di PowerApps Dynamics CRM Online

1. Aprire il [portale di Azure](https://portal.azure.com).

2. Selezionare **Sfoglia** e quindi selezionare **Active Directory**:

	> [AZURE.NOTE] Questo consente di aprire Active Directory nel portale di Azure classico.

3. Selezionare il nome tenant dell'organizzazione:  
![Avviare Azure Active Directory][7]

4. Selezionare la scheda **Applicazioni** e selezionare **Aggiungi**:  
![Applicazioni tenant ADD][8]

5. In **Aggiungi applicazione**:

	1. Immettere un **Nome** per l'applicazione.  
	2. Lasciare il tipo di applicazione come **Web**.  
	3. Selezionare **Avanti**.

	![Aggiungere l'applicazione ADD - info app][9]

6. In **Proprietà app**:

	1. Immettere l'**URL DI ACCESSO** dell'applicazione. Poiché si intende eseguire l'autenticazione con AAD per PowerApps, impostare l'URL di accesso su _https://login.windows.net_.
	2. Immettere un valore valido di **URI ID APP** per l'app.  
	3. Selezionare **OK**.  

	![Aggiungere l'applicazione ADD - proprietà app][10]

7. Al termine, si verrà reindirizzati alla nuova app ADD. Selezionare **Configura**:  
![App ADD Contoso][11]

8. Impostare l’**URL di risposta** nella sezione _OAuth 2_ sull'URL di reindirizzamento ricevuto quando è stata aggiunta la nuova API di Dynamics CRM Online nel portale di Azure (in questo argomento):  
![Configurare l'applicazione ADD Contoso][12]

9. Selezionare **Salva**.

Viene creata una nuova applicazione Azure Active Directory È possibile utilizzare questa app nella configurazione dell'API di Dynamics CRM Online nel portale di Azure.

## Vedere le API REST

Informazioni di riferimento sull'[API REST Dynamics CRM Online](../connectors/create-api-crmonline.md).


## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Dynamics CRM Online alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)

<!-- References -->

[1]: ./media/powerapps-create-api-crmonline/browseall.png
[2]: ./media/powerapps-create-api-crmonline/allresources.png
[3]: ./media/powerapps-create-api-crmonline/browse-to-registered-apis.PNG
[4]: ./media/powerapps-create-api-crmonline/add-api.PNG
[5]: ./media/powerapps-create-api-crmonline/select-crmonline-api.PNG
[6]: ./media/powerapps-create-api-crmonline/configure-crmonline-settings.PNG
[7]: ./media/powerapps-create-api-crmonline/launch-aad.PNG
[8]: ./media/powerapps-create-api-crmonline/aad-tenant-applications.PNG
[9]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-appinfo.PNG
[10]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-app-properties.PNG
[11]: ./media/powerapps-create-api-crmonline/contoso-aad-app.PNG
[12]: ./media/powerapps-create-api-crmonline/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0309_2016-->

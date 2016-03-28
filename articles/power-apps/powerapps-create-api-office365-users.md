<properties
	pageTitle="Aggiungere l'API di Office 365 Users a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di Office 365 Users nell’ambiente del servizio app dell'organizzazione"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Creare una nuova API Office 365 Users in PowerApps Enterprise

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

Aggiungere l'API Office 365 Users all'ambiente del servizio app dell'organizzazione (tenant).

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni:  
![][14]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*:  
![][15]  

4. In **PowerApps** selezionare **Gestione API**:    
![Sfogliare le API registrate][1]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API:  
![Add API][2]

6. Immettere un **nome** descrittivo per l'API.  
	
7. In **Origine** selezionare **API disponibili** per selezionare le API predefinite e selezionare **Office 365 Users**:  
![selezionare l’API di Office 365 Users][3]

8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**:  
![configurare le impostazioni dell’API di Office 365 Users][4]

9. Immettere l’*Id client* e il *segreto client* dell'applicazione di Office 365 Azure Active Directory (ADD). Se non è disponibile, vedere la sezione "Registrare un'app di ADD per l'utilizzo con PowerApps" in questo argomento per creare i valori ID e segreto necessari.  

	> [AZURE.IMPORTANT] Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.  

10. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di Office 365 Users all'ambiente del servizio app.

## Facoltativo: registrare un'app di ADD per l'utilizzo con API di PowerApps Office 365 Users

Se non si dispone di un'app ADD esistente con i valori chiave e segreto, utilizzare i passaggi seguenti per creare l'applicazione e ottenere i valori necessari.

1. Aprire il [portale di Azure][5].

2. Selezionare **Sfoglia** e quindi selezionare **Active Directory**:  

	> [AZURE.NOTE] Questo consente di aprire Active Directory nel portale di Azure classico.

3. Selezionare il nome tenant dell'organizzazione:  
![Avviare Azure Active Directory][6]

4. Selezionare la scheda **Applicazioni** e selezionare **Aggiungi**:  
![Applicazioni tenant ADD][7]

5. In **Aggiungi applicazione**:  

	1. Immettere un **Nome** per l'applicazione.  
	2. Lasciare il tipo di applicazione come **Web**.  
	3. Selezionare **Avanti**.  

	![Aggiungere l'applicazione ADD - info app][8]

6. In **Proprietà app**:  

	1. Immettere l'**URL DI ACCESSO** dell'applicazione. Poiché si intende eseguire l'autenticazione con AAD per PowerApps, impostare l'URL di accesso su \__https://login.windows.net_.
	2. Immettere un valore valido di **URI ID APP** per l'app.  
	3. Selezionare **OK**.  

	![Aggiungere l'applicazione ADD - proprietà app][9]

7. Al termine, si verrà reindirizzati alla nuova app ADD. Selezionare **Configura**:  
![App ADD Contoso][10]

8. Impostare l’**URL di risposta** nella sezione _OAuth 2_ sull'URL di reindirizzamento ricevuto quando è stata aggiunta la nuova API di Office 365 Users nel portale di Azure (in questo argomento). Selezionare **Aggiungi applicazione**:  
![Configurare l'applicazione ADD Contoso][11]

9. Nella finestra **Autorizzazioni per altre applicazioni** selezionare **API di Office 365 Unified (Anteprima)** e selezionare **OK**.

10. Nella pagina di configurazione, notare che _API di Office 365 Unified (Anteprima)_ viene aggiunto all’elenco _Autorizzazioni per altre applicazioni_.

11. Selezionare **Autorizzazioni delegate** per _API di Office 365 Unified (anteprima)_ e selezionare l’autorizzazione **Leggere i profili di base di tutti gli utenti**.

Viene creata una nuova applicazione Azure Active Directory È possibile utilizzare questa app nella configurazione dell'API di Office 365 Users nel portale di Azure.

Per informazioni utili sulle applicazioni AAD, vedere [Come vengono aggiunte le applicazioni in Azure AD e perché](../active-directory/active-directory-how-applications-are-added.md).

## Vedere le API REST

Informazioni di riferimento sull'[API REST Office 365 Users](../connectors/create-api-office365-users.md).

## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Office 365 Users alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-office365-users/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-users/add-api.PNG
[3]: ./media/powerapps-create-api-office365-users/select-office365-users-api.PNG
[4]: ./media/powerapps-create-api-office365-users/configure-office365-users-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-users/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-users/contoso-aad-app-configure.PNG

<!----HONumber=AcomDC_0309_2016-->
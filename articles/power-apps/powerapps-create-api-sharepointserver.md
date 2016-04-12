<properties
	pageTitle="Aggiungere l'API di SharePoint Server a PowerApps Enterprise | Microsoft Azure"
	description="Creare o configurare una nuova API di SharePoint Server nell’ambiente del servizio app dell'organizzazione"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
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

# Creare una nuova API SharePoint Server in PowerApps Enterprise

Aggiungere l'API SharePoint Server all'ambiente del servizio app dell'organizzazione (tenant).

## Creare l'API nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere con l'account aziendale. Ad esempio, accedere con *NomeUtente*@*Azienda*.com. Quando si esegue questa operazione, si accede automaticamente alla sottoscrizione della propria società.
 
2. Selezionare **Sfoglia** nella barra delle applicazioni: ![][14]

3. Nell'elenco, è possibile scorrere per trovare PowerApps o digitare *powerapps*: ![][15]

4. In **PowerApps** selezionare **Gestione API**: ![Sfogliare le API registrate][5]

5. In **Gestione API** selezionare **Aggiungi** per aggiungere la nuova API: ![Add API][6]

6. Immettere un **nome** descrittivo per l'API.
7. In **Origine** selezionare **API disponibili** per selezionare le API predefinite e selezionare **SharePoint Server**. 
8. Selezionare **Impostazioni - Configurare le impostazioni necessarie**.
9. Immettere *Id Client* e *Chiave app* di SharePoint Server Azure Active Directory (app ADD) e *URL SharePoint* e *Id risorsa* dell’app ADD Proxy. Seguire i passaggi descritti nella sezione seguente per configurare la connettività al server SharePoint locale.  

	> [AZURE.IMPORTANT] Salvare l'**URL di reindirizzamento**. Questo valore potrebbe essere necessario più avanti in questo argomento.
	
10. Selezionare **OK** per completare i passaggi.

Al termine, verrà aggiunta una nuova API di SharePoint Server all'ambiente del servizio app.


## Configurare la connettività a un Server SharePoint locale

SharePoint Server utilizza Active Directory per l'autenticazione degli utenti. Le API negli ambienti gestiti delle app vengono autenticate mediante Azure Active Directory (ADD). È necessario scambiare il token ADD dell'utente e convertirlo nel token di AD. Questo token di AD può quindi essere utilizzato per connettersi al servizio locale.

[Azure Application Proxy (ADD Proxy)](../active-directory/active-directory-application-proxy-publish.md) viene utilizzato per questo requisito. È un servizio di Azure in disponibilità generale e protegge l'accesso remoto e SSO alle applicazioni Web on-premise. I passaggi per abilitare il Proxy di ADD sono ben documentati in MSDN. In generale, i passaggi includono:

1. [Abilitare i servizi del proxy di applicazione](../active-directory/active-directory-application-proxy-enable.md) - Questo include:  

	- Abilitare il proxy dell’applicazione in Azure AD
	- Installare e registrare il connettore del Proxy dell’applicazione Azure

2. [Pubblicare le applicazioni con il proxy dell’applicazione](../active-directory/active-directory-application-proxy-publish.md) - Questo include:

	- Pubblicare un'app del proxy dell’applicazione tramite la procedura guidata. Annotare l'URL esterno del sito di sharepoint intranet dopo aver creato l'app proxy.
	- Assegnazione di utenti e gruppi all'applicazione.
	- Immettere la configurazione avanzata come SPN (nome entità servizio) che viene utilizzato dal connettore proxy dell'applicazione per recuperare il token Kerberos locale.

Una volta creata l'app proxy, è necessario creare un'altra app ADD che deleghi all'applicazione proxy. Ciò è necessario per ottenere il token di accesso e il token di aggiornamento necessari per il flusso di autorizzazioni. È possibile creare una nuova applicazione ADD seguendo [queste istruzioni](../active-directory/active-directory-integrating-applications.md).

## Riepilogo e passaggi successivi
In questo argomento, è stata aggiunta l'API di Office 365 Outlook alla PowersApps Enterprise. Successivamente, fornire agli utenti accesso all'API, in modo che sia possibile aggiungerla alle loro app:

[Aggiungere una connessione e fornire agli utenti l'accesso](powerapps-manage-api-connection-user-access.md)


<!--References-->
[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_0309_2016-->
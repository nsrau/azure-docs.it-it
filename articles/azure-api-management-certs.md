<properties 
	pageTitle="Caricare un certificato di API di gestione di Microsoft Azure nel portale" 
	description="Informazioni su come caricare il certificato di gestione API athe in Microsoft Azure" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="na" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/20/2015"
	ms.author="adegeo"/>


# Caricare un certificato di gestione API di gestione di Azure

I certificati di gestione consentono di eseguire l'autenticazione con l'API di gestione del servizio fornito da Azure. Molti programmi e strumenti (ad esempio Visual Studio o Azure SDK) utilizzeranno questi certificati per automatizzare la configurazione e la distribuzione dei vari servizi di Azure. Questi non sono realmente correlati ai servizi cloud.

>[AZURE.WARNING]Pertanto, prestare attenzione prima di effettuare questa operazione. Questi tipi di certificati consentono a chiunque si autentica con loro di gestire la sottoscrizione in che cui sono associate.

Ulteriori informazioni sui certificati Azure (compresa la creazione di un certificato autofirmato sono )[disponibili](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)per l'utente se è necessario.

È inoltre possibile utilizzare[Azure Active Directory](http://azure.microsoft.com/documentation/services/active-directory/)per autenticare il codice client per scopi di automazione.

## Creazione di un certificato di gestione

Dopo aver ottenuto un certificato di gestione creato, (file con estensione .cer solo con la chiave pubblica), è possibile caricarlo nel portale. Quando il certificato è disponibile nel portale, chiunque disponga di una corrispondenza certificate (chiave privata) può connettersi tramite l'API di gestione e accedere alle risorse per la sottoscrizione associata.

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).
2. Fare clic su**impostazioni**sul lato sinistro del portale (potrebbe essere necessario scorrere verso il basso). 
    
    ![Impostazioni](./media/azure-api-management-certs/settings.png)

3. Scegliere i**certificati di gestione**scheda.

    ![Impostazioni](./media/azure-api-management-certs/certificates-tab.png)
    
4. Fare clic sul pulsante **Upload**.

    ![Impostazioni](./media/azure-api-management-certs/upload.png)
    
5. Compilare le informazioni della finestra di dialogo e fare clic su al completamento**segno di spunta**.

    ![Impostazioni](./media/azure-api-management-certs/upload-dialog.png)

## Passaggi successivi

Ora che si dispone di un certificato di gestione associato a una sottoscrizione, è possibile (dopo aver installato il certificato corrispondente in locale) a livello di programmazione connettersi alla[API REST di Gestione servizio](https://msdn.microsoft.com/library/azure/ee460799.aspx)e automatizzare le varie risorse di Azure che possono essere associate a tale sottoscrizione.

<!---HONumber=July15_HO5-->
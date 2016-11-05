---
title: 'Esercitazione: Creare un''API personalizzata usando Azure Resource Manager in PowerApp e nei flussi logici | Microsoft Docs'
description: Esercitazione di Azure Resource Manager per la creazione di un'API personalizzata in PowerApp e nei flussi logici
services: ''
suite: powerapps
documentationcenter: ''
author: sunaysv
manager: erikre
editor: ''

ms.service: powerapps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2016
ms.author: mandia

---
# Esercitazione: Creare un'API ARM personalizzata e protetta di AAD per PowerApp e flussi logici
In questa esercitazione vengono illustrati i passaggi necessari per registrare un file Swagger che descrive l'[API ARM][6], e quindi connettersi all'API personalizzata in PowerApp.

> [!IMPORTANT]
> Questo argomento è stato spostato nel sito powerapps.microsoft.com in [Esercitazione: Creare un'API ARM personalizzata e protetta di AAD per PowerApp e flussi logici](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/). Passare a PowerApps per visualizzare la versione più recente. Questo collegamento di Azure verrà archiviato.
> 
> 

## Elementi necessari per iniziare
* Una sottoscrizione di Azure.
* Un account PowerApp

## Abilitare l'autenticazione in Azure Active Directory
In primo luogo, è necessario creare un'applicazione di Azure Active Directory (AAD) che esegue l'autenticazione quando si chiama l'endpoint dell'API ARM.

1. Per creare un'applicazione di AAD, accedere alla [sottoscrizione di Azure][7] e passare ad **Active Directory**: ![](./media/powerapps-azure-resource-manager-tutorial/azureaad.png "Azure Active Directory")
2. In questa pagina scegliere la directory in cui si vuole creare l'applicazione di AAD. Selezionare la directory, quindi nella scheda **Applicazioni** selezionare **Aggiungi**: ![](./media/powerapps-azure-resource-manager-tutorial/azureapplication.png "Applicazione di Azure")
3. Immettere un nome per l'applicazione, selezionare **Applicazione Web e/o API Web**, quindi fare clic sul pulsante **Avanti**: ![](./media/powerapps-azure-resource-manager-tutorial/newapplication.png "Nuova applicazione")
4. In **URL di accesso** immettere: **http://login.windows.net**. In **URI ID app** immettere un URI univoco. Selezionare quindi **Completa**: ![](./media/powerapps-azure-resource-manager-tutorial/newapplication2.png "Nuova seconda applicazione")
5. Dopo aver creato l'applicazione di AAD, passare alla scheda **Configura**. In questa scheda è possibile configurare le autorizzazioni per l'applicazione.
6. In **Autorizzazioni per altre applicazioni** selezionare **Aggiungi applicazione** e immettere le seguenti autorizzazioni, come illustrato: ![](./media/powerapps-azure-resource-manager-tutorial/permissions.png "Autorizzazioni")
   
    Concedere le autorizzazioni necessarie per l'applicazione: ![](./media/powerapps-azure-resource-manager-tutorial/permissions2.png "Concedere autorizzazioni")
7. Selezionare una durata in **Chiavi**. **Copiare e salvare la chiave in un percorso sicuro** perché sarà necessaria in un secondo momento. Annotare anche l'**ID client**: ![](./media/powerapps-azure-resource-manager-tutorial/configurekeys.png "Configurare le chiavi")
8. In **Single Sign-On** immettere l'URL seguente in **URL di risposta**: https://msmanaged-na.consent.azure-apim.net/redirect: ![](./media/powerapps-azure-resource-manager-tutorial/redirecturl.png "URL di reindirizzamento")
9. **Salvare** le modifiche. **Copiare e salvare la chiave in un percorso sicuro**.

## Aggiungere la connessione in PowerApp o nei flussi logici
Dopo avere configurato l'applicazione di AAD, procedere con l'aggiunta dell'API personalizzata.

1. Aprire il [portale Web di PowerApps][1], accedere alla scheda **Connections** (Connessioni) e selezionare **Add a connection** (Aggiungi connessione) nell'angolo in alto a destra: ![](./media/powerapps-azure-resource-manager-tutorial/createnewconnection.png "Creare un'API personalizzata")
2. Selezionare **Add a Custom API** (Aggiungi un'API personalizzata): ![](./media/powerapps-azure-resource-manager-tutorial/connecttocustomapi.png "Creare un'API personalizzata")
3. Caricare il file Swagger ARM, disponibile [per il download][8]\: ![](./media/powerapps-azure-resource-manager-tutorial/createcustom.png "Creare un'API personalizzata")
4. Nella schermata successiva, poiché il file Swagger viene rilevato per l'autenticazione di AAD, è necessario immettere l'ID client AAD, il segreto client, ovvero la **chiave** archiviata in un percorso sicuro e altre impostazioni: ![](./media/powerapps-azure-resource-manager-tutorial/oauthsettings.png "Impostazioni OAuth")
5. Se tutto è configurato correttamente, è possibile usare l'API ARM personalizzata creando una connessione e quindi farvi riferimento durante la compilazione di PowerApp o del flusso logico: ![](./media/powerapps-azure-resource-manager-tutorial/createdcustomapi.png "API personalizzata creata")

È possibile accedere allo stesso modo ai dati che vengono esposti tramite API RESTful e autenticati con OAuth2 di AAD.

Per altri dettagli sulla creazione di PowerApp e di flussi logici, vedere i riferimenti seguenti:

* [Connettersi a Office 365, Twitter e Microsoft Translator][5]
* [Visualizzare i dati da Office 365][4]
* [Creare un'app dai dati][3]
* [Introduzione ai flussi logici][2]

Per domande o commenti sulle API personalizzate, inviare un messaggio di posta elettronica all'indirizzo [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: https://msdn.microsoft.com/library/azure/dn790568.aspx
[7]: https://manage.windowsazure.com
[8]: http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json

<!---HONumber=AcomDC_0713_2016-->
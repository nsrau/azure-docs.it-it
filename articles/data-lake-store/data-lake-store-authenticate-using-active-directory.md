---
title: 'Autenticazione da servizio a servizio: Data Lake Store con Azure Active Directory | Documentazione Microsoft'
description: Informazioni su come ottenere l&quot;autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: dac6c9f3be7b4535f8cb30a9ec0c1e398ca5ff28


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticazione da servizio a servizio](data-lake-store-authenticate-using-active-directory.md)
> * [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store usa Azure Active Directory per l'autenticazione. Prima di creare un'applicazione che funziona con Azure Data Lake Store o Azure Data Lake Analytics, per prima cosa è necessario stabilire come si desidera eseguire l'autenticazione dell'applicazione con Azure Active Directory (Azure AD). Le opzioni disponibili sono due:

* Autenticazione dell'utente finale 
* Autenticazione da servizio a servizio (questo articolo) 

Entrambe queste opzioni comportano che l'applicazione venga fornita con un token OAuth 2.0, che viene associato a ogni richiesta effettuata ad Azure Data Lake Store o Azure Data Lake Analytics.

Questo articolo illustra come creare un'applicazione Web di Azure AD per l'autenticazione da servizio a servizio. Per istruzioni sulla configurazione dell'applicazione Azure AD per l'autenticazione dell'utente finale, vedere [End-user authentication with Data Lake Store using Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticazione dell'utente finale con Data Lake Store tramite Azure Active Directory).

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-active-directory-application"></a>Creare un'applicazione di Active Directory
Questa sezione contiene informazioni su come creare e configurare un'applicazione Web di Azure AD per l'autenticazione da servizio a servizio con Azure Data Lake Store tramite Azure Active Directory. Si noti che la creazione di una "applicazione di Active Directory" crea automaticamente un'entità servizio, ma non crea effettivamente un'app o codice.

### <a name="step-1-create-an-azure-active-directory-application"></a>Passaggio 1: Creare un'applicazione di Azure Active Directory
1. Accedere all'account di Azure tramite il [portale classico](https://manage.windowsazure.com/).
2. Selezionare **Active Directory** dal riquadro di sinistra.
   
     ![selezionare Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. Selezionare il tipo di Active Directory che si vuole usare per creare la nuova applicazione. Se sono disponibili più tipi di Active Directory, creare l'applicazione nella directory in cui si trova la sottoscrizione. È possibile concedere l'accesso solo alla risorsa nella sottoscrizione per le applicazioni presenti nella stessa directory della sottoscrizione.  
   
     ![scegliere la directory](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. Per visualizzare le applicazioni nella directory, fare clic su **Applicazioni**.
   
     ![visualizzare le applicazioni](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. Se non è stata creata un'applicazione in questa directory, dovrebbe essere visualizzata un'immagine simile a questa riportata di seguito. Fare clic su **AGGIUNGI APPLICAZIONE**
   
     ![Aggiungi applicazione](./media/data-lake-store-authenticate-using-active-directory/create-application.png)
   
     In alternativa, fare clic su **Aggiungi** nel riquadro inferiore.
   
     ![add](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)
6. Specificare un nome per l'applicazione e selezionare il tipo di applicazione che si vuole creare. Per questa esercitazione creare un' **APPLICAZIONE WEB E/O API WEB** e fare clic sul pulsante Avanti.
   
     ![assegnare un nome all'applicazione](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

    > [!TIP]
    > Specificare un nome per l'applicazione per facilitarne la ricerca in futuro. Nel corso di questa esercitazione si effettuerà la ricerca di questa applicazione per poi assegnarla a un account di Data Lake Store.
    > 
    > 

7. Compilare le proprietà per l'app. Per **URL ACCESSO**specificare l'URI di un sito Web che descrive l'applicazione. L'esistenza del sito Web non viene convalidata. 
   Per **URI ID APP**specificare l'URI che identifica l'applicazione.
   
     ![proprietà dell'applicazione](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)
   
    Fare clic sul segno di spunta per completare la procedura guidata e creare l'applicazione.

### <a name="step-2-get-client-id-client-secret-and-token-endpoint"></a>Passaggio 2: Ottenere l'ID client, il segreto client e l'endpoint di token
Quando si esegue l'accesso a livello di codice, è necessario l'ID dell'applicazione. Se l'applicazione viene eseguita con le proprie credenziali, è necessaria anche una chiave di autenticazione.

1. Fare clic sulla scheda **Configura** per configurare la password dell'applicazione.
   
     ![configura applicazione](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)
2. Copiare l' **ID CLIENT**.
   
     ![ID CLIENT](./media/data-lake-store-authenticate-using-active-directory/client-id.png)
3. Se l'applicazione viene eseguita con credenziali proprie, scorrere fino alla sezione **Chiavi** e selezionare la durata della validità della password.
   
     ![chiavi](./media/data-lake-store-authenticate-using-active-directory/create-key.png)
4. Selezionare **Salva** per creare la chiave.
   
    ![Salva](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)
   
    Viene visualizzata la chiave salvata, che è possibile copiare. Dato che non sarà possibile recuperare la chiave in seguito, copiarla ora.
   
    ![chiave salvata](./media/data-lake-store-authenticate-using-active-directory/save-key.png)
5. Recuperare l'endpoint di token selezionando **Visualizza endpoint** nella parte inferiore della schermata e recuperare il valore per il campo **Endpoint di token OAuth 2.0**, come illustrato di seguito.  
   
    ![tenant id](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

#### <a name="note-down-the-following-properties-that-you-will-need-for-the-next-steps"></a>Annotare le proprietà seguenti, che verranno usate nei passaggi successivi:
1. Nome dell'ID dell'applicazione Web creato nel passaggio 1.6 precedente
2. ID client recuperato nel passaggio 2.2 precedente
3. Chiave creata nel passaggio 2.4 precedente
4. ID tenant recuperato nel passaggio 2.5 precedente

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Passaggio 3: Assegnare l'applicazione Azure AD al file o alla cartella dell'account di Azure Data Lake Store (solo per l'autenticazione da servizio a servizio)
1. Accedere al nuovo [portale di Azure](https://portal.azure.com) e aprire l'account Azure Data Lake Store da associare all'applicazione Azure Active Directory creata in precedenza.
2. Nel pannello dell'account di Archivio Data Lake, fare clic su **Esplora dati**.
   
    ![Creare directory nell'account Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Creare directory nell'account Data Lake Store")
3. Nel pannello **Esplora dati** fare clic sul file o sulla cartella a cui si desidera fornire l'accesso all'applicazione Azure AD e quindi fare clic su **Accesso**. Per configurare l'accesso a un file, è necessario fare clic su **Accesso** dal pannello **Anteprima file**.
   
    ![Configurare elenchi di controllo di accesso nel file system di Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Configurare elenchi di controllo di accesso nel file system di Data Lake")
4. Il pannello di **accesso** elenca i profili di accesso standard e personalizzato già assegnati all'elemento radice. Fare clic sull'icona **Aggiungi** per aggiungere elenchi di controllo per l'accesso personalizzato.
   
    ![Elencare gli accessi standard e personalizzati](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Elencare gli accessi standard e personalizzati")
5. Fare clic sull'icona **Aggiungi** per aprire il pannello **Aggiungi accesso personalizzato**. In questo pannello fare clic su **Seleziona utente o gruppo** e, in questo **pannello**, cercare l'applicazione di Azure Active Directory precedentemente creata. Se è presente un elevato numero di gruppi, usare la casella di testo nella parte superiore per filtrare in base al nome del gruppo. Fare clic sul gruppo che si desidera aggiungere e quindi su **Seleziona**.
   
    ![Aggiungere un gruppo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Aggiungere un gruppo")
6. Fare clic su **Selezionare le autorizzazioni**, selezionare le autorizzazioni e se si desidera assegnare le autorizzazioni come un ACL predefinito, ACL di accesso o entrambi. Fare clic su **OK**.
   
    ![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Assegnare autorizzazioni a un gruppo")
   
    Per altre informazioni sulle autorizzazioni in Data Lake Store e gli ACL predefiniti/di accesso, vedere [Controllo di accesso in Data Lake Store](data-lake-store-access-control.md).
7. Nel pannello **Aggiungi accesso personalizzato** fare clic su **OK**. Il gruppo appena aggiunto, con le autorizzazioni associate, risulterà ora elencato nel pannello di **accesso** .
   
    ![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Assegnare autorizzazioni a un gruppo")    

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata un'applicazione Web di Azure AD e sono state raccolte le informazioni necessarie nelle applicazioni client create tramite .NET SDK, Java SDK e così via. È ora possibile passare agli articoli seguenti che illustrano come usare l'applicazione Web di Azure AD per eseguire prima l'autenticazione con Data Lake Store e quindi altre operazioni sull'archivio.

* [Introduzione a Azure Data Lake Store utilizzando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Introduzione ad Azure Data Lake Store con Java SDK](data-lake-store-get-started-java-sdk.md)

Questo articolo ha illustrato le procedure di base necessarie per creare ed eseguire un'entità utente per l'applicazione. Per altre informazioni, vedere gli articoli seguenti:
* [Usare PowerShell per creare un'entità servizio](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Usare l'autenticazione del certificato per l'autenticazione basata su entità servizio](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Other methods to authenticate to Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios) (Altri metodi di autenticazione in Azure AD)





<!--HONumber=Jan17_HO4-->



---
title: 'Autenticazione da servizio a servizio: Data Lake Store con Azure Active Directory | Documentazione Microsoft'
description: Informazioni su come ottenere l'autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 9a4502ecfd5471cc2a968f995e66e595f59384b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticazione da servizio a servizio](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Store usa Azure Active Directory per l'autenticazione. Prima di creare un'applicazione che funzioni con Azure Data Lake Store, è necessario stabilire come autenticare l'applicazione con Azure Active Directory (Azure AD). Le opzioni disponibili sono due:

* Autenticazione dell'utente finale 
* Autenticazione da servizio a servizio (questo articolo) 

Entrambe queste opzioni comportano che l'applicazione venga fornita con un token OAuth 2.0, che viene associato a ogni richiesta eseguita ad Azure Data Lake Store.

Questo articolo illustra come creare un'**applicazione Web di Azure AD per l'autenticazione da servizio a servizio**. Per istruzioni sulla configurazione dell'applicazione Azure AD per l'autenticazione dell'utente finale, vedere [Autenticazione dell'utente finale con Data Lake Store tramite Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Passaggio 1: Creare un'applicazione Web di Active Directory

Creare e configurare un'applicazione Web di Azure AD per l'autenticazione da servizio a servizio con Azure Data Lake Store tramite Azure Active Directory. Per istruzioni, vedere [Creare un'applicazione Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Mentre si seguono le istruzioni nel collegamento precedente, assicurarsi di selezionare **App Web/API** come tipo di applicazione, come illustrato nella schermata seguente:

![Creare un'app Web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Creare un'app Web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Passaggio 2: Ottenere l'ID applicazione, la chiave di autenticazione e l'ID tenant
Quando si esegue l'accesso a livello di codice, è necessario l'ID dell'applicazione. Se l'applicazione viene eseguita con le proprie credenziali, è necessaria anche una chiave di autenticazione.

* Per istruzioni su come recuperare l'ID applicazione e la chiave di autenticazione, definita anche segreto client, per l'applicazione, vedere [Ottenere l'ID applicazione e la chiave di autenticazione](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Per istruzioni su come recuperare l'ID tenant, vedere [Ottenere l'ID tenant](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>Passaggio 3: Assegnare l'applicazione Azure AD al file o alla cartella dell'account di Azure Data Lake Store


1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'account Azure Data Lake Store che si desidera associare all'applicazione Azure Active Directory creata in precedenza.
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
7. Nel pannello **Aggiungi accesso personalizzato** fare clic su **OK**. Il gruppo appena aggiunto, con le autorizzazioni associate, è ora elencato nel pannello di **Accesso**.
   
    ![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Assegnare autorizzazioni a un gruppo")

> [!NOTE]
> Se si desidera usare gli SDK per creare un account Data Lake Store, è necessario assegnare l'applicazione web di Azure AD come ruolo per il gruppo di risorse in cui viene creato l'account Data Lake Store.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Passaggio 4: Ottenere l'endpoint di token OAuth 2.0 (solo per applicazioni basate su Java)

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su Active Directory dal riquadro a sinistra.

2. Nel riquadro a sinistra fare clic su **Registrazioni per l'app**.

3. Nella parte superiore del pannello Registrazioni per l'app fare clic su **Endpoint**.

    ![Endpoint di token OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Endpoint di token OAuth")

4. Copiare l'endpoint di token OAuth 2.0 dall'elenco di endpoint.

    ![Endpoint di token OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Endpoint di token OAuth")   

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata un'applicazione Web di Azure AD e sono state raccolte le informazioni necessarie nelle applicazioni client create tramite .NET SDK, Java, Python, API REST e così via. È ora possibile passare agli articoli seguenti che illustrano come usare l'applicazione nativa di Azure AD per eseguire prima l'autenticazione con Data Lake Store e quindi altre operazioni sull'archivio.

* [Autenticazione da servizio a servizio con Data Lake Store tramite Java](data-lake-store-service-to-service-authenticate-java.md)
* [Autenticazione da servizio a servizio con Data Lake Store tramite .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Autenticazione da servizio a servizio con Data Lake Store tramite Python](data-lake-store-service-to-service-authenticate-python.md)
* [Autenticazione da servizio a servizio con Data Lake Store tramite API REST](data-lake-store-service-to-service-authenticate-rest-api.md)



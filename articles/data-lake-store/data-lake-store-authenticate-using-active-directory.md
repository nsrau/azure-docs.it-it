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
ms.date: 04/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 1d712ef6987a4af2014bedb54378f288bcf535a8
ms.lasthandoff: 04/22/2017


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

Questo articolo illustra come creare un'**applicazione Web di Azure AD per l'autenticazione da servizio a servizio**. Per istruzioni sulla configurazione dell'applicazione Azure AD per l'autenticazione dell'utente finale, vedere [End-user authentication with Data Lake Store using Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticazione dell'utente finale con Data Lake Store tramite Azure Active Directory).

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Passaggio 1: Creare un'applicazione Web di Active Directory

Creare e configurare un'applicazione Web di Azure AD per l'autenticazione da servizio a servizio con Azure Data Lake Store tramite Azure Active Directory. Per istruzioni, vedere [Creare un'applicazione Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Mentre si seguono le istruzioni nel collegamento riportato sopra, assicurarsi di selezionare **App Web/API** come tipo di applicazione, come illustrato nella schermata seguente.

![Creare un'app Web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Creare un'app Web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Passaggio 2: ottenere l'ID applicazione, la chiave di autenticazione e l'ID del tenant
Quando si esegue l'accesso a livello di codice, è necessario l'ID dell'applicazione. Se l'applicazione viene eseguita con le proprie credenziali, è necessaria anche una chiave di autenticazione.

* Per istruzioni su come recuperare l'ID applicazione e la chiave di autenticazione, definita anche segreto client, per l'applicazione, vedere [Ottenere l'ID applicazione e la chiave di autenticazione](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Per istruzioni su come recuperare l'ID tenant, vedere [Ottenere l'ID tenant](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Passaggio 3: Assegnare l'applicazione Azure AD al file o alla cartella dell'account di Azure Data Lake Store (solo per l'autenticazione da servizio a servizio)
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

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Passaggio 4: Ottenere l'endpoint di token OAuth 2.0 (solo per applicazioni basate su Java)

1. Accedere al nuovo [portale di Azure](https://portal.azure.com) e fare clic su Active Directory dal riquadro a sinistra.

2. Nel riquadro a sinistra fare clic su **Registrazioni per l'app**.

3. Nella parte superiore del pannello Registrazioni per l'app fare clic su **Endpoint**.

    ![Endpoint di token OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Endpoint di token OAuth")

4. Copiare l'endpoint di token OAuth 2.0 dall'elenco di endpoint.

    ![Endpoint di token OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Endpoint di token OAuth")   

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata un'applicazione Web di Azure AD e sono state raccolte le informazioni necessarie nelle applicazioni client create tramite .NET SDK, Java SDK e così via. È ora possibile passare agli articoli seguenti che illustrano come usare l'applicazione Web di Azure AD per eseguire prima l'autenticazione con Data Lake Store e quindi altre operazioni sull'archivio.

* [Introduzione a Azure Data Lake Store utilizzando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Introduzione ad Azure Data Lake Store con Java SDK](data-lake-store-get-started-java-sdk.md)

Questo articolo ha illustrato le procedure di base necessarie per creare ed eseguire un'entità utente per l'applicazione. Per altre informazioni, vedere gli articoli seguenti:
* [Usare PowerShell per creare un'entità servizio](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Usare l'autenticazione del certificato per l'autenticazione basata su entità servizio](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Other methods to authenticate to Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios) (Altri metodi di autenticazione in Azure AD)




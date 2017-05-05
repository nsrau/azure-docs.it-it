---
title: 'Autenticazione dell&quot;utente finale: Data Lake Store con Azure Active Directory | Documentazione Microsoft'
description: Informazioni su come ottenere l&quot;autenticazione dell&quot;utente finale con Data Lake Store tramite Azure Active Directory
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: c20f5c39b00992d801909c8e5de292f3c2f12673
ms.lasthandoff: 04/22/2017


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticazione dell'utente finale con Data Lake Store tramite Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticazione da servizio a servizio](data-lake-store-authenticate-using-active-directory.md)
> * [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store usa Azure Active Directory per l'autenticazione. Prima di creare un'applicazione che funziona con Azure Data Lake Store o Azure Data Lake Analytics, per prima cosa è necessario stabilire come si desidera eseguire l'autenticazione dell'applicazione con Azure Active Directory (Azure AD). Le opzioni disponibili sono due:

* Autenticazione dell'utente finale (questo articolo)
* Autenticazione da servizio a servizio

Entrambe queste opzioni comportano che l'applicazione venga fornita con un token OAuth 2.0, che viene associato a ogni richiesta effettuata ad Azure Data Lake Store o Azure Data Lake Analytics.

Questo articolo illustra come creare un'**applicazione nativa di Azure AD per l'autenticazione dell'utente finale**. Per istruzioni sulla configurazione dell'applicazione Azure AD per l'autenticazione da servizio a servizio, vedere [Autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* ID sottoscrizione personale. È possibile recuperarlo dal portale di Azure. Ad esempio, è disponibile dal pannello dell'account Data Lake Store.
  
    ![Ottenere l'ID sottoscrizione](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Il nome di dominio di Azure AD. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. Nello screenshot seguente il nome di dominio è **contoso.onmicrosoft.com**, mentre il GUID racchiuso tra parentesi quadre è l'ID tenant. 
  
    ![Ottenere il dominio AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Autenticazione dell'utente finale
Questo è l'approccio consigliato se si desidera che un utente finale acceda all'applicazione tramite Azure AD. L'applicazione sarà in grado di accedere alle risorse di Azure con lo stesso livello di accesso dell'utente che ha effettuato l'accesso. L'utente finale dovrà fornire le sue credenziali periodicamente affinché l'applicazione possa mantenere attivo l'accesso.

L'accesso effettuato dall'utente finale comporta l'assegnazione di un token di accesso e un token di aggiornamento all'applicazione. Il token di accesso viene associato a ogni richiesta effettuata a Data Lake Store o Data Lake Analytics, ed è valido per un'ora, per impostazione predefinita. Il token di aggiornamento può essere usato per ottenere un nuovo token di accesso, ed è valido per un massimo di due settimane per impostazione predefinita (se usato di frequente). È possibile usare due diversi approcci per l'accesso degli utenti finali.

### <a name="using-the-oauth-20-pop-up"></a>Usando la finestra popup OAuth 2.0
L'applicazione può attivare una finestra popup di autorizzazione OAuth 2.0, in cui l'utente finale può immettere le credenziali. Questa opzione funziona anche con l'autenticazione a due fattori (2FA) di Azure AD, se necessario. 

> [!NOTE]
> Questo metodo non è ancora supportato in Azure AD Authentication Library (ADAL) per Python o Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Accesso diretto tramite le credenziali dell'utente
L'applicazione può fornire direttamente le credenziali utente ad Azure AD. Questo metodo funziona solo con gli account utente con ID organizzazione. Non è compatibile con gli account utente personali/con "ID dinamico", inclusi quelli che terminano con @outlook.com o @live.com. Inoltre, questo metodo non è compatibile con gli account utente che richiedono l'autenticazione a due fattori (2FA) di Azure AD.

### <a name="what-do-i-need-to-use-this-approach"></a>Di cosa ho bisogno per usare questo approccio?
* Il nome di dominio di Azure AD. È già elencato nei prerequisiti riportati in questo articolo.
* **L'applicazione nativa** di Azure AD
* L'ID applicazione per l'applicazione nativa di Azure AD
* L'URI di reindirizzamento per l'applicazione nativa di Azure AD
* Impostare autorizzazioni delegate


## <a name="step-1-create-an-active-directory-native-application"></a>Passaggio 1: creare un'applicazione nativa di Active Directory

Creare e configurare un'applicazione nativa di Azure AD per l'autenticazione dell'utente finale con Azure Data Lake Store tramite Azure Active Directory. Per istruzioni, vedere [Creare un'applicazione Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Mentre si seguono le istruzioni nel collegamento riportato sopra, assicurarsi di selezionare **Nativa** come tipo di applicazione, come illustrato nella schermata seguente.

![Creare un'app Web](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Creare un'app nativa")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Passaggio 2: ottenere l'ID dell'applicazione e l'URI di reindirizzamento

Vedere [Ottenere l'ID dell'applicazione](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) per recuperare l'ID dell'applicazione, definito anche ID client nel portale di Azure classico, dell'applicazione nativa Azure AD.

Per recuperare l'URI di reindirizzamento, attenersi alla procedura seguente.

1. Dal Portale di Azure selezionare **Azure Active Directory**, fare clic su **Registrazioni per l'app**, quindi individuare l'applicazione nativa Azure AD Azure appena creata e fare clic.

2. Dal pannello **Impostazioni** per l'applicazione fare clic su **URI di reindirizzamento**.

    ![Ottenere l'URI di reindirizzamento](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Copiare il valore visualizzato.


## <a name="step-3-set-permissions"></a>Passaggio 3: Impostare le autorizzazioni

1. Dal Portale di Azure selezionare **Azure Active Directory**, fare clic su **Registrazioni per l'app**, quindi individuare l'applicazione nativa Azure AD Azure appena creata e fare clic.

2. Dal pannello **Impostazioni** dell'applicazione selezionare **Autorizzazioni necessarie** e fare clic su **Aggiungi**.

    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Nel pannello **Aggiungi accesso all'API** fare clic su **Selezionare un'API**, fare clic su **Azure Data Lake**, quindi fare clic su **Seleziona**.

    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Nel pannello **Aggiungi accesso all'API** fare clic su **Selezionare le autorizzazioni**, selezionare la casella di controllo per concedere l'**accesso completo a Data Lake Store** e quindi fare clic su **Seleziona**.

    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Fare clic su **Done**.

5. Ripetere gli ultimi due passaggi per concedere le autorizzazioni anche per le **API Gestione dei servizi di Windows Azure**.
   
## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata un'applicazione nativa di Azure AD e sono state raccolte le informazioni necessarie nelle applicazioni client create tramite .NET SDK, Java SDK e così via. È ora possibile passare agli articoli seguenti che illustrano come usare l'applicazione Web di Azure AD per eseguire prima l'autenticazione con Data Lake Store e quindi altre operazioni sull'archivio.

* [Introduzione a Azure Data Lake Store utilizzando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Introduzione ad Azure Data Lake Store con Java SDK](data-lake-store-get-started-java-sdk.md)
* [Introduzione ad Azure Data Lake Store con API REST](data-lake-store-get-started-rest-api.md)



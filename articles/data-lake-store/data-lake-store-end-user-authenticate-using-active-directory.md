---
title: Eseguire l&quot;autenticazione con Data Lake Store usando Active Directory | Documentazione Microsoft
description: Informazioni su come eseguire l&quot;autenticazione con Data Lake Store usando Active Directory
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
ms.date: 10/17/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7e7c9fd2fe7e6327cd39c4c26583c8fd556c9044


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticazione dell'utente finale con Data Lake Store tramite Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticazione da servizio a servizio](data-lake-store-authenticate-using-active-directory.md)
> * [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store usa Azure Active Directory per l'autenticazione. Prima di creare un'applicazione che funziona con Azure Data Lake Store o Azure Data Lake Analytics, per prima cosa è necessario stabilire come si desidera eseguire l'autenticazione dell'applicazione con Azure Active Directory (Azure AD). Le opzioni disponibili sono due:

* Autenticazione dell'utente finale 
* Autenticazione da servizio a servizio 

Entrambe queste opzioni comportano che l'applicazione venga fornita con un token OAuth 2.0, che viene associato a ogni richiesta effettuata ad Azure Data Lake Store o Azure Data Lake Analytics.

Questo articolo illustra come creare un'applicazione Web di Azure AD per l'autenticazione dell'utente finale. Per istruzioni sulla configurazione dell'applicazione Azure AD per l'autenticazione da servizio a servizio, vedere [Autenticazione da servizio a servizio con Data Lake Store tramite Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* ID sottoscrizione personale. È possibile recuperarlo dal portale di Azure. Ad esempio, è disponibile dal pannello dell'account Data Lake Store.
  
    ![Ottenere l'ID sottoscrizione](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)
* Il nome di dominio di Azure AD. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. Nello screenshot seguente il nome di dominio è **contoso.microsoft.com**, mentre il GUID racchiuso tra parentesi quadre è l'ID tenant. 
  
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
L'applicazione può fornire direttamente le credenziali utente ad Azure AD. Questo metodo funziona solo con gli account utente con ID organizzazione; non è compatibile con gli account utente personali / con "ID dinamico", inclusi quelli che terminano con @outlook.com o @live.com.. Inoltre, questo metodo non è compatibile con gli account utente che richiedono l'autenticazione a due fattori (2FA) di Azure AD.

### <a name="what-do-i-need-to-use-this-approach"></a>Di cosa ho bisogno per usare questo approccio?
* Il nome di dominio di Azure AD. È già elencato nei prerequisiti riportati in questo articolo.
* L'**applicazione Web**di Azure AD
* L'ID client per l'applicazione Web di Azure AD
* L'URI di risposta per l'applicazione Web di Azure AD
* Impostare autorizzazioni delegate

Per istruzioni su come creare un'applicazione Web di Azure AD e configurarla per i requisiti elencati in precedenza, vedere la sezione [Creare un'applicazione di Active Directory](#create-an-active-directory-application) seguente. 

## <a name="create-an-active-directory-application"></a>Creare un'applicazione di Active Directory
Questa sezione contiene informazioni su come creare e configurare un'applicazione Web di Azure AD per l'autenticazione dell'utente finale con Azure Data Lake Store tramite Azure Active Directory.

### <a name="step-1-create-an-azure-active-directory-application"></a>Passaggio 1: Creare un'applicazione di Azure Active Directory
> [!NOTE]
> Nella procedura seguente viene usato il portale di Azure. È possibile anche creare un'applicazione Azure AD tramite [Azure PowerShell](../resource-group-authenticate-service-principal.md) o l'[interfaccia della riga di comando di Azure](../resource-group-authenticate-service-principal-cli.md).
> 
> 

1. Accedere all'account di Azure tramite il [portale classico](https://manage.windowsazure.com/).
2. Selezionare **Active Directory** dal riquadro di sinistra.
   
     ![selezionare Active Directory](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
3. Selezionare il tipo di Active Directory che si vuole usare per creare la nuova applicazione. Se sono disponibili più tipi di Active Directory, creare l'applicazione nella directory in cui si trova la sottoscrizione. È possibile concedere l'accesso solo alla risorsa nella sottoscrizione per le applicazioni presenti nella stessa directory della sottoscrizione.  
   
     ![scegliere la directory](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
4. Per visualizzare le applicazioni nella directory, fare clic su **Applicazioni**.
   
     ![visualizzare le applicazioni](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)
5. Se non è stata creata un'applicazione in questa directory, dovrebbe essere visualizzata un'immagine simile a questa riportata di seguito. Fare clic su **AGGIUNGI APPLICAZIONE**
   
     ![Aggiungi applicazione](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)
   
     In alternativa, fare clic su **Aggiungi** nel riquadro inferiore.
   
     ![add](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)
6. Specificare un nome per l'applicazione e selezionare il tipo di applicazione che si vuole creare. Per questa esercitazione creare un' **APPLICAZIONE WEB E/O API WEB** e fare clic sul pulsante Avanti.
   
     ![assegnare un nome all'applicazione](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)
7. Compilare le proprietà per l'app. Per **URL ACCESSO**specificare l'URI di un sito Web che descrive l'applicazione. L'esistenza del sito Web non viene convalidata. 
   Per **URI ID APP**specificare l'URI che identifica l'applicazione.
   
     ![proprietà dell'applicazione](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)
   
    Fare clic sul segno di spunta per completare la procedura guidata e creare l'applicazione.

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>Passaggio 2: Ottenere l'ID client, l'URI di risposta e impostare le autorizzazioni delegate
1. Fare clic sulla scheda **Configura** per configurare la password dell'applicazione.
   
     ![configura applicazione](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)
2. Copiare l' **ID CLIENT**.
   
     ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)
3. Nella sezione **Single Sign-On** copiare l'**URI di risposta**.
   
    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)
4. In **Autorizzazioni per altre applicazioni** fare clic su **Aggiungi applicazione**
   
    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)
5. Nella procedura guidata **Autorizzazioni per altre applicazioni** selezionare **Azure Data Lake** e **API** **Gestione dei servizi Windows Azure** e fare clic sul segno di spunta.
6. Per impostazione predefinita, il valore di **Autorizzazioni delegate** per i servizi appena aggiunti è impostato su zero. Fare clic nell'elenco a discesa **Autorizzazioni delegate** per Azure Data Lake e il servizio di gestione Windows Azure e selezionare le caselle di controllo disponibili per impostare i valori su 1. Il risultato dovrebbe avere l'aspetto seguente.
   
     ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
7. Fare clic su **Save**.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata un'applicazione Web di Azure AD e sono state raccolte le informazioni necessarie nelle applicazioni client create tramite .NET SDK, Java SDK e così via. È ora possibile passare agli articoli seguenti che illustrano come usare l'applicazione Web di Azure AD per eseguire prima l'autenticazione con Data Lake Store e quindi altre operazioni sull'archivio.

* [Introduzione a Azure Data Lake Store utilizzando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Introduzione ad Azure Data Lake Store con Java SDK](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Nov16_HO3-->



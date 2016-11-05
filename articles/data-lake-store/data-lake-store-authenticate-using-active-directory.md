---
title: L'autenticazione con Data Lake Store usando Active Directory | Microsoft Docs
description: Informazioni su come eseguire l'autenticazione con Data Lake Store usando Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme

---
# <a name="authenticate-with-data-lake-store-using-azure-active-directory"></a>Eseguire l'autenticazione con Data Lake Store usando Azure Active Directory
Azure Data Lake Store usa Azure Active Directory per l'autenticazione. Prima di creare un'applicazione che funziona con Azure Data Lake Store o Azure Data Lake Analytics, per prima cosa è necessario stabilire come si desidera eseguire l'autenticazione dell'applicazione con Azure Active Directory (Azure AD). Le opzioni disponibili sono due:

* Autenticazione dell'utente finale 
* Autenticazione da servizio a servizio 

Entrambe queste opzioni comportano che l'applicazione venga fornita con un token OAuth 2.0, che viene associato a ogni richiesta effettuata ad Azure Data Lake Store o Azure Data Lake Analytics.

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* ID sottoscrizione personale. È possibile recuperarlo dal portale di Azure. Ad esempio, è disponibile dal pannello dell'account Data Lake Store.
  
    ![Ottenere l'ID sottoscrizione](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)
* Il nome di dominio di Azure AD. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. 
  
    ![Ottenere il dominio AAD](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Autenticazione dell'utente finale
Questo è l'approccio consigliato se si desidera che un utente finale acceda all'applicazione tramite Azure AD. L'applicazione sarà in grado di accedere alle risorse di Azure con lo stesso livello di accesso dell'utente che ha effettuato l'accesso. L'utente finale dovrà fornire le sue credenziali periodicamente affinché l'applicazione possa mantenere attivo l'accesso.

L'accesso effettuato dall'utente finale comporta l'assegnazione di un token di accesso e un token di aggiornamento all'applicazione. Il token di accesso viene associato a ogni richiesta effettuata a Data Lake Store o Data Lake Analytics, ed è valido per un'ora, per impostazione predefinita. Il token di aggiornamento può essere usato per ottenere un nuovo token di accesso, ed è valido per un massimo di due settimane per impostazione predefinita (se usato di frequente). È possibile usare due diversi approcci per l'accesso degli utenti finali.

### <a name="using-the-oauth-2.0-pop-up"></a>Usando la finestra popup OAuth 2.0
L'applicazione può attivare una finestra popup di autorizzazione OAuth 2.0, in cui l'utente finale può immettere le credenziali. Questa opzione funziona anche con l'autenticazione a due fattori (2FA) di Azure AD, se necessario. 

> [!NOTE]
> Questo metodo non è ancora supportato in Azure AD Authentication Library (ADAL) per Python o Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Accesso diretto tramite le credenziali dell'utente
L'applicazione può fornire direttamente le credenziali utente ad Azure AD. Questo metodo funziona solo con gli account utente con ID organizzazione; non è compatibile con gli account utente personali / con "ID dinamico", inclusi quelli che terminano con @outlook.com o @live.com.. Inoltre, questo metodo non è compatibile con gli account utente che richiedono l'autenticazione a due fattori (2FA) di Azure AD.

### <a name="what-do-i-need-to-use-this-approach?"></a>Di cosa ho bisogno per usare questo approccio?
* Il nome di dominio di Azure AD (già elencato nei prerequisiti riportati in questo articolo).
* L' **applicazione client nativa**di Azure AD. 
* L'ID client per l'applicazione client nativa di Azure AD.
* URI di reindirizzamento per l'applicazione client nativa di Azure AD. 

Per istruzioni su come creare un'applicazione Azure AD e recuperare l'ID client, vedere [Creare un'applicazione di Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application). 

> [!NOTE]
> Le istruzioni disponibili tramite i collegamenti riportati sopra si riferiscono a un'applicazione Web di Azure AD, ma i passaggi sono gli stessi anche se si sceglie di creare un'applicazione client nativa.
> 
> 

## <a name="service-to-service-authentication"></a>Autenticazione da servizio a servizio
Questo è l'approccio consigliato se si desidera che l'applicazione esegua automaticamente l'autenticazione con Azure AD, senza richiedere necessariamente le credenziali all'utente finale. L'applicazione sarà in grado di autenticarsi per tutto il periodo di validità delle relative credenziali, che può essere personalizzato per durare anni.

### <a name="what-do-i-need-to-use-this-approach?"></a>Di cosa ho bisogno per usare questo approccio?
* Il nome di dominio di Azure AD (già elencato nei prerequisiti riportati in questo articolo).
* **Applicazione Web**di Azure AD.
* L'ID client per l'applicazione Web di Azure AD.
  
  > [!NOTE]
  > Per istruzioni su come creare un'applicazione Azure AD e recuperare l'ID client, vedere [Creare un'applicazione di Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application).
  > 
  > 
* Configurare l'applicazione Web di Azure AD in modo che usi un certificato o il segreto client. Per creare un'applicazione Web usando un certificato, vedere [Creare un'entità servizio con certificato](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).
* Abilitare l'accesso per l'applicazione Web di Azure AD al file/cartella di Data Lake Store o all'account Data Lake Analytics che si desidera usare. Per istruzioni su come fornire accesso a un'applicazione Azure AD in un file o cartella di Data Lake Store, vedere [Assegnare utenti o gruppi di sicurezza come elenchi di controllo di accesso al file system di Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Azure Data Lake Store utilizzando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Introduzione ad Azure Data Lake Store con Java SDK](data-lake-store-get-started-java-sdk.md)

<!--HONumber=Oct16_HO2-->



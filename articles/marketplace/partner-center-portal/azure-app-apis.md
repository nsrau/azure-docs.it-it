---
title: Onboarding API for Azure apps in the Commercial Marketplace
description: API prerequisites for Azure apps in commercial marketplace on Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279760"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>API per l'onboarding delle app di Azure nel Centro per i partner

Usare *l'API* di invio del Centro per i partner per eseguire query a livello di codice, creare invii e pubblicare offerte di Azure.Use the Partner Center submission API to programmatically query, create submissions for, and publish Azure offers.  Questa API è utile se il tuo account gestisce molte offerte e vuoi automatizzare e ottimizzare il processo di invio per queste offerte.

## <a name="api-prerequisites"></a>Prerequisiti API

Per usare l'API del Centro per i partner per i prodotti di Azure, è necessario disporre di alcune risorse a livello di codice:There are a few programmatic assets that you need in order to use the Partner Center API for Azure Products: 

- un'applicazione Azure Active Directory.
- un token di accesso di Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Passaggio 1: Completare i prerequisiti per l'utilizzo dell'API di invio del Centro per i partnerStep 1: Complete prerequisites for using the Partner Center submission API

Prima di iniziare a scrivere codice per chiamare l'API di invio del Centro per i partner, assicurarsi di aver completato i prerequisiti seguenti.

- Tu o la tua organizzazione dovete disporre di una directory Azure AD e avere autorizzazioni di [amministratore globale](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la directory. Se usi già Office 365 o altri servizi aziendali Microsoft, la directory Azure AD è già disponibile. In caso contrario, è possibile [creare un nuovo Azure AD nel Centro per](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) i partner senza costi aggiuntivi.

- È necessario [associare un'applicazione Azure AD all'account del Centro per](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) i partner e ottenere l'ID tenant, l'ID client e la chiave. You need these values to obtain an Azure AD access token, which you will use in calls to the Microsoft Store submission API.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Come associare un'applicazione Azure AD all'account del Centro per i partnerHow to associate an Azure AD application with your Partner Center account

Per usare l'API di invio di Microsoft Store, è necessario associare un'applicazione Azure AD all'account del Centro per i partner, recuperare l'ID tenant e l'ID client per l'applicazione e generare una chiave. L'applicazione Azure AD rappresenta l'app o il servizio da cui si vuole chiamare l'API di invio del Centro per i partner. Sono necessari l'ID tenant, l'ID client e la chiave per ottenere un token di accesso di Azure AD da passare all'API.

>[!Note]
>È necessario eseguire questa attività una sola volta. Dopo avere ottenuto l'ID tenant, l'ID client e la chiave, puoi riutilizzarli ogni volta che devi creare un nuovo token di accesso di Azure AD.

1. Nel Centro per i partner [associare l'account Del Centro per i partner dell'organizzazione alla directory di Azure AD dell'organizzazione.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. Successivamente, dalla pagina **Utenti** nella sezione **Impostazioni account** del Centro per i partner, [aggiungere l'applicazione Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) che rappresenta l'app o il servizio che verrà usato per accedere agli invii per l'account del Centro per i partner. Assicurarsi di assegnare all'applicazione il ruolo **Manager.** Se l'applicazione non esiste ancora nella directory di Azure AD, è possibile [creare una nuova applicazione Azure AD nel Centro per i partner.](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)
1. Tornare alla pagina **Utenti,** fare clic sul nome dell'applicazione Azure AD per passare alle impostazioni dell'applicazione e copiare i valori **ID tenant** e **ID client.**
1. Fai clic su **Aggiungi nuova chiave**. Nella schermata successiva prendi nota del valore indicato in **Chiave**. Non potrai accedere di nuovo a queste informazioni dopo la chiusura della pagina. Per altre informazioni, vedere [Gestire le chiavi per un'applicazione Azure AD.](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Passaggio 2: Ottenere un token di accesso Azure AD

Prima di chiamare uno dei metodi nell'API di invio del Centro per i partner, è necessario ottenere un token di accesso di Azure AD passato all'intestazione **Authorization** di ogni metodo nell'API. Dopo aver ottenuto un token di accesso, hai 60 minuti per usarlo prima che scada. Dopo la scadenza del token, è possibile aggiornare il token in modo da poter continuare a usarlo nelle chiamate future all'API.

Per ottenere il token di accesso, seguire le istruzioni in Chiamate da [servizio a servizio tramite credenziali client](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) per inviare un `HTTP POST` `https://login.microsoftonline.com/<tenant_id>/oauth2/token` endpoint. Di seguito è riportato un esempio di richiesta:Here is a sample request:

Copia JSON
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Per *tenant_id* il valore `POST URI` tenant_id nei parametri *client_id* e *client_secret,* specificare l'ID tenant, l'ID client e la chiave per l'applicazione recuperati dal Centro per i partner nella sezione precedente. Per il parametro *resource* devi specificare `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Passaggio 3: Usare l'API di invio di Microsoft StoreStep 3: Use the Microsoft Store submission API

Dopo aver creato un token di accesso di Azure AD, è possibile chiamare i metodi nell'API di invio del Centro per i partner. Per creare o aggiornare gli invii, in genere si chiamano più metodi nell'API di invio del Centro per i partner in un ordine specifico. Per informazioni su ogni scenario e la sintassi di ogni metodo, vedere ingestion API swagger.

https://apidocs.microsoft.com/services/partneringestion/

---
title: API di onboarding per app di Azure nel Marketplace commerciale
description: Prerequisiti API per le app di Azure nel Marketplace commerciale nel centro per i partner Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80279760"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>API per l'onboarding delle app di Azure nel centro per i partner

Usare l' *API di invio del centro* per i partner per eseguire query a livello di codice, creare invii per e pubblicare offerte di Azure.  Questa API è utile se l'account gestisce molte offerte e si vuole automatizzare e ottimizzare il processo di invio per queste offerte.

## <a name="api-prerequisites"></a>Prerequisiti delle API

Sono disponibili alcune risorse programmatiche necessarie per usare l'API del centro per i partner per i prodotti Azure: 

- applicazione Azure Active Directory.
- un token di accesso Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Passaggio 1: completare i prerequisiti per l'uso dell'API di invio del centro per i partner

Prima di iniziare a scrivere il codice per chiamare l'API di invio del centro per i partner, verificare di aver completato i prerequisiti seguenti.

- Tu o la tua organizzazione dovete disporre di una directory Azure AD e avere autorizzazioni di [amministratore globale](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la directory. Se usi già Office 365 o altri servizi aziendali Microsoft, la directory Azure AD è già disponibile. In caso contrario, è possibile [creare un nuovo Azure ad nel centro](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) per i partner senza costi aggiuntivi.

- È necessario [associare un'applicazione Azure ad all'account del centro per i partner](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) e ottenere l'ID tenant, l'ID client e la chiave. Questi valori sono necessari per ottenere un token di accesso Azure AD, che sarà usato nelle chiamate all'API di invio del Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Come associare un'applicazione Azure AD all'account del centro per i partner

Per usare l'API di invio Microsoft Store, è necessario associare un'applicazione Azure AD all'account del centro per i partner, recuperare l'ID del tenant e l'ID client per l'applicazione e generare una chiave. L'applicazione Azure AD rappresenta l'app o il servizio da cui si vuole chiamare l'API di invio del centro per i partner. Sono necessari l'ID tenant, l'ID client e la chiave per ottenere un token di accesso di Azure AD da passare all'API.

>[!Note]
>È sufficiente eseguire questa attività una sola volta. Dopo avere ottenuto l'ID tenant, l'ID client e la chiave, puoi riutilizzarli ogni volta che devi creare un nuovo token di accesso di Azure AD.

1. In centro per i partner [associare l'account del centro per i partner dell'organizzazione alla directory di Azure ad dell'organizzazione](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Quindi, dalla pagina **utenti** nella sezione **Impostazioni account** del centro per i partner, [aggiungere l'applicazione Azure ad](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) che rappresenta l'app o il servizio che verrà usato per accedere agli invii per l'account del centro per i partner. Assicurarsi di assegnare questa applicazione al ruolo di **gestione** . Se l'applicazione non esiste ancora nella directory Azure AD, è possibile [creare una nuova applicazione Azure ad nel centro per i partner](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Tornare alla pagina **utenti** , fare clic sul nome dell'applicazione Azure ad per passare alle impostazioni dell'applicazione e copiare i valori **ID tenant** e **ID client** .
1. Fai clic su **Aggiungi nuova chiave**. Nella schermata successiva prendi nota del valore indicato in **Chiave**. Non potrai accedere di nuovo a queste informazioni dopo la chiusura della pagina. Per altre informazioni, vedere [gestire le chiavi per un'applicazione Azure ad](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Passaggio 2: Ottenere un token di accesso Azure AD

Prima di chiamare uno dei metodi nell'API di invio del centro per i partner, è prima necessario ottenere un token di accesso Azure AD passato all'intestazione di **autorizzazione** di ogni metodo nell'API. Dopo aver ottenuto un token di accesso, hai 60 minuti per usarlo prima che scada. Dopo la scadenza del token, è possibile aggiornare il token in modo che sia possibile continuare a usarlo in chiamate future all'API.

Per ottenere il token di accesso, seguire le istruzioni riportate in [chiamate da servizio a servizio con le credenziali client](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) per inviare un oggetto `HTTP POST` all' `https://login.microsoftonline.com/<tenant_id>/oauth2/token` endpoint. Ecco una richiesta di esempio:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Per il valore *tenant_id* nei parametri `POST URI` e *client_id* e *CLIENT_SECRET* specificare l'ID tenant, l'ID client e la chiave per l'applicazione recuperata dal centro per i partner nella sezione precedente. Per il parametro *resource* devi specificare `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Passaggio 3: usare l'API di invio Microsoft Store

Una volta che si dispone di un token di accesso Azure AD, è possibile chiamare i metodi nell'API di invio del centro per i partner. Per creare o aggiornare gli invii, in genere si chiamano più metodi nell'API di invio del centro per i partner in un ordine specifico. Per informazioni su ogni scenario e sulla sintassi di ogni metodo, vedere l'API di inserimento.

https://apidocs.microsoft.com/services/partneringestion/

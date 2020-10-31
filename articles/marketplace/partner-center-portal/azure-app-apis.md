---
title: API di invio del Centro per i partner per l'onboarding di app di Azure nel marketplace commerciale Microsoft
description: Informazioni sui prerequisiti per l'uso dell'API di invio del Centro per i partner per app di Azure nel marketplace commerciale del Centro per i partner Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 87f27f316914f3efce5a750f50471c65dceca84e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127842"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>API di invio del Centro per i partner per eseguire l'onboarding di app di Azure nel Centro per i partner

Usare l' *API di invio del Centro per i partner* per eseguire query a livello programmatico, creare invii per offerte di Azure e pubblicarle.  Questa API è utile se l'account gestisce molte offerte e si vuole automatizzare e ottimizzare il processo di invio per queste offerte.

## <a name="api-prerequisites"></a>Prerequisiti delle API

Sono disponibili alcune risorse programmatiche necessarie per usare l'API del Centro per i partner per i prodotti Azure: 

- un'applicazione Azure Active Directory.
- un token di accesso di Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Passaggio 1: Prerequisiti completi per l'uso dell'API di invio del Centro per i partner

Prima di iniziare a scrivere il codice per chiamare l'API di invio del Centro per i partner, verificare di aver soddisfatto i prerequisiti seguenti.

- L'utente o l'organizzazione deve disporre di una directory di Azure AD e dell'autorizzazione come [amministratore aziendale](../../active-directory/roles/permissions-reference.md) per la directory. Se si usa già Microsoft 365 o altri servizi aziendali di Microsoft, è già presente Azure AD directory. In caso contrario, è possibile [creare una nuova directory di Azure AD nel Centro per i partner](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) senza costi aggiuntivi.

- È necessario [associare un'applicazione Azure AD all'account del Centro per i partner](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) e ottenere l'ID tenant, l'ID client e la chiave. Questi valori sono necessari per ottenere un token di accesso di Azure AD, che sarà usato nelle chiamate all'API di invio di Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Procedura di associazione di un'applicazione Azure AD all'account del Centro per i partner

Per usare l'API di invio Microsoft Store, è necessario associare un'applicazione Azure AD all'account del Centro per i partner, recuperare l'ID tenant e l'ID client per l'applicazione e generare una chiave. L'applicazione Azure AD rappresenta l'app o il servizio da cui si vuole chiamare l'API di invio del Centro per i partner. Per ottenere un token di accesso di Azure AD da passare all'API, sono necessari l'ID tenant, l'ID client e la chiave.

>[!Note]
>È sufficiente eseguire questa attività una sola volta. Quando si dispone di ID tenant, ID client e chiave, è possibile riutilizzarli ogni volta che è necessario creare un nuovo token di accesso Azure AD.

1. Nel Centro per i partner, [associare l'account del Centro per i partner dell'organizzazione alla directory di Azure AD dell'organizzazione](/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Quindi, dalla pagina **Utenti** nella sezione **Impostazioni account** del Centro per i partner, [aggiungere l'applicazione Azure AD](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) che rappresenta l'app o il servizio che verrà usato per accedere agli invii per l'account del Centro per i partner. Assicurarsi di assegnare a questa applicazione il ruolo di **Manager** . Se l'applicazione non esiste ancora nella directory di Azure AD, è possibile [creare una nuova applicazione Azure AD nel Centro per i partner](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Tornare alla pagina **Utenti** , fare clic sul nome dell'applicazione Azure AD per passare alle impostazioni e copiare i valori di **ID tenant** e **ID client** .
1. Fare clic su **Aggiungi nuova chiave** . Nella schermata seguente, copiare il valore della **chiave** . Non sarà più possibile accedere a queste informazioni dopo aver lasciato la pagina. Per altre informazioni, vedere [Gestire le chiavi per un'applicazione Azure AD](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Passaggio 2: Ottenere un token di accesso di Azure AD

Prima di chiamare un metodo nell'API di invio del Centro per i partner è necessario ottenere un token di accesso Azure AD da passare all'intestazione **Autorizzazione** di ogni metodo nell'API. Dopo aver ottenuto un token di accesso, questo sarà disponibile per 60 minuti prima della scadenza. Dopo la scadenza del token, è possibile aggiornarlo in modo da continuare a usarlo in chiamate future all'API.

Per ottenere il token di accesso, seguire le istruzioni riportate in [Chiamate da servizio a servizio tramite le credenziali del client](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) per inviare un `HTTP POST` all'endpoint `https://login.microsoftonline.com/<tenant_id>/oauth2/token`. Di seguito viene riportata una richiesta di esempio:

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

Per il valore *tenant_id* in `POST URI` e i parametri *client_id* e *client_secret* , specificare l'ID tenant, l'ID client e la chiave per l'applicazione recuperata dal Centro per i partner nella sezione precedente. Per il parametro *resource* è necessario specificare `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Passaggio 3: Usare un'API di invio Microsoft Store

Una volta che si dispone di un token di accesso Azure AD, è possibile chiamare i metodi nell'API di invio del Centro per i partner. Per creare o aggiornare gli invii, in genere vengono chiamati più metodi nell'API di invio del Centro per i partner in un ordine specifico. Per informazioni su ogni scenario e sulla sintassi di ogni metodo, vedere lo swagger dell'API di inserimento.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [Creare un asset tecnico per macchine virtuali di Azure](create-azure-container-technical-assets.md)
* Informazioni su come creare un'[offerta di contenitore di Azure](create-azure-container-offer.md)
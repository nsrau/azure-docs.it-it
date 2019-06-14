---
title: Autenticazione e autorizzazione per gli ancoraggi spaziale di Azure | Microsoft Docs
description: Scopri i vari modi che un'app o un servizio può eseguire l'autenticazione di Azure spaziali Anchor e i livelli di controllo che è necessario controllare l'accesso agli ancoraggi spaziale di Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2b3f4cf4099459b655fc0e370935ddc8079de810
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073933"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticazione e autorizzazione per gli ancoraggi spaziale di Azure

In questa sezione e saranno trattati i vari modi, che è possibile eseguire l'autenticazione agli ancoraggi spaziale di Azure da app o servizio web, i modi in cui è possibile usare controllo degli accessi in base al ruolo nella Directory di Azure (Azure AD) per controllare l'accesso agli account Anchor spaziale.  

## <a name="overview"></a>Panoramica

![Una panoramica dell'autenticazione per gli ancoraggi spaziale di Azure](./media/spatial-anchors-authentication-overview.png)

Per accedere a un determinato account di Azure gli ancoraggi spaziali, i client devono innanzitutto ottenere un token di accesso da Azure misto realtà Security Token Service (STS). I token ottenuti dal servizio token di sicurezza in tempo reale per 24 ore e contengono informazioni per i servizi spaziali ancoraggi prendere decisioni di autorizzazione per l'account e garantire che solo le entità autorizzate possono accedere a quell'account. 

I token di accesso possono essere ottenuti di exchange da entrambe le chiavi dell'account o i token rilasciati AD Azure. 

Le chiavi dell'account consentono di iniziare rapidamente a usare sull'uso del servizio di Azure gli ancoraggi spaziali; Tuttavia, prima di distribuire l'applicazione nell'ambiente di produzione, è consigliabile aggiornare l'app per usare l'autenticazione basata su Active Directory di Azure. 

I token di autenticazione di Azure AD sono disponibili due modi:

- Se si compila un'applicazione aziendale e l'azienda Usa Azure AD come il sistema di identità, basata sull'utente di Azure è possibile usare l'autenticazione di Active Directory nell'app e concedere l'accesso agli account Anchor spaziali usando gruppi di sicurezza di Azure AD esistenti, o direttamente agli utenti dell'organizzazione. 
- In caso contrario, si consiglia di ottenere i token di Azure AD da un servizio web che supporta l'app. Utilizzo di un servizio web di supporto è il metodo di autenticazione consigliato per le applicazioni di produzione, in quanto evita di incorporare le credenziali per l'accesso a Azure gli ancoraggi spaziale nell'applicazione client. 

## <a name="account-keys"></a>Chiavi dell'account

Usando le chiavi dell'account per l'accesso all'account di Azure gli ancoraggi spaziali è il modo più semplice per iniziare. Sono disponibili le chiavi dell'account nel portale di Azure. Passare all'account, quindi selezionare la scheda "Chiavi".

![Una panoramica dell'autenticazione per gli ancoraggi spaziale di Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Due chiavi vengono resi disponibili, che sono entrambi contemporaneamente valide per l'accesso all'account Anchor spaziale. È consigliabile aggiornare regolarmente la chiave utilizzata per accedere all'account; Grazie a due separare enable chiavi valide tali aggiornamenti senza tempi di inattività; è sufficiente per l'aggiornamento, in alternativa, la chiave primaria e chiave secondaria. 

il SDK include il supporto incorporato per l'autenticazione con le chiavi dell'account; è sufficiente impostare la proprietà AccountKey per l'oggetto cloudSession. 

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

Al termine dell'operazione, il SDK gestisce lo scambio di chiave dell'account per un token di accesso e la necessaria memorizzazione nella cache dei token per l'app. 

> [!WARNING] 
> È consigliabile usare le chiavi dell'account per l'onboarding veloce, ma durante lo sviluppo o la creazione di prototipi solo. Si consiglia di non rendere disponibile l'applicazione nell'ambiente di produzione usando una chiave dell'account incorporato in esso e usare invece il basata sugli utenti o basata su servizi di Azure l'autenticazione AD approcci successivo elencato.

## <a name="azure-ad-user-authentication"></a>Autenticazione utente di Azure AD

Per le applicazioni destinate a utenti di Azure Active Directory, l'approccio consigliato consiste nell'utilizzare un token di Azure AD per l'utente, che è possibile ottenere usando la libreria ADAL, come descritto nella documentazione seguente: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); è deve seguire la procedura indicata in "Guide introduttive", tra cui:

1. Configurazione nel portale di Azure
    1.  Registrare l'applicazione in Azure AD lo stesso **applicazione nativa**. Durante la registrazione, sarà necessario determinare se l'applicazione deve essere multi-tenant o non e fornire il reindirizzamento di URL consentiti per l'applicazione.  
    2.  Concedere l'accesso dell'applicazione o gli utenti alla risorsa: 
        1.  Passare alla risorsa Anchor spaziale nel portale di Azure
        2.  Passare al **controllo di accesso (IAM)** scheda
        3.  Riscontri **aggiungere un'assegnazione di ruolo**
            1.  [Selezionare un ruolo](#role-based-access-control)
            2.  Nel **seleziona** immettere il nome di utente/i, gruppi e/o applicazioni a cui si desidera assegnare l'accesso. 
            3.  Hit **salvare**.
2. Nel codice:
    1.  Assicurarsi di usare la **ID applicazione** e **Uri di reindirizzamento** dell'applicazione di Azure AD come la **ID client** e **RedirectUri** parametri in ADAL
    2.  Impostare le informazioni del tenant:
        1.  Se l'applicazione supporta **mia organizzazione solo**, sostituire questo valore con il **ID Tenant** oppure **nome Tenant** (ad esempio, contoso.microsoft.com)
        2.  Se l'applicazione supporta **gli account in qualsiasi directory dell'organizzazione**, sostituire questo valore con **organizzazioni**
        3.  Se l'applicazione supporta **gli utenti con account Microsoft tutte**, sostituire questo valore con **comuni**
    3.  Nella richiesta del token, impostare il **resource** a "https://sts.mixedreality.azure.com". "Risorsa" indica ad Azure AD che l'applicazione sta richiedendo un token per il servizio di Azure gli ancoraggi spaziale.  

Con questo, l'applicazione deve essere in grado di ottenere da ADAL un token di Azure AD. è possibile impostare tale token di Azure AD come la **authenticationToken** nell'oggetto configurazione di sessione del cloud. 

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

## <a name="azure-ad-service-authentication"></a>Autenticazione del servizio Azure AD

L'opzione consigliata per la distribuzione delle App sfruttando Azure Anchor spaziali nell'ambiente di produzione è sfruttare un servizio back-end che verrà broker Rifiuta richieste di autenticazione. Lo schema generale dovrebbe essere come descritto nella figura seguente:

![Una panoramica dell'autenticazione per gli ancoraggi spaziale di Azure](./media/spatial-anchors-aad-authentication.png)

In questo caso, si presuppone che l'app Usa un proprio meccanismo di (ad esempio: Account Microsoft, PlayFab, Facebook, Google ID, nome utente e password personalizzata e così via.) Per eseguire l'autenticazione al servizio back-end. Dopo che gli utenti vengono autenticati al servizio back-end, che servizio può recuperare un token di Azure AD, scambiarlo con un token di accesso per Azure spaziali ancoraggi e restituirlo all'applicazione client.

Il token di accesso di Azure AD viene recuperato usando la libreria ADAL, come descritto nella documentazione seguente: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); è necessario seguire i passaggi elencati in "Guide introduttive", che includono:

1.  Configurazione nel portale di Azure:
    1.  Registrare l'applicazione in Azure AD:
        1.  Nel portale di Azure, passare a **Azure Active Directory**e selezionare **registrazioni per l'app**
        2.  Selezionare **registrazione nuova applicazione**
        3.  Immettere il nome dell'applicazione, seleziona **app Web / API** come tipo di applicazione e immettere l'URL di autenticazione per il servizio. Quindi premere **Create**.
        4.  In tale applicazione, premere **le impostazioni**, quindi selezionare la **chiavi** scheda. Immettere il nome della chiave, selezionare una durata e hit **salvare**. Assicurarsi di salvare il valore della chiave che viene visualizzato in quel momento, perché sarà necessario includerla nel codice del servizio web.
    2.  Concedere l'accesso dell'applicazione e/o gli utenti alla risorsa:
        1.  Passare alla risorsa Anchor spaziale nel portale di Azure
        2.  Passare al **controllo di accesso (IAM)** scheda
        3.  Riscontri **aggiungere un'assegnazione di ruolo**
        1.  [Selezionare un ruolo](#role-based-access-control)
        2.  Nel **seleziona** campo immettere il nome delle applicazioni è stato creato e per cui si desidera assegnare l'accesso. Se si desidera che gli utenti dell'app hanno ruoli diversi rispetto all'account Anchor spaziale, è consigliabile registrare più applicazioni in Azure AD e assegnare a ogni ruolo separato. Quindi implementare la logica di autorizzazione per usare il ruolo più appropriato per gli utenti.  
    3.  Hit **salvare**.
2.  Nel codice (Nota: è possibile usare il servizio di esempio incluso in GitHub):
    1.  Assicurarsi di usare l'ID dell'applicazione, il segreto dell'applicazione e Uri di reindirizzamento dell'applicazione di Azure AD come l'ID client, segreto e i parametri URI di reindirizzamento in ADAL
    2.  Impostare l'ID tenant per il proprio ID tenant AAAzure aggiungere nel parametro autorità ADAL
    3.  Nella richiesta del token, impostare il **resource** a "https://sts.mixedreality.azure.com" 

Con questo, il servizio back-end può recuperare un token di Azure AD. Può quindi scambiare un token di MR che verrà restituito al client. Utilizzando un token di Azure AD per recuperare un token di MR viene eseguita tramite una chiamata REST. Di seguito è una chiamata di esempio:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

In cui l'intestazione dell'autorizzazione è formattato come segue: `Bearer <accoundId>:<accountKey>`

E la risposta contiene il token MR in testo normale.
 
Token MR viene quindi restituito al client. L'app client può quindi impostarlo come relativo token di accesso nel file di configurazione di sessione di cloud.

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per controllare il livello di accesso concesse alle applicazioni, servizi o agli utenti di Azure AD del servizio, i ruoli seguenti sono stati creati per assegnare in base alle necessità per gli account di Azure gli ancoraggi spaziale:

- **Proprietario dell'Account Anchor spaziali**: applicazioni o utenti che dispongono di questo ruolo sono in grado di creare ancoraggi spaziali, eseguire una query per tali ed eliminarli. Quando si esegue l'autenticazione a un account usando le chiavi dell'account, il **proprietario dell'Account Anchor spaziali** ruolo viene assegnato per l'entità autenticata. 
- **Collaboratore Account di ancoraggi spaziali**: applicazioni o utenti che dispongono di questo ruolo sono in grado di creare ancoraggi spaziali, query, ma non eliminarle. 
- **Spaziali Anchor Account lettore**: le applicazioni o gli utenti che dispongono di questo ruolo sono in grado di eseguire una query per gli ancoraggi spaziali, ma non è possibile crearne uno nuovo, eliminare quelli esistenti o aggiornare i metadati su Anchor spaziali solo. In genere viene utilizzato per le applicazioni in cui alcuni utenti curare dell'ambiente, mentre altri utenti solo possibile richiamare gli ancoraggi precedentemente spostati in quell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

Creare la prima app con Azure Anchor spaziale.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

---
title: Risolvere i problemi di verifica dell'autore - Microsoft Identity Platform | Azure
description: Viene descritto come risolvere i problemi di verifica dell'editore per la piattaforma di identità Microsoft chiamando Microsoft Graph API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: fd49e922e5952f5a7c4b7f477dd33d6518010428
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088324"
---
# <a name="troubleshoot-publisher-verification"></a>Risolvere i problemi di verifica dell'autore
Se non si riesce a completare il processo o si verifica un comportamento imprevisto con la [Verifica dell'editore](publisher-verification-overview.md), è consigliabile iniziare eseguendo le operazioni seguenti se si ricevono errori o si verifica un comportamento imprevisto: 

1. Esaminare i [requisiti](publisher-verification-overview.md#requirements) e verificare che siano stati soddisfatti tutti.

1. Vedere le istruzioni per [contrassegnare un'app come con autore verificato](mark-app-as-publisher-verified.md) e assicurarsi che tutti i passaggi siano stati eseguiti correttamente.

1. Controllare l'elenco dei [problemi comuni](#common-issues).

1. Riprodurre la richiesta usando [Graph Explorer](#making-microsoft-graph-api-calls) per raccogliere informazioni aggiuntive ed escludere eventuali problemi nell'interfaccia utente.

## <a name="common-issues"></a>Problemi comuni
Di seguito sono riportati alcuni problemi comuni che possono verificarsi durante il processo. 

- **Non si conosce l'ID di Microsoft Partner Network (MPN ID) o non si sa chi è il contatto principale per l'account** 
    1. Accedere alla [pagina di registrazione di MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Accedere con un account utente nel tenant di Azure AD primario dell'organizzazione 
    1. Se esiste già un account MPN, verrà riconosciuto e l'utente verrà aggiunto all'account 
    1. Passare alla [pagina del profilo del partner](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) in cui sono elencati l'ID MPN e il contatto dell'account primario

- **Non conosco chi Azure AD amministratore globale (noto anche come amministratore della società o amministratore tenant), come posso trovarli? Cosa accade all'amministratore dell'applicazione o all'amministratore di applicazioni cloud?**
    1. Accedere al [portale di Azure AD](https://aad.portal.azure.com) usando un account utente nel tenant primario dell'organizzazione
    1. Passare a [Gestione ruoli](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Fare clic sul ruolo di amministratore desiderato
    1. Verrà visualizzato l'elenco degli utenti assegnati al ruolo

- **Non si conoscono gli amministratori dell'account MPN** Passare alla [pagina di gestione degli utenti MPN](https://partner.microsoft.com/pcv/users) e filtrare l'elenco di utenti per vedere quali sono gli utenti con i diversi ruoli di amministratore.

- **Si verifica un errore che segnala che l'ID MPN non è valido o che non è possibile accedervi.**
    1. Passare al [profilo partner](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) e verificare che: 
        - L'ID MPN sia corretto. 
        - Non siano visualizzati errori o "azioni in sospeso" e lo stato di verifica nel profilo di business legale e nelle informazioni sul partner sia "autorizzato" o "operazione riuscita".
    1. Passare alla [pagina di gestione del tenant di MPN](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) e verificare che il tenant in cui è registrata l'app e di cui si sta usando un account utente per l'accesso sia incluso nell'elenco dei tenant associati. Se è necessario aggiungere un tenant aggiuntivo, seguire le istruzioni riportate [qui](https://docs.microsoft.com/partner-center/multi-tenant-account). Tenere presente che a tutti gli amministratori globali di qualsiasi tenant aggiunto verranno concessi privilegi di amministratore globale per l'account del centro per i partner.
    1. Passare alla [pagina Gestione utenti MPN](https://partner.microsoft.com/pcv/users) e verificare che l'utente che si sta eseguendo l'accesso sia un amministratore globale, un amministratore MPN o un account amministratore. Se è necessario aggiungere un utente a un ruolo in centro per i partner, seguire le istruzioni riportate [qui](https://docs.microsoft.com/partner-center/create-user-accounts-and-set-permissions).

- **Quando si accede al portale di Azure AD, non viene visualizzata alcuna app registrata. Perché?** 
    È possibile che le registrazioni dell'app siano state create usando un account utente diverso in questo tenant, un account personale/utente o un tenant diverso. Assicurarsi di aver eseguito l'accesso con l'account corretto nel tenant in cui sono state create le registrazioni dell'app.

- **Si verifica un errore relativo all'autenticazione a più fattori. Cosa dovrei fare?** 
    Assicurarsi che la [funzionalità autenticazione](../fundamentals/concept-fundamentals-mfa-get-started.md) a più fattori sia abilitata e necessaria per l'utente con cui si esegue l'accesso e per questo scenario. Ad esempio, l'autenticazione a più fattori può essere:
    - Sempre necessario per l'utente con cui si sta effettuando l'accesso
    - [Obbligatorio per gestione di Azure](../conditional-access/howto-conditional-access-policy-azure-management.md).
    - [Obbligatorio per il tipo di amministratore](../conditional-access/howto-conditional-access-policy-admin-mfa.md) con cui si esegue l'accesso.

## <a name="making-microsoft-graph-api-calls"></a>Eseguire chiamate API Microsoft Graph 

Se si verifica un problema, ma non si riesce a capirne il motivo in base a ciò che viene visualizzato nell'interfaccia utente, può essere utile eseguire ulteriori operazioni di risoluzione dei problemi usando chiamate API Microsoft Graph per eseguire le stesse operazioni che è possibile eseguire nel portale di registrazione delle app.

Il modo più semplice per effettuare queste richieste consiste nell'usare [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). È anche possibile prendere in considerazione altre opzioni, ad esempio l'uso [Postman](https://www.postman.com/) o l'uso di PowerShell per [richiamare una richiesta Web](/powershell/module/microsoft.powershell.utility/invoke-webrequest).  

È possibile usare Microsoft Graph sia per impostare l'autore verificato per l'app che per annullarne l'impostazione e controllare il risultato dopo l'esecuzione di una di queste operazioni. Il risultato può essere visualizzato sia nell'oggetto [applicazione](/graph/api/resources/application) corrispondente alla registrazione dell'app che in qualsiasi [entità servizio](/graph/api/resources/serviceprincipal) di cui è stata creata un'istanza da tale app. Per altre informazioni sulla relazione tra questi oggetti, vedere: [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](app-objects-and-service-principals.md).  

Di seguito sono riportati alcuni esempi di richieste utili:  

### <a name="set-verified-publisher"></a>Impostare l'autore verificato 

Richiesta

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Risposta 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* è l'ID MPN. 

### <a name="unset-verified-publisher"></a>Annullare l'impostazione dell'autore verificato 

Richiesta:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Risposta 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Ottenere informazioni sull'autore verificato dall'applicazione 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Ottenere informazioni sull'autore verificato dall'entità servizio 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Informazioni di riferimento sugli errori 

Di seguito è riportato un elenco dei potenziali codici di errore che possono essere ricevuti, sia durante la risoluzione dei problemi con Microsoft Graph che nel corso del processo nel portale di registrazione delle app.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

L'ID MPN specificato (<MPNID>) non esiste o non è possibile accedervi. Specificare un ID MPN valido e riprovare. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

L'ID MPN specificato (<MPNID>) non è valido. Specificare un ID MPN valido e riprovare. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

L'ID MPN specificato (<MPNID>) non è valido. Specificare un ID MPN valido e riprovare. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

L'ID MPN (<MPNID>) specificato non ha completato il processo di verifica. Completare questo processo in Partner Center e riprovare. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

L'ID MPN specificato (<MPNID>) non è valido. Specificare un ID MPN valido e riprovare. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

L'ID MPN specificato (<MPNID>) non è valido. Specificare un ID MPN valido e riprovare. 

### <a name="applicationnotfound"></a>ApplicationNotFound  

Impossibile trovare l'applicazione di destinazione (<AppId>). Specificare un ID di applicazione valido e riprovare. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Questa funzionalità non è supportata in un tenant Azure AD B2C. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Questa funzionalità non è supportata in un tenant verificato tramite posta elettronica. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

L'applicazione di destinazione ( \<AppId\> ) deve avere un set di domini editore. Impostare un dominio dell'autore e riprovare.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

Il dominio dell'autore dell'applicazione di destinazione (<publisherDomain>) non corrisponde al dominio usato per eseguire la verifica tramite posta elettronica in Partner Center (<pcDomain>). Assicurarsi che questi domini corrispondano e riprovare. 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

Non si è autorizzati a configurare la proprietà dell'autore verificato per l'applicazione (<AppId>) 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

L'ID MPN non è stato specificato nel corpo della richiesta o il tipo di contenuto della richiesta non è "application/json". 

### <a name="msanotsupported"></a>MSANotSupported  

Questa funzionalità non è supportata per gli account utente Microsoft. Sono supportate solo le applicazioni registrate in Azure AD da un utente Azure AD. 

## <a name="next-steps"></a>Passaggi successivi

Se sono state esaminate tutte le informazioni precedenti e si riceve ancora un errore da Microsoft Graph, raccogliere quante più informazioni possibili tra quelle indicate di seguito relative alla richiesta non riuscita e [contattare il supporto tecnico Microsoft](developer-support-help-options.md#open-a-support-request).

- Timestamp 
- CorrelationId 
- ObjectID o UserPrincipalName dell'utente che ha eseguito l'accesso 
- ObjectId dell'applicazione di destinazione
- AppId dell'applicazione di destinazione
- TenantId in cui è registrata l'app
- ID MPN
- Richiesta REST eseguita 
- Codice di errore e messaggio restituiti 

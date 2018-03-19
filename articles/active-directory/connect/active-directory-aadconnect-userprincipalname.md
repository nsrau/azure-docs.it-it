---
title: Popolamento di UserPrincipalName di Azure AD
description: Il documento seguente descrive come viene popolato l'attributo UserPrincipalName.
author: billmath
ms.author: billmath
ms.date: 02/02/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 96b12fbddd4293c55e9029b194416541ca44c622
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="azure-ad-userprincipalname-population"></a>Popolamento di UserPrincipalName di Azure AD

Questo articolo descrive come viene popolato l'attributo UserPrincipalName in Azure Active Directory (Azure AD).
Il valore dell'attributo UserPrincipalName è il nome utente di Azure AD per gli account utente.

## <a name="upn-terminology"></a>Terminologia UPN
In questo articolo viene usata la terminologia seguente:

|Termine|DESCRIZIONE|
|-----|-----|
|Dominio iniziale|Il dominio predefinito (onmicrosoft.com) nel tenant di Azure AD. Ad esempio, contoso.onmicrosoft.com.|
|MOERA (Microsoft Online Email Routing Address, indirizzo di routing della posta elettronica Microsoft Online)|Azure AD calcola l'indirizzo MOERA dall'attributo MailNickName di Azure AD e il dominio iniziale di Azure AD, ad esempio &lt;MailNickName&gt;&#64;&lt;dominio iniziale&gt;.|
|Attributo mailNickName locale|Attributo in Active Directory il cui valore rappresenta l'alias di un utente in un'organizzazione Exchange.|
|Attributo mail locale|Attributo in Active Directory il cui valore rappresenta l'indirizzo di posta elettronica di un utente.|
|Indirizzo SMTP primario|Indirizzo di posta elettronica primario di un oggetto destinatario di Exchange. Ad esempio, SMTP:user@contoso.com.|
|ID di accesso alternativo|Attributo locale diverso da UserPrincipalName, ad esempio l'attributo mail, usato per l'accesso.|

## <a name="what-is-userprincipalname"></a>Che cos'è UserPrincipalName?
UserPrincipalName è un attributo che rappresenta un nome di accesso Internet per un utente in base allo standard Internet [RFC 822](http://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Formato UPN
Un UPN è costituito da un prefisso UPN (il nome dell'account utente) e un suffisso UPN (nome di dominio DNS). Il prefisso viene unito al suffisso usando il simbolo "\@\". Ad esempio, "someone@example.com". Un UPN deve essere univoco tra tutti gli oggetti entità di sicurezza in una foresta di directory. 

## <a name="upn-in-azure-ad"></a>UPN in Azure AD 
L'UPN viene usato da Azure AD per consentire agli utenti di accedere.  L'UPN che un utente può usare dipende dal fatto che il dominio sia stato verificato o meno.  Se il dominio è stato verificato, un utente con tale suffisso potrà accedere ad Azure AD.  

L'attributo viene sincronizzato da Azure AD Connect.  Durante l'installazione è possibile visualizzare i domini che sono stati verificati e quelli che non lo sono.

   ![Domini non verificati](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) 

## <a name="alternate-login-id"></a>ID di accesso alternativo
In alcuni ambienti, a causa di criteri aziendali o dipendenze di applicazioni line-of-business locali, gli utenti finali possono conoscere solo il proprio indirizzo di posta elettronica e non l'UPN.

L’ID di accesso alternativo consente di configurare un'esperienza in cui gli utenti possono accedere con un attributo diverso dal relativo nome dell’entità locale, ad esempio mail.

Per abilitare l'ID di accesso alternativo con Azure AD, non sono necessari passaggi di configurazione aggiuntivi quando si usa Azure AD Connect. È possibile configurare l'ID alternativo direttamente dalla procedura guidata. Vedere la configurazione di accesso di Azure AD per gli utenti nella sezione Sincronizzazione. Nell'elenco a discesa **Nome dell'entità utente** selezionare l'attributo per l'ID di accesso alternativo.

![Domini non verificati](./media/active-directory-aadconnect-userprincipalname/altloginid.png)  

Per altre informazioni, vedere [Configurare l'ID di accesso alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) e [Configurazione dell'accesso ad Azure AD](active-directory-aadconnect-get-started-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Suffisso UPN non verificato
Se il suffisso dell'attributo UserPrincipalName locale/ID di accesso alternativo non è verificato con il tenant di Azure AD, il valore dell'attributo UserPrincipalName di Azure AD viene impostato sull'indirizzo MOERA. Azure AD calcola l'indirizzo MOERA dall'attributo MailNickName di Azure AD e il dominio iniziale di Azure AD, ad esempio &lt;MailNickName&gt;&#64;&lt;dominio iniziale&gt;.

## <a name="verified-upn-suffix"></a>Suffisso UPN verificato
Se il suffisso dell'attributo UserPrincipalName locale/ID di accesso alternativo è verificato con il tenant di Azure AD, il valore dell'attributo UserPrincipalName di Azure AD corrisponde al valore dell'attributo UserPrincipalName locale/ID di accesso alternativo.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Calcolo del valore dell'attributo MailNickName di Azure AD
Poiché il valore dell'attributo UserPrincipalName di Azure AD può essere impostato sull'indirizzo MOERA, è importante comprendere come viene calcolato il valore dell'attributo MailNickName di Azure AD, che è il prefisso dell'indirizzo MOERA.

Quando un oggetto utente viene sincronizzato con un tenant di Azure AD per la prima volta, Azure AD controlla gli elementi seguenti nell'ordine indicato e imposta il valore dell'attributo MailNickName sul primo elemento esistente:

- Attributo mailNickName locale
- Prefisso dell'indirizzo SMTP primario
- Prefisso dell'attributo mail locale
- Prefisso dell'attributo userPrincipalName locale/ID di accesso alternativo
- Prefisso dell'indirizzo SMTP secondario

Quando gli aggiornamenti a un oggetto utente vengono sincronizzati con il tenant di Azure AD, Azure AD aggiorna il valore dell'attributo MailNickName solo in caso di aggiornamento al valore dell'attributo mailNickName locale.

>[!IMPORTANT]
>Azure AD ricalcola il valore dell'attributo UserPrincipalName solo nel caso in cui un aggiornamento al valore dell'attributo UserPrincipalName locale/ID di accesso alternativo venga sincronizzato con il tenant di Azure AD. 
>
>Ogni volta che Azure AD ricalcola l'attributo UserPrincipalName, ricalcola anche l'indirizzo MOERA. 

## <a name="upn-scenarios"></a>Scenari UPN
Di seguito sono illustrati scenari di esempio di come viene calcolato l'UPN in base allo scenario specifico.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenario 1: Suffisso UPN non verificato - sincronizzazione iniziale

Oggetto utente locale:
- mailNickName: &lt;non impostato&gt;
- proxyAddresses: {SMTP:us1@contoso.com}
- mail: us2@contoso.com
- userPrincipalName: us3@contoso.com`

Sincronizzazione dell'oggetto utente con il tenant di Azure AD per la prima volta
- Impostare l'attributo MailNickName di Azure AD sul prefisso dell'indirizzo SMTP primario.
- Impostazione dell'indirizzo MOERA su &lt;MailNickName&gt;&#64;&lt;dominio iniziale&gt;.
- Impostazione dell'attributo UserPrincipalName di Azure AD sull'indirizzo MOERA.

Oggetto utente del tenant di Azure AD:
- MailNickName: us1           
- UserPrincipalName: us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenario 2: Suffisso UPN non verificato - impostazione dell'attributo mailNickName locale

Oggetto utente locale:
- mailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- mail: us2@contoso.com
- userPrincipalName: us3@contoso.com

Sincronizzazione dell'aggiornamento dell'attributo mailNickName locale con il tenant di Azure AD
- Aggiornamento dell'attributo MailNickName di Azure AD con l'attributo mailNickName locale.
- Poiché non ci sono aggiornamenti dell'attributo userPrincipalName locale, non vengono apportate modifiche all'attributo UserPrincipalName di Azure AD.

Oggetto utente del tenant di Azure AD:
- MailNickName: us4
- UserPrincipalName: us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenario 3: Suffisso UPN non verificato - aggiornamento dell'attributo userPrincipalName locale

Oggetto utente locale:
- mailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- mail: us2@contoso.com
- userPrincipalName: us5@contoso.com

Sincronizzazione dell'aggiornamento dell'attributo userPrincipalName locale con il tenant di Azure AD
- L'aggiornamento dell'attributo userPrincipalName locale attiva il ricalcolo dell'indirizzo MOERA e dell'attributo UserPrincipalName di Azure AD.
- Impostazione dell'indirizzo MOERA su &lt;MailNickName&gt;&#64;&lt;dominio iniziale&gt;.
- Impostazione dell'attributo UserPrincipalName di Azure AD sull'indirizzo MOERA.

Oggetto utente del tenant di Azure AD:
- MailNickName: us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenario 4: Suffisso UPN non verificato - aggiornamento dell'indirizzo SMTP primario e dell'attributo mail locale

Oggetto utente locale:
- mailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- mail: us7@contoso.com
- userPrincipalName: us5@contoso.com

Sincronizzazione dell'aggiornamento dell'attributo mail locale e dell'indirizzo SMTP primario con il tenant di Azure AD
- Dopo la sincronizzazione iniziale dell'oggetto utente, gli aggiornamenti dell'attributo mail locale e dell'indirizzo SMTP primario non influiscono sull'attributo MailNickName né sull'attributo UserPrincipalName di Azure AD.

Oggetto utente del tenant di Azure AD:
- MailNickName: us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenario 5: Suffisso UPN verificato - aggiornamento del suffisso dell'attributo userPrincipalName locale

Oggetto utente locale:
- mailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- mail: us7@contoso.com
- userPrincipalName: us5@verified.contoso.com

Sincronizzazione dell'aggiornamento dell'attributo userPrincipalName locale con il tenant di Azure AD
- L'aggiornamento dell'attributo userPrincipalName locale attiva il ricalcolo dell'attributo UserPrincipalName di Azure AD.
- Impostazione dell'attributo UserPrincipalName di Azure AD sull'attributo userPrincipalName locale in quanto il suffisso UPN è verificato con il tenant di Azure AD.

Oggetto utente del tenant di Azure AD:
- MailNickName: us4     
- UserPrincipalName: us5@verified.contoso.com

## <a name="next-steps"></a>Passaggi successivi
- [Integrare le directory locali con Azure Active Directory](active-directory-aadconnect.md)
- [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)

---
title: Configurare la Federazione diretta con un AD FS per B2B-Azure AD
description: Informazioni su come configurare AD FS come provider di identità per la Federazione diretta, in modo che gli utenti possano accedere alle app Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3d7c47ff0a2c533bf12a67958a913b22915f75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909562"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Esempio: Federazione diretta con Active Directory Federation Services (AD FS) (anteprima)

> [!NOTE]
> La federazione diretta è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo descrive come configurare la [Federazione diretta](direct-federation.md) usando Active Directory Federation Services (ad FS) come un provider di identità SAML 2,0 o WS-Fed. Per supportare la Federazione diretta, è necessario configurare determinati attributi e attestazioni nel provider di identità. Per illustrare come configurare un provider di identità per la federazione diretta, si userà Active Directory Federation Services (AD FS) come esempio. Verrà illustrato come configurare AD FS sia come provider di identità SAML sia come provider di identità WS-Fed.

> [!NOTE]
> Questo articolo descrive come configurare AD FS per SAML e WS-Fed a scopo illustrativo. Per le integrazioni di Federazione dirette in cui il provider di identità è AD FS, è consigliabile usare WS-Fed come protocollo. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Configurare AD FS per la Federazione diretta SAML 2,0
È possibile configurare Azure AD B2B per la federazione con i provider di identità che usano il protocollo SAML con alcuni requisiti specifici indicati di seguito. Per illustrare i passaggi di configurazione SAML, in questa sezione viene illustrato come configurare AD FS per SAML 2,0. 

Per configurare la federazione diretta, è necessario che gli attributi seguenti vengano ricevuti nella risposta SAML 2.0 del provider di identità. Questi attributi possono essere configurati tramite il collegamento al file XML del servizio token di sicurezza online o mediante immissione manuale. Passaggio 12 in [creare un'istanza di ad FS di test](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descrive come trovare gli endpoint di ad FS o come generare l'URL dei metadati, ad esempio `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` . 

|Attributo  |valore  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Destinatari     |`urn:federation:MicrosoftOnline`         |
|Issuer     |URI dell'autorità emittente del provider di identità partner, ad esempio `http://www.example.com/exk10l6w90DHM0yi...`         |

Le attestazioni seguenti devono essere configurate nel token SAML 2,0 emesso dal provider di identità:


|Attributo  |valore  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


La sezione successiva illustra come configurare gli attributi e le attestazioni necessari usando AD FS come esempio di un provider di identità SAML 2,0.

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare questa procedura, è necessario che un server AD FS sia già configurato e funzionante. Per informazioni sulla configurazione di un server AD FS, vedere [creare un'istanza di test AD FS 3,0 in una macchina virtuale di Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Aggiungere la descrizione dell'attestazione

1. Nel server di ad FS selezionare **strumenti**  >  **gestione ad FS**.
2. Nel riquadro di spostamento selezionare **Service**  >  **descrizioni attestazioni**servizio.
3. In **azioni**selezionare **Aggiungi descrizione attestazione**.
4. Nella finestra **Aggiungi una descrizione dell'attestazione** specificare i valori seguenti:

   - **Nome visualizzato**: identificatore persistente
   - **Identificatore attestazione**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Selezionare la casella di controllo **pubblica questa descrizione attestazione nei metadati federativi come tipo di attestazione che questo servizio federativo può accettare**.
   - Selezionare la casella di controllo **pubblica questa descrizione attestazione nei metadati federativi come tipo di attestazione che questo servizio federativo può inviare**.

5. Fare clic su **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Aggiungere le regole di attestazione e attendibilità relying party

1. Nel server ad FS passare a **strumenti**  >  **gestione ad FS**.
2. Nel riquadro di spostamento selezionare **relazioni di trust**  >  **attendibilità**componente.
3. In **azioni**selezionare **Aggiungi attendibilità componente**. 
4. Nella procedura guidata Aggiungi attendibilità relying party per **selezionare un'origine dati**, usare l'opzione **Importa i dati relativi al relying party pubblicati online o in una rete locale**. Specificare l'URL dei metadati di federazione https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml . Lasciare le altre selezioni predefinite. Selezionare **Chiudi**.
5. Verrà visualizzata la procedura guidata **modifica regole attestazione** .
6. Nella procedura guidata **modifica regole attestazione** selezionare **Aggiungi regola**. In **Scegli tipo di regola**selezionare **Inviare attributi LDAP come attestazioni**. Selezionare **Avanti**.
7. In **Configura regola attestazione**specificare i valori seguenti: 

   - **Nome regola attestazione**: regola attestazione posta elettronica 
   - **Archivio attributi**: Active Directory 
   - **Attributo LDAP**: indirizzi di posta elettronica 
   - **Tipo di attestazione in uscita**: indirizzo di posta elettronica

8. Selezionare **Fine**.
9. Nella finestra **modifica regole attestazione** viene visualizzata la nuova regola. Fare clic su **Applica**. 
10. Fare clic su **OK**.  

### <a name="create-an-email-transform-rule"></a>Creare una regola di trasformazione della posta elettronica
1. Passare a **modifica regole attestazione** e fare clic su **Aggiungi regola**. In **Scegli tipo di regola**selezionare **trasforma un'attestazione in ingresso** e fare clic su **Avanti**. 
2. In **Configura regola attestazione**specificare i valori seguenti: 

   - **Nome regola attestazione**: regola di trasformazione posta elettronica 
   - **Tipo di attestazione in ingresso**: indirizzo di posta elettronica 
   - **Tipo di attestazione in uscita**: ID nome 
   - **Formato ID nome in uscita**: identificatore persistente 
   - Selezionare **Pass-through di tutti i valori attestazione**.

3. Fare clic su **Fine**. 
4. Nella finestra **modifica regole attestazione** vengono visualizzate le nuove regole. Fare clic su **Applica**. 
5. Fare clic su **OK**. Il server AD FS è ora configurato per la Federazione diretta usando il protocollo SAML 2,0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Configurare AD FS per WS-Fed Federazione diretta 
Azure AD B2B può essere configurato per la Federazione con provider di identità che usano il protocollo WS-Fed con i requisiti specifici elencati di seguito. Attualmente, i due provider WS-Fed che sono stati testati per la compatibilità con Azure AD sono Active Directory Federation Services e Shibboleth. In questo caso si userà Active Directory Federation Services (AD FS) come esempio del provider di identità WS-Fed. Per ulteriori informazioni sulla creazione di un trust relying party tra un provider WS-Fed conforme con Azure AD, scaricare la documentazione sulla compatibilità del provider di identità Azure AD.

Per configurare la federazione diretta, è necessario che gli attributi seguenti vengano ricevuti nel messaggio WS-Fed del provider di identità. Questi attributi possono essere configurati tramite il collegamento al file XML del servizio token di sicurezza online o mediante immissione manuale. Passaggio 12 in [creare un'istanza di ad FS di test](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descrive come trovare gli endpoint di ad FS o come generare l'URL dei metadati, ad esempio `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` .
 
|Attributo  |valore  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Destinatari     |`urn:federation:MicrosoftOnline`         |
|Issuer     |URI dell'autorità emittente del provider di identità partner, ad esempio `http://www.example.com/exk10l6w90DHM0yi...`         |

Attestazioni necessarie per il token WS-Fed rilasciato dal provider di identità:

|Attributo  |valore  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

La sezione successiva illustra come configurare gli attributi e le attestazioni necessari usando AD FS come esempio di un provider di identità WS-Fed.

### <a name="before-you-begin"></a>Prima di iniziare
Prima di iniziare questa procedura, è necessario che un server AD FS sia già configurato e funzionante. Per informazioni sulla configurazione di un server AD FS, vedere [creare un'istanza di test AD FS 3,0 in una macchina virtuale di Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Aggiungere le regole di attestazione e attendibilità relying party 
1. Nel server ad FS passare a **strumenti**  >  **gestione ad FS**. 
1. Nel riquadro di spostamento selezionare **relazioni di trust**  >  **attendibilità**componente. 
1. In **azioni**selezionare **Aggiungi attendibilità componente**.  
1. Per **selezionare l'origine dati**nella procedura guidata Aggiungi relying party trust, utilizzare l'opzione **Importa i dati relativi al relying party pubblicati online o in una rete locale**. Specificare l'URL dei metadati di Federazione: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml` .  Lasciare le altre selezioni predefinite. Selezionare **Chiudi**.
1. Verrà visualizzata la procedura guidata **modifica regole attestazione** . 
1. Nella procedura guidata **modifica regole attestazione** selezionare **Aggiungi regola**. In **Scegli tipo di regola**selezionare **Invia attestazioni usando una regola personalizzata**. Selezionare *Avanti*. 
1. In **Configura regola attestazione**specificare i valori seguenti:

   - **Nome regola attestazione**: rilascia ID non modificabile  
   - **Regola personalizzata**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selezionare **Fine**. 
1. Nella finestra **modifica regole attestazione** viene visualizzata la nuova regola. Fare clic su **Applica**.  
1. Nella stessa procedura guidata **modifica regole attestazione** selezionare **Aggiungi regola**. In **tipo di regola cohose**selezionare **Invia attributi LDAP come attestazioni**. Selezionare **Avanti**.
1. In **Configura regola attestazione**specificare i valori seguenti: 

   - **Nome regola attestazione**: regola attestazione posta elettronica  
   - **Archivio attributi**: Active Directory  
   - **Attributo LDAP**: indirizzi di posta elettronica  
   - **Tipo di attestazione in uscita**: indirizzo di posta elettronica 

1.  Selezionare **Fine**. 
1.  Nella finestra **modifica regole attestazione** viene visualizzata la nuova regola. Fare clic su **Applica**.  
1.  Fare clic su **OK**. Il server AD FS è ora configurato per la Federazione diretta tramite WS-Fed.

## <a name="next-steps"></a>Passaggi successivi
A questo punto, è possibile [configurare la Federazione diretta in Azure ad](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) nel portale Azure ad o tramite PowerShell. 

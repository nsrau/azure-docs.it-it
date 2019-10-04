---
title: Configurare la federazione diretta con AD FS per B2B - Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare AD FS come provider di identità per la federazione diretta in modo che gli utenti guest possono accedere alle App Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544322"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Esempio: Federazione diretta con Active Directory Federation Services (AD FS) (anteprima)
|     |
| --- |
| La federazione diretta è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Questo articolo descrive come configurare [indirizzare federation](direct-federation.md) usando Active Directory Federation Services (ADFS) come provider di identità SAML 2.0 o WS-Fed. Per supportare la federazione diretta, determinati attributi e le attestazioni devono essere configurate per il provider di identità. Per illustrare come configurare un provider di identità per la federazione diretta, useremo Active Directory Federation Services (ADFS) come esempio. Ecco come configurare AD FS come provider di identità SAML e come un provider di identità WS-Fed.

> [!NOTE]
> Questo articolo descrive come configurare ADFS per SAML e WS-Fed a scopo illustrativo. Per le integrazioni di federazione diretta in cui il provider di identità è AD FS, è consigliabile usare WS-Fed come protocollo. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Configurare AD FS per la federazione diretta SAML 2.0
Azure AD B2B può essere configurato per attuare la federazione con provider di identità che usano il protocollo SAML con requisiti specifici elencati di seguito. Per illustrare i passaggi di configurazione SAML, questa sezione illustra come configurare ADFS per SAML 2.0. 

Per configurare la federazione diretta, gli attributi seguenti devono essere ricevuti nella risposta dal provider di identità SAML 2.0. Questi attributi possono essere configurati mediante il collegamento al servizio token di sicurezza online file XML o immesse manualmente. Al passaggio 12 [creare un'istanza di test AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descrive come trovare gli endpoint AD FS o come generare l'URL dei metadati, ad esempio `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`. 

|Attributo  |Value  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Audience     |`urn:federation:MicrosoftOnline`         |
|Issuer     |L'URI dell'autorità emittente del partner provider di identità, ad esempio `http://www.example.com/exk10l6w90DHM0yi...`         |

Le attestazioni seguenti devono essere configurati nel token SAML 2.0 rilasciato dal provider di identità:


|Attributo  |Value  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


Nella sezione successiva illustra come configurare gli attributi richiesti e le attestazioni con AD FS come esempio di provider di identità SAML 2.0.

### <a name="before-you-begin"></a>Prima di iniziare

Un server AD FS deve essere già up e il funzionamento prima di iniziare questa procedura. Per informazioni sulla configurazione di un server AD FS, vedere [creare un'istanza di test AD FS 3.0 su una macchina virtuale di Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Aggiungere la descrizione di attestazione

1. Nel server AD FS, selezionare **degli strumenti** > **Gestione AD FS**.
2. Nel riquadro di spostamento, selezionare **assistenza** > **descrizioni di attestazioni**.
3. Sotto **azioni**, selezionare **Aggiungi descrizione attestazione**.
4. Nel **aggiungere una descrizione di attestazione** finestra, specificare i valori seguenti:

   - **Nome visualizzato**: Identificatore permanente
   - **Identificatore di attestazione**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Selezionare la casella di controllo **pubblica questa descrizione di attestazione nei metadati federativi come tipo di attestazione che può accettare questo servizio federativo**.
   - Selezionare la casella di controllo **pubblica questa descrizione di attestazione nei metadati federativi come tipo di attestazione che questo servizio federativo può inviare**.

5. Fare clic su **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Aggiungere il trust della relying party e regole attestazione

1. Nel server AD FS, passare a **degli strumenti** > **Gestione AD FS**.
2. Nel riquadro di spostamento, selezionare **relazioni di Trust** > **Relying Party Trusts**.
3. Sotto **azioni**, selezionare **Aggiungi Trust Relying Party**. 
4. Nella add relying party trust procedura guidata per **selezionare un'origine dati**, usare l'opzione **Importa dati sulla relying party pubblicati online o in una rete locale**. Specificare questi metadati di federazione URL - https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml. Lasciare le altre selezioni predefinite. Selezionare **Chiudi**.
5. Il **Modifica regole attestazione** verrà visualizzata la procedura guidata.
6. Nel **Modifica regole attestazione** procedura guidata, selezionare **Add Rule**. Nelle **scegliere il tipo di regola**, selezionare **inviare attributi LDAP come attestazioni**. Selezionare **Avanti**.
7. Nelle **Configura regola attestazione**, specificare i valori seguenti: 

   - **Nome regola attestazione**: Regola di attestazione di posta elettronica 
   - **Archivio di attributi**: Active Directory 
   - **Attributo LDAP**: Indirizzi di posta elettronica 
   - **Tipo di attestazione in uscita**: Indirizzo di posta elettronica

8. Selezionare **Fine**.
9. Il **Modifica regole attestazione** finestra verrà visualizzata la nuova regola. Fare clic su **Apply**. 
10. Fare clic su **OK**.  

### <a name="create-an-email-transform-rule"></a>Creare una regola di trasformazione del messaggio di posta elettronica
1. Passare a **Modifica regole attestazione** e fare clic su **Add Rule**. Nelle **scegliere il tipo di regola**, selezionare **trasformare un'attestazione in ingresso** e fare clic su **Next**. 
2. Nelle **Configura regola attestazione**, specificare i valori seguenti: 

   - **Nome regola attestazione**: Regola di trasformazione di posta elettronica 
   - **Tipo di attestazione in ingresso**: Indirizzo di posta elettronica 
   - **Tipo di attestazione in uscita**: ID nome 
   - **Formato ID nome in uscita**: Identificatore permanente 
   - Selezionare **Pass-through di tutti i valori attestazione**.

3. Fare clic su **Finish**. 
4. Il **Modifica regole attestazione** finestra visualizzerà le nuove regole. Fare clic su **Apply**. 
5. Fare clic su **OK**. Il server AD FS è ora configurato per la federazione diretta con il protocollo SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Configurare AD FS per la federazione diretta di WS-Fed 
Azure AD B2B può essere configurato per attuare la federazione con provider di identità che usano il protocollo WS-Fed con i requisiti specifici elencati di seguito. Attualmente, i due provider di WS-Fed sono stati testati per garantire la compatibilità con Azure AD includono AD FS e Shibboleth. In questo caso, si userà Active Directory Federation Services (ADFS) come esempio di provider di identità WS-Fed. Per altre informazioni su come stabilire un trust della relying party tra un provider conforme a WS-Fed con Azure AD, scaricare la documentazione di compatibilità Provider di identità di Azure AD.

Per configurare la federazione diretta, gli attributi seguenti devono essere ricevuti nel messaggio di WS-Fed dal provider di identità. Questi attributi possono essere configurati mediante il collegamento al servizio token di sicurezza online file XML o immesse manualmente. Al passaggio 12 [creare un'istanza di test AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descrive come trovare gli endpoint AD FS o come generare l'URL dei metadati, ad esempio `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`.
 
|Attributo  |Value  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Audience     |`urn:federation:MicrosoftOnline`         |
|Issuer     |L'URI dell'autorità emittente del partner provider di identità, ad esempio `http://www.example.com/exk10l6w90DHM0yi...`         |

Attestazioni necessarie per il token WS-Fed rilasciato dal provider di identità:

|Attributo  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

La sezione successiva illustra come configurare gli attributi obbligatori e delle attestazioni con AD FS come esempio di provider di identità WS-Fed.

### <a name="before-you-begin"></a>Prima di iniziare
Un server AD FS deve essere già up e il funzionamento prima di iniziare questa procedura. Per informazioni sulla configurazione di un server AD FS, vedere [creare un'istanza di test AD FS 3.0 su una macchina virtuale di Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Aggiungere il trust della relying party e regole attestazione 
1. Nel server AD FS, passare a **degli strumenti** > **Gestione AD FS**. 
1. Nel riquadro di spostamento, selezionare **relazioni di Trust** > **Relying Party Trusts**. 
1. Sotto **azioni**, selezionare **Aggiungi Trust Relying Party**.  
1. Nella finestra Aggiungi relying guidata attendibilità, per **selezionare un'origine dati**, usare l'opzione **Importa dati sulla relying party pubblicati online o in una rete locale**. Specificare l'URL dei metadati di federazione: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`.  Lasciare le altre selezioni predefinite. Selezionare **Chiudi**.
1. Il **Modifica regole attestazione** verrà visualizzata la procedura guidata. 
1. Nel **Modifica regole attestazione** procedura guidata, selezionare **Add Rule**. Nelle **scegliere il tipo di regola**, selezionare **inviare attestazioni mediante una regola personalizzata**. Selezionare *Avanti*. 
1. Nelle **Configura regola attestazione**, specificare i valori seguenti:

   - **Nome regola attestazione**: Id non modificabile del problema  
   - **Regola personalizzata**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selezionare **Fine**. 
1. Il **Modifica regole attestazione** finestra verrà visualizzata la nuova regola. Fare clic su **Apply**.  
1. Nella stessa **Modifica regole attestazione** procedura guidata, selezionare **Add Rule**. Nelle **tipo di regola Cohose**, selezionare **inviare attributi LDAP come attestazioni**. Selezionare **Avanti**.
1. Nelle **Configura regola attestazione**, specificare i valori seguenti: 

   - **Nome regola attestazione**: Regola di attestazione di posta elettronica  
   - **Archivio di attributi**: Active Directory  
   - **Attributo LDAP**: Indirizzi di posta elettronica  
   - **Tipo di attestazione in uscita**: Indirizzo di posta elettronica 

1.  Selezionare **Fine**. 
1.  Il **Modifica regole attestazione** finestra verrà visualizzata la nuova regola. Fare clic su **Apply**.  
1.  Fare clic su **OK**. Il server AD FS è ora configurato per la federazione diretta con WS-Fed.

## <a name="next-steps"></a>Passaggi successivi
Successivamente si imposterà [configurare la federazione diretta in Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) nel portale di Azure AD o tramite PowerShell. 

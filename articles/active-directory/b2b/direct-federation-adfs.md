---
title: Configurare la federazione diretta con un'AD FS per B2B - Azure ADSet up direct federation with an AD FS for B2B - Azure AD
description: Informazioni su come configurare ADFS come provider di identità per la federazione diretta in modo che gli ospiti possano accedere alle app di Azure AD
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
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272840"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Esempio: federazione diretta con Active Directory Federation Services (ADFS) (anteprima)Example: Direct federation with Active Directory Federation Services (AD FS) (preview)
|     |
| --- |
| La federazione diretta è una funzionalità di anteprima pubblica di Azure Active Directory.Direct federation is a public preview feature of Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

In questo articolo viene descritto come configurare la [federazione diretta](direct-federation.md) utilizzando Active Directory Federation Services (ADFS) come provider di identità SAML 2.0 o WS-Fed. Per supportare la federazione diretta, alcuni attributi e attestazioni devono essere configurati presso il provider di identità. Per illustrare come configurare un provider di identità per la federazione diretta, verrà utilizzato Active Directory Federation Services (ADFS) come esempio. Verrà illustrato come configurare ADFS sia come provider di identità SAML che come provider di identità WS-Fed.

> [!NOTE]
> In questo articolo viene descritto come configurare ADFS sia per SAML che per WS-Fed a scopo illustrativo. Per le integrazioni di federazione diretta in cui il provider di identità è ADFS, è consigliabile utilizzare WS-Fed come protocollo. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Configurare ADFS per la federazione diretta SAML 2.0
Azure AD B2B può essere configurato per la federazione con provider di identità che usano il protocollo SAML con requisiti specifici elencati di seguito. Per illustrare i passaggi di configurazione SAML, in questa sezione viene illustrato come configurare ADFS per SAML 2.0. 

Per configurare la federazione diretta, è necessario ricevere i seguenti attributi nella risposta SAML 2.0 dal provider di identità. Questi attributi possono essere configurati collegando al file XML del servizio token di sicurezza online o immettendoli manualmente. Passaggio 12 in [Creare un'istanza](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) di ADFS di prova viene descritto come trovare `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`gli endpoint di ADFS o come generare l'URL dei metadati, ad esempio . 

|Attributo  |valore  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Destinatari     |`urn:federation:MicrosoftOnline`         |
|Issuer     |L'URI dell'autorità emittente dell'IdP del partner, ad esempio`http://www.example.com/exk10l6w90DHM0yi...`         |

Le attestazioni seguenti devono essere configurate nel token SAML 2.0 rilasciato dal provider di identità:


|Attributo  |valore  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


Nella sezione successiva viene illustrato come configurare gli attributi e le attestazioni obbligatorie utilizzando ADFS come esempio di provider di identità SAML 2.0.

### <a name="before-you-begin"></a>Prima di iniziare

Un server ADFS deve essere già configurato e funzionante prima di iniziare questa procedura. Per informazioni sulla configurazione di un server ADFS, vedere Creare un'istanza di AD FS 3.0 di test in una macchina virtuale di Azure.For help with setting up an AD FS server, see [Create a test AD FS 3.0 instance on an Azure virtual machine.](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)

### <a name="add-the-claim-description"></a>Aggiungere la descrizione dell'attestazione

1. Nel server ADFS selezionare **Strumenti** > **di gestione di ADFS**.
2. Nel riquadro di spostamento selezionare**Descrizioni attestazione** **servizio** > .
3. In **Azioni**selezionare **Aggiungi descrizione attestazione**.
4. Nella finestra **Aggiungi descrizione attestazione** specificare i valori seguenti:In the Add a Claim Description window, specify the following values:

   - **Nome visualizzato**: Identificatore permanente
   - **Identificatore attestazione**:`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Selezionare la casella di controllo **Pubblica la descrizione dell'attestazione nei metadati federativi come tipo di attestazione che il servizio federativo può accettare.**
   - Selezionare la casella di controllo **Pubblica la descrizione dell'attestazione nei metadati federativi come tipo di attestazione che il servizio federativo può inviare.**

5. Fare clic su **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Aggiungere l'attendibilità della relying party e le regole attestazione

1. Nel server ADFS passare a **Strumenti** > **gestione AD FS**.
2. Nel riquadro di spostamento selezionare Trust**parte trust(Trust)** di **trust-party** > .
3. In **Azioni**selezionare **Aggiungi attendibilità componente**. 
4. Nell'Aggiunta guidata attendibilità componente per **Seleziona origine dati**utilizzare l'opzione Importa dati sulla **relying party pubblicata online o in una rete locale.** Specificare l'URL https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmldei metadati federativi- . Lasciare altre selezioni predefinite. Selezionare **Chiudi**.
5. Verrà visualizzata la procedura guidata **Modifica regole attestazione.**
6. Nella procedura guidata **Modifica regole attestazione** selezionare **Aggiungi regola**. In **Scegli tipo di regola**selezionare Invia attributi LDAP come **attestazioni**. Fare clic su **Avanti**.
7. In **Configura regola attestazione**specificare i valori seguenti: 

   - **Nome regola attestazione**: Regola attestazione tramite posta elettronica 
   - **Archivio attributi**: Active Directory 
   - **Attributo LDAP**: Indirizzi di posta elettronica 
   - **Tipo di attestazione in uscita**: Indirizzo e-mail

8. Fare clic su **Fine**.
9. Nella finestra **Modifica regole attestazione** verrà visualizzata la nuova regola. Fare clic su **Applica**. 
10. Fare clic su **OK**.  

### <a name="create-an-email-transform-rule"></a>Creare una regola di trasformazione della posta elettronicaCreate an email transform rule
1. Passare a **Modifica regole attestazione** e fare clic su **Aggiungi regola**. In **Scegli tipo di regola**selezionare Trasforma **un'attestazione in ingresso** e fare clic su **Avanti**. 
2. In **Configura regola attestazione**specificare i valori seguenti: 

   - **Nome regola attestazione**: Regola di trasformazione posta elettronica 
   - **Tipo di reclamo in entrata**: Indirizzo e-mail 
   - **Tipo di attestazione in uscita**: ID nome 
   - **Formato ID nome in uscita**: Identificatore permanente 
   - Selezionare **Pass-through di tutti i valori attestazione**.

3. Fare clic su **Fine**. 
4. Nella finestra **Modifica regole attestazione** verranno visualizzate le nuove regole. Fare clic su **Applica**. 
5. Fare clic su **OK**. Il server ADFS è ora configurato per la federazione diretta utilizzando il protocollo SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Configurare ADFS per la federazione diretta WS-FedConfigure AD FS for WS-Fed direct federation 
Azure AD B2B può essere configurato per la federazione con provider di identità che usano il protocollo WS-Fed con i requisiti specifici elencati di seguito. Attualmente, i due provider WS-Fed sono stati testati per la compatibilità con Azure AD includono AD FS e Shibboleth. In questo caso, useremo Active Directory Federation Services (ADFS) come esempio del provider di identità WS-Fed. Per altre informazioni su come stabilire una relying party trust tra un provider compatibile con WS-Fed con Azure AD, scaricare i documenti sulla compatibilità dei provider di identità di Azure AD.

Per impostare la federazione diretta, i seguenti attributi devono essere ricevuti nel messaggio WS-Fed dal provider di identità. Questi attributi possono essere configurati collegando al file XML del servizio token di sicurezza online o immettendoli manualmente. Passaggio 12 in [Creare un'istanza](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) di ADFS di prova viene descritto come trovare `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`gli endpoint di ADFS o come generare l'URL dei metadati, ad esempio .
 
|Attributo  |valore  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Destinatari     |`urn:federation:MicrosoftOnline`         |
|Issuer     |L'URI dell'autorità emittente dell'IdP del partner, ad esempio`http://www.example.com/exk10l6w90DHM0yi...`         |

Attestazioni richieste per il token WS-Fed emesso dall'IdP:

|Attributo  |valore  |
|---------|---------|
|Idimmutabile     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

Nella sezione successiva viene illustrato come configurare gli attributi e le attestazioni obbligatori utilizzando ADFS come esempio di provider di identità WS-Fed.

### <a name="before-you-begin"></a>Prima di iniziare
Un server ADFS deve essere già configurato e funzionante prima di iniziare questa procedura. Per informazioni sulla configurazione di un server ADFS, vedere Creare un'istanza di AD FS 3.0 di test in una macchina virtuale di Azure.For help with setting up an AD FS server, see [Create a test AD FS 3.0 instance on an Azure virtual machine.](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Aggiungere l'attendibilità della relying party e le regole attestazione 
1. Nel server ADFS passare a **Strumenti** > **gestione AD FS**. 
1. Nel riquadro di spostamento selezionare Trust**parte trust(Trust)** di **trust-party** > . 
1. In **Azioni**selezionare **Aggiungi attendibilità componente**.  
1. Nell'Aggiunta guidata trust della relying party, per **Seleziona origine dati,** utilizzare l'opzione **Importa dati sulla relying party pubblicata online o in una rete locale.** Specificare l'URL `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`dei metadati federativi: .  Lasciare altre selezioni predefinite. Selezionare **Chiudi**.
1. Verrà visualizzata la procedura guidata **Modifica regole attestazione.** 
1. Nella procedura guidata **Modifica regole attestazione** selezionare **Aggiungi regola**. In **Scegli tipo di regola**selezionare Invia **attestazioni tramite una regola personalizzata.** Fare clic su *Avanti*. 
1. In **Configura regola attestazione**specificare i valori seguenti:

   - **Nome regola attestazione**: ID non modificabile problema  
   - **Regola personalizzata**:`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Fare clic su **Fine**. 
1. Nella finestra **Modifica regole attestazione** verrà visualizzata la nuova regola. Fare clic su **Applica**.  
1. Nella stessa procedura guidata **Modifica regole attestazione** selezionare **Aggiungi regola**. In **Tipo di regola cohose**selezionare Invia attributi LDAP come **attestazioni**. Fare clic su **Avanti**.
1. In **Configura regola attestazione**specificare i valori seguenti: 

   - **Nome regola attestazione**: Regola attestazione tramite posta elettronica  
   - **Archivio attributi**: Active Directory  
   - **Attributo LDAP**: Indirizzi di posta elettronica  
   - **Tipo di attestazione in uscita**: Indirizzo e-mail 

1.  Fare clic su **Fine**. 
1.  Nella finestra **Modifica regole attestazione** verrà visualizzata la nuova regola. Fare clic su **Applica**.  
1.  Fare clic su **OK**. Il server ADFS è ora configurato per la federazione diretta utilizzando WS-Fed.

## <a name="next-steps"></a>Passaggi successivi
Successivamente, si [configurerà](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) la federazione diretta in Azure AD nel portale di Azure AD o tramite PowerShell.Next, you'll configure direct federation either in Azure AD either in the Azure AD portal or by using PowerShell. 

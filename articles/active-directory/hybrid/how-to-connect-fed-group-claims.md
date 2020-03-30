---
title: Configurare le attestazioni di gruppo per le applicazioni con Azure Active Directory Documenti Microsoft
description: Informazioni su come configurare le attestazioni di gruppo per l'uso con Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408403"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Configurare attestazioni di gruppo per le applicazioni con Azure Active Directory (anteprima pubblica)Configure group claims for applications with Azure Active Directory (Public Preview)

Azure Active Directory può fornire informazioni sull'appartenenza a un gruppo di utenti nei token da usare all'interno delle applicazioni.  Sono supportati due modelli principali:

- Gruppi identificati dall'attributo identificatore di oggetto (OID) di Azure Active Directory (generalmente disponibile)Groups identified by their Azure Active Directory object identifier (OID) attribute (Generally Available)
- Gruppi identificati dagli attributi sAMAccountName o GroupSID per gruppi e utenti sincronizzati in Active Directory (AD) (anteprima pubblica)

> [!IMPORTANT]
> Ci sono una serie di avvertenze da notare per questa funzionalità di anteprima:
>
>- Il supporto per l'utilizzo di sAMAccountName e degli attributi dell'identificatore di sicurezza (SID) sincronizzati da locale è progettato per consentire lo spostamento di applicazioni esistenti da ADFS e da altri provider di identità. I gruppi gestiti in Azure AD non contengono gli attributi necessari per generare queste attestazioni.
>- Nelle organizzazioni più grandi il numero di gruppi di cui un utente è membro può superare il limite che Azure Active Directory aggiungerà a un token. 150 gruppi per un token SAML e 200 per un token JWT. Questo può portare a risultati imprevedibili. Se gli utenti dispongono di un numero elevato di appartenenze ai gruppi, è consigliabile usare l'opzione per limitare i gruppi emessi nelle attestazioni ai gruppi pertinenti per l'applicazione.  
>- Per lo sviluppo di nuove applicazioni o nei casi in cui l'applicazione può essere configurata per tale applicazione e in cui non è necessario il supporto di gruppi annidati, è consigliabile che l'autorizzazione in-app sia basata sui ruoli dell'applicazione anziché sui gruppi.  Ciò limita la quantità di informazioni che devono essere inserite nel token, è più sicura e separa l'assegnazione utente dalla configurazione dell'app.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Attestazioni di gruppo per le applicazioni di migrazione da ADFS e altri provider di identitàGroup claims for applications migrating from AD FS and other identity providers

Molte applicazioni configurate per l'autenticazione con ADFS si basano sulle informazioni sull'appartenenza al gruppo sotto forma di attributi del gruppo di Windows AD.   Questi attributi sono il gruppo sAMAccountName, che può essere qualificato in base al nome di dominio, o l'identificatore di protezione del gruppo di Windows (GroupSID).  Quando l'applicazione è federata con ADFS, ADFS utilizza la funzione TokenGroups per recuperare le appartenenze ai gruppi per l'utente.

Un'app che è stata spostata da ADFS deve avere richieste nello stesso formato. Le attestazioni di gruppo e ruolo possono essere generate da Azure Active Directory contenente il dominio qualificato sAMAccountName o il GroupSID sincronizzato da Active Directory anziché l'identità di Azure Active Directory del gruppo.

I formati supportati per le attestazioni di gruppo sono:

- **ObjectId gruppo** di Azure Active Directory (disponibile per tutti i gruppi)
- **sAMAccountName** (disponibile per i gruppi sincronizzati da Active Directory)
- **NetbiosDomain-sAMAccountName** (disponibile per i gruppi sincronizzati da Active Directory)
- **NomeDominioDNS:sAMAccountName** (disponibile per i gruppi sincronizzati da Active Directory)
- **Identificatore** di sicurezza del gruppo locale (disponibile per i gruppi sincronizzati da Active Directory)

> [!NOTE]
> Gli attributi SID di gruppo sAMAccountName e On-Premises sono disponibili solo per gli oggetti Gruppo sincronizzati da Active Directory.   Non sono disponibili nei gruppi creati in Azure Active Directory o Office365.   Le applicazioni configurate in Azure Active Directory per ottenere gli attributi di gruppo locali sincronizzati li ottengono solo per i gruppi sincronizzati.

## <a name="options-for-applications-to-consume-group-information"></a>Opzioni per le applicazioni per l'utilizzo delle informazioni sul gruppo

Le applicazioni possono chiamare l'endpoint dei gruppi MS Graph per ottenere informazioni sui gruppi per l'utente autenticato. Questa chiamata garantisce che tutti i gruppi di cui un utente è membro siano disponibili anche quando è coinvolto un numero elevato di gruppi.  L'enumerazione dei gruppi è quindi indipendente dalle limitazioni relative alle dimensioni dei token.

Tuttavia, se un'applicazione esistente prevede di utilizzare le informazioni di gruppo tramite attestazioni, Azure Active Directory può essere configurato con diversi formati di attestazioni.  Valutare le opzioni seguenti:

- Quando si utilizza l'appartenenza al gruppo per scopi di autorizzazione all'applicazione è preferibile utilizzare l'ObjectID del gruppo. L'ObjectID gruppo non è modificabile e univoco in Azure Active Directory e disponibile per tutti i gruppi.
- Se si utilizza il gruppo locale sAMAccountName per l'autorizzazione, utilizzare nomi completi di dominio;  c'è meno possibilità che i nomi si scontrino. sAMAccountName può essere univoco all'interno di un dominio di Active Directory, ma se più di un dominio di Active Directory è sincronizzato con un tenant di Azure Active Directory è possibile che più di un gruppo abbiano lo stesso nome.
- Considerare l'utilizzo dei [ruoli applicazione](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) per fornire un livello di riferimento indiretto tra l'appartenenza al gruppo e l'applicazione.   L'applicazione prende quindi decisioni di autorizzazione interne in base alle vongole di ruolo nel token.
- Se l'applicazione è configurata per ottenere gli attributi di gruppo sincronizzati da Active Directory e un gruppo non contiene tali attributi, non verrà incluso nelle attestazioni.
- Le attestazioni di gruppo nei token includono gruppi nidificati, tranne quando si usa l'opzione per limitare le attestazioni di gruppo ai gruppi assegnati all'applicazione.  Se un utente è membro di GroupB e GroupB è un membro di GroupA, le attestazioni di gruppo per l'utente conterranno sia GroupA che GroupB. Quando gli utenti di un'organizzazione dispongono di un numero elevato di appartenenze ai gruppi, il numero di gruppi elencati nel token può aumentare le dimensioni del token.  Azure Active Directory limita il numero di gruppi che verrà generato in un token a 150 per le asserzioni SAML e 200 per JWT.  Se un utente è membro di un numero maggiore di gruppi, i gruppi vengono omessi e viene invece incluso un collegamento all'endpoint Graph per ottenere informazioni sul gruppo.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Prerequisiti per l'utilizzo degli attributi di gruppo sincronizzati da Active DirectoryPrerequisites for using Group attributes synchronized from Active Directory

Le attestazioni di appartenenza al gruppo possono essere generate nei token per qualsiasi gruppo se si utilizza il formato ObjectId. Per usare attestazioni di gruppo in formati diversi da ObjectId del gruppo, i gruppi devono essere sincronizzati da Active Directory usando Azure AD Connect.To use group claims in formats other than the group ObjectId, the groups must be synchronized from Active Directory using Azure AD Connect.

Esistono due passaggi per configurare Azure Active Directory per generare i nomi dei gruppi per i gruppi di Active Directory.There are two steps to configuring Azure Active Directory to emit group names for Active Directory Groups.

1. **Sincronizzare i nomi dei gruppi da Active Directory** Prima che Azure Active Directory possa generare i nomi dei gruppi o il SID del gruppo locale nelle attestazioni di gruppo o ruolo, gli attributi necessari devono essere sincronizzati da Active Directory.  È necessario eseguire Azure AD Connect versione 1.2.70 o successiva.   Le versioni precedenti di Azure AD Connect rispetto alla versione 1.2.70 sincronizzeranno gli oggetti gruppo da Active Directory, ma non includeranno gli attributi del nome del gruppo necessari.  Eseguire l'aggiornamento alla versione corrente.

2. **Configurare la registrazione dell'applicazione in Azure Active Directory per includere attestazioni di gruppo nei tokenConfigure the application registration in Azure Active Directory to include group claims in tokens** Le attestazioni di gruppo possono essere configurate nella sezione Applicazioni enterprise del portale o usando il manifesto dell'applicazione nella sezione Registrazioni applicazioni.  Per configurare le attestazioni di gruppo nel manifesto dell'applicazione, vedere "Configurazione della registrazione dell'applicazione Azure Active Directory per gli attributi di gruppo" di seguito.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Aggiungere attestazioni di gruppo ai token per le applicazioni SAML utilizzando la configurazione SSOAdd group claims to tokens for SAML applications using SSO configuration

Per configurare attestazioni di gruppo per una raccolta o un'applicazione SAML non raccolta, aprire **Applicazioni aziendali**, fare clic sull'applicazione nell'elenco, selezionare Configurazione **Single Sign-On**e quindi selezionare **Attributi utente & Attestazioni**.

Fare clic su **Aggiungi un'attestazione di gruppo**  

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Utilizzare i pulsanti di opzione per selezionare i gruppi da includere nel token

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Selezione | Descrizione |
|----------|-------------|
| **Tutti i gruppi** | Genera gruppi di sicurezza, liste di distribuzione e ruoli.  |
| **Gruppi di sicurezza** | Genera gruppi di sicurezza di cui l'utente è membro nell'attestazione dei gruppi |
| **Ruoli di directory** | Se all'utente vengono assegnati ruoli di directory, questi vengono generati come attestazione 'wids' (l'attestazione di gruppi non verrà emessa) |
| **Gruppi assegnati all'applicazione** | Genera solo i gruppi assegnati in modo esplicito all'applicazione e l'utente è membro di |

Ad esempio, per generare tutti i gruppi di sicurezza di cui l'utente è membro, selezionare Gruppi di sicurezza

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Per generare gruppi usando gli attributi di Active Directory sincronizzati da Active Directory anziché dagli oggetti ID di Azure AD, selezionare il formato richiesto dall'elenco a discesa. Solo i gruppi sincronizzati da Active Directory verranno inclusi nelle attestazioni.

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Per generare solo i gruppi assegnati all'applicazione, selezionare **Gruppi assegnati all'applicazione**

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

I gruppi assegnati all'applicazione verranno inclusi nel token.  Altri gruppi di cui l'utente è membro verranno omessi.  Con questa opzione i gruppi nidificati non sono inclusi e l'utente deve essere un membro diretto del gruppo assegnato all'applicazione.

Per modificare i gruppi assegnati all'applicazione, selezionare l'applicazione dall'elenco **Applicazioni aziendali** e quindi fare clic su **Utenti e gruppi** dal menu di spostamento a sinistra dell'applicazione.

Vedere il documento [Assegnare un utente o un gruppo a un'app aziendale](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) per informazioni dettagliate sulla gestione dell'assegnazione di gruppo alle applicazioni.

### <a name="advanced-options"></a>Advanced Options

Il modo in cui vengono emesse le rivendicazioni di gruppo può essere modificato dalle impostazioni in Opzioni avanzate

Personalizzare il nome dell'attestazione di gruppo: se selezionata, è possibile specificare un tipo di attestazione diverso per le attestazioni di gruppo.   Immettere il tipo di attestazione nel campo Nome e lo spazio dei nomi facoltativo per l'attestazione nel campo dello spazio dei nomi.

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Alcune applicazioni richiedono che le informazioni sull'appartenenza al gruppo vengano visualizzate nell'attestazione 'ruolo'. Facoltativamente, è possibile generare i gruppi dell'utente come ruoli selezionando la casella 'Genera gruppi a attestazioni ruolo'.

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Se l'opzione per generare i dati del gruppo come ruoli viene utilizzata, nell'attestazione del ruolo verranno visualizzati solo i gruppi.  Qualsiasi ruolo applicazione assegnato dall'utente non verrà visualizzato nell'attestazione del ruolo.

### <a name="edit-the-group-claims-configuration"></a>Modificare la configurazione delle attestazioni di gruppo

Dopo aver aggiunto una configurazione di attestazione di gruppo alla configurazione Attributi utente & Attestazioni, l'opzione per aggiungere un'attestazione di gruppo verrà disattivata.  Per modificare la configurazione attestazione di gruppo, fare clic sull'attestazione di gruppo nell'elenco **Attestazioni aggiuntive.**

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configurare la registrazione dell'applicazione Azure AD per gli attributi di gruppoConfigure the Azure AD Application Registration for group attributes

Le attestazioni di gruppo possono essere configurate anche nella sezione [Attestazioni facoltative](../../active-directory/develop/active-directory-optional-claims.md) del manifesto dell'applicazione . [Application Manifest](../../active-directory/develop/reference-app-manifest.md)

1. Nel portale ->Azure Active Directory -> Application Registrations->Manifesto di >applicazione Select

2. Abilitare le attestazioni di appartenenza al gruppo modificando groupMembershipClaimEnable group membership claims by changing the groupMembershipClaim

I valori validi sono:

| Selezione | Descrizione |
|----------|-------------|
| **"Tutti"** | Emette gruppi di sicurezza, liste di distribuzione e ruoli |
| **"Gruppo di sicurezza"** | Genera gruppi di sicurezza di cui l'utente è membro nell'attestazione dei gruppi |
| **"RuoloDirectory** | Se all'utente vengono assegnati ruoli di directory, questi vengono generati come attestazione 'wids' (l'attestazione di gruppi non verrà emessa) |
| **"Gruppo Applicazioni** | Genera solo i gruppi assegnati in modo esplicito all'applicazione e l'utente è membro di |

   Ad esempio:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Per impostazione predefinita, gli OggettiOggetti gruppo verranno generati nel valore dell'attestazione di gruppo.  Per modificare il valore dell'attestazione in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in ruolo, usare la configurazione OptionalClaims nel modo seguente:To modify the claim value to contain on premises group attributes, or to change the claim type to role, use OptionalClaims configuration as follows:

3. Impostare attestazioni facoltative di configurazione nome gruppo.

   Se si desidera che i gruppi nel token contengano gli attributi del gruppo di Active Directory locale, specificare a quale attestazione facoltativa del tipo di token deve essere applicata nella sezione relativa alle attestazioni facoltative.  È possibile elencare più tipi di token:Multiple token types can be listed:

   - idToken per il token ID OIDC
   - accessToken per il token di accesso OAuth/OIDC
   - Saml2Token per i token SAML.

   > [!NOTE]
   > Il tipo Saml2Token si applica ai token di formato SAML1.1 e SAML2.0

   Per ogni tipo di token pertinente, modificare l'attestazione dei gruppi per usare la sezione OptionalClaims nel manifesto. Lo schema OptionalClaims è il seguente:The OptionalClaims schema is as follows:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schema delle attestazioni facoltativoOptional Claims Schema | valore |
   |----------|-------------|
   | **Nome:** | Devono essere "gruppi" |
   | **fonte:** | Non usato. Omettere o specificare null |
   | **Essenziale:** | Non usato. Omettere o specificare false |
   | **additionalProperties:** | Elenco di proprietà aggiuntive.  Le opzioni valide sono "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties sono necessari solo uno dei "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se ne è presente più di uno, viene utilizzato il primo e tutti gli altri vengono ignorati.

   Alcune applicazioni richiedono informazioni di gruppo sull'utente nell'attestazione ruolo.  Per modificare il tipo di attestazione da un'attestazione di gruppo a un'attestazione di ruolo, aggiungere "emit_as_roles" a proprietà aggiuntive.  I valori di gruppo verranno generati nell'attestazione del ruolo.

   > [!NOTE]
   > Se viene utilizzato "emit_as_roles" qualsiasi ruolo applicazione configurato che l'utente è assegnato non verrà visualizzato nell'attestazione di ruolo

### <a name="examples"></a>Esempi

Emetti gruppi come nomi di gruppo nei token di accesso OAuth nel formato dnsDomainName/SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Per emettere i nomi dei gruppi da restituire nel formato netbiosDomain-samAccountName come attestazione dei ruoli in SAML e token ID OIDC:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Passaggi successivi

[Assegnare un utente o gruppo a un'app aziendale](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[Configurare le attestazioni per i ruoli](../../active-directory/develop/active-directory-enterprise-app-role-management.md)

---
title: Configurare le attestazioni di gruppo per le applicazioni con Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare le attestazioni di gruppo per l'utilizzo con Azure AD.
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
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170480"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Configurare le attestazioni di gruppo per le applicazioni con Azure Active Directory (anteprima pubblica)

Azure Active Directory possibile fornire le informazioni sull'appartenenza a un gruppo di utenti nei token per l'utilizzo all'interno delle applicazioni.  Sono supportati due modelli principali:

- Gruppi identificati in base al relativo attributo ID oggetto (OID) Azure Active Directory (disponibile a livello generale)
- Gruppi identificati da attributi sAMAccountName o GroupSID per i gruppi e gli utenti di Active Directory (AD) sincronizzati (anteprima pubblica)

> [!IMPORTANT]
> Per questa funzionalità di anteprima è necessario tenere presenti alcune avvertenze:
>
>- Il supporto per l'utilizzo degli attributi sAMAccountName e ID di sicurezza (SID) sincronizzati dall'ambiente locale è progettato per consentire lo stato di trasferimento delle applicazioni esistenti da AD FS e altri provider di identità. I gruppi gestiti in Azure AD non contengono gli attributi necessari per emettere le attestazioni.
>- Nelle organizzazioni più grandi il numero di gruppi di cui un utente è membro può superare il limite che Azure Active Directory aggiungerà a un token. 150 gruppi per un token SAML e 200 per un JWT. Questo può causare risultati imprevedibili. Se si tratta di un potenziale problema, è consigliabile eseguire i test e, se necessario, attendere fino a quando non si aggiungono miglioramenti per consentire la limitazione delle attestazioni ai gruppi rilevanti per l'applicazione.  
>- Per lo sviluppo di nuove applicazioni o nei casi in cui l'applicazione può essere configurata e il supporto per gruppi annidati non è obbligatorio, è consigliabile che l'autorizzazione in-app sia basata sui ruoli applicazione anziché sui gruppi.  Questo limita la quantità di informazioni che devono essere inserite nel token, è più sicuro e separa l'assegnazione dell'utente dalla configurazione dell'app.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Attestazioni di gruppo per le applicazioni che eseguono la migrazione da AD FS e altri provider di identità

Molte applicazioni configurate per l'autenticazione con AD FS si basano sulle informazioni sull'appartenenza ai gruppi sotto forma di attributi del gruppo di Windows AD.   Questi attributi sono il gruppo sAMAccountName, che può essere qualificato per nome di dominio o l'ID di sicurezza del gruppo di Windows (GroupSID).  Quando l'applicazione è federata con AD FS, AD FS usa la funzione TokenGroups per recuperare l'appartenenza ai gruppi per l'utente.

Per trovare la corrispondenza con il token che un'app riceverebbe da AD FS, le attestazioni di gruppo e ruolo possono essere emesse che contengono l'oggetto sAMAccountName qualificato del dominio anziché il Azure Active Directory objectID del gruppo.

I formati supportati per le attestazioni di gruppo sono:

- **ObjectID del gruppo di Azure Active Directory** (Disponibile per tutti i gruppi)
- **sAMAccountName** (Disponibile per i gruppi sincronizzati da Active Directory)
- **NetbiosDomain\sAMAccountName** (Disponibile per i gruppi sincronizzati da Active Directory)
- **DNSDomainName\sAMAccountName** (Disponibile per i gruppi sincronizzati da Active Directory)
- **Identificatore di sicurezza del gruppo locale** (Disponibile per i gruppi sincronizzati da Active Directory)

> [!NOTE]
> gli attributi SID del gruppo sAMAccountName e locale sono disponibili solo negli oggetti gruppo sincronizzati da Active Directory.   Non sono disponibili per i gruppi creati in Azure Active Directory o Office365.   Le applicazioni configurate in Azure Active Directory per ottenere gli attributi del gruppo locale sincronizzati li ottengono solo per i gruppi sincronizzati.

## <a name="options-for-applications-to-consume-group-information"></a>Opzioni per le applicazioni per l'utilizzo di informazioni sui gruppi

Un modo per ottenere informazioni sui gruppi da parte delle applicazioni consiste nel chiamare l'endpoint dei gruppi di grafi per recuperare l'appartenenza al gruppo per l'utente autenticato. Questa chiamata garantisce che tutti i gruppi di cui un utente è membro siano disponibili anche quando è presente un numero elevato di gruppi e l'applicazione deve enumerare tutti i gruppi di cui l'utente è membro.  L'enumerazione dei gruppi è quindi indipendente dalle limitazioni delle dimensioni del token.

Tuttavia, se un'applicazione esistente prevede di utilizzare le informazioni sul gruppo tramite attestazioni nel token ricevuto, Azure Active Directory possibile configurare con una serie di diverse opzioni di attestazione per soddisfare le esigenze dell'applicazione.  Valutare le opzioni seguenti:

- Quando si utilizza l'appartenenza a un gruppo per scopi di autorizzazione all'interno dell'applicazione, è preferibile utilizzare il gruppo ObjectID, che non è modificabile e univoco in Azure Active Directory e disponibile per tutti i gruppi.
- Se si usa il gruppo locale sAMAccountName per l'autorizzazione, usare i nomi completi del dominio;  è possibile che si verifichino situazioni in cui si verificano conflitti di nomi. sAMAccountName può essere univoco in un dominio Active Directory, ma se più di un dominio di Active Directory è sincronizzato con un tenant Azure Active Directory è possibile che più di un gruppo abbia lo stesso nome.
- Si consiglia di usare i [ruoli applicazione](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) per fornire un livello di riferimento indiretto tra l'appartenenza al gruppo e l'applicazione.   L'applicazione quindi prende le decisioni di autorizzazione interne basate su vongole del ruolo nel token.
- Se l'applicazione è configurata in modo da ottenere gli attributi di gruppo sincronizzati da Active Directory e un gruppo non contiene tali attributi, non verrà incluso nelle attestazioni.
- Le attestazioni di gruppo nei token includono gruppi annidati.   Se un utente è un membro di GroupB e GroupB è un membro di GroupA, le attestazioni di gruppo per l'utente conterranno sia GroupA che GroupB. Per le organizzazioni con utilizzo intensivo di gruppi e utenti annidati con un numero elevato di appartenenze ai gruppi, il numero di gruppi elencati nel token può aumentare le dimensioni del token.   Azure Active Directory limita il numero di gruppi che emetterà in un token a 150 per le asserzioni SAML e 200 per JWT per impedire che i token risultino troppo grandi.  Se un utente è membro di un numero maggiore di gruppi rispetto al limite, i gruppi vengono emessi insieme a un collegamento all'endpoint Graph per ottenere informazioni sul gruppo.

> Prerequisiti per l'uso di attributi di gruppo sincronizzati da Active Directory:   I gruppi devono essere sincronizzati da Active Directory usando Azure AD Connect.

Per la configurazione di Azure Active Directory per la creazione di nomi di gruppo per i gruppi di Active Directory sono necessari due passaggi.

1. **Sincronizzare i nomi dei gruppi da Active Directory** Prima che Azure Active Directory possibile creare i nomi dei gruppi o il SID del gruppo locale nelle attestazioni di gruppo o ruolo, è necessario sincronizzare gli attributi necessari da Active Directory.  È necessario eseguire Azure AD Connect versione 1.2.70 o successiva.   Prima della versione 1.2.70 Azure AD Connect sincronizza gli oggetti gruppo da Active Directory, ma non include gli attributi del nome del gruppo necessari per impostazione predefinita.  È necessario eseguire l'aggiornamento alla versione corrente.

2. **Configurare la registrazione dell'applicazione in Azure Active Directory per includere le attestazioni di gruppo nei token** Le attestazioni di gruppo possono essere configurate nella sezione applicazioni aziendali del portale per una raccolta o un'applicazione SAML SSO non raccolta oppure usando il manifesto dell'applicazione nella sezione registrazioni per l'applicazione.  Per configurare le attestazioni di gruppo nel manifesto dell'applicazione, vedere "configurazione della registrazione dell'applicazione Azure Active Directory per gli attributi di gruppo" di seguito.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Configurare le attestazioni di gruppo per le applicazioni SAML usando la configurazione SSO

Per configurare le attestazioni di gruppo per una raccolta o un'applicazione SAML non raccolta, aprire applicazioni aziendali, fare clic sull'applicazione nell'elenco e selezionare configurazione Single Sign-on.

Selezionare l'icona di modifica accanto a "gruppi restituiti nel token"

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Usare i pulsanti di opzione per selezionare i gruppi da includere nel token

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Selezione | Descrizione |
|----------|-------------|
| **Tutti i gruppi** | Genera i gruppi di sicurezza e le liste di distribuzione.   Causa anche i ruoli della directory a cui l'utente è assegnato come attestazione "WIDS" e tutti i ruoli applicazione a cui l'utente è assegnato nell'attestazione dei ruoli. |
| **Gruppi di sicurezza** | Emette i gruppi di sicurezza di cui l'utente è membro nell'attestazione dei gruppi |
| **Liste di distribuzione** | Genera liste di distribuzione di cui l'utente è membro |
| **Ruoli della directory** | Se all'utente sono assegnati ruoli della directory, questi vengono emessi come attestazione "WIDS" (l'attestazione dei gruppi non verrà emessa) |

Ad esempio, per creare tutti i gruppi di sicurezza di cui l'utente è membro, selezionare gruppi di sicurezza.

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Per creare gruppi usando Active Directory gli attributi sincronizzati da Active Directory anziché Azure AD ObjectID selezionare il formato richiesto dall'elenco a discesa.  Questa operazione sostituisce l'ID oggetto nelle attestazioni con valori stringa contenenti nomi di gruppo.   Solo i gruppi sincronizzati da Active Directory verranno inclusi nelle attestazioni.

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>Opzioni avanzate

Il modo in cui vengono emesse le attestazioni di gruppo può essere modificato dalle impostazioni in opzioni avanzate

Personalizzare il nome dell'attestazione di gruppo:  Se questa opzione è selezionata, è possibile specificare un tipo di attestazione diverso per le attestazioni di gruppo.   Immettere il tipo di attestazione nel campo nome e nello spazio dei nomi facoltativo per l'attestazione nel campo spazio dei nomi.

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Per alcune applicazioni è necessario che le informazioni sull'appartenenza al gruppo vengano visualizzate nell'attestazione "Role". Facoltativamente, è possibile creare i gruppi dell'utente come ruoli selezionando la casella di controllo "Crea gruppi di attestazioni di ruolo".

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Se si usa l'opzione per creare dati di gruppo come ruoli, solo i gruppi verranno visualizzati nell'attestazione del ruolo.  Qualsiasi ruolo applicazione assegnato dall'utente non verrà visualizzato nell'attestazione del ruolo.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configurare la registrazione dell'applicazione Azure AD per gli attributi di gruppo

Le attestazioni di gruppo possono essere configurate anche nella sezione [facoltativa attestazioni](../../active-directory/develop/active-directory-optional-claims.md) del [manifesto dell'applicazione](../../active-directory/develop/reference-app-manifest.md).

1. Nel portale-> Azure Active Directory-> registrazioni dell'applicazione-> selezionare applicazione-> manifesto

2. Abilitare le attestazioni di appartenenza al gruppo modificando il groupMembershipClaim

   I valori validi sono:

   - Tutti
   - SecurityGroup
   - DistributionList
   - DirectoryRole

   Esempio:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Per impostazione predefinita, il gruppo ObjectID verrà emesso nel valore dell'attestazione del gruppo.  Per modificare il valore dell'attestazione in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in role, usare la configurazione di OptionalClaims come indicato di seguito:

3. Imposta la configurazione del nome del gruppo attestazioni facoltative.

   Se si vuole che i gruppi nel token contengano gli attributi di gruppo AD locali nella sezione attestazioni facoltative, specificare il tipo di token a cui deve essere applicata l'attestazione facoltativa, il nome dell'attestazione facoltativa richiesta ed eventuali proprietà aggiuntive desiderate.  È possibile elencare più tipi di token:

   - idToken per il token ID OIDC
   - accessToken per il token di accesso OAuth/OIDC
   - Saml2Token per i token SAML.

   > [!NOTE]
   > Il tipo Saml2Token si applica ai token di formato SAML 1.1 e SAML 2.0

   Per ogni tipo di token pertinente, modificare l'attestazione groups in modo da usare la sezione OptionalClaims nel manifesto. Lo schema OptionalClaims è il seguente:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schema delle attestazioni facoltativo | Value |
   |----------|-------------|
   | **nome** | Deve essere "groups" |
   | **origine** | Non usato. Omettere o specificare null |
   | **essenziale** | Non usato. Omettere o specificare false |
   | **additionalProperties:** | Elenco di proprietà aggiuntive.  Le opzioni valide sono "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties è necessario solo uno dei "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se è presente più di un oggetto, viene usato il primo e tutti gli altri vengono ignorati.

   Per alcune applicazioni sono necessarie informazioni sul gruppo relative all'utente nell'attestazione del ruolo.  Per modificare il tipo di attestazione in da un'attestazione di gruppo a un'attestazione di ruolo, aggiungere "emit_as_roles" alle proprietà aggiuntive.  I valori del gruppo verranno emessi nell'attestazione del ruolo.

   > [!NOTE]
   > Se viene usato "emit_as_roles", tutti i ruoli applicazione configurati che l'utente è assegnato non verranno visualizzati nell'attestazione del ruolo

### <a name="examples"></a>Esempi

Creare gruppi come nomi di gruppo nei token di accesso OAuth in formato dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Per creare i nomi dei gruppi da restituire nel formato netbiosDomain\samAccountName come attestazione dei ruoli nei token ID SAML e OIDC:

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

[Informazioni sull'identità ibrida](whatis-hybrid-identity.md)

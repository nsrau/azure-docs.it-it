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
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 72ec59d0082071746cb8db2b06412d90b4958914
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359960"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Configurare le attestazioni di gruppo per le applicazioni con Azure Active Directory

Azure Active Directory possibile fornire le informazioni sull'appartenenza a un gruppo di utenti nei token per l'utilizzo all'interno delle applicazioni.  Sono supportati due modelli principali:

- Gruppi identificati da un attributo OID (Azure Active Directory Object Identifier)
- Gruppi identificati da attributi sAMAccountName o GroupSID per utenti e gruppi sincronizzati con Active Directory (AD)

> [!IMPORTANT]
> Per questa funzionalità è necessario tenere presenti alcune avvertenze:
>
>- Il supporto per l'utilizzo degli attributi sAMAccountName e ID di sicurezza (SID) sincronizzati dall'ambiente locale è progettato per consentire lo stato di trasferimento delle applicazioni esistenti da AD FS e altri provider di identità. I gruppi gestiti in Azure AD non contengono gli attributi necessari per emettere le attestazioni.
>- Nelle organizzazioni più grandi il numero di gruppi di cui un utente è membro può superare il limite che Azure Active Directory aggiungerà a un token. 150 gruppi per un token SAML e 200 per un JWT. Questo può causare risultati imprevedibili. Se gli utenti hanno un numero elevato di appartenenze ai gruppi, è consigliabile usare l'opzione per limitare i gruppi emessi nelle attestazioni ai gruppi pertinenti per l'applicazione.  
>- Per lo sviluppo di nuove applicazioni o nei casi in cui l'applicazione può essere configurata e il supporto per gruppi annidati non è obbligatorio, è consigliabile che l'autorizzazione in-app sia basata sui ruoli applicazione anziché sui gruppi.  Questo limita la quantità di informazioni che devono essere inserite nel token, è più sicuro e separa l'assegnazione dell'utente dalla configurazione dell'app.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Attestazioni di gruppo per le applicazioni che eseguono la migrazione da AD FS e altri provider di identità

Molte applicazioni configurate per l'autenticazione con AD FS si basano sulle informazioni sull'appartenenza ai gruppi sotto forma di attributi del gruppo di Windows AD.   Questi attributi sono il gruppo sAMAccountName, che può essere qualificato per nome di dominio o l'ID di sicurezza del gruppo di Windows (GroupSID).  Quando l'applicazione è federata con AD FS, AD FS usa la funzione TokenGroups per recuperare l'appartenenza ai gruppi per l'utente.

Un'app che è stata spostata da AD FS necessita di attestazioni nello stesso formato. È possibile che le attestazioni di gruppo e ruolo vengano emesse da Azure Active Directory che contengono l'oggetto sAMAccountName qualificato del dominio o il GroupSID sincronizzato da Active Directory anziché il Azure Active Directory objectID del gruppo.

I formati supportati per le attestazioni di gruppo sono:

- **ObjectID del gruppo di Azure Active Directory** (disponibile per tutti i gruppi)
- **sAMAccountName** (disponibile per i gruppi sincronizzati da Active Directory)
- **NetbiosDomain\sAMAccountName** (disponibile per i gruppi sincronizzati da Active Directory)
- **DNSDomainName\sAMAccountName** (disponibile per i gruppi sincronizzati da Active Directory)
- **Identificatore di sicurezza del gruppo locale** (disponibile per i gruppi sincronizzati da Active Directory)

> [!NOTE]
> gli attributi SID del gruppo sAMAccountName e locale sono disponibili solo negli oggetti gruppo sincronizzati da Active Directory.   Non sono disponibili per i gruppi creati in Azure Active Directory o Office365.   Le applicazioni configurate in Azure Active Directory per ottenere gli attributi del gruppo locale sincronizzati li ottengono solo per i gruppi sincronizzati.

## <a name="options-for-applications-to-consume-group-information"></a>Opzioni per le applicazioni per l'utilizzo di informazioni sui gruppi

Le applicazioni possono chiamare l'endpoint dei gruppi MS Graph per ottenere informazioni sui gruppi per l'utente autenticato. Questa chiamata garantisce che tutti i gruppi di cui un utente è membro siano disponibili anche in presenza di un numero elevato di gruppi.  L'enumerazione dei gruppi è quindi indipendente dalle limitazioni delle dimensioni del token.

Tuttavia, se un'applicazione esistente prevede di utilizzare le informazioni sul gruppo tramite attestazioni, Azure Active Directory possibile configurare con diversi formati di attestazioni.  Valutare le opzioni seguenti:

- Quando si usa l'appartenenza a un gruppo per scopi di autorizzazione all'interno dell'applicazione, è preferibile usare il valore ObjectID del gruppo. Il valore ObjectID del gruppo non è modificabile e univoco in Azure Active Directory e disponibile per tutti i gruppi.
- Se si usa il gruppo locale sAMAccountName per l'autorizzazione, usare i nomi completi del dominio;  è possibile che si verifichi un conflitto tra i nomi. sAMAccountName può essere univoco all'interno di un dominio di Active Directory, ma se più di un dominio di Active Directory viene sincronizzato con un tenant Azure Active Directory è possibile che più di un gruppo abbia lo stesso nome.
- Si consiglia di usare i [ruoli applicazione](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) per fornire un livello di riferimento indiretto tra l'appartenenza al gruppo e l'applicazione.   L'applicazione quindi prende le decisioni di autorizzazione interne basate su vongole del ruolo nel token.
- Se l'applicazione è configurata in modo da ottenere gli attributi di gruppo sincronizzati da Active Directory e un gruppo non contiene tali attributi, non verrà incluso nelle attestazioni.
- Le attestazioni di gruppo nei token includono gruppi annidati tranne quando si usa l'opzione per limitare le attestazioni di gruppo ai gruppi assegnati all'applicazione.  Se un utente è un membro di GroupB e GroupB è un membro di GroupA, le attestazioni di gruppo per l'utente conterranno sia GroupA che GroupB. Quando gli utenti di un'organizzazione hanno un numero elevato di appartenenze a gruppi, il numero di gruppi elencati nel token può aumentare le dimensioni del token.  Azure Active Directory limita il numero di gruppi che emetterà in un token a 150 per le asserzioni SAML e 200 per JWT.  Se un utente è membro di un numero maggiore di gruppi, i gruppi vengono omessi e viene invece incluso un collegamento all'endpoint Graph per ottenere informazioni sul gruppo.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Prerequisiti per l'uso di attributi di gruppo sincronizzati da Active Directory

Se si usa il formato ObjectId, le attestazioni di appartenenza a gruppi possono essere emesse in token per qualsiasi gruppo. Per usare le attestazioni di gruppo in formati diversi da ObjectId del gruppo, i gruppi devono essere sincronizzati da Active Directory usando Azure AD Connect.

Per la configurazione di Azure Active Directory per la creazione di nomi di gruppo per i gruppi di Active Directory sono necessari due passaggi.

1. **Sincronizzare i nomi dei gruppi da Active Directory** Prima che Azure Active Directory possibile creare i nomi dei gruppi o il SID del gruppo locale nelle attestazioni di gruppo o ruolo, è necessario sincronizzare gli attributi necessari da Active Directory.  È necessario eseguire Azure AD Connect versione 1.2.70 o successiva.   Le versioni precedenti di Azure AD Connect rispetto a 1.2.70 Sincronizza gli oggetti gruppo da Active Directory, ma non include gli attributi del nome del gruppo necessari.  Eseguire l'aggiornamento alla versione corrente.

2. **Configurare la registrazione dell'applicazione in Azure Active Directory per includere le attestazioni di gruppo nei token** Le attestazioni di gruppo possono essere configurate nella sezione applicazioni aziendali del portale o usando il manifesto dell'applicazione nella sezione registrazioni dell'applicazione.  Per configurare le attestazioni di gruppo nel manifesto dell'applicazione, vedere "configurazione della registrazione dell'applicazione Azure Active Directory per gli attributi di gruppo" di seguito.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Aggiungere attestazioni di gruppo ai token per le applicazioni SAML usando la configurazione SSO

Per configurare le attestazioni di gruppo per una raccolta o un'applicazione SAML non raccolta, aprire **applicazioni aziendali**, fare clic sull'applicazione nell'elenco, selezionare **configurazione Single Sign-on**e quindi selezionare **attributi utente & attestazioni**.

Fare clic su **Aggiungi un'attestazione di gruppo**  

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Usare i pulsanti di opzione per selezionare i gruppi da includere nel token

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Selezione | Descrizione |
|----------|-------------|
| **Tutti i gruppi** | Genera i gruppi di sicurezza e gli elenchi di distribuzione e i ruoli.  |
| **Gruppi di sicurezza** | Emette i gruppi di sicurezza di cui l'utente è membro nell'attestazione dei gruppi |
| **Ruoli della directory** | Se all'utente sono assegnati ruoli della directory, questi vengono emessi come attestazione "WIDS" (i gruppi non verranno emessi) |
| **Gruppi assegnati all'applicazione** | Emette solo i gruppi assegnati in modo esplicito all'applicazione e l'utente è un membro di |

Ad esempio, per creare tutti i gruppi di sicurezza di cui l'utente è membro, selezionare gruppi di sicurezza.

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Per creare gruppi usando Active Directory gli attributi sincronizzati da Active Directory anziché Azure AD ObjectID selezionare il formato richiesto dall'elenco a discesa. Solo i gruppi sincronizzati da Active Directory verranno inclusi nelle attestazioni.

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Per creare solo i gruppi assegnati all'applicazione, selezionare **i gruppi assegnati all'applicazione**

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

I gruppi assegnati all'applicazione verranno inclusi nel token.  Gli altri gruppi di cui l'utente è membro verranno omessi.  Con questa opzione i gruppi annidati non sono inclusi e l'utente deve essere un membro diretto del gruppo assegnato all'applicazione.

Per modificare i gruppi assegnati all'applicazione, selezionare l'applicazione nell'elenco **applicazioni aziendali** e quindi fare clic su **utenti e gruppi** nel menu di navigazione a sinistra dell'applicazione.

Per informazioni dettagliate sulla gestione dell'assegnazione dei gruppi alle applicazioni, vedere il documento [assegnare un utente o un gruppo a un'app aziendale](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) .

### <a name="advanced-options"></a>Opzioni avanzate

Il modo in cui vengono emesse le attestazioni di gruppo può essere modificato dalle impostazioni in opzioni avanzate

Personalizzare il nome dell'attestazione di gruppo: se selezionato, è possibile specificare un tipo di attestazione diverso per le attestazioni di gruppo.   Immettere il tipo di attestazione nel campo nome e nello spazio dei nomi facoltativo per l'attestazione nel campo spazio dei nomi.

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Per alcune applicazioni è necessario che le informazioni sull'appartenenza al gruppo vengano visualizzate nell'attestazione "Role". Facoltativamente, è possibile creare i gruppi dell'utente come ruoli selezionando la casella di controllo "Crea gruppi di attestazioni di ruolo".

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Se si usa l'opzione per creare dati di gruppo come ruoli, solo i gruppi verranno visualizzati nell'attestazione del ruolo.  Qualsiasi ruolo applicazione assegnato dall'utente non verrà visualizzato nell'attestazione del ruolo.

### <a name="edit-the-group-claims-configuration"></a>Modificare la configurazione delle attestazioni del gruppo

Una volta aggiunta una configurazione di attestazione gruppo agli attributi utente & configurazione delle attestazioni, l'opzione per aggiungere un'attestazione di gruppo sarà disattivata.  Per modificare la configurazione dell'attestazione di gruppo, fare clic sull'attestazione gruppo nell'elenco **attestazioni aggiuntive** .

![interfaccia utente delle attestazioni](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configurare la registrazione dell'applicazione Azure AD per gli attributi di gruppo

Le attestazioni di gruppo possono essere configurate anche nella sezione [facoltativa attestazioni](../../active-directory/develop/active-directory-optional-claims.md) del [manifesto dell'applicazione](../../active-directory/develop/reference-app-manifest.md).

1. Nel portale->Azure Active Directory-> registrazioni dell'applicazione->selezionare applicazione->manifesto

2. Abilitare le attestazioni di appartenenza al gruppo modificando il groupMembershipClaim

I valori validi sono:

| Selezione | Descrizione |
|----------|-------------|
| **Tutti** | Genera gruppi di sicurezza, elenchi di distribuzione e ruoli |
| **"SecurityGroup"** | Emette i gruppi di sicurezza di cui l'utente è membro nell'attestazione dei gruppi |
| **"DirectoryRole** | Se all'utente sono assegnati ruoli della directory, questi vengono emessi come attestazione "WIDS" (i gruppi non verranno emessi) |
| **"ApplicationGroup** | Emette solo i gruppi assegnati in modo esplicito all'applicazione e l'utente è un membro di |

   Ad esempio:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Per impostazione predefinita, verrà restituito il gruppo ObjectID nel valore dell'attestazione del gruppo.  Per modificare il valore dell'attestazione in modo che contenga gli attributi del gruppo locale o per modificare il tipo di attestazione in un ruolo, usare la configurazione OptionalClaims come indicato di seguito:

3. Impostare le attestazioni facoltative della configurazione del nome del gruppo.

   Se si vuole che i gruppi nel token contengano gli attributi di gruppo AD locali, specificare l'attestazione facoltativa per il tipo di token da applicare alla sezione attestazioni facoltative.  È possibile elencare più tipi di token:

   - idToken per il token ID OIDC
   - accessToken per il token di accesso OAuth/OIDC
   - Saml2Token per i token SAML.

   > [!NOTE]
   > Il tipo Saml2Token si applica ai token nel formato SAML1.1 e SAML2.0

   Per ogni tipo di token pertinente, modificare l'attestazione dei gruppi affinché usi la sezione OptionalClaims nel manifesto. Lo schema OptionalClaims è il seguente:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schema delle attestazioni facoltativo | valore |
   |----------|-------------|
   | **name:** | Deve essere "groups" |
   | **source:** | Non usato. Omettere o specificare null |
   | **essential:** | Non usato. Omettere o specificare false |
   | **additionalProperties:** | Elenco di proprietà aggiuntive.  Le opzioni valide sono "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   In additionalProperties è necessario solo un "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se sono presenti più proprietà, viene usata la prima e tutte le altre vengono ignorate.

   Per alcune applicazioni sono necessarie informazioni sul gruppo relative all'utente nell'attestazione del ruolo.  Per modificare il tipo di attestazione in da un'attestazione di gruppo a un'attestazione di ruolo, aggiungere "emit_as_roles" alle proprietà aggiuntive.  I valori del gruppo verranno restituiti nell'attestazione del ruolo.

   > [!NOTE]
   > Se viene usata la proprietà "emit_as_roles", i ruoli applicazione configurati a cui l'utente è assegnato non verranno visualizzati nell'attestazione del ruolo

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

- [Aggiungere l'autorizzazione utilizzando gruppi & attestazioni di gruppi a un'app Web ASP.NET Core (esempio di codice)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [Assegnare un utente o gruppo a un'app aziendale](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [Configurare le attestazioni per i ruoli](../../active-directory/develop/active-directory-enterprise-app-role-management.md)

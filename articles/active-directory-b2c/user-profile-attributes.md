---
title: Attributi del profilo utente in Azure Active Directory B2C
description: Informazioni sugli attributi del tipo di risorsa utente supportati dal profilo utente della directory Azure AD B2C. Informazioni sugli attributi predefiniti, sulle estensioni e sul modo in cui gli attributi vengono mappati a Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80057299"
---
# <a name="user-profile-attributes"></a>Attributi del profilo utente

Il profilo utente della directory Azure Active Directory (Azure AD) B2C viene fornito con un set predefinito di attributi, ad esempio nome, cognome, città, codice postale e numero di telefono. È possibile estendere il profilo utente con i dati dell'applicazione senza che sia necessario un archivio dati esterno. La maggior parte degli attributi che possono essere utilizzati con Azure AD B2C profili utente è supportata anche da Microsoft Graph. Questo articolo descrive gli attributi del profilo utente Azure AD B2C supportati. Vengono inoltre annotati gli attributi non supportati da Microsoft Graph, nonché Microsoft Graph attributi che non devono essere utilizzati con Azure AD B2C.

> [!IMPORTANT]
> Non usare gli attributi predefiniti o di estensione per archiviare dati personali sensibili, ad esempio le credenziali dell'account, i numeri di identificazione degli enti pubblici, i dati di titolari di carte, i dati finanziari, le informazioni sanitarie o informazioni riservate.

È anche possibile eseguire l'integrazione con sistemi esterni. È possibile, ad esempio, utilizzare Azure AD B2C per l'autenticazione, ma delegare a un database di gestione delle relazioni con i clienti (CRM) esterno o al database di fedeltà dei clienti come fonte autorevole di dati del cliente. Per ulteriori informazioni, vedere la soluzione [profilo remoto](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) .

Nella tabella seguente sono elencati gli attributi del [tipo di risorsa utente](https://docs.microsoft.com/graph/api/resources/user) supportati dal profilo utente della directory Azure ad B2C. Fornisce le informazioni seguenti su ogni attributo:

- Nome dell'attributo usato da Azure AD B2C (seguito dal nome del Microsoft Graph tra parentesi, se diverso)
- Tipo di dati attribute
- Descrizione attributo
- Se l'attributo è disponibile nell'portale di Azure
- Se l'attributo può essere usato in un flusso utente
- Se l'attributo può essere utilizzato in un criterio personalizzato [Azure ad profilo tecnico](active-directory-technical-profile.md) e in quale sezione (&lt;InputClaims&gt;, &lt;OutputClaims&gt;o &lt;PersistedClaims&gt;)

|Nome     |Type     |Descrizione|Portale di Azure|Flussi degli utenti|Criteri personalizzati|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Indica se l'account utente è abilitato o disabilitato: **true** se l'account è abilitato; in caso contrario, **false**.|Sì|No|Reso permanente, output|
|ageGroup        |string|Il gruppo di età dell'utente. Valori possibili: null, undefined, minor, Adult, NotAdult.|Sì|No|Reso permanente, output|
|alternativeSecurityId ([identità](manage-user-accounts-graph-api.md#identities-property))|string|Singola identità utente del provider di identità esterno.|No|No|Input, reso permanente, output|
|alternativeSecurityIds ([identità](manage-user-accounts-graph-api.md#identities-property))|raccolta securityId alternativa|Raccolta di identità utente da provider di identità esterni.|No|No|Reso permanente, output|
|city            |string|Città in cui si trova l'utente. Lunghezza massima 128.|Sì|Sì|Reso permanente, output|
|consentProvidedForMinor|string|Indica se il consenso è stato fornito per un minore. Valori consentiti: null, concesse, negate o notRequired.|Sì|No|Reso permanente, output|
|country         |string|Paese in cui si trova l'utente. Esempio: "US" o "UK". Lunghezza massima 128.|Sì|Sì|Reso permanente, output|
|createdDateTime|Datetime|Data di creazione dell'oggetto utente. Sola lettura.|No|No|Reso permanente, output|
|creationType    |string|Se l'account utente è stato creato come account locale per un tenant di Azure Active Directory B2C, il valore è LocalAccount o nameCoexistence. Sola lettura.|No|No|Reso permanente, output|
|dateOfBirth     |Data|Data di nascita.|No|No|Reso permanente, output|
|department      |string|Nome del reparto in cui lavora l'utente. Lunghezza massima 64.|Sì|No|Reso permanente, output|
|displayName     |string|Nome visualizzato per l'utente. Lunghezza massima 256.|Sì|Sì|Reso permanente, output|
|facsimileTelephoneNumber<sup>1</sup>|string|Numero del fax aziendale dell'utente.|Sì|No|Reso permanente, output|
|givenName       |string|Nome specificato dell'utente. Lunghezza massima 64.|Sì|Sì|Reso permanente, output|
|jobTitle        |string|Posizione dell'utente. Lunghezza massima 128.|Sì|Sì|Reso permanente, output|
|immutableId     |string|Identificatore che viene in genere usato per gli utenti migrati da Active Directory locali.|No|No|Reso permanente, output|
|legalAgeGroupClassification|string|Classificazione del gruppo Age Legal. Sola lettura e calcolata in base alle proprietà ageGroup e consentProvidedForMinor. Valori consentiti: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult e Adult.|Sì|No|Reso permanente, output|
|legalCountry<sup>1</sup>  |string|Paese a scopo legale.|No|No|Reso permanente, output|
|mail            |string|Indirizzo SMTP dell'utente, ad esempio "bob@contoso.com". Di sola lettura.|No|No|Reso permanente, output|
|mailNickName    |string|Alias di posta elettronica dell'utente. Lunghezza massima 64.|No|No|Reso permanente, output|
|Mobile (mobilePhone) |string|Numero di cellulare principale dell'utente. Lunghezza massima 64.|Sì|No|Reso permanente, output|
|netId           |string|ID rete.|No|No|Reso permanente, output|
|objectId        |string|Identificatore univoco globale (GUID) che rappresenta l'identificatore univoco per l'utente. Esempio: 12345678-9ABC-def0-1234-56789abcde. Sola lettura, non modificabile.|Sola lettura|Sì|Input, reso permanente, output|
|otherMails      |Raccolta di tipi string|Elenco di indirizzi e-mail aggiuntivi per l'utente. Esempio: ["bob@contoso.com", "Robert@fabrikam.com"].|Sì (indirizzo di posta elettronica alternativo)|No|Reso permanente, output|
|password        |string|Password per l'account locale durante la creazione dell'utente.|No|No|Persisted|
|passwordPolicies     |string|Criteri della password. Si tratta di una stringa costituita da nomi di criteri diversi separati da virgola. ad esempio "DisablePasswordExpiration, DisableStrongPassword".|No|No|Reso permanente, output|
|physicalDeliveryOfficeName (officeLocation)|string|Posizione dell'ufficio nella sede di lavoro dell'utente. Lunghezza massima 128.|Sì|No|Reso permanente, output|
|postalCode      |string|CAP dell'indirizzo postale dell'utente. Il CAP è specifico del paese dell'utente. Negli Stati Uniti, questo attributo contiene il codice postale. Lunghezza massima 40.|Sì|No|Reso permanente, output|
|preferredLanguage    |string|Lingua preferita dell'utente. Deve seguire il codice ISO 639-1. Esempio: "en-US".|No|No|Reso permanente, output|
|refreshTokensValidFromDateTime|Datetime|Tutti i token di aggiornamento emessi prima di questa ora non sono validi e le applicazioni riceveranno un errore quando si usa un token di aggiornamento non valido per acquisire un nuovo token di accesso. In tal caso, l'applicazione dovrà acquisire un nuovo token di aggiornamento effettuando una richiesta all'endpoint di autorizzazione. Di sola lettura.|No|No|Output|
|signInNames ([identità](manage-user-accounts-graph-api.md#identities-property)) |string|Nome di accesso univoco dell'utente account locale di qualsiasi tipo nella directory. Usare questa opzione per ottenere un utente con valore di accesso senza specificare il tipo di account locale.|No|No|Input|
|signInNames. userName ([identità](manage-user-accounts-graph-api.md#identities-property)) |string|Nome utente univoco dell'account locale utente nella directory. Usare questa operazione per creare o ottenere un utente con un nome utente di accesso specifico. Se si specifica questa opzione solo in PersistedClaims durante l'operazione patch, gli altri tipi di signInNames vengono rimossi. Se si desidera aggiungere un nuovo tipo di signInNames, è necessario anche salvare in modo permanente signInNames esistente.|No|No|Input, reso permanente, output|
|signInNames. phoneNumber ([identità](manage-user-accounts-graph-api.md#identities-property)) |string|Numero di telefono univoco dell'utente account locale nella directory. Usare questa operazione per creare o ottenere un utente con un numero di telefono di accesso specifico. Se si specifica questa opzione solo in PersistedClaims durante l'operazione patch, gli altri tipi di signInNames vengono rimossi. Se si desidera aggiungere un nuovo tipo di signInNames, è necessario anche salvare in modo permanente signInNames esistente.|No|No|Input, reso permanente, output|
|signInNames. emailAddress ([identità](manage-user-accounts-graph-api.md#identities-property))|string|Indirizzo di posta elettronica univoco dell'utente account locale nella directory. Usare questa operazione per creare o ottenere un utente con un indirizzo di posta elettronica di accesso specifico. Se si specifica questa opzione solo in PersistedClaims durante l'operazione patch, gli altri tipi di signInNames vengono rimossi. Se si desidera aggiungere un nuovo tipo di signInNames, è necessario anche salvare in modo permanente signInNames esistente.|No|No|Input, reso permanente, output|
|state           |string|Stato dell'indirizzo dell'utente. Lunghezza massima 128.|Sì|Sì|Reso permanente, output|
|streetAddress   |string|Indirizzo della sede di lavoro dell'utente. Lunghezza massima 1024.|Sì|Sì|Reso permanente, output|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|string|Il numero di telefono secondario dell'utente, usato per l'autenticazione a più fattori.|Sì|No|Reso permanente, output|
|strongAuthenticationEmailAddress<sup>1</sup>|string|Indirizzo SMTP dell'utente. Esempio: "bob@contoso.com" questo attributo viene usato per l'accesso con il criterio username, per archiviare l'indirizzo di posta elettronica dell'utente. Indirizzo di posta elettronica usato in un flusso di reimpostazione della password.|Sì|No|Reso permanente, output|
|strongAuthenticationPhoneNumber<sup>1</sup>|string|Il numero di telefono primario dell'utente, usato per l'autenticazione a più fattori.|Sì|No|Reso permanente, output|
|surname         |string|Cognome dell'utente. Lunghezza massima 64.|Sì|Sì|Reso permanente, output|
|telephoneNumber (prima voce di businessPhones)|string|Numero di telefono principale della sede di lavoro dell'utente.|Sì|No|Reso permanente, output|
|userPrincipalName    |string|Nome dell'entità utente (UPN) dell'utente. Il nome UPN è un nome di accesso in sintassi Internet per l'utente in base allo standard Internet RFC 822. Il dominio deve essere presente nella raccolta dei domini verificati del tenant. Questa proprietà è obbligatoria quando viene creato un account. Non modificabili:|No|No|Input, reso permanente, output|
|usageLocation   |string|Proprietà obbligatoria per utenti a cui vengono assegnate licenze per requisiti legali dei quali servizi verificare la disponibilità nei paesi. Non ammette i valori NULL. Codice paese indicato con due lettere (standard ISO 3166). Esempi: "US", "JP" e "GB".|Sì|No|Reso permanente, output|
|userType        |string|Valore stringa che può essere usato per classificare i tipi di utente nella directory. Il valore deve essere member. Di sola lettura.|Sola lettura|No|Reso permanente, output|
|userState (externalUserState)<sup>2</sup>|string|Solo per Azure AD account B2B, indica se l'invito è PendingAcceptance o accettato.|No|No|Reso permanente, output|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|Mostra il timestamp per l'ultima modifica apportata alla proprietà UserState.|No|No|Reso permanente, output|
|<sup>1</sup> Non supportato da Microsoft Graph<br><sup>2</sup> Non deve essere usato con Azure AD B2C||||||


## <a name="extension-attributes"></a>Attributi di estensione

Spesso è necessario creare attributi personalizzati, come nei casi seguenti:

- Un'applicazione per i clienti deve essere mantenute per un attributo, ad esempio **LoyaltyNumber**.
- Un provider di identità ha un identificatore utente univoco che deve essere salvato, ad esempio **uniqueUserGUID**.
- Un percorso utente personalizzato deve essere mantenuto per uno stato di un utente, ad esempio **migrationStatus**.

Con Azure AD B2C è possibile estendere il set di attributi archiviati in ogni account utente. Gli attributi di estensione [estendono lo schema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) degli oggetti utente nella directory. Gli attributi di estensione possono essere registrati solo in un oggetto applicazione, anche se possono contenere dati per un utente. L'attributo di estensione è associato all'applicazione denominata B2C-Extensions-app. Non modificare questa applicazione, perché viene usata da Azure AD B2C per archiviare i dati utente. Questa applicazione è reperibile in Azure Active Directory Registrazioni app.

> [!NOTE]
> - È possibile scrivere fino a 100 attributi di estensione in qualsiasi account utente.
> - Se l'applicazione B2C-Extensions-app viene eliminata, gli attributi di estensione vengono rimossi da tutti gli utenti insieme ai dati che contengono.
> - Se l'applicazione elimina un attributo di estensione, questo viene rimosso da tutti gli account utente e i valori vengono eliminati.
> - Il nome sottostante dell'attributo di estensione viene generato nel formato "Extension_" + ID applicazione + "_" + nome attributo. Ad esempio, se si crea un attributo di estensione LoyaltyNumber e l'ID applicazione B2C-Extensions-app è 831374b3-BD50-41BF-aa54-263ec9e050fc, il nome dell'attributo di estensione sottostante sarà: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Usare il nome sottostante quando si eseguono API Graph query per creare o aggiornare gli account utente.

Quando si definisce una proprietà in un'estensione dello schema sono supportati i tipi di dati seguenti:

|Tipo di proprietà |Osservazioni  |
|--------------|---------|
|Boolean    | Valori possibili: **true** o **false**. |
|Datetime   | Deve essere specificato nel formato ISO 8601. Verrà archiviato in formato UTC.   |
|Integer    | Valore a 32 bit.               |
|string     | 256 caratteri al massimo.     |

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sugli attributi di estensione:
- [Estensioni dello schema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definire attributi personalizzati con il flusso utente](user-flow-custom-attributes.md)
- [Definire attributi personalizzati con criteri personalizzati](custom-policy-custom-attributes.md)

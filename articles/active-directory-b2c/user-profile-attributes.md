---
title: Attributi del profilo utente in Azure Active Directory B2C
description: Informazioni sugli attributi del tipo di risorsa utente supportati dal profilo utente della directory B2C di Azure AD. Informazioni sugli attributi incorporati, le estensioni e il mapping degli attributi a Microsoft Graph.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057299"
---
# <a name="user-profile-attributes"></a>Attributi del profilo utente

Il profilo utente della directory B2C di Azure Active Directory (Azure AD) include un set predefinito di attributi, ad esempio nome, cognome, città, codice postale e numero di telefono. È possibile estendere il profilo utente con i dati dell'applicazione personalizzati senza richiedere un archivio dati esterno. La maggior parte degli attributi che possono essere usati con i profili utente B2C di Azure AD sono supportati anche da Microsoft Graph. Questo articolo descrive gli attributi del profilo utente B2C di Azure AD supportati. Prende inoltre nota degli attributi che non sono supportati da Microsoft Graph, nonché degli attributi di Microsoft Graph che non devono essere usati con Azure AD B2C.

> [!IMPORTANT]
> Non utilizzare gli attributi incorporati o di estensione per archiviare dati personali sensibili, ad esempio le credenziali dell'account, i numeri di identificazione governativi, i dati dei titolari di carta, i dati dell'account finanziario, le informazioni sanitarie o le informazioni di base riservate.

È inoltre possibile integrare con sistemi esterni. Ad esempio, è possibile usare Azure AD B2C per l'autenticazione, ma delegare a un database di fidelizzazione delle relazioni con i clienti (CRM) esterno o al database di fedeltà dei clienti come origine autorevole dei dati dei clienti. Per altre informazioni, vedere la soluzione [di profilo remoto.](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)

Nella tabella seguente sono elencati gli attributi del [tipo di risorsa utente](https://docs.microsoft.com/graph/api/resources/user) supportati dal profilo utente della directory B2C di Azure AD. Fornisce le seguenti informazioni su ogni attributo:

- Nome dell'attributo usato da Azure AD B2C (seguito dal nome di Microsoft Graph tra parentesi, se diverso)Attribute name used by Azure AD B2C (followed by the Microsoft Graph name in parentheses, if different)
- Tipo di dati degli attributi
- Descrizione dell'attributo
- Se l'attributo è disponibile nel portale di AzureIf the attribute is available in the Azure portal
- Se l'attributo può essere utilizzato in un flusso utente
- Se l'attributo può essere usato in un profilo&lt;tecnico&gt;di &lt;Azure&gt; [AD](active-directory-technical-profile.md) dei criteri personalizzati e in quale sezione ( InputClaims , OutputClaims o &lt;PersistedClaims&gt;)

|Nome     |Type     |Descrizione|Portale di Azure|Flussi degli utenti|Criteri personalizzati|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Se l'account utente è abilitato o disabilitato: **true** se l'account è abilitato, in caso contrario **false**.|Sì|No|Persisted, Output|
|ageGroup        |string|Gruppo di età dell'utente. Valori possibili: null, Undefined, Minor, Adult, NotAdult.|Sì|No|Persisted, Output|
|alternativeSecurityId ([Identities](manage-user-accounts-graph-api.md#identities-property))|string|Un'identità utente singolo dal provider di identità esterno.|No|No|Input, Persistented, Output|
|alternativeSecurityIds ([Identities](manage-user-accounts-graph-api.md#identities-property))|raccolta securityId alternativa|Raccolta di identità utente da provider di identità esterni.|No|No|Persisted, Output|
|city            |string|Città in cui si trova l'utente. Lunghezza massima 128.|Sì|Sì|Persisted, Output|
|consentProvidedForMinor|string|Se il consenso è stato fornito per un minore. Valori consentiti: null, granted, denied o notRequired.|Sì|No|Persisted, Output|
|country         |string|Paese in cui si trova l'utente. Esempio: "US" o "UK". Lunghezza massima 128.|Sì|Sì|Persisted, Output|
|createdDateTime|Datetime|Data di creazione dell'oggetto utente. Sola lettura.|No|No|Persisted, Output|
|creationType    |string|Se l'account utente è stato creato come account locale per un tenant B2C di Azure Active Directory, il valore è LocalAccount o nameCoexistence. Sola lettura.|No|No|Persisted, Output|
|dateOfBirth     |Data|Data di nascita.|No|No|Persisted, Output|
|department      |string|Nome del reparto in cui lavora l'utente. Lunghezza massima 64.|Sì|No|Persisted, Output|
|displayName     |string|Nome visualizzato per l'utente. Lunghezza massima 256.|Sì|Sì|Persisted, Output|
|facsimileTelephoneNumber<sup>1</sup>|string|Numero del fax aziendale dell'utente.|Sì|No|Persisted, Output|
|givenName       |string|Nome specificato dell'utente. Lunghezza massima 64.|Sì|Sì|Persisted, Output|
|jobTitle        |string|Posizione dell'utente. Lunghezza massima 128.|Sì|Sì|Persisted, Output|
|immutableId     |string|Identificatore che viene in genere utilizzato per gli utenti migrati da Active Directory locale.|No|No|Persisted, Output|
|legalAgeGroupClassification|string|Classificazione legale dei gruppi di età. Sola lettura e calcolata in base alle proprietà ageGroup e consentProvidedForMinor. Valori consentiti: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult e adult.|Sì|No|Persisted, Output|
|legalPaese<sup>1</sup>  |string|paese per scopi legali.|No|No|Persisted, Output|
|mail            |string|L'indirizzo SMTP dell'utente,bob@contoso.comad esempio " ". Di sola lettura.|No|No|Persisted, Output|
|mailNickName    |string|Alias di posta elettronica dell'utente. Lunghezza massima 64.|No|No|Persisted, Output|
|mobile (mobilePhone) |string|Numero di cellulare principale dell'utente. Lunghezza massima 64.|Sì|No|Persisted, Output|
|Netid           |string|ID di rete.|No|No|Persisted, Output|
|objectId        |string|Identificatore univoco globale (GUID) che è l'identificatore univoco per l'utente. Esempio: 12345678-9abc-def0-1234-56789abcde. Sola lettura, Immutabile.|Sola lettura|Sì|Input, Persistented, Output|
|otherMails      |Raccolta di tipi string|Elenco di indirizzi e-mail aggiuntivi per l'utente. Esempio: ["bob@contoso.com", "Robert@fabrikam.com"].|Sì (e-mail alternativa)|No|Persisted, Output|
|password        |string|La password per l'account locale durante la creazione dell'utente.|No|No|Persisted|
|passwordPolicies     |string|Criterio della password. Si tratta di una stringa costituita da un nome di criterio diverso separato da virgola. ad esempio "DisablePasswordExpiration, DisableStrongPassword".|No|No|Persisted, Output|
|physicalDeliveryOfficeName (officeLocation)|string|Posizione dell'ufficio nella sede di lavoro dell'utente. Lunghezza massima 128.|Sì|No|Persisted, Output|
|postalCode      |string|CAP dell'indirizzo postale dell'utente. Il CAP è specifico del paese dell'utente. Negli Stati Uniti, questo attributo contiene il codice postale. Lunghezza massima 40.|Sì|No|Persisted, Output|
|preferredLanguage    |string|Lingua preferita dell'utente. Deve seguire il codice ISO 639-1. Esempio: "en-US".|No|No|Persisted, Output|
|refreshTokensValidFromDateTime|Datetime|Tutti i token di aggiornamento rilasciati prima di questo periodo non sono validi e le applicazioni riceveranno un errore quando si usa un token di aggiornamento non valido per acquisire un nuovo token di accesso. In questo caso, l'applicazione dovrà acquisire un nuovo token di aggiornamento effettuando una richiesta all'endpoint di autorizzazione. Di sola lettura.|No|No|Output|
|signInNames ([identità](manage-user-accounts-graph-api.md#identities-property)) |string|Nome di accesso univoco dell'utente dell'account locale di qualsiasi tipo nella directory. Utilizzare questa opzione per ottenere un utente con valore di accesso senza specificare il tipo di account locale.|No|No|Input|
|signInNames.userName ([identità](manage-user-accounts-graph-api.md#identities-property)) |string|Nome utente univoco dell'utente dell'account locale nella directory. Utilizzare questa opzione per creare o ottenere un utente con un nome utente di accesso specifico. Specificando questo valore in PersistedClaims solo durante l'operazione Patch verranno rimossi altri tipi di signInNames.Specifying this in PersistedClaims alone during Patch operation will remove other types of signInNames. Se si desidera aggiungere un nuovo tipo di signInNames, è inoltre necessario rendere persistenti i signInNames esistenti.|No|No|Input, Persistented, Output|
|signInNames.phoneNumber ([Identità](manage-user-accounts-graph-api.md#identities-property)) |string|Numero di telefono univoco dell'utente dell'account locale nella directory. Utilizzare questa opzione per creare o ottenere un utente con un numero di telefono di accesso specifico. Specificando questo valore in PersistedClaims solo durante l'operazione Patch verranno rimossi altri tipi di signInNames.Specifying this in PersistedClaims alone during Patch operation will remove other types of signInNames. Se si desidera aggiungere un nuovo tipo di signInNames, è inoltre necessario rendere persistenti i signInNames esistenti.|No|No|Input, Persistented, Output|
|signInNames.emailAddress ([identità](manage-user-accounts-graph-api.md#identities-property))|string|Indirizzo di posta elettronica univoco dell'utente dell'account locale nella directory. Usare questa opzione per creare o ottenere un utente con un indirizzo di posta elettronica di accesso specifico. Specificando questo valore in PersistedClaims solo durante l'operazione Patch verranno rimossi altri tipi di signInNames.Specifying this in PersistedClaims alone during Patch operation will remove other types of signInNames. Se si desidera aggiungere un nuovo tipo di signInNames, è inoltre necessario rendere persistenti i signInNames esistenti.|No|No|Input, Persistented, Output|
|state           |string|Stato dell'indirizzo dell'utente. Lunghezza massima 128.|Sì|Sì|Persisted, Output|
|streetAddress   |string|Indirizzo della sede di lavoro dell'utente. Lunghezza massima 1024.|Sì|Sì|Persisted, Output|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|string|Numero di telefono secondario dell'utente, utilizzato per l'autenticazione a più fattori.|Sì|No|Persisted, Output|
|StrongAuthenticationEmailAddress<sup>1</sup>|string|Indirizzo SMTP dell'utente. Esempio:bob@contoso.com" " Questo attributo viene utilizzato per l'accesso con i criteri nome utente, per archiviare l'indirizzo di posta elettronica dell'utente. Indirizzo di posta elettronica quindi utilizzato in un flusso di reimpostazione della password.|Sì|No|Persisted, Output|
|strongAuthenticationPhoneNumber<sup>1</sup>|string|Numero di telefono principale dell'utente, utilizzato per l'autenticazione a più fattori.|Sì|No|Persisted, Output|
|surname         |string|Cognome dell'utente. Lunghezza massima 64.|Sì|Sì|Persisted, Output|
|telephoneNumber (primo ingresso di businessPhones)|string|Numero di telefono principale della sede di lavoro dell'utente.|Sì|No|Persisted, Output|
|userPrincipalName    |string|Nome dell'entità utente (UPN) dell'utente. Il nome UPN è un nome di accesso in sintassi Internet per l'utente in base allo standard Internet RFC 822. Il dominio deve essere presente nella raccolta di domini verificati del tenant. Questa proprietà è obbligatoria quando viene creato un account. Non modificabili:|No|No|Input, Persistented, Output|
|usageLocation   |string|Proprietà obbligatoria per utenti a cui vengono assegnate licenze per requisiti legali dei quali servizi verificare la disponibilità nei paesi. Non ammette i valori NULL. Codice paese indicato con due lettere (standard ISO 3166). Esempi: "US", "JP" e "GB".|Sì|No|Persisted, Output|
|userType        |string|Valore stringa che può essere utilizzato per classificare i tipi di utente nella directory. Il valore deve essere Membro. Di sola lettura.|Sola lettura|No|Persisted, Output|
|userState (externalUserState)<sup>2</sup>|string|Solo per l'account B2B di Azure AD indica se l'invito è PendingAcceptance o Accepted.|No|No|Persisted, Output|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|Visualizza il timestamp per l'ultima modifica apportata alla proprietà UserState.|No|No|Persisted, Output|
|<sup>1 : il</sup> nome del Non supportato da Microsoft Graph<br><sup>2 Il</sup> nome del sistema Non deve essere usato con Azure AD B2C||||||


## <a name="extension-attributes"></a>Attributi di estensione

Spesso è necessario creare attributi personalizzati, come nei seguenti casi:

- Un'applicazione rivolta ai clienti deve essere mantenuta per un attributo come **LoyaltyNumber**.
- Un provider di identità ha un identificatore utente univoco che deve essere salvato, ad esempio **uniqueUserGUID**.
- Un percorso utente personalizzato deve essere mantenuto per uno stato di un utente, ad esempio **migrationStatus**.

Con Azure AD B2C è possibile estendere il set di attributi archiviati in ogni account utente. Gli attributi di [estensione estendono lo schema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) degli oggetti utente nella directory. Gli attributi di estensione possono essere registrati solo in un oggetto applicazione, anche se potrebbero contenere dati per un utente. L'attributo di estensione è associato all'applicazione denominata b2c-extensions-app. Non modificare questa applicazione, come viene usata da Azure AD B2C per l'archiviazione dei dati utente. È possibile trovare questa applicazione in Registrazioni app di Azure Active Directory.You can find this application under Azure Active Directory App registrations.

> [!NOTE]
> - È possibile scrivere fino a 100 attributi di estensione in qualsiasi account utente.
> - Se l'applicazione b2c-extensions-app viene eliminata, tali attributi di estensione vengono rimossi da tutti gli utenti insieme ai dati che contengono.
> - Se un attributo di estensione viene eliminato dall'applicazione, viene rimosso da tutti gli account utente e i valori vengono eliminati.
> - Il nome sottostante dell'attributo di estensione viene generato nel formato "Extension_" , id applicazione , ovvero "_" e nome dell'attributo. Ad esempio, se si crea un attributo di estensione LoyaltyNumber e l'ID applicazione b2c-extensions-app è 831374b3-bd50-41bf-aa54-263ec9e050fc, il nome dell'attributo di estensione sottostante sarà: extension_831374b3bd5041bfaa54263ec9e050fc_ Numero fedeltà. Utilizzare il nome sottostante quando si eseguono query dell'API Graph per creare o aggiornare gli account utente.

I tipi di dati seguenti sono supportati quando si definisce una proprietà in un'estensione dello schema:The following data types are supported when defining a property in a schema extension:

|Tipo di proprietà |Osservazioni  |
|--------------|---------|
|Boolean    | Valori possibili: **true** o **false**. |
|Datetime   | Deve essere specificato nel formato ISO 8601. Verrà archiviato in formato UTC.   |
|Integer    | Valore a 32 bit.               |
|string     | 256 caratteri al massimo.     |

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sugli attributi di estensione:
- [Estensioni dello schema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definire attributi personalizzati con il flusso utenteDefine custom attributes with user flow](user-flow-custom-attributes.md)
- [Definire attributi personalizzati con criteri personalizzatiDefine custom attributes with custom policy](custom-policy-custom-attributes.md)

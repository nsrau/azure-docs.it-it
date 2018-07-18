---
title: Gestire l'accesso utente in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come identificare minorenni, raccogliere dati su paese e data di nascita e ottenere l'accettazione delle condizioni di uso nell'applicazione con Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/04/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 52cf34d56f87d2543fb272ce99dbe011bc0ea037
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34711128"
---
# <a name="manage-user-access-in-azure-ad-b2c"></a>Gestire l'accesso utente in Azure AD B2C

Questo articolo contiene informazioni su come è possibile gestire l'accesso utente per le applicazioni con Azure Active Directory (AD) B2C. La gestione dell'accesso nell'applicazione include le operazioni seguenti:

- Identificazione dei minorenni e controllo dell'accesso per l'uso dell'applicazione
- Richiesta del consenso dei genitori per l'uso delle applicazioni da parte dei minorenni
- Raccolta dei dati su paese e data di nascita
- Acquisizione del contratto per le condizioni per l'utilizzo e controllo dell'accesso

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

>[!Note] 
>Questo articolo contiene informazioni che possono essere usate per supportare i requisiti nell'ambito del Regolamento generale sulla protezione dei dati (GDPR). Per informazioni generali sul GDPR, vedere la [sezione di Service Trust Portal dedicata al GDPR](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="control-minor-access"></a>Controllare l'accesso dei minorenni

Le applicazioni e le organizzazioni possono decidere di impedire ai minorenni l'uso di applicazioni e servizi non destinati a questi destinatari. In alternativa, le applicazioni e le organizzazioni possono decidere di accettare i minorenni e successivamente di gestire il consenso dei genitori e offrire esperienze consentite ai minorenni, come indicato dalle regole business e permesso dalle normative. 

Se un utente viene identificato come un minorenne, il flusso utente in Azure AD B2C può essere impostato su una delle tre opzioni seguenti:

- **Invio di un token JWT id_token firmato all'applicazione** - L'utente viene registrato nella directory e un token viene restituito all'applicazione. L'applicazione quindi procede usando le regole business. Ad esempio, l'applicazione può continuare con un processo di consenso dei genitori. A tale scopo, è possibile scegliere di ricevere le attestazioni **ageGroup** e **consentProvidedForMinor** dall'applicazione.
- **invio di un token JSON non firmato all'applicazione** - Azure AD B2C comunica all'applicazione che l'utente è un minorenne e invia lo stato del consenso dei genitori dell'utente. L'applicazione quindi procede usando le regole business. Se un token JSON non completa l'autenticazione in modo corretto, l'applicazione deve elaborare l'utente non autenticato in base alle attestazioni incluse nel token JSON, che possono includere **name**, **email**, **ageGroup**, and **consentProvidedForMinor**.
- **Blocco dell'utente** - Se l'utente è un minorenne e il consenso dei genitori non è stato concesso.  Azure AD B2C può presentare una schermata all'utente che lo informa del blocco.  Non verrà emesso alcun token, l'accesso è bloccato e durante un processo di registrazione non verrà creato alcun account utente. Per implementare questo scenario, è necessario presentare una pagina di contenuto HTML/CSS adatta per informare l'utente e visualizzare le opzioni appropriate. Per le nuove registrazioni non sono necessarie altre azioni da parte dell'applicazione.

## <a name="get-parental-consent"></a>Ottenere il consenso dei genitori

A seconda delle normative che l'applicazione deve rispettare, potrebbe essere necessario che il consenso dei genitori venga concesso da un utente verificato come adulto.  Azure Active Directory B2C non offre un'esperienza per verificare l'età degli individui né per consentire a un adulto verificato di concedere il consenso dei genitori a un minorenne.  Questa esperienza deve essere offerta dall'applicazione o da un altro provider di servizi.

Di seguito è riportato un esempio di un flusso utente per l'acquisizione del consenso dei genitori:

1. Un'operazione [API Graph di Azure Active Directory](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) identifica l'utente come un minorenne e restituisce i dati dell'utente all'applicazione in forma di un token JSON non firmato.
2. L'applicazione elabora il token JSON e mostra una schermata per comunicare al minorenne che è necessario il consenso dei genitori e che richiede il consenso di un genitore online. 
3. Azure AD B2C visualizza una procedura di accesso tramite cui l'utente può accedere normalmente ed emette un token per l'applicazione impostato per includere **legalAgeGroupClassification = "minorWithParentalConsent"** L'applicazione raccoglie l'indirizzo di posta elettronica del genitore e verifica che il genitore sia un adulto tramite una fonte attendibile, ad esempio carta di identità, patente o carta di credito. In caso affermativo, l'applicazione richiede al minorenne di eseguire l'accesso tramite il flusso utente di Azure AD B2C. Se il consenso è stato negato, ovvero se **legalAgeGroupClassification = "minorWithoutParentalConsent"**, Azure AD B2C restituisce un token JSON (non un account di accesso) all'applicazione per riavviare il processo di concessione del consenso. È facoltativamente possibile personalizzare il flusso utente in base al quale un minorenne o un adulto può ottenere nuovamente l'accesso all'account del minorenne inviando un codice di registrazione ufficiale all'indirizzo di posta elettronica del minorenne o a quello dell'adulto.
4. L'applicazione offre un'opzione al minorenne per revocare il consenso.
5. Quando il minorenne o l'adulto revoca il consenso, l'API Graph di Azure AD consente di impostare **consetProvidedForMinor** su **denied**. In alternativa, l'applicazione può scegliere di eliminare un minorenne per cui il consenso è stato revocato. È facoltativamente possibile personalizzare il flusso utente in base al quale il minorenne autenticato (o il genitore che ne usa l'account) può revocare il consenso. Azure AD B2C registra **consentProvidedForMinor** come **denied**.

Per altre informazioni su **legalAgeGroupClassification**, **consentProvidedForMinor** e **ageGroup**, vedere [User Resource Type](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/user) (Tipo di risorsa utente). Per altre informazioni sugli attributi personalizzati, vedere [Usare attributi personalizzati per raccogliere informazioni sugli utenti](active-directory-b2c-reference-custom-attr.md). Quando si indirizzano attributi estesi tramite l'API Graph di Azure AD, la versione estesa dell'attributo deve essere usata come "extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth": "2011-01-01T00:00:00Z"

## <a name="gather-date-of-birth-and-country-data"></a>Raccogliere dati sul paese e la data di nascita

Le applicazioni può basarsi su Azure AD B2C per raccogliere dati sul paese e sulla data di nascita da tutti gli utenti durante la registrazione. Se le informazioni sul paese e sulla data di nascita non esistono ancora, possono essere richieste all'utente durante la fase di autenticazione (accesso) successiva. Gli utenti non saranno in grado di procedere senza indicare le informazioni sul paese e sulla data di nascita. In base al paese e alla data di nascita indicati, Azure AD B2C determina se l'utente è considerato un minorenne secondo gli standard normativi del paese. 

Un flusso di utente personalizzato consente di raccogliere le informazioni sul paese e la data di nascita e di usare le trasformazioni di attestazione di Azure AD B2C per determinare l'elemento **ageGroup** e rendere persistente il risultato o le informazioni sul paese e la data di nascita nella directory.

I passaggi seguenti mostrano la logica che viene usata per calcolare **ageGroup** a partire dalla data di nascita:

1. Provare a trovare il paese in base al relativo codice nell'elenco. Se il paese non è presente, tornare a **Default**.
2. Se il nodo **MinorConsent** è presente nell'elemento relativo al paese:  <br>a. Calcolare la data minima in cui sarebbe stato necessario che l'utente fosse nato per essere considerato un adulto. Esempio: se la data di nascita è 14/3/2015 e **MinorConsent** è 18, la data di nascita minima deve essere 14/3/2000.
    <br>b. Confrontare la data di nascita minima con la data di nascita effettiva. Se la data di nascita minima precede la data di nascita dell'utente, il calcolo restituisce **Minor** come calcolo della fascia d'età.
3. Se il nodo **MinorNoConsentRequired** è presente nell'elemento relativo al paese, ripetere i passaggi 2a e 2b usano il **MinorNoConsentRequired**. Il risultato di 2b restituisce **MinorNoConsentRequired** se la data di nascita minima precede la data di nascita dell'utente. 
4. Se nessuno dei due calcoli ha restituito true, il calcolo restituisce **Adult**.

Se un'applicazione ha raccolto in modo affidabile i dati sul paese e la data di nascita tramite altri metodi, l'applicazione può usare l'API GRAPH per aggiornare il record utente con queste informazioni. Ad esempio: 

- Se è noto che l'utente è un adulto, aggiornare l'attributo directory **ageGroup** con il valore **Adult**.
- Se è noto che l'utente è un minorenne, aggiornare l'attributo directory **ageGroup** con il valore **Minorenne** e impostare **consentProvidedForMinor** in base alle esigenze.

Per altre informazioni sulla raccolta dei dati sulla data di nascita, vedere [Utilizzo del controllo dell'accesso in base all'età in Azure Active Directory B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Acquisire il contratto per le condizioni per l'utilizzo

Quando si sviluppa l'applicazione, in genere si acquisisce l'accettazione delle condizioni per l'utilizzo da parte dell'utente nelle applicazioni, senza alcuna partecipazione o con una partecipazione minore dalla directory dell'utente.  È possibile, tuttavia, usare un flusso utente di Azure AD B2C per acquisire il contratto delle condizioni per l'utilizzo, limitare l'accesso a meno che non venga concessa l'accettazione e imporre l'accettazione delle modifiche future alle condizioni per l'utilizzo in base alla data dell'ultima accettazione e a quella dell'ultima versione delle condizioni per l'utilizzo.

Le **Condizioni per l'utilizzo** possono anche includere la dicitura per acconsentire a condividere i dati con terze parti.  L'accettazione di queste condizioni da parte di un utente può essere acquisita tecnicamente insieme oppure l'utente può essere in grado di accettare una condizione e non l'altra in base alle regole business e alle normative locali.

I passaggi seguenti descrivono le funzionalità per la gestione delle condizioni per l'utilizzo:

1. Registrare l'accettazione delle condizioni per l'utilizzo e la data di accettazione tramite l'API Graph e gli attributi estesi. Questa operazione può essere eseguita tramite flussi utente predefiniti e personalizzati. Si consiglia di creare e usare gli attributi **extension_termsOfUseConsentDateTime** e **extension_termsOfUseConsentVersion**.
2. Creare una casella di controllo obbligatoria denominata "Accettare le Condizioni per l'utilizzo" e registrare il risultato durante l'iscrizione. Questa operazione può essere eseguita tramite flussi utente predefiniti e personalizzati.
3. Azure AD B2C archivia il contratto delle condizioni per l'utilizzo e il consenso. È possibile usare l'API Graph per eseguire una query sullo stato di qualsiasi utente leggendo l'attributo di estensione usato per registrare la risposta, ad esempio **termsOfUseTestUpdateDateTime**. Questa operazione può essere eseguita tramite flussi utente predefiniti e personalizzati.
4. Richiedere l'accettazione delle condizioni per l'utilizzo aggiornate confrontando la data di accettazione a quella dell'ultima versione delle condizioni per l'utilizzo. Questa operazione può essere eseguita solo usando un flusso utente personalizzato. Usare l'attributo esteso **extension_termsOfUseConsentDateTime** e confrontare il valore con l'attestazione di **termsOfUseTextUpdateDateTime**. Se l'accettazione è precedente, forzare una nuova schermata di accettazione autodichiarata, in caso contrario bloccare l'accesso usando la logica dei criteri.
5. Richiedere l'accettazione delle condizioni per l'utilizzo aggiornate confrontando il numero di versione dell'accettazione con quello dell'ultima versione accettata. Questa operazione può essere eseguita solo usando un flusso utente personalizzato. Usare l'attributo esteso **extension_termsOfUseConsentDateTime** e confrontare il valore con l'attestazione di **extension_termsOfUseConsentVersion**. Se l'accettazione è precedente, forzare una nuova schermata di accettazione autodichiarata, in caso contrario bloccare l'accesso usando la logica dei criteri.

L'acquisizione del consenso delle condizioni per l'utilizzo può essere visualizzata all'utente nelle situazioni seguenti:

- Un nuovo esegue l'iscrizione. Le condizioni per l'utilizzo vengono visualizzate e il risultato del consenso viene archiviato.
- Un utente accede e in precedenza ha già accettato il consenso per i termini del contratto più recenti o attivi. Le condizioni per l'utilizzo non vengono visualizzate.
- Un utente accede e non ancora accettato le condizioni per l'utilizzo attive o più recenti. Le condizioni per l'utilizzo vengono visualizzate e il risultato del consenso viene archiviato.
- Un utente accede e ha già accettato le condizioni per l'utilizzo meno recenti, attualmente aggiornate a una versione più recente. Le condizioni per l'utilizzo vengono visualizzate e il risultato del consenso viene archiviato.

La figura seguente illustra il flusso utente consigliato:

![flusso di accettazione utente](./media/manage-user-access/user-flow.png) 

Di seguito viene riportato un esempio di consenso a condizioni per l'utilizzo in base all'elemento DateTime in un'attestazione:

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Di seguito viene riportato un esempio di consenso a condizioni per l'utilizzo in base all'elemento Version in un'attestazione:

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations> 
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eliminare ed esportare i dati utente in [Gestire i dati utente](manage-user-data.md)

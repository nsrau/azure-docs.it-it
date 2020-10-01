---
title: Durata del token configurabile
titleSuffix: Microsoft identity platform
description: Informazioni su come impostare la durata dei token emessi dalla piattaforma di identità Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: a9bf992a65914afb8fa800041b57ad9f44ba4fa0
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595607"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Durata del token configurabile nella piattaforma di identità Microsoft (anteprima)

È possibile specificare la durata di un token emesso dalla piattaforma di identità Microsoft. La durata dei token può essere impostata per tutte le app di un'organizzazione, per un'applicazione multi-tenant (più organizzazioni) o per un'entità servizio specifica in un'organizzazione. Attualmente, tuttavia, non è supportata la configurazione delle durate dei token per le [entità servizio gestite di identità](../managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Dopo aver ascoltato i clienti durante l'anteprima, sono state implementate le [funzionalità di gestione delle sessioni di autenticazione](../conditional-access/howto-conditional-access-session-lifetime.md) in Azure ad l'accesso condizionale. È possibile usare questa nuova funzionalità per configurare la durata dei token di aggiornamento impostando la frequenza di accesso. Dopo il 30 maggio 2020 nessun nuovo tenant sarà in grado di usare i criteri di durata dei token configurabili per configurare i token di sessione e di aggiornamento. La deprecazione avverrà entro diversi mesi, il che significa che non verranno rispettati i criteri di sessione e di aggiornamento dei token esistenti. È comunque possibile configurare la durata dei token di accesso dopo la deprecazione.

In Azure AD, un oggetto criteri rappresenta un set di regole applicate a singole applicazioni o a tutte le applicazioni di un'organizzazione. Ogni tipo di criteri ha una struttura univoca con un set di proprietà che vengono applicate agli oggetti a cui sono assegnate.

È possibile designare un oggetto criteri come predefinito per l'organizzazione. I criteri predefiniti vengono applicati a tutte le applicazioni all'interno dell'organizzazione, fino a quando non vengono sostituiti da criteri con priorità più alta. È anche possibile assegnare criteri ad applicazioni specifiche. L'ordine di priorità varia in base al tipo di criteri.

Per esempi, vedere [esempi di come configurare la durata dei token](configure-token-lifetimes.md).

> [!NOTE]
> I criteri di durata dei token configurabili si applicano solo ai client desktop e per dispositivi mobili che accedono alle risorse di SharePoint Online e OneDrive for business e non si applicano alle sessioni del browser Web.
> Per gestire la durata delle sessioni del browser Web per SharePoint Online e OneDrive for business, usare la funzionalità [Durata sessione di accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md) . Fare riferimento ai [blog di SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) per altre informazioni sulla configurazione dei timeout di sessione inattiva.

## <a name="token-types"></a>Tipi di token

È possibile impostare i criteri di durata dei token per i token di aggiornamento, i token di accesso, i token SAML, i token di sessione e i token ID.

### <a name="access-tokens"></a>Token di accesso

I client usano i token di accesso per accedere a una risorsa protetta. I token di accesso possono essere usati solo per una combinazione specifica di utente, client e risorsa. Non è possibile revocare i token di accesso, che rimangono validi fino alla scadenza. Un attore malintenzionato può usare un eventuale token di accesso ottenuto per la sua intera durata. Regolare la durata dei token di accesso significa trovare un compromesso tra il miglioramento delle prestazioni di sistema e l'aumento dell'intervallo di tempo durante il quale il client conserva l'accesso dopo che l'account dell'utente è stato disabilitato. Il miglioramento delle prestazioni di sistema si ottiene riducendo il numero delle volte in cui un client deve acquisire un token di accesso aggiornato.  Il valore predefinito è un'ora. Dopo un'ora, il client deve usare il token di aggiornamento per acquisire un nuovo token di aggiornamento e un nuovo token di accesso (in genere in modo automatico). 

### <a name="saml-tokens"></a>Token SAML

I token SAML vengono utilizzati da numerose applicazioni SAAS basate sul Web e vengono ottenuti utilizzando l'endpoint del protocollo SAML2 di Azure Active Directory. Vengono inoltre utilizzate da applicazioni che utilizzano WS-Federation. La durata predefinita del token è 1 ora. Dal punto di vista di un'applicazione, il periodo di validità del token viene specificato dal valore NotOnOrAfter dell' `<conditions …>` elemento nel token. Al termine del periodo di validità del token, il client deve avviare una nuova richiesta di autenticazione, che verrà spesso soddisfatta senza l'accesso interattivo come risultato del token di sessione Single Sign-on (SSO).

Il valore di NotOnOrAfter può essere modificato utilizzando il `AccessTokenLifetime` parametro in un oggetto `TokenLifetimePolicy` . Verrà impostato sulla durata configurata nel criterio, se presente, più un fattore di sfasamento di clock di cinque minuti.

Il NotOnOrAfter di conferma dell'oggetto specificato nell' `<SubjectConfirmationData>` elemento non è influenzato dalla configurazione della durata del token. 

### <a name="refresh-tokens"></a>Token di aggiornamento

Quando un client acquisisce un token di accesso per accedere a una risorsa protetta, riceve anche un token di aggiornamento. Il token di aggiornamento consente di ottenere nuove coppie di token di accesso/aggiornamento alla scadenza del token di accesso attuale. Il token di aggiornamento è associato a una combinazione di utente e client e può essere [revocato in qualsiasi momento](access-tokens.md#token-revocation). La validità del token di aggiornamento viene quindi controllata ogni volta che viene usato.  I token di aggiornamento non sono revocati quando vengono usati per recuperare nuovi token di accesso. La procedura consigliata consiste, tuttavia, nell'eliminare in modo sicuro il token obsoleto quando se ne ottiene uno nuovo. 

È importante distinguere tra client riservati e client pubblici, perché questa caratteristica influisce sulla durata del periodo in cui i token di aggiornamento possono essere usati. Per altre informazioni sui diversi tipi di client, vedere la specifica [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Durata dei token con token di aggiornamento per client riservati
I client riservati sono applicazioni che possono archiviare in modo sicuro una password client, ovvero un segreto, e possono dimostrare che le richieste provengono dall'applicazione client protetta e non da un attore malintenzionato. Un'app Web, ad esempio, è un client riservato perché può archiviare un segreto client nel server Web e non viene esposto. I token di aggiornamento rilasciati per questi flussi più sicuri hanno una durata `until-revoked`, che non può essere modificata tramite i criteri e non può essere revocata con reimpostazioni di password volontarie.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Durata dei token con token di aggiornamento per client pubblici

I client pubblici non possono archiviare in modo sicuro una password client, ovvero un segreto. Un'app iOS o Android, ad esempio, non può offuscare un segreto dal proprietario della risorsa e per questo è considerata un client pubblico. È possibile impostare criteri sulle risorse per evitare che i token di aggiornamento di client pubblici precedenti a un periodo specificato ottengano una nuova coppia di token di accesso/aggiornamento. A tale scopo, usare la [Proprietà tempo inattività massimo token di aggiornamento](#refresh-token-max-inactive-time) ( `MaxInactiveTime` ). È anche possibile usare i criteri per impostare un intervallo di tempo oltre il quale i token di aggiornamento non vengono più accettati. A tale scopo, usare la proprietà validità [massima token di aggiornamento a fattore singolo](#single-factor-session-token-max-age) o validità [massima token di sessione](#multi-factor-refresh-token-max-age) a più fattori. È possibile regolare la durata dei token di aggiornamento per controllare quando e con quale frequenza viene richiesto all'utente di immettere di nuovo le credenziali durante l'uso di un'applicazione client pubblica, anziché rieseguirne automaticamente l'autenticazione.

> [!NOTE]
> La proprietà validità massima indica il periodo di tempo in cui è possibile usare un singolo token. 

### <a name="id-tokens"></a>Token ID
I token ID vengono passati a siti Web e client nativi e contengono informazioni relative al profilo di un utente. Ogni token ID è associato a una combinazione specifica di utente e client ed è considerato valido fino alla relativa scadenza. La durata della sessione di un utente in un'applicazione Web corrisponde in genere alla durata del token ID rilasciato per l'utente. È possibile regolare la durata di un token ID per controllare la frequenza con cui l'applicazione Web scade la sessione dell'applicazione e la frequenza con cui l'utente deve essere riautenticato con la piattaforma di identità Microsoft (in modo invisibile all'utente).

### <a name="single-sign-on-session-tokens"></a>Token di sessione Single Sign-On
Quando un utente esegue l'autenticazione con Microsoft Identity Platform, viene stabilita una sessione di Single Sign-On (SSO) con il browser dell'utente e la piattaforma delle identità Microsoft. Questa sessione è rappresentata dal token di sessione SSO, sotto forma di cookie. Il token di sessione SSO non è associato a una risorsa/applicazione client specifica. I token di sessione SSO possono essere revocati e la relativa validità viene verificata ogni volta che vengono usati.

Microsoft Identity Platform usa due tipi di token di sessione SSO, ovvero permanenti e non permanenti. I token di sessione permanenti vengono archiviati dal browser come cookie permanenti. I token di sessione non permanenti vengono archiviati come cookie di sessione I cookie di sessione vengono eliminati quando il browser viene chiuso. In genere, viene archiviato un token di sessione non permanente. Ma quando durante l'autenticazione l'utente seleziona la casella di controllo **Mantieni l'accesso**, viene archiviato un token di sessione permanente.

I token di sessione non permanenti hanno una durata di 24 ore, I token permanenti hanno una durata di 90 giorni. Ogni volta che un token di sessione SSO viene usato entro il relativo periodo di validità, il periodo di validità viene esteso per altre 24 ore o 90 giorni, a seconda del tipo di token. Se un token di sessione SSO non viene usato entro il relativo periodo di validità, è considerato scaduto e non viene più accettato.

È possibile usare criteri per impostare il periodo di tempo dopo il rilascio del primo token di sessione oltre il quale tale token non viene più accettato. A tale scopo, usare la proprietà validità massima token di sessione. È possibile regolare la durata di un token di sessione per controllare quando e con quale frequenza viene richiesto a un utente di immettere nuovamente le credenziali, anziché essere autenticate automaticamente quando si usa un'applicazione Web.

### <a name="token-lifetime-policy-properties"></a>Proprietà dei criteri per la durata dei token
I criteri per la durata dei token rappresentano un tipo di oggetto criteri contenente le regole di durata dei token. Usare le proprietà dei criteri per controllare la durata di token specifici. Se non si impostano criteri, il valore di durata predefinito viene applicato dal sistema.

### <a name="configurable-token-lifetime-properties"></a>Proprietà configurabili per la durata dei token
| Proprietà | Stringa proprietà criteri | Impatto | Predefinito | Minima | Massimo |
| --- | --- | --- | --- | --- | --- |
| Durata dei token di accesso |AccessTokenLifetime<sup>2</sup> |Token di accesso, token ID, token SAML2 |1 ora |10 minuti |1 giorno |
| Tempo inattività massimo token di aggiornamento |MaxInactiveTime |Token di aggiornamento |90 giorni |10 minuti |90 giorni |
| Validità massima token di aggiornamento a fattore singolo |MaxAgeSingleFactor |Token di aggiornamento (per tutti gli utenti) |Fino a revoca |10 minuti |Fino alla revoca<sup>1</sup> |
| Validità massima token di aggiornamento a più fattori |MaxAgeMultiFactor |Token di aggiornamento (per tutti gli utenti) | 180 giorni |10 minuti |Fino alla revoca<sup>1</sup> |
| Validità massima token di sessione a fattore singolo |MaxAgeSessionSingleFactor |Token di sessione (permanenti e non permanenti) |Fino a revoca |10 minuti |Fino alla revoca<sup>1</sup> |
| Validità massima token di sessione a più fattori |MaxAgeSessionMultiFactor |Token di sessione (permanenti e non permanenti) | 180 giorni |10 minuti |Fino alla revoca<sup>1</sup> |

* <sup>1</sup> La durata esplicita massima che è possibile impostare per questi attributi è 365 giorni.
* <sup>2</sup> Per assicurarsi che il client Web Microsoft teams funzioni, è consigliabile mantenere AccessTokenLifetime a più di 15 minuti per Microsoft teams.

### <a name="exceptions"></a>Eccezioni
| Proprietà | Impatto | Predefinito |
| --- | --- | --- |
| Validità massima dei token di aggiornamento (rilasciati a utenti federati con informazioni sulla revoca insufficienti<sup>1</sup>) |Token di aggiornamento (rilasciati a utenti federati con informazioni sulla revoca insufficienti<sup>1</sup>) |12 ore |
| Tempo inattività massimo token di aggiornamento (rilasciata a client riservati) |Token di aggiornamento (rilasciati a client riservati) |90 giorni |
| Validità massima token di aggiornamento (rilasciata a client riservati) |Token di aggiornamento (rilasciati a client riservati) |Fino a revoca |

* <sup>1</sup> gli utenti federati con informazioni di revoca insufficienti includono tutti gli utenti che non dispongono dell'attributo "LastPasswordChangeTimestamp" sincronizzato. A questi utenti viene assegnata questa durata massima breve, perché Azure Active Directory non è in grado di verificare quando revocare i token legati a una credenziale precedente, ad esempio una password modificata, ed è necessario ricontrollarli più spesso per assicurarsi che l'utente e i token associati siano ancora in esecuzione. Per migliorare questa esperienza, gli amministratori tenant devono assicurarsi che stiano sincronizzando l'attributo "LastPasswordChangeTimestamp" (può essere impostato sull'oggetto utente usando PowerShell o AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Definizione della priorità e valutazione dei criteri
È possibile creare e quindi assegnare criteri per la durata dei token a un'applicazione specifica, all'organizzazione e alle entità servizio. A un'applicazione specifica si possono applicare più criteri. Di seguito sono riportate le regole su cui si basano i criteri per la durata dei token validi:

* Se i criteri sono assegnati in modo esplicito all'entità servizio, vengono applicati.
* Se all'entità servizio non sono assegnati criteri in modo esplicito, vengono applicati i criteri assegnati in modo esplicito all'elemento organizzazione padre dell'entità servizio.
* Se all'entità servizio o all'organizzazione non sono assegnati criteri in modo esplicito, vengono applicati i criteri assegnati all'applicazione.
* Se all'entità servizio, all'organizzazione o all'oggetto applicazione non sono stati assegnati criteri, verranno applicati i valori predefiniti. Vedere in proposito la tabella in [Proprietà configurabili per la durata dei token](#configurable-token-lifetime-properties).

Per altre informazioni sulla relazione tra oggetti applicazione e oggetti entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](app-objects-and-service-principals.md).

La validità di un token viene valutata quando questo viene usato. Vengono applicati i criteri con la priorità più alta per l'applicazione a cui si accede.

Tutti gli intervalli di tempo usati qui sono formattati in base all'oggetto[TimeSpan](/dotnet/api/system.timespan) C# - D.HH:MM:SS.  Quindi 80 giorni e 30 minuti sarebbe `80.00:30:00`.  La D iniziale può essere eliminata se è zero, pertanto 90 minuti sarebbe `00:90:00`.  

> [!NOTE]
> Di seguito è riportato uno scenario di esempio.
>
> Un utente vuole accedere a due applicazioni Web: l'applicazione Web A e l'applicazione Web B.
> 
> Fattori:
> * Entrambe le applicazioni Web si trovano nello stesso elemento organizzazione padre.
> * I criteri per la durata dei token 1 con una validità massima del token di sessione di otto ore vengono impostati come valore predefinito per l'elemento organizzazione padre.
> * L'applicazione Web A è di tipo normale e non è collegata a criteri.
> * L'applicazione Web B viene usata per processi riservati. La relativa entità servizio è collegata ai criteri per la durata dei token 2, che hanno una validità massima del token di sessione di 30 minuti.
>
> Alle ore 12:00, l'utente avvia una nuova sessione del browser e tenta di accedere all'applicazione Web A. L'utente viene reindirizzato alla piattaforma di identità Microsoft e viene richiesto di effettuare l'accesso. Nel browser viene creato un cookie con un token di sessione. L'utente viene di nuovo reindirizzato all'applicazione Web A con un token ID che gli consente di accedere all'applicazione.
>
> Alle 12:15, l'utente tenta di accedere all'applicazione Web B. Il browser reindirizza a Microsoft Identity Platform, che rileva il cookie di sessione. L'entità servizio dell'applicazione Web B è collegata ai criteri per la durata dei token 2, ma è anche parte dell'elemento organizzazione padre associato ai criteri per la durata dei token 1 predefiniti. Vengono applicati i criteri per la durata dei token 2 perché i criteri collegati alle entità servizio hanno una priorità maggiore rispetto a quelli predefiniti dell'organizzazione. Il token di sessione è stato rilasciato negli ultimi 30 minuti, quindi è considerato valido. L'utente viene di nuovo reindirizzato all'applicazione Web B con un token ID che consente l'accesso.
>
> Alle ore 1:00, l'utente tenta di accedere all'applicazione Web A. L'utente viene reindirizzato alla piattaforma di identità Microsoft. L'applicazione Web A non è collegata a criteri, ma si trova in un'organizzazione con criteri per la durata dei token 1 predefiniti. Vengono quindi applicati tali criteri. Viene rilevato il cookie di sessione rilasciato nelle ultime otto ore. L'utente viene reindirizzato automaticamente all'applicazione Web A con un nuovo token ID. Non è necessario che l'utente esegua l'autenticazione.
>
> Immediatamente dopo, l'utente tenta di accedere all'applicazione Web B. L'utente viene reindirizzato alla piattaforma di identità Microsoft. Come prima, vengono applicati i criteri per la durata dei token 2. Dato che il token è stato rilasciato da oltre 30 minuti, all'utente viene richiesto di immettere nuovamente le credenziali di accesso. Vengono rilasciati un nuovo token di sessione e un nuovo token ID. L'utente può quindi accedere all'applicazione Web B.
>
>

## <a name="configurable-policy-property-details"></a>Dettagli sulla proprietà dei criteri configurabili
### <a name="access-token-lifetime"></a>Durata dei token di accesso
**Stringa:** AccessTokenLifetime

**Effetti:** Token di accesso, token ID, token SAML

**Riepilogo:** questo tipo di criteri controlla per quanto tempo i token di accesso e ID della risorsa sono considerati validi. Riducendo la proprietà Durata dei token di accesso è possibile ridurre il rischio che un token di accesso o un token ID vengano usati da un attore malintenzionato per un lungo periodo di tempo. (Questi token non possono essere revocati). Il compromesso è che le prestazioni sono compromesse, perché i token devono essere sostituiti più spesso.

Per un esempio, vedere [creare criteri per l'accesso Web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

### <a name="refresh-token-max-inactive-time"></a>Tempo inattività massimo token di aggiornamento
**Stringa:** MaxInactiveTime

**Impatto:** token di aggiornamento

**Riepilogo:** questo tipo di criteri controlla dopo quanto tempo dall'emissione un token di aggiornamento non potrà più essere usato da un client per il recupero di una nuova coppia di token di accesso/aggiornamento quando tenterà di accedere alla risorsa. Dal momento che quando viene usato un token di aggiornamento ne viene in genere restituito uno nuovo, questi criteri impediscono l'accesso se il client prova ad accedere a qualsiasi risorsa con il token di aggiornamento corrente durante il periodo di tempo specificato.

Questi criteri impongono agli utenti che non hanno svolto attività sul client nel tempo specificato di eseguire di nuovo l'autenticazione per recuperare un nuovo token di aggiornamento.

La proprietà Tempo inattività massimo token di aggiornamento deve essere impostata su un valore inferiore rispetto alle proprietà Validità massima token a fattore singolo e Validità massima token di aggiornamento a più fattori.

Per un esempio, vedere [creare un criterio per un'app nativa che chiama un'API Web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-refresh-token-max-age"></a>Validità massima token di aggiornamento a fattore singolo
**String:** MaxAgeSingleFactor

**Impatto:** token di aggiornamento

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può usare un token di aggiornamento per ottenere una nuova coppia di token di accesso/aggiornamento dopo l'ultima autenticazione a fattore singolo. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di aggiornamento, l'utente può usare il flusso del token di aggiornamento per il periodo di tempo specificato. (Questo è vero purché il token di aggiornamento corrente non venga revocato e non viene lasciato inutilizzato per un periodo di tempo maggiore del tempo di inattività). A questo punto, l'utente deve eseguire di nuovo l'autenticazione per ricevere un nuovo token di aggiornamento.

Riducendo la validità massima, gli utenti devono eseguire l'autenticazione più spesso. Dal momento che l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare questa proprietà su un valore minore o uguale a quello della proprietà Validità massima token di aggiornamento a più fattori.

Per un esempio, vedere [creare un criterio per un'app nativa che chiama un'API Web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="multi-factor-refresh-token-max-age"></a>Validità massima token di aggiornamento a più fattori
**Strings:** MaxAgeMultiFactor

**Impatto:** token di aggiornamento

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può usare un token di aggiornamento per ottenere una nuova coppia di token di accesso/aggiornamento dopo l'ultima autenticazione a più fattori. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di aggiornamento, l'utente può usare il flusso del token di aggiornamento per il periodo di tempo specificato. (Questo è vero purché il token di aggiornamento corrente non venga revocato e non è inutilizzato per un periodo più lungo rispetto al tempo di inattività). A questo punto, gli utenti devono eseguire di nuovo l'autenticazione per ricevere un nuovo token di aggiornamento.

Riducendo la validità massima, gli utenti devono eseguire l'autenticazione più spesso. Dal momento che l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare questa proprietà su un valore maggiore o uguale a quello della proprietà Validità massima token di aggiornamento a fattore singolo.

Per un esempio, vedere [creare un criterio per un'app nativa che chiama un'API Web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-session-token-max-age"></a>Validità massima token di sessione a fattore singolo
**Stringa** MaxAgeSessionSingleFactor

**Impatto:** token di sessione (permanenti e non permanenti)

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può usare un token di sessione per ottenere un nuovo token ID e un nuovo token di sessione dopo l'ultima autenticazione a fattore singolo. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di sessione, l'utente può usare il flusso del token di sessione per il periodo di tempo specificato. (Questo valore è true purché il token di sessione corrente non venga revocato e non sia scaduto). Dopo il periodo di tempo specificato, l'utente deve eseguire di nuovo l'autenticazione per ricevere un nuovo token di sessione.

Riducendo la validità massima, gli utenti devono eseguire l'autenticazione più spesso. Dal momento che l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare questa proprietà su un valore minore o uguale a quello della proprietà Validità massima token di sessione a più fattori.

Per un esempio, vedere [creare criteri per l'accesso Web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

### <a name="multi-factor-session-token-max-age"></a>Validità massima token di sessione a più fattori
**Stringa:** MaxAgeSessionMultiFactor

**Impatto:** token di sessione (permanenti e non permanenti)

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può usare un token di sessione per ottenere un nuovo token ID e un nuovo token di sessione dopo l'ultima autenticazione a più fattori. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di sessione, l'utente può usare il flusso del token di sessione per il periodo di tempo specificato. (Questo valore è true purché il token di sessione corrente non venga revocato e non sia scaduto). Dopo il periodo di tempo specificato, l'utente deve eseguire di nuovo l'autenticazione per ricevere un nuovo token di sessione.

Riducendo la validità massima, gli utenti devono eseguire l'autenticazione più spesso. Dal momento che l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare questa proprietà su un valore maggiore o uguale a quello della proprietà Validità massima token di sessione a fattore singolo.

## <a name="cmdlet-reference"></a>Informazioni di riferimento sui cmdlet

Questi sono i cmdlet disponibili nel [modulo Azure Active Directory PowerShell per l'anteprima Graph](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true).

### <a name="manage-policies"></a>Gestire i criteri

È possibile usare i cmdlet riportati di seguito per gestire i criteri.

| Cmdlet | Descrizione | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Crea nuovi criteri. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Ottiene tutti i criteri di Azure AD o i criteri specificati. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Ottiene tutte le app e le entità servizio collegate a criteri specifici. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Aggiorna i criteri esistenti. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Elimina i criteri specificati. |

### <a name="application-policies"></a>Criteri dell'applicazione
È possibile usare i cmdlet riportati di seguito per i criteri dell'applicazione.</br></br>

| Cmdlet | Descrizione | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Collega i criteri specificati a un'applicazione. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Ottiene i criteri assegnati a un'applicazione. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Rimuove i criteri da un'applicazione. |

### <a name="service-principal-policies"></a>Criteri dell'entità servizio
È possibile usare i cmdlet riportati di seguito per i criteri dell'entità servizio.

| Cmdlet | Descrizione | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Collega i criteri specificati a un'entità servizio. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Ottiene i criteri collegati all'entità servizio specificata.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Rimuove i criteri dall'entità servizio specificata.|

## <a name="license-requirements"></a>Requisiti relativi alle licenze

Per usare questa funzionalità è necessario disporre di una licenza di Azure AD Premium P1. Per trovare la licenza corretta per i propri requisiti, vedere [confronto tra le funzionalità disponibili a livello generale delle edizioni Premium e gratuita](https://azure.microsoft.com/pricing/details/active-directory/).

Anche i clienti con [licenze di Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) possono accedere alle funzionalità di accesso condizionale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [esempi di come configurare la durata dei token](configure-token-lifetimes.md).
---
title: Durata dei token configurabili in Azure Active Directory | Microsoft Docs
description: Informazioni su come impostare la durata per i token rilasciati da Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: billmath
ms.custom: aaddev
ms.reviewer: anchitn
ms.openlocfilehash: 553283f246b701b5084f0a3a9914d7ceb8826fe4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Durata dei token configurabili in Azure Active Directory (anteprima pubblica)
È possibile specificare la durata di un token rilasciato da Azure Active Directory (Azure AD). La durata dei token può essere impostata per tutte le app di un'organizzazione, per un'applicazione multi-tenant (più organizzazioni) o per un'entità servizio specifica in un'organizzazione.

> [!NOTE]
> Questa funzionalità è attualmente disponibile in anteprima pubblica. Potrebbe essere necessario ripristinare o rimuovere eventuali modifiche. La funzionalità è disponibile in tutte le sottoscrizioni di Azure Active Directory durante l'anteprima pubblica. Tuttavia, quando la funzionalità sarà disponibile a livello generale, alcuni aspetti potrebbero richiedere una sottoscrizione di [Azure Active Directory Premium](active-directory-get-started-premium.md).
>
>

In Azure AD, un oggetto criteri rappresenta un set di regole applicate a singole applicazioni o a tutte le applicazioni di un'organizzazione. Ogni tipo di criteri ha una struttura univoca con un set di proprietà che vengono applicate agli oggetti a cui sono assegnate.

È possibile designare un oggetto criteri come predefinito per l'organizzazione. I criteri predefiniti vengono applicati a tutte le applicazioni all'interno dell'organizzazione, fino a quando non vengono sostituiti da criteri con priorità più alta. È anche possibile assegnare criteri ad applicazioni specifiche. L'ordine di priorità varia in base al tipo di criteri.

> [!NOTE]
> I criteri per la durata dei token configurabili non sono supportati per SharePoint Online.  Anche se si ha la possibilità di creare questo criterio tramite PowerShell, SharePoint Online non lo riconoscerà. Fare riferimento ai [blog di SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) per altre informazioni sulla configurazione dei timeout di sessione inattiva.
>* La durata predefinita per il token di accesso di SharePoint Online è 1 ora. 
>* Il tempo di inattività massimo predefinito del token di aggiornamento di SharePoint Online è 90 giorni.
>

## <a name="token-types"></a>Tipi di token

È possibile impostare i criteri di durata dei token per i token di aggiornamento, i token di accesso, i token di sessione e i token ID.

### <a name="access-tokens"></a>Token di accesso
I client usano i token di accesso per accedere a una risorsa protetta. I token di accesso possono essere usati solo per una combinazione specifica di utente, client e risorsa. Non è possibile revocare i token di accesso, che rimangono validi fino alla scadenza. Un attore malintenzionato può usare un eventuale token di accesso ottenuto per la sua intera durata. Regolare la durata dei token di accesso significa trovare un compromesso tra il miglioramento delle prestazioni di sistema e l'aumento dell'intervallo di tempo durante il quale il client conserva l'accesso dopo che l'account dell'utente è stato disabilitato. Il miglioramento delle prestazioni di sistema si ottiene riducendo il numero delle volte in cui un client deve acquisire un token di accesso aggiornato.

### <a name="refresh-tokens"></a>Token di aggiornamento
Quando un client acquisisce un token di accesso per accedere a una risorsa protetta, riceve sia un token di aggiornamento che un token di accesso. Il token di aggiornamento consente di ottenere nuove coppie di token di accesso/aggiornamento alla scadenza del token di accesso attuale. Il token di aggiornamento è associato a una combinazione di utente e client e può essere revocato. La validità del token di aggiornamento viene quindi controllata ogni volta che viene usato.

È importante distinguere tra client riservati e client pubblici. Per altre informazioni sui diversi tipi di client, vedere la specifica [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Durata dei token con token di aggiornamento per client riservati
I client riservati sono applicazioni che possono archiviare in modo sicuro una password client, ovvero un segreto, e possono dimostrare che le richieste provengono dall'applicazione client e non da un attore malintenzionato. Un'app Web, ad esempio, è un client riservato perché può archiviare un segreto client nel server Web e non viene esposto. I token di aggiornamento rilasciati per questi flussi più sicuri hanno una durata `until-revoked`, che non può essere modificata tramite i criteri e non può essere revocata con reimpostazioni di password volontarie.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Durata dei token con token di aggiornamento per client pubblici

I client pubblici non possono archiviare in modo sicuro una password client, ovvero un segreto. Un'app iOS o Android, ad esempio, non può offuscare un segreto dal proprietario della risorsa e per questo è considerata un client pubblico. È possibile impostare criteri sulle risorse per evitare che i token di aggiornamento di client pubblici precedenti a un periodo specificato ottengano una nuova coppia di token di accesso/aggiornamento. A tale scopo, usare la proprietà Tempo inattività massimo token di aggiornamento. È anche possibile usare i criteri per impostare un intervallo di tempo oltre il quale i token di aggiornamento non vengono più accettati. A tale scopo, usare la proprietà Validità massima del token di aggiornamento. È possibile regolare la durata dei token di aggiornamento per controllare quando e con quale frequenza viene richiesto all'utente di immettere di nuovo le credenziali durante l'uso di un'applicazione client pubblica, anziché rieseguirne automaticamente l'autenticazione.

### <a name="id-tokens"></a>Token ID
I token ID vengono passati a siti Web e client nativi e contengono informazioni relative al profilo di un utente. Ogni token ID è associato a una combinazione specifica di utente e client ed è considerato valido fino alla relativa scadenza. La durata della sessione di un utente in un'applicazione Web corrisponde in genere alla durata del token ID rilasciato per l'utente. È possibile regolare la durata del token ID per controllare con quale frequenza si verifica la scadenza di una sessione in un'applicazione Web ed è necessario rieseguire l'autenticazione dell'utente con Azure AD, in modo automatico o interattivo.

### <a name="single-sign-on-session-tokens"></a>Token di sessione Single Sign-On
Quando un utente esegue l'autenticazione con Azure AD, viene stabilita una sessione Single Sign-On (SSO) con Azure AD e il browser dell'utente. Questa sessione è rappresentata dal token di sessione SSO, sotto forma di cookie. Si noti che il token di sessione SSO non è associato a un'applicazione client o a una risorsa specifica. I token di sessione SSO possono essere revocati e la relativa validità viene verificata ogni volta che vengono usati.

Azure AD usa due tipi di token di sessione SSO, ovvero permanenti e non permanenti. I token di sessione permanenti vengono archiviati dal browser come cookie permanenti. I token di sessione non permanenti vengono archiviati come cookie di sessione e vengono eliminati alla chiusura del browser. In genere viene archiviato un token di sessione non permanente. Ma quando durante l'autenticazione l'utente seleziona la casella di controllo **Mantieni l'accesso**, viene archiviato un token di sessione permanente.

I token di sessione non permanenti hanno una durata di 24 ore, mentre i token permanenti hanno una durata di 180 giorni. Ogni volta che un token di sessione SSO viene usato entro il relativo periodo di validità, tale periodo viene esteso per altre 24 ore o altri 180 giorni, a seconda del tipo di token. Se un token di sessione SSO non viene usato entro il relativo periodo di validità, è considerato scaduto e non viene più accettato.

È possibile usare criteri per impostare il periodo di tempo dopo il rilascio del primo token di sessione oltre il quale tale token non viene più accettato. A tale scopo, usare la proprietà Validità massima del token di sessione. È possibile regolare la durata dei token di sessione per controllare quando e con quale frequenza viene richiesto all'utente di immettere di nuovo le credenziali durante l'uso di un'applicazione Web, anziché eseguirne automaticamente l'autenticazione.

### <a name="token-lifetime-policy-properties"></a>Proprietà dei criteri per la durata dei token
I criteri per la durata dei token rappresentano un tipo di oggetto criteri contenente le regole di durata dei token. Usare le proprietà dei criteri per controllare la durata di token specifici. Se non si impostano criteri, il valore di durata predefinito viene applicato dal sistema.

### <a name="configurable-token-lifetime-properties"></a>Proprietà configurabili per la durata dei token
| Proprietà | Stringa proprietà criteri | Impatto | Predefinito | Minima | Massima |
| --- | --- | --- | --- | --- | --- |
| Durata dei token di accesso |AccessTokenLifetime |Token di accesso, token ID, token SAML2 |1 ora |10 minuti |1 giorno |
| Tempo inattività massimo token di aggiornamento |MaxInactiveTime |Token di aggiornamento |90 giorni |10 minuti |90 giorni |
| Validità massima token di aggiornamento a fattore singolo |MaxAgeSingleFactor |Token di aggiornamento (per tutti gli utenti) |Fino a revoca |10 minuti |Fino alla revoca<sup>1</sup> |
| Validità massima token di aggiornamento a più fattori |MaxAgeMultiFactor |Token di aggiornamento (per tutti gli utenti) |Fino a revoca |10 minuti |Fino alla revoca<sup>1</sup> |
| Validità massima token di sessione a fattore singolo |MaxAgeSessionSingleFactor<sup>2</sup> |Token di sessione (permanenti e non permanenti) |Fino a revoca |10 minuti |Fino alla revoca<sup>1</sup> |
| Validità massima token di sessione a più fattori |MaxAgeSessionMultiFactor<sup>3</sup> |Token di sessione (permanenti e non permanenti) |Fino a revoca |10 minuti |Fino alla revoca<sup>1</sup> |

* <sup>1</sup> La durata esplicita massima che è possibile impostare per questi attributi è 365 giorni.
* <sup>2</sup> Se **MaxAgeSessionSingleFactor** non è impostato, questo valore assume il valore di **MaxAgeSingleFactor**. Se nessuno dei due parametri è impostato, la proprietà assume il valore predefinito, vale a dire fino alla revoca.
* <sup>3</sup> Se **MaxAgeSessionMultiFactor** non è impostato, questo valore assume il valore di **MaxAgeMultiFactor**. Se nessuno dei due parametri è impostato, la proprietà assume il valore predefinito, vale a dire fino alla revoca.

### <a name="exceptions"></a>Eccezioni
| Proprietà | Impatto | Predefinito |
| --- | --- | --- |
| Validità massima dei token di aggiornamento (rilasciati a utenti federati con informazioni sulla revoca insufficienti<sup>1</sup>) |Token di aggiornamento (rilasciati a utenti federati con informazioni sulla revoca insufficienti<sup>1</sup>) |12 ore |
| Tempo inattività massimo token di aggiornamento (rilasciata a client riservati) |Token di aggiornamento (rilasciati a client riservati) |90 giorni |
| Validità massima token di aggiornamento (rilasciata a client riservati) |Token di aggiornamento (rilasciati a client riservati) |Fino a revoca |

* <sup>1</sup>Gli utenti federati con informazioni di revoca insufficienti comprendono tutti gli utenti che non dispongono dell'attributo "LastPasswordChangeTimestamp" sincronizzato. Questi utenti hanno una validità massima breve perché AAD non è in grado di verificare in quale momento revocare i token che sono collegati a una credenziale precedente (ad esempio una password che è stata modificata) e devono eseguire più spesso nuove verifiche per garantire che l'utente e i token associati siano ancora attivi. Per migliorare questa esperienza, gli amministratori del tenant devono assicurare che la sincronizzazione dell'attributo "LastPasswordChangeTimestamp" sia in corso (può essere impostata sull'oggetto utente tramite Powershell o AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Definizione della priorità e valutazione dei criteri
È possibile creare e quindi assegnare criteri per la durata dei token a un'applicazione specifica, all'organizzazione e alle entità servizio. A un'applicazione specifica si possono applicare più criteri. Di seguito sono riportate le regole su cui si basano i criteri per la durata dei token validi:

* Se i criteri sono assegnati in modo esplicito all'entità servizio, vengono applicati.
* Se all'entità servizio non sono assegnati criteri in modo esplicito, vengono applicati i criteri assegnati in modo esplicito all'elemento organizzazione padre dell'entità servizio.
* Se all'entità servizio o all'organizzazione non sono assegnati criteri in modo esplicito, vengono applicati i criteri assegnati all'applicazione.
* Se all'entità servizio, all'organizzazione o all'oggetto applicazione non sono stati assegnati criteri, vengono applicati i valori predefiniti. Vedere in proposito la tabella in [Proprietà configurabili per la durata dei token](#configurable-token-lifetime-properties).

Per altre informazioni sulla relazione tra oggetti applicazione e oggetti entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](active-directory-application-objects.md).

La validità di un token viene valutata quando questo viene usato. Vengono applicati i criteri con la priorità più alta per l'applicazione a cui si accede.

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
> Alle 12.00 l'utente avvia una nuova sessione del browser e tenta di accedere all'applicazione Web A. L'utente viene reindirizzato ad Azure AD a cui dovrà accedere. Nel browser viene creato un cookie con un token di sessione. L'utente viene di nuovo reindirizzato all'applicazione Web A con un token ID che gli consente di accedere all'applicazione.
>
> Alle 12.15 l'utente tenta di accedere all'applicazione Web B. Il browser lo reindirizza ad Azure AD che rileva il cookie di sessione. L'entità servizio dell'applicazione Web B è collegata ai criteri per la durata dei token 2, ma è anche parte dell'elemento organizzazione padre associato ai criteri per la durata dei token 1 predefiniti. Vengono applicati i criteri per la durata dei token 2 perché i criteri collegati alle entità servizio hanno una priorità maggiore rispetto a quelli predefiniti dell'organizzazione. Il token di sessione è stato rilasciato negli ultimi 30 minuti, quindi è considerato valido. L'utente viene di nuovo reindirizzato all'applicazione Web B con un token ID che consente l'accesso.
>
> Alle 13.00 l'utente tenta di accedere all'applicazione Web A e viene reindirizzato ad Azure AD. L'applicazione Web A non è collegata a criteri, ma si trova in un'organizzazione con criteri per la durata dei token 1 predefiniti. Vengono quindi applicati tali criteri. Viene rilevato il cookie di sessione rilasciato nelle ultime otto ore. L'utente viene reindirizzato automaticamente all'applicazione Web A con un nuovo token ID. Non è necessario che l'utente esegua l'autenticazione.
>
> Immediatamente dopo, l'utente prova ad accedere all'applicazione Web B e viene reindirizzato ad Azure AD. Come prima, vengono applicati i criteri per la durata dei token 2. Dato che il token è stato rilasciato da oltre 30 minuti, all'utente viene richiesto di immettere nuovamente le credenziali di accesso. Vengono rilasciati un nuovo token di sessione e un nuovo token ID. L'utente può quindi accedere all'applicazione Web B.
>
>

## <a name="configurable-policy-property-details"></a>Dettagli sulla proprietà dei criteri configurabili
### <a name="access-token-lifetime"></a>Durata dei token di accesso
**Stringa:** AccessTokenLifetime

**Impatto:** token di accesso, token ID

**Riepilogo:** questo tipo di criteri controlla per quanto tempo i token di accesso e ID della risorsa sono considerati validi. Riducendo la proprietà Durata dei token di accesso è possibile ridurre il rischio che un token di accesso o un token ID vengano usati da un attore malintenzionato per un lungo periodo di tempo. Questi token non possono essere revocati. Questo però influisce negativamente sulle prestazioni, perché è necessario sostituire i token più spesso.

### <a name="refresh-token-max-inactive-time"></a>Tempo inattività massimo token di aggiornamento
**Stringa:** MaxInactiveTime

**Impatto:** token di aggiornamento

**Riepilogo:** questo tipo di criteri controlla dopo quanto tempo dall'emissione un token di aggiornamento non potrà più essere usato da un client per il recupero di una nuova coppia di token di accesso/aggiornamento quando tenterà di accedere alla risorsa. Dal momento che quando viene usato un token di aggiornamento ne viene in genere restituito uno nuovo, questi criteri impediscono l'accesso se il client prova ad accedere a qualsiasi risorsa con il token di aggiornamento corrente durante il periodo di tempo specificato.

Questi criteri impongono agli utenti che non hanno svolto attività sul client nel tempo specificato di eseguire di nuovo l'autenticazione per recuperare un nuovo token di aggiornamento.

La proprietà Tempo inattività massimo token di aggiornamento deve essere impostata su un valore inferiore rispetto alle proprietà Validità massima token a fattore singolo e Validità massima token di aggiornamento a più fattori.

### <a name="single-factor-refresh-token-max-age"></a>Validità massima token di aggiornamento a fattore singolo
**String:** MaxAgeSingleFactor

**Impatto:** token di aggiornamento

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può usare un token di aggiornamento per ottenere una nuova coppia di token di accesso/aggiornamento dopo l'ultima autenticazione a fattore singolo. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di aggiornamento, l'utente può usare il flusso del token di aggiornamento per il periodo di tempo specificato. Questo purché il token di aggiornamento corrente non venga revocato e non rimanga inutilizzato oltre il tempo di inattività. A questo punto, l'utente deve eseguire di nuovo l'autenticazione per ricevere un nuovo token di aggiornamento.

Riducendo la validità massima, gli utenti devono eseguire l'autenticazione più spesso. Dal momento che l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare questa proprietà su un valore minore o uguale a quello della proprietà Validità massima token di aggiornamento a più fattori.

### <a name="multi-factor-refresh-token-max-age"></a>Validità massima token di aggiornamento a più fattori
**Strings:** MaxAgeMultiFactor

**Impatto:** token di aggiornamento

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può usare un token di aggiornamento per ottenere una nuova coppia di token di accesso/aggiornamento dopo l'ultima autenticazione a più fattori. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di aggiornamento, l'utente può usare il flusso del token di aggiornamento per il periodo di tempo specificato. Questo purché il token di aggiornamento corrente non venga revocato e non rimanga inutilizzato oltre il tempo di inattività. A questo punto, gli utenti devono eseguire di nuovo l'autenticazione per ricevere un nuovo token di aggiornamento.

Riducendo la validità massima, gli utenti devono eseguire l'autenticazione più spesso. Dal momento che l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare questa proprietà su un valore maggiore o uguale a quello della proprietà Validità massima token di aggiornamento a fattore singolo.

### <a name="single-factor-session-token-max-age"></a>Validità massima token di sessione a fattore singolo
**Stringa** MaxAgeSessionSingleFactor

**Impatto:** token di sessione (permanenti e non permanenti)

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può usare un token di sessione per ottenere un nuovo token ID e un nuovo token di sessione dopo l'ultima autenticazione a fattore singolo. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di sessione, l'utente può usare il flusso del token di sessione per il periodo di tempo specificato. Questo purché il token di sessione corrente non venga revocato e non sia scaduto. Dopo il periodo di tempo specificato, l'utente deve eseguire di nuovo l'autenticazione per ricevere un nuovo token di sessione.

Riducendo la validità massima, gli utenti devono eseguire l'autenticazione più spesso. Dal momento che l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare questa proprietà su un valore minore o uguale a quello della proprietà Validità massima token di sessione a più fattori.

### <a name="multi-factor-session-token-max-age"></a>Validità massima token di sessione a più fattori
**Stringa:** MaxAgeSessionMultiFactor

**Impatto:** token di sessione (permanenti e non permanenti)

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può usare un token di sessione per ottenere un nuovo token ID e un nuovo token di sessione dopo l'ultima autenticazione a più fattori. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di sessione, l'utente può usare il flusso del token di sessione per il periodo di tempo specificato. Questo purché il token di sessione corrente non venga revocato e non sia scaduto. Dopo il periodo di tempo specificato, l'utente deve eseguire di nuovo l'autenticazione per ricevere un nuovo token di sessione.

Riducendo la validità massima, gli utenti devono eseguire l'autenticazione più spesso. Dal momento che l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare questa proprietà su un valore maggiore o uguale a quello della proprietà Validità massima token di sessione a fattore singolo.

## <a name="example-token-lifetime-policies"></a>Criteri per la durata dei token di esempio
In Azure AD sono disponibili diversi scenari che permettono di creare e gestire la durata dei token per le app, le entità servizio e l'intera organizzazione. Questa sezione illustra alcuni scenari comuni relativi ai criteri che consentono di definire nuove regole per:

* Durata del token
* Tempo di inattività massimo del token
* Validità massima del token

Gli esempi illustrano come:

* Gestire i criteri predefiniti di un'organizzazione
* Creare criteri per l'accesso Web
* Creare criteri per un'app nativa che chiama un'API Web
* Gestire criteri avanzati

### <a name="prerequisites"></a>prerequisiti
Gli esempi seguenti mostrano come creare, aggiornare, collegare ed eliminare criteri per le app, le entità servizio e l'intera organizzazione. Se non si ha familiarità con Azure AD, è consigliabile vedere [come ottenere un tenant di Azure AD](active-directory-howto-tenant.md) prima di procedere con questi esempi.  

Per iniziare, seguire questa procedura:

1. Scaricare l'ultima [versione di anteprima del modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
2. Eseguire il comando `Connect` per accedere all'account amministratore di Azure AD. Eseguire questo comando ogni volta che si avvia una nuova sessione.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Per visualizzare tutti i criteri creati nell'organizzazione, eseguire questo comando. Questo comando va usato dopo la maggior parte delle operazioni negli scenari indicato di seguito L'esecuzione del comando consente anche di ottenere ** ** dei criteri.

    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Esempio: gestire i criteri predefiniti di un'organizzazione
In questo esempio vengono creati criteri che permettono agli utenti di eseguire l'accesso con una frequenza minore nell'intera organizzazione. A tale scopo, vengono creati criteri per la durata dei token di aggiornamento a fattore singolo che vengono applicati a tutta l'organizzazione. Tali criteri vengono applicati a ogni applicazione nell'organizzazione e a ogni entità servizio per cui ancora non sono impostati criteri.

1. Creare i criteri per la durata dei token.

    1.  Impostare il token di aggiornamento a fattore singolo in modo che sia valido fino alla revoca. In questo modo il token non scade fino a quando non viene revocato l'accesso. Creare la definizione dei criteri seguente:

        ```PowerShell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Per creare i criteri, eseguire questo comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Per visualizzare i nuovi criteri e ottenere il relativo **ObjectId**, eseguire questo comando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Aggiornare i criteri.

    Il primo criterio impostato in questo esempio potrebbe non essere rigoroso quanto richiesto dal servizio. Per impostare il token di aggiornamento a fattore singolo in modo che scada tra due giorni, eseguire questo comando:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Esempio: creare criteri per l'accesso Web

In questo esempio vengono creati i criteri in base ai quali viene richiesto agli utenti di eseguire più spesso l'autenticazione nell'app Web. Questi criteri consentono di impostare la durata dei token di accesso/ID e la validità massima di un token di sessione a più fattori per l'entità servizio dell'app Web.

1. Creare i criteri per la durata dei token.

    Questi criteri per l'accesso Web consentono di impostare su due ore la durata dei token di accesso/ID e la validità massima di un token di sessione a fattore singolo.

    1.  Per creare i criteri, eseguire questo comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Per visualizzare i nuovi criteri e ottenere il relativo **ObjectId**, eseguire questo comando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2.  Assegnare i criteri all'entità servizio. È necessario ottenere anche l'**ObjectId** dell'entità servizio. 

    1.  Per visualizzare tutte le entità servizio dell'organizzazione, è possibile eseguire query in [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) o [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). È anche possibile testare questa funzione in [Azure AD Graph Explorer](https://graphexplorer.cloudapp.net/)e [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) usando l'account di Azure AD.

    2.  Dopo aver ottenuto l'**ObjectId** dell'entità servizio, eseguire questo comando:

        ```PowerShell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Esempio: creare criteri per un'app nativa che chiama un'API Web
In questo esempio vengono creati i criteri in base ai quali viene richiesto agli utenti di eseguire l'autenticazione con minore frequenza. Questi criteri prolungano anche l'intervallo di tempo di inattività dell'utente prima che sia necessario rieseguire l'autenticazione. I criteri vengono applicati all'API web. Quando l'app nativa richiede l'API Web come risorsa, vengono applicati questi criteri.

1. Creare i criteri per la durata dei token.

    1.  Per creare criteri rigidi per un'API Web, eseguire questo comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Per visualizzare i nuovi criteri e ottenere il relativo **ObjectId**, eseguire questo comando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Assegnare i criteri all'API Web. È necessario ottenere anche l'**ObjectId** dell'app. Il modo migliore per trovare l'**ObjectId** dell'app è tramite il [portale di Azure](https://portal.azure.com/).

   Dopo aver ottenuto l'**ObjectId** dell'app, eseguire questo comando:

        ```PowerShell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Esempio: gestire criteri avanzati
In questo esempio vengono creati alcuni criteri per illustrare il funzionamento del sistema di priorità. Viene illustrato anche come gestire più criteri applicati a vari oggetti.

1. Creare i criteri per la durata dei token.

    1.  Per creare i criteri predefiniti di un'organizzazione in base ai quali la durata dei token di aggiornamento a fattore singolo è impostata su 30 giorni, eseguire questo comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Per visualizzare i nuovi criteri e ottenere il relativo **ObjectId**, eseguire questo comando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Assegnare i criteri a un'entità servizio.

    A questo punto sono disponibili criteri che si applicano all'intera organizzazione. Si supponga di voler conservare tali criteri della durata di 30 giorni per un'entità servizio specifica, impostando però i criteri predefiniti dell'organizzazione sul valore limite superiore, ovvero fino alla revoca.

    1.  Per visualizzare tutte le entità servizio dell'organizzazione, è possibile eseguire query in [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) o [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). È anche possibile testare questa funzione in [Azure AD Graph Explorer](https://graphexplorer.cloudapp.net/)e [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) usando l'account di Azure AD.

    2.  Dopo aver ottenuto l'**ObjectId** dell'entità servizio, eseguire questo comando:

            ```PowerShell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Impostare il flag `IsOrganizationDefault` su false.

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Creare nuovi criteri predefiniti dell'organizzazione.

    ```PowerShell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    I criteri originali sono ora collegati all'entità servizio e i nuovi criteri sono impostati come criteri predefiniti dell'organizzazione. È importante ricordare che i criteri applicati alle entità servizio hanno priorità rispetto a quelli predefiniti dell'organizzazione.

## <a name="cmdlet-reference"></a>Informazioni di riferimento sui cmdlet

### <a name="manage-policies"></a>Gestire i criteri

È possibile usare i cmdlet riportati di seguito per gestire i criteri.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Crea nuovi criteri.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matrice del codice JSON in formato stringa contenente tutte le regole dei criteri. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Stringa relativa al nome dei criteri. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Se true, imposta i criteri come predefiniti dell'organizzazione. Se false, non esegue alcuna operazione. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Tipo di criteri. Per la durata dei token usare sempre "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Facoltativo] |Imposta un ID alternativo per i criteri. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Ottiene tutti i criteri di Azure AD o i criteri specificati.

```PowerShell
Get-AzureADPolicy
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code>[Facoltativo] |**ObjectId (Id)** dei criteri da usare. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Ottiene tutte le app e le entità servizio collegate a criteri specifici.

```PowerShell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** dei criteri da usare. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aggiorna i criteri esistenti.

```PowerShell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** dei criteri da usare. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Stringa relativa al nome dei criteri. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Facoltativo] |Matrice del codice JSON in formato stringa contenente tutte le regole dei criteri. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Facoltativo] |Se true, imposta i criteri come predefiniti dell'organizzazione. Se false, non esegue alcuna operazione. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Facoltativo] |Tipo di criteri. Per la durata dei token usare sempre "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Facoltativo] |Imposta un ID alternativo per i criteri. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Elimina i criteri specificati.

```PowerShell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** dei criteri da usare. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Criteri dell'applicazione
È possibile usare i cmdlet riportati di seguito per i criteri dell'applicazione.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Collega i criteri specificati a un'applicazione.

```PowerShell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** dell'applicazione. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** dei criteri. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Ottiene i criteri assegnati a un'applicazione.

```PowerShell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** dell'applicazione. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Rimuove i criteri da un'applicazione.

```PowerShell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** dell'applicazione. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** dei criteri. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Criteri dell'entità servizio
È possibile usare i cmdlet riportati di seguito per i criteri dell'entità servizio.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Collega i criteri specificati a un'entità servizio.

```PowerShell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** dell'applicazione. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** dei criteri. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Ottiene i criteri collegati all'entità servizio specificata.

```PowerShell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** dell'applicazione. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Rimuove i criteri dall'entità servizio specificata.

```PowerShell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametri | DESCRIZIONE | Esempio |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** dell'applicazione. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** dei criteri. | `-PolicyId <ObjectId of Policy>` |

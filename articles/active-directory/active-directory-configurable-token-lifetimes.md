---
title: Durata dei token configurabili in Azure Active Directory  | Documentazione Microsoft
description: "Questa funzionalità viene usata da amministratori e sviluppatori per specificare la durata dei token emessi da Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: d7e635f7e84ac53399309bf4ec8a7fa9e70e3728
ms.openlocfilehash: aa18efb0c622ae38eea0de28f25c72788e6d0f20
ms.lasthandoff: 03/01/2017


---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Durata dei token configurabili in Azure Active Directory (anteprima pubblica)
> [!NOTE]
> Questa funzionalità è attualmente disponibile come anteprima pubblica.  È consigliabile essere preparati a ripristinare o rimuovere eventuali modifiche.  Durante l'anteprima pubblica chiunque avrà la possibilità di provare questa funzionalità, ma alcuni aspetti potrebbero richiedere una [sottoscrizione di Azure AD Premium](active-directory-get-started-premium.md) quando saranno disponibili a livello generali.
> 
> 

## <a name="introduction"></a>Introduzione
Questa funzionalità viene usata da amministratori e sviluppatori per specificare la durata dei token emessi da Azure AD. La durata dei token può essere configurata per tutte le app in un'organizzazione, per un'applicazione multi-tenant (più organizzazioni) o per un'entità servizio specifica in un'organizzazione.

In Azure AD, un oggetto criteri rappresenta un set di regole definito per singole applicazioni o tutte le applicazioni in un'organizzazione.  Ogni tipo di criteri ha una struttura univoca con un set di proprietà che vengono in seguito applicate agli oggetti a cui sono assegnate.

Per un'organizzazione possono essere designati criteri predefiniti, i quali hanno effetto su qualsiasi applicazione che risiede all'interno dell'organizzazione, fino a quando non vengono sostituiti da criteri con priorità più alta. I criteri possono anche essere assegnati ad applicazioni specifiche. L'ordine di priorità varia in base al tipo di criteri.

I criteri per la durata dei token possono essere configurati per token di aggiornamento, token di accesso, token di sessione e token ID.

### <a name="access-tokens"></a>Token di accesso
Un token di accesso viene usato da un client per accedere a una risorsa protetta e può essere usato solo per una combinazione specifica di utente, client e risorsa. Non è possibile revocare i token di accesso, che rimangono validi fino alla scadenza. Un attore malintenzionato può usare un eventuale token di accesso ottenuto per la sua intera durata.  Regolando la durata dei token di accesso si può trovare un compromesso tra il miglioramento delle prestazioni di sistema e l'aumento del tempo durante il quale il client conserva l'accesso dopo che l'account dell'utente è stato disabilitato.  Il miglioramento delle prestazioni di sistema si ottiene riducendo il numero delle volte in cui un client deve acquisire un token di accesso aggiornato. 

### <a name="refresh-tokens"></a>Token di aggiornamento
Quando un client acquisisce un token di accesso per accedere a una risorsa protetta, riceve sia un token di aggiornamento sia un token di accesso. Il token di aggiornamento consente di ottenere nuove coppie di token di accesso/aggiornamento alla scadenza del token di accesso attuale. I token di aggiornamento sono associati a combinazioni di utente e client. Possono essere revocati e la relativa validità viene verificata ogni volta che vengono usati.

È importante distinguere i client riservati da quelli pubblici. Per altre informazioni sui diversi tipi di client, vedere la specifica [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Durata dei token con token di aggiornamento client riservati
I client riservati sono applicazioni che possono archiviare in modo sicuro la password di un client (segreto), garantendo in questo modo che le richieste provengono dall'applicazione client e non da un attore malintenzionato. Un'app Web, ad esempio, è un client riservato perché può archiviare un segreto client nel server Web e in questo modo non viene esposto. Poiché questi flussi sono più sicuri, la durata predefinita dei token di aggiornamento emessi per essi è maggiore e non può essere modificata tramite i criteri.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Durata dei token con token di aggiornamento client pubblici 

I client pubblici non riescono ad archiviare in modo sicuro una password client (segreto). Un'app iOS/Android, ad esempio, non può offuscare un segreto dal proprietario della risorsa e per questo è considerata un client pubblico.  È possibile impostare criteri sulle risorse per evitare che i token di aggiornamento di client pubblici precedenti a un periodo specificato ottengano una nuova coppia di token di accesso/aggiornamento (Tempo inattività massimo token di aggiornamento).  I criteri possono anche essere usati per impostare un periodo di tempo oltre il quale i token di aggiornamento non vengono più accettati (Validità massima token di aggiornamento).  Regolando la durata dei token di aggiornamento è possibile controllare quando e con quale frequenza sarà richiesto all'utente di digitare di nuovo le credenziali durante l'uso di un'applicazione client pubblica, invece di rieseguirne l'autenticazione automatica.

### <a name="id-tokens"></a>Token ID
I token ID vengono passati a siti Web e client nativi e contengono le informazioni sul profilo di un utente. Ogni token ID è associato a una combinazione specifica di utente e client ed è considerato valido fino alla scadenza.  La durata della sessione di un utente in un'applicazione Web corrisponde in genere alla durata del token ID emesso per l'utente.  Regolando la durata del token ID è possibile controllare con quale frequenza avviene la scadenza di una sessione in un'applicazione Web ed è necessario che per l'utente venga di nuovo eseguita l'autenticazione ad Azure AD (in modo automatico o interattivo).

### <a name="single-sign-on-session-token"></a>Token di sessione Single Sign-On
Quando un utente esegue l'autenticazione ad Azure AD e seleziona la casella "Mantieni l'accesso", viene stabilita una sessione Single Sign-On con Azure AD e il browser dell'utente.  Questa sessione è rappresentata dal token di sessione Single Sign-On, sotto forma di cookie. È importante notare che il token di sessione SSO non è associato a un'applicazione client/risorsa specifica. I token di sessione SSO possono essere revocati e la relativa validità viene verificata ogni volta che vengono usati.

Esistono due tipi di token di sesione SSO. I token di sessione permanenti sono archiviati dal browser come cookie permanenti, mentre quelli non permanenti sono archiviati come cookie di sessione e vengono eliminati definitivamente alla chiusura del browser.

I token di sessione non permanenti hanno una durata di 24 ore, mentre quelli permanenti hanno una durata di 180 giorni. Ogni volta che il token di sessione SSO viene usato durante il relativo periodo di validità, tale periodo viene esteso per altre 24 ore o altri 180 giorni. Se il token di sessione SSO non viene usato durante il relativo periodo di validità, sarà considerato scaduto e non verrà più accettato. 

È possibile usare i criteri per impostare un periodo di tempo dopo l'emissione del primo token di sessione oltre il quale i token di sessione non sono più accettati (Validità massima token di sessione).  Regolando la durata del token di sessione è possibile controllare quando e con quale frequenza sarà richiesto all'utente di digitare di nuovo le credenziali durante l'uso di un'applicazione Web, invece di rieseguirne l'autenticazione automatica.

### <a name="token-lifetime-policy-properties"></a>Proprietà dei criteri per la durata dei token
I criteri per la durata dei token rappresentano un tipo di oggetto criteri contenente le regole di durata dei token.  Le proprietà dei criteri consentono di controllare la durata di token specifici.  Se non si impostano criteri, il valore di durata predefinito viene applicato dal sistema.

### <a name="configurable-token-lifetime-properties"></a>Proprietà configurabili per la durata dei token
| Proprietà | Stringa proprietà criteri | Impatto | Default | Minima | Massima |
| --- | --- | --- | --- | --- | --- |
| Durata dei token di accesso |AccessTokenLifetime |Token di accesso, token ID, token SAML2 |1 ora |10 minuti |1 giorno |
| Tempo inattività massimo token di aggiornamento |MaxInactiveTime |Token di aggiornamento |14 giorni |10 minuti |90 giorni |
| Validità massima token di aggiornamento a fattore singolo |MaxAgeSingleFactor |Token di aggiornamento (per tutti gli utenti) |90 giorni |10 minuti |Fino a revoca* |
| Validità massima token di aggiornamento a più fattori |MaxAgeMultiFactor |Token di aggiornamento (per tutti gli utenti) |90 giorni |10 minuti |Fino a revoca* |
| Validità massima token di sessione a fattore singolo |MaxAgeSessionSingleFactor** |Token di sessione (permanenti e non permanenti) |Fino a revoca |10 minuti |Fino a revoca* |
| Validità massima token di sessione a più fattori |MaxAgeSessionMultiFactor*** |Token di sessione (permanenti e non permanenti) |Fino a revoca |10 minuti |Fino a revoca* |

* *365 giorni è la durata esplicita massima che può essere impostata per questi attributi.
* **Se MaxAgeSessionSingleFactor non è impostato, questo valore assume il valore di MaxAgeSingleFactor. Se nessuno dei due parametri è impostato, la proprietà assume il valore predefinito until-revoked (fino a revoca).
* ***Se MaxAgeSessionMultiFactor non è impostato, questo valore assume il valore di MaxAgeMultiFactor. Se nessuno dei due parametri è impostato, la proprietà assume il valore predefinito until-revoked (fino a revoca).

### <a name="exceptions"></a>Eccezioni
| Proprietà | Impatto | Default |
| --- | --- | --- |
| Tempo inattività massimo token di aggiornamento (utenti federati con informazioni sulla revoca insufficienti) |Token di aggiornamento (emissione per utenti federati con informazioni sulla revoca insufficienti) |12 ore |
| Tempo inattività massimo token di aggiornamento (client riservati) |Token di aggiornamento (emissione per client riservati) |90 giorni |
| Validità massima token di aggiornamento (emissione per client riservati) |Token di aggiornamento (emissione per client riservati) |Fino a revoca |

### <a name="priority-and-evaluation-of-policies"></a>Priorità e valutazione dei criteri
I criteri per la durata dei token possono essere creati e assegnati ad applicazioni, organizzazioni ed entità servizio specifiche. È quindi possibile che più criteri si applichino a un'applicazione specifica. Di seguito sono riportate le regole su cui si basano i criteri per la durata dei token validi:

* Se i criteri sono assegnati in modo esplicito all'entità servizio, verranno applicati. 
* Se all'entità servizio non sono assegnati criteri in modo esplicito, verranno applicati i criteri assegnati in modo esplicito all'elemento organizzazione padre dell'entità servizio. 
* Se all'entità servizio o all'organizzazione non sono assegnati criteri in modo esplicito, verranno applicati i criteri assegnati all'applicazione. 
* Se all'entità servizio, all'organizzazione o all'oggetto applicazione non sono stati assegnati criteri, verranno applicati i valori predefiniti (vedere la tabella precedente).

Per altre informazioni sulla relazione tra oggetti applicazione e oggetti entità servizio in Azure AD, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](active-directory-application-objects.md).

La validità di un token viene valutata quando viene usato. Vengono applicati i criteri con la priorità più alta per l'applicazione a cui si accede.

> [!NOTE]
> Esempio
> 
> Un utente desidera accedere a 2 applicazioni Web, A e B. 
> 
> * Entrambe le applicazioni si trovano nello stesso elemento organizzazione padre. 
> * I criteri per la durata dei token 1 con una validità massima del token di sessione di 8 ore vengono impostati come valore predefinito per l'elemento organizzazione padre.
> * L'applicazione Web A è di tipo normale e non è collegata a criteri. 
> * L'applicazione Web B viene usata per processi importanti e la relativa entità servizio è collegata ai criteri per la durata dei token 2 con una validità massima token di sessione di 30 minuti.
> 
> Alle 12.00 l'utente apre una nuova sessione del browser e tenta di accedere all'applicazione Web A. L'utente viene reindirizzato ad Azure AD a cui dovrà accedere. Verrà rilasciato nel browser un cookie con un token di sessione. L'utente viene di nuovo reindirizzato all'applicazione Web A con un token ID che consente di accedere all'applicazione.
> 
> Alle 12.15 l'utente tenta quindi di accedere all'applicazione Web B. Il browser viene reindirizzato ad Azure AD che rileva il cookie di sessione. L'entità servizio dell'applicazione Web B è collegata ai criteri 2, ma è anche parte dell'elemento organizzazione padre associato ai criteri predefiniti 1. Vengono applicati i criteri 2 perché i criteri collegati alle entità servizio hanno una priorità maggiore rispetto a quelli predefiniti dell'organizzazione. Il token di sessione è stato emesso negli ultimi 30 minuti, quindi è considerato valido. L'utente viene di nuovo reindirizzato all'applicazione Web B con un token ID che consente l'accesso.
> 
> Alle 13.00 l'utente tenta di passare all'applicazione Web A e viene reindirizzato ad Azure AD. L'applicazione Web A non è collegata a criteri, ma si trova in un'organizzazione collegata ai criteri predefiniti 1. Vengono quindi applicati tali criteri. Viene rilevato il cookie di sessione che è stato emesso in origine nelle ultime 8 ore e l'utente viene automaticamente reindirizzato all'applicazione Web A con un nuovo token ID, senza la necessità di effettuare l'autenticazione.
> 
> L'utente tenta immediatamente di accedere all'applicazione Web B e viene reindirizzato ad Azure AD. Come in precedenza, vengono applicati i criteri 2. Poiché il token è stato emesso entro i 30 minuti precedenti, all'utente verrà richiesto di digitare di nuovo le credenziali. Verrà avviata a questo punto una nuova sessione e verrà emesso un nuovo token ID. L'utente potrà quindi accedere all'applicazione Web B.
> 
> 

## <a name="configurable-policy-properties-in-depth"></a>Proprietà dei criteri configurabili - Informazioni dettagliate
### <a name="access-token-lifetime"></a>Durata dei token di accesso
**Stringa:** AccessTokenLifetime

**Impatto:** token di accesso, token ID

**Riepilogo:** questo tipo di criteri controlla per quanto tempo i token di accesso e ID della risorsa sono considerati validi. La riduzione della durata dei token di accesso attenua il rischio che un token di accesso o ID possa essere usato da un attore malintenzionato per un lungo periodo di tempo, visto che tali token non possono essere revocati, ma influisce negativamente sulle prestazioni, poiché i token dovranno essere sostituiti più spesso.

### <a name="refresh-token-max-inactive-time"></a>Tempo inattività massimo token di aggiornamento
**Stringa:** MaxInactiveTime

**Impatto:** token di aggiornamento

**Riepilogo:** questo tipo di criteri controlla dopo quanto tempo dall'emissione un token di aggiornamento non potrà più essere usato da un client per il recupero di una nuova coppia di token di accesso/aggiornamento quando tenterà di accedere alla risorsa. Poiché quando un token di aggiornamento viene usato ne viene in genere restituito uno nuovo, affinché tali criteri impediscano l'accesso, è necessario che il client non abbia comunicato con risorse che usano il token di aggiornamento corrente durante il periodo di tempo specificato. 

Questi criteri impongono agli utenti che non hanno svolto attività sul client nel tempo specificato di eseguire di nuovo l'autenticazione per recuperare un nuovo token di aggiornamento. 

È importante notare che Tempo inattività massimo token di aggiornamento deve essere impostato su un valore minore rispetto a Validità massima token a fattore singolo e Validità massima token di aggiornamento a più fattori.

### <a name="single-factor-refresh-token-max-age"></a>Validità massima token di aggiornamento a fattore singolo
**String:** MaxAgeSingleFactor

**Impatto:** token di aggiornamento

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può continuare a usare i token di aggiornamento per ottenere nuove coppie di token di accesso/aggiornamento dopo l'ultima autenticazione a fattore singolo. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di aggiornamento, un utente potrà usare il flusso di tale token per il periodo di tempo specificato (purché il token di aggiornamento corrente non venga revocato né rimanga inutilizzato per un periodo superiore rispetto al tempo di inattività). A questo punto, l'utente dovrà eseguire di nuovo l'autenticazione per poter ricevere un nuovo token di aggiornamento. 

Riducendo la validità massima, gli utenti dovranno eseguire l'autenticazione più spesso. Poiché l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare i criteri su un valore uguale o minore rispetto a Validità massima token di aggiornamento a più fattori.

### <a name="multi-factor-refresh-token-max-age"></a>Validità massima token di aggiornamento a più fattori
**Strings:** MaxAgeMultiFactor

**Impatto:** token di aggiornamento

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può continuare a usare i token di aggiornamento per ottenere nuove coppie di token di accesso/aggiornamento dopo l'ultima autenticazione a più fattori. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di aggiornamento, un utente potrà usare il flusso di tale token per il periodo di tempo specificato (purché il token di aggiornamento corrente non venga revocato né rimanga inutilizzato per un periodo superiore rispetto al tempo di inattività). A questo punto, l'utente dovrà eseguire di nuovo l'autenticazione per poter ricevere un nuovo token di aggiornamento. 

Riducendo la validità massima, gli utenti dovranno eseguire l'autenticazione più spesso. Poiché l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare i criteri su un valore uguale o maggiore rispetto a Validità massima token di aggiornamento a fattore singolo.

### <a name="single-factor-session-token-max-age"></a>Validità massima token di sessione a fattore singolo
**Stringa** MaxAgeSessionSingleFactor

**Impatto:** token di sessione (permanenti e non permanenti)

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può continuare a usare i token di sessione per ottenere nuovi token ID e di sessione dopo l'ultima autenticazione a fattore singolo. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di sessione, un utente potrà usare il flusso di tale token per il periodo di tempo specificato (purché il token di sessione corrente non venga revocato né sia scaduto). A questo punto, l'utente dovrà eseguire di nuovo l'autenticazione per poter ricevere un nuovo token di sessione. 

Riducendo la validità massima, gli utenti dovranno eseguire l'autenticazione più spesso. Poiché l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare i criteri su un valore uguale o minore rispetto a Validità massima token di sessione a più fattori.

### <a name="multi-factor-session-token-max-age"></a>Validità massima token di sessione a più fattori
**Stringa:** MaxAgeSessionMultiFactor

**Impatto:** token di sessione (permanenti e non permanenti)

**Riepilogo:** questo tipo di criteri controlla per quanto tempo un utente può continuare a usare i token di sessione per ottenere nuovi token ID e di sessione dopo l'ultima autenticazione a più fattori. Dopo aver eseguito l'autenticazione e aver ricevuto un nuovo token di sessione, un utente potrà usare il flusso di tale token per il periodo di tempo specificato (purché il token di sessione corrente non venga revocato né sia scaduto). A questo punto, l'utente dovrà eseguire di nuovo l'autenticazione per poter ricevere un nuovo token di sessione. 

Riducendo la validità massima, gli utenti dovranno eseguire l'autenticazione più spesso. Poiché l'autenticazione a fattore singolo è considerata meno sicura dell'autenticazione a più fattori, è consigliabile impostare i criteri su un valore uguale o maggiore rispetto a Validità massima token di sessione a fattore singolo.

## <a name="sample-token-lifetime-policies"></a>Criteri per la durata dei token di esempio
La possibilità di creare e gestire la durata dei token per app, entità servizio e organizzazione nel complesso rende disponibili scenari totalmente nuovi in Azure AD.  Verranno descritti alcuni scenari comuni relativi ai criteri che consentiranno di definire nuove regole per i seguenti elementi:

* Durata del token
* Tempi di inattività massima del token
* Validità massima del token

Verranno descritti alcuni scenari, tra cui:

* Gestione dei criteri predefiniti di un'organizzazione
* Creazione di criteri per l'accesso Web
* Creazione di criteri per app native tramite la chiamata a un'API Web
* Gestione di criteri avanzati 

### <a name="prerequisites"></a>Prerequisiti
Negli scenari di esempio verranno creati, aggiornati, collegati ed eliminati criteri relativi ad app, entità servizio e organizzazione nel complesso.  Se non si ha esperienza in Azure AD, controllare [questo articolo](active-directory-howto-tenant.md) per prepararsi prima di procedere con questi esempi.  

1. Per iniziare, scaricare la [versione di anteprima dei cmdlet Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview) più recente. 
2. Quando si hanno a disposizione i cmdlet Azure AD PowerShell, eseguire il comando Connect per accedere all'account amministratore di Azure AD. Questa operazione dovrà essere eseguita ogni volta che si avvia una nuova sessione.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Eseguire questo comando per visualizzare tutti i criteri creati per l'organizzazione.  Questo comando deve essere usato dopo la maggior parte delle operazioni negli scenari indicato di seguito  e consentirà anche di ottenere l'**ID oggetto** dei criteri. 
   
    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="sample-managing-a-organizations-default-policy"></a>Esempio: Gestione dei criteri predefiniti di un'organizzazione
In questo esempio verranno creati i criteri che consentono agli utenti di effettuare l'accesso con una frequenza minore nell'ambito dell'intera organizzazione. 

A tale scopo, vengono creati i criteri per la durata dei token di aggiornamento a fattore singolo che vengono applicati all'organizzazione. Questi criteri verranno applicati a ogni applicazione nell'organizzazione e a ogni entità servizio per cui ancora non sono impostati criteri. 

#### <a name="1-create-a-token-lifetime-policy"></a>1. Creare i criteri per la durata dei token.

Impostare il token di aggiornamento a fattore singolo su "until-revoked", in modo tale che il token non scadrà fino a quando non verrà revocato l'accesso.  Di seguito è indicata la definizione dei criteri che verrà creata:

```PowerShell
@('{
    "TokenLifetimePolicy":
    {
        "Version":1, 
        "MaxAgeSingleFactor":"until-revoked"
    }
}')
```

Eseguire quindi il comando indicato di seguito per creare i criteri. 

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
```

Per visualizzare i nuovi criteri e ottenere il relativo ObjectID, eseguire il comando indicato di seguito.

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-update-the-policy"></a>2. Aggiornare i criteri

Si ritiene che i primi criteri non sono abbastanza rigidi per il servizio e si desidera fare in modo che i token di aggiornamento a fattore singolo scadano dopo 2 giorni. Eseguire il comando indicato di seguito. 

```PowerShell
Set-AzureADPolicy -ObjectId <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
```

#### <a name="3-youre-done"></a>3. L'operazione è completata. 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>Esempio. Creazione di criteri per l'accesso Web

In questo esempio verranno creati i criteri in base ai quali verrà richiesto agli utenti di eseguire più spesso l'autenticazione all'app Web. Questi criteri consentiranno di impostare la durata dei token di accesso/ID e la validità massima di un token di sessione a più fattori per l'entità servizio dell'app Web.

#### <a name="1-create-a-token-lifetime-policy"></a>1. Creare i criteri per la durata dei token.

Questi criteri per l'accesso Web consentiranno di impostare la durata dei token di accesso/ID e la validità massima di un token di sessione a fattore singolo su 2 ore.

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
```

Per visualizzare i nuovi criteri e ottenere il relativo ObjectID, eseguire il comando indicato di seguito.

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-assign-the-policy-to-your-service-principal"></a>2. Assegnare i criteri all'entità servizio.

I nuovi criteri verranno collegati a un'entità servizio  e sarà anche necessario poter accedere a **ObjectId** di tale entità. Per visualizzare tutte le entità servizio dell'organizzazione, è possibile eseguire una query su [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) o passare allo [strumento Graph Explorer](https://graphexplorer.cloudapp.net/) e accedere all'account Azure AD. 

Dopo aver recuperato l'elemento **ObjectId**, eseguire il comando indicato di seguito.

```PowerShell
Add-AzureADServicePrincipalPolicy -ObjectId <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
```

#### <a name="3-youre-done"></a>3. Operazione completata 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>Esempio. Creazione di criteri per app native tramite la chiamata a un'API Web
In questo esempio verranno creati i criteri in base ai quali verrà richiesto agli utenti di eseguire l'autenticazione con una frequenza minore e verrà aumentato il tempo di inattività consentito prima di dover eseguire di nuovo l'autenticazione. Tali criteri verranno applicati all'API Web, in modo tale da essere applicati quando l'app nativa richiederà tale API come risorsa.

#### <a name="1-create-a-token-lifetime-policy"></a>1. Creare i criteri per la durata dei token. 

Questo comando consente di creare criteri rigidi per un'API Web. 

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
```

Per visualizzare i nuovi criteri e ottenere il relativo ObjectID, eseguire il comando indicato di seguito.

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-assign-the-policy-to-your-web-api"></a>2. Assegnare i criteri all'API Web.

I nuovi criteri verranno collegati a un'applicazione  e sarà anche necessario poter accedere a **ObjectId** di tale applicazione. Il modo migliore per trovare l'elemento **ObjectId** dell'app è usando il [portale di Azure](https://portal.azure.com/). 

Dopo aver recuperato l'elemento **ObjectId**, eseguire il comando indicato di seguito.

```PowerShell
Add-AzureADApplicationPolicy -ObjectId <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
```

#### <a name="3-youre-done"></a>3. Operazione completata 

### <a name="sample-managing-an-advanced-policy"></a>Esempio. Gestione di criteri avanzati
In questo esempio verranno creati alcuni criteri che consentiranno di dimostrare come funziona il sistema di priorità e come è possibile gestire più criteri applicati a diversi oggetti. Verranno fornite informazioni sulla priorità dei criteri descritti in precedenza e verranno indicati dettagli utili per gestire scenari più complicati. 

#### <a name="1-create-a-token-lifetime-policy"></a>1. Creare i criteri per la durata dei token.

Questa operazione è abbastanza semplice. Sono stati creati i criteri predefiniti di un'organizzazione, in base ai quali la durata dei token di aggiornamento a fattore singolo è impostata su 30 giorni. 

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
```

Per visualizzare i nuovi criteri e ottenere il relativo ObjectID, eseguire il comando indicato di seguito.

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-assign-the-policy-to-a-service-principal"></a>2. Assegnare i criteri a un'entità servizio.

Sono ora disponibili criteri per l'intera organizzazione.  Si supponga di voler conservare tali criteri della durata di 30 giorni per un'entità servizio specifica, impostando però i criteri predefiniti dell'organizzazione sul valore limite superiore, ovvero "until-revoked" (fino a revoca). 

Come prima operazione, i nuovi criteri verranno collegati a un'entità servizio  e sarà anche necessario poter accedere a **ObjectId** di tale entità. Per visualizzare tutte le entità servizio dell'organizzazione, è possibile eseguire una query su [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) o passare allo [strumento Graph Explorer](https://graphexplorer.cloudapp.net/) e accedere all'account Azure AD. 

Dopo aver recuperato l'elemento **ObjectId**, eseguire il comando indicato di seguito.

```PowerShell
Add-AzureADServicePrincipalPolicy -ObjectId <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
```

#### <a name="3-set-the-isorganizationdefault-flag-to-false"></a>3. Impostare il flag IsOrganizationDefault su false.

```PowerShell
Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
```

#### <a name="4-create-a-new-organization-default-policy"></a>4. Creare nuovi criteri predefiniti di un'organizzazione.

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
```

#### <a name="5-youre-done"></a>5. Operazione completata 

I criteri originali sono ora collegati all'entità servizio e i nuovi criteri sono impostati come criteri predefiniti dell'organizzazione.  È importante ricordare che i criteri applicati alle entità servizio hanno priorità rispetto a quelli predefiniti dell'organizzazione. 

## <a name="cmdlet-reference"></a>Informazioni di riferimento sui cmdlet

### <a name="manage-policies"></a>Gestire i criteri

I cmdlet riportati di seguito possono essere usati per gestire i criteri.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Crea nuovi criteri.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type> 
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matrice dell'oggetto stringified JSON contenente tutte le regole dei criteri. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Stringa relativa al nome dei criteri |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Se true, imposta i criteri come criteri predefiniti dell'organizzazione, se false non esegue alcuna operazione |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Tipo di criteri. Per la durata del token usare sempre "TokenLifetimePolicy" | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Facoltativo] |Imposta un ID alternativo per i criteri. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Ottiene tutti i criteri AzureAD o i criteri specificati 

```PowerShell
Get-AzureADPolicy 
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code>[Facoltativo] |ObjectID dei criteri da ottenere. |`-ObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Ottiene tutte le app e le entità servizio collegate a criteri specifici

```PowerShell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of Policy> 
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |ObjectID dei criteri da ottenere. |`-ObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aggiorna i criteri esistenti

```PowerShell
Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName <string> 
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |ObjectID dei criteri da ottenere. |`-ObjectId <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Stringa relativa al nome dei criteri |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Facoltativo] |Matrice dell'oggetto stringified JSON contenente tutte le regole dei criteri. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Facoltativo] |Se true, imposta i criteri come criteri predefiniti dell'organizzazione, se false non esegue alcuna operazione |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Facoltativo] |Tipo di criteri. Per la durata del token usare sempre "TokenLifetimePolicy" |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Facoltativo] |Imposta un ID alternativo per i criteri. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Elimina i criteri specificati

```PowerShell
 Remove-AzureADPolicy -ObjectId <ObjectId of Policy>
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |ObjectID dei criteri da ottenere. | `-ObjectId <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Criteri dell'applicazione
I cmdlet riportati di seguito possono essere usati per i criteri dell'applicazione.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Collega i criteri specificati a un'applicazione

```PowerShell
Add-AzureADApplicationPolicy -ObjectId <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |ObjectID dell'applicazione. | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |ObjectID dei criteri. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Ottiene i criteri assegnati a un'applicazione

```PowerShell
Get-AzureADApplicationPolicy -ObjectId <ObjectId of Application>
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |ObjectID dell'applicazione. | `-ObjectId <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Rimuove i criteri da un'applicazione

```PowerShell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |ObjectID dell'applicazione. | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |ObjectID dei criteri. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Criteri dell'entità servizio
I cmdlet riportati di seguito possono essere usati per i criteri dell'entità servizio.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Collega i criteri specificati a un'entità servizio

```PowerShell
Add-AzureADServicePrincipalPolicy -ObjectId <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |ObjectID dell'applicazione. | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |ObjectID dei criteri. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Ottiene i criteri collegati all'entità servizio specificata

```PowerShell
Get-AzureADServicePrincipalPolicy -ObjectId <ObjectId of ServicePrincipal>
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |ObjectID dell'applicazione. | `-ObjectId <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Rimuove i criteri dall'entità servizio specificata

```PowerShell
Remove-AzureADServicePrincipalPolicy -ObjectId <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametri | Descrizione | Esempio |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |ObjectID dell'applicazione. | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |ObjectID dei criteri. | `-PolicyId <ObjectId of Policy>` |



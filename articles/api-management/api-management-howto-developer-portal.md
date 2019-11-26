---
title: Panoramica del portale per sviluppatori di gestione API di Azure-gestione API di Azure | Microsoft Docs
description: Informazioni sul portale per sviluppatori in gestione API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/22/2019
ms.author: apimpm
ms.openlocfilehash: 2b69fdd7abefca360433fc9fb090569cba23febe
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454398"
---
# <a name="azure-api-management-developer-portal-overview"></a>Panoramica del portale per sviluppatori di gestione API di Azure

Il portale per sviluppatori è un sito Web completamente personalizzabile e completamente personalizzabile con la documentazione delle API. Si tratta del punto in cui i consumer di API possono individuare le API, informazioni su come usarle, richiedere l'accesso e provarle.

Questo articolo descrive le differenze tra le versioni Self-Hosted e quelle gestite del portale per sviluppatori in gestione API. Viene inoltre illustrata l'architettura e vengono fornite le risposte alle domande più frequenti.

> [!WARNING]
>
> [Informazioni su come eseguire la migrazione dalla versione di anteprima alla versione disponibile a livello generale](#preview-to-ga) del portale per sviluppatori.

![Portale per sviluppatori di Gestione API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Versioni gestite e self-hosted

È possibile creare il portale per sviluppatori in due modi:

- **Versione gestita** : la modifica e la personalizzazione del portale, che è incorporata nell'istanza di gestione API ed è accessibile tramite l'URL `<your-api-management-instance-name>.developer.azure-api.net`. Per informazioni su come accedere e personalizzare il portale gestito, vedere [questo articolo della documentazione](api-management-howto-developer-portal-customize.md) .
- **Versione self-hosted** : tramite la distribuzione e l'hosting automatico del portale all'esterno di un'istanza di gestione API. Questo approccio consente di modificare la codebase del portale ed estendere la funzionalità di base fornita. È anche necessario aggiornare il portale alla versione più recente. Per informazioni dettagliate e istruzioni, vedere il [repository GitHub con il codice sorgente del portale][1]. L' [esercitazione per la versione gestita](api-management-howto-developer-portal-customize.md) scorre il pannello amministrativo del portale, disponibile anche nella versione self-hosted.

## <a name="portal-architectural-concepts"></a>Concetti relativi all'architettura del portale

I componenti del portale possono essere divisi logicamente in due categorie: *codice* e *contenuto*.

Il *codice* viene mantenuto nel [repository GitHub][1] e include:

- Widget, che rappresentano elementi visivi e combinano HTML, JavaScript, funzionalità di stile, impostazioni e mapping del contenuto. Gli esempi sono un'immagine, un paragrafo di testo, un modulo, un elenco di API e così via.
- Definizione dello stile, che specifica il modo in cui i widget possono essere con stile
- Motore, che genera pagine Web statiche dal contenuto del portale ed è scritto in JavaScript
- Editor visivo, che consente la personalizzazione e la creazione in browser

Il *contenuto* è suddiviso in due sottocategorie: contenuto del *portale* e *contenuto di gestione API*.

Il *contenuto del portale* è specifico del portale e include:

- Pagine, ad esempio pagina di destinazione, esercitazioni sulle API, post di Blog
- Immagini multimediali, animazioni e altro contenuto basato su file
- Layout: modelli, che corrispondono a un URL e definiscono la modalità di visualizzazione delle pagine
- Stili-valori per le definizioni di stile, ad esempio tipi di carattere, colori e bordi
- Impostazioni-configurazione, ad esempio favicon, metadati del sito Web

Il *contenuto del portale*, ad eccezione dei supporti, è espresso come documenti JSON.

Il *contenuto di gestione API* include entità quali API, operazioni, prodotti e sottoscrizioni.

Il portale è basato su un fork adattato del [Framework Paperbits](https://paperbits.io/). La funzionalità Paperbits originale è stata estesa per fornire widget specifici di gestione API (ad esempio, un elenco di API, un elenco di prodotti) e un connettore al servizio gestione API per il salvataggio e il recupero del contenuto.

## <a name="faq"></a>Domande frequenti

In questa sezione vengono riportate le risposte alle domande comuni sul nuovo portale per sviluppatori, che sono di natura generale. Per domande specifiche per la versione self-hosted, vedere [la sezione wiki del repository GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> come è possibile eseguire la migrazione dalla versione di anteprima del portale?

Usando la versione di anteprima del portale per sviluppatori, è stato effettuato il provisioning del contenuto di anteprima nel servizio gestione API. Il contenuto predefinito è stato modificato in modo significativo nella versione disponibile a livello generale per migliorare l'esperienza utente. Include anche nuovi widget.

Se si usa la versione gestita, reimpostare il contenuto del portale facendo clic su **Reimposta contenuto** nella sezione del menu **operazioni** . Se si conferma questa operazione, verrà rimosso tutto il contenuto del portale ed eseguito il provisioning del nuovo contenuto predefinito. Il motore del portale è stato aggiornato automaticamente nel servizio gestione API.

![Reimposta contenuto portale](media/api-management-howto-developer-portal/reset-content.png)

Se si usa la versione self-hosted, usare il `scripts/cleanup.bat` e `scripts/generate.bat` dal repository GitHub per rimuovere il contenuto esistente ed effettuare il provisioning di nuovo contenuto. Assicurarsi di aggiornare il codice del portale alla versione più recente dal repository GitHub in anticipo.

Se non si vuole reimpostare il contenuto del portale, è possibile prendere in considerazione l'uso di widget appena disponibili in tutte le pagine. I widget esistenti sono stati aggiornati automaticamente alle versioni più recenti.

Se il provisioning del portale è stato eseguito dopo l'annuncio della disponibilità generale, dovrebbe già presentare il nuovo contenuto predefinito. Non è richiesta alcuna azione da parte dell'utente.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Come è possibile eseguire la migrazione dal portale per sviluppatori precedente al nuovo portale per sviluppatori?

I portali sono incompatibili ed è necessario eseguire la migrazione manuale del contenuto.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Il nuovo portale dispone di tutte le funzionalità del vecchio portale?

Il nuovo portale per sviluppatori non supporta *applicazioni* e *problemi*. Se sono stati usati *problemi* nel portale precedente e ne sono necessari in una nuova, pubblicare un commento in [un problema di GitHub dedicato](https://github.com/Azure/api-management-developer-portal/issues/122).

L'autenticazione con OAuth nella console per sviluppatori interattiva non è ancora supportata. È possibile tenere traccia dello stato di avanzamento tramite [il problema di GitHub](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Il portale precedente è stato deprecato?

I portali Developer e Publisher precedenti sono ora funzionalità *legacy* , che riceveranno solo gli aggiornamenti della sicurezza. Le nuove funzionalità verranno implementate solo nel nuovo portale per sviluppatori.

La deprecazione dei portali legacy verrà annunciata separatamente. In caso di domande, problemi o commenti, è possibile generarli [in un problema dedicato di GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="how-can-i-automate-portal-deployments"></a>Come è possibile automatizzare le distribuzioni del portale?

È possibile accedere a livello di codice e gestire il contenuto del portale per sviluppatori tramite l'API REST, indipendentemente dal fatto che si usi una versione gestita o self-hosted.

L'API è documentata nella [sezione wiki del repository GitHub][2]. Può anche essere usato per automatizzare le migrazioni di contenuto del portale tra ambienti, ad esempio da un ambiente di test all'ambiente di produzione. Altre informazioni su questo processo sono disponibili [in questo articolo della documentazione](https://aka.ms/apimdocs/migrateportal) su GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Il portale supporta i modelli di Azure Resource Manager e/o è compatibile con gestione API DevOps Resource Kit?

No.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-new-managed-portal-dependencies"></a>È necessario abilitare la connettività VNet aggiuntiva per le nuove dipendenze del portale gestito?

Nella maggior parte dei casi-no.

Se il servizio gestione API si trova in una VNet interna, il portale per sviluppatori è accessibile solo dall'interno della rete. Il nome host dell'endpoint di gestione deve essere risolto nell'indirizzo VIP interno del servizio dal computer usato per accedere all'interfaccia amministrativa del portale. Assicurarsi che l'endpoint di gestione sia registrato nel DNS. In caso di errata configurazione, verrà visualizzato un errore: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Ho assegnato un dominio personalizzato di gestione API e il portale pubblicato non funziona

Dopo aver aggiornato il dominio, per rendere effettive le modifiche è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) .

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>È stato aggiunto un provider di identità e non è possibile visualizzarlo nel portale

Dopo aver configurato un provider di identità (ad esempio, AAD, AAD B2C), è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) per rendere effettive le modifiche.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Ho configurato la delega e il portale non lo usa

Dopo aver configurato la delega, è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) per rendere effettive le modifiche.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Le altre modifiche di configurazione di gestione API non sono state propagate nel portale per sviluppatori

Per la maggior parte delle modifiche di configurazione, ad esempio VNet, accesso e termini del prodotto, è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a>Viene ricevuto un errore CORS quando si usa la console interattiva

La console interattiva esegue una richiesta API sul lato client dal browser. È possibile risolvere il problema CORS aggiungendo [un criterio CORS](api-management-cross-domain-policies.md#CORS) sulle API. È possibile specificare tutti i parametri manualmente o usare i valori dei caratteri jolly `*`. Ad esempio:

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

> [!NOTE]
> 
> Se si applicano i criteri CORS nell'ambito del prodotto, invece dell'ambito delle API e l'API usa l'autenticazione con chiave di sottoscrizione tramite un'intestazione, la console non funzionerà.
>
> Il browser rilascia automaticamente una richiesta HTTP OPTIONS, che non contiene un'intestazione con la chiave di sottoscrizione. A causa della chiave di sottoscrizione mancante, gestione API non può associare la chiamata OPTIONS a un prodotto, quindi non può applicare i criteri CORS.
>
> Come soluzione alternativa è possibile passare la chiave della sottoscrizione in un parametro di query.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quali autorizzazioni sono necessarie per modificare il portale per sviluppatori?

Se viene visualizzato l'errore `Oops. Something went wrong. Please try again later.` quando si apre il portale in modalità amministrativa, potrebbero mancare le autorizzazioni necessarie (RBAC).

I portali legacy hanno richiesto l'autorizzazione `Microsoft.ApiManagement/service/getssotoken/action` nell'ambito del servizio (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) per consentire all'amministratore utente di accedere ai portali. Il nuovo portale richiede l'autorizzazione `Microsoft.ApiManagement/service/users/token/action` nell'ambito `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`.

È possibile usare lo script di PowerShell seguente per creare un ruolo con l'autorizzazione necessaria. Ricordarsi di modificare il parametro `<subscription-id>`. 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Una volta creato, il ruolo può essere concesso a qualsiasi utente dalla sezione **controllo di accesso (IAM)** nel portale di Azure. Assegnando questo ruolo a un utente, verrà assegnata l'autorizzazione nell'ambito del servizio. L'utente sarà in grado di generare token SAS per conto di *qualsiasi* utente nel servizio. Come minimo, questo ruolo deve essere assegnato all'amministratore del servizio. Il comando di PowerShell seguente illustra come assegnare il ruolo a un utente `user1` nell'ambito più basso per evitare di concedere autorizzazioni non necessarie all'utente: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Dopo che le autorizzazioni sono state concesse a un utente, l'utente deve disconnettersi e accedere di nuovo al portale di Azure per rendere effettive le nuove autorizzazioni.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Viene visualizzato l'errore `Unable to start the portal. See if settings are specified correctly (...)`

Questo errore viene visualizzato quando una chiamata `GET` `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` ha esito negativo. La chiamata viene eseguita dal browser dall'interfaccia amministrativa del portale.

Se il servizio gestione API si trova in una VNet, vedere la domanda di connettività VNet precedente.

L'errore di chiamata può anche essere causato da un certificato SSL, che viene assegnato a un dominio personalizzato e non è considerato attendibile dal browser. Come mitigazione, è possibile rimuovere l'endpoint di gestione. gestione API del dominio personalizzato esegue il fallback all'endpoint predefinito con un certificato attendibile.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul nuovo portale per sviluppatori:

- [Accedere e personalizzare il portale per sviluppatori gestiti](api-management-howto-developer-portal-customize.md)
- [Configurare la versione self-hosted del portale][2]

Esplora altre risorse:

- [Repository GitHub con il codice sorgente][1]
- [Roadmap pubblica del progetto][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects
---
title: Panoramica del portale per sviluppatori di gestione API di Azure
titleSuffix: Azure API Management
description: Informazioni sul portale per sviluppatori in gestione API. Il portale per sviluppatori è il punto in cui gli utenti possono trovare le API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/28/2020
ms.author: apimpm
ms.openlocfilehash: 068f15cc06a76412836176dc13237741ea14d135
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614051"
---
# <a name="azure-api-management-developer-portal-overview"></a>Panoramica del portale per sviluppatori di Gestione API

Il portale per sviluppatori è un sito Web completamente personalizzabile e completamente personalizzabile con la documentazione delle API. Si tratta del punto in cui i consumer di API possono individuare le API, informazioni su come usarle, richiedere l'accesso e provarle.

Questo articolo descrive le differenze tra le versioni Self-Hosted e quelle gestite del portale per sviluppatori in gestione API. Viene inoltre illustrata l'architettura e vengono fornite le risposte alle domande più frequenti.

![Portale per sviluppatori di Gestione API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> <a name="migrate-from-legacy"></a> Il nuovo portale per sviluppatori non è compatibile con il portale per sviluppatori Legacy e la migrazione automatica non è possibile. È necessario ricreare manualmente il contenuto (pagine, testo, file multimediali) e personalizzare l'aspetto del nuovo portale. Per istruzioni, vedere [l'esercitazione sul portale per sviluppatori](api-management-howto-developer-portal-customize.md) .

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a> Versioni gestite e self-hosted

È possibile creare il portale per sviluppatori in due modi:

- **Versione gestita** : modifica e personalizzazione del portale, integrato nell'istanza di gestione API, accessibile tramite l'URL `<your-api-management-instance-name>.developer.azure-api.net` . Per informazioni su come accedere e personalizzare il portale gestito, vedere [questo articolo della documentazione](api-management-howto-developer-portal-customize.md) .
- **Versione self-hosted** : tramite la distribuzione e l'hosting automatico del portale all'esterno di un'istanza di gestione API. Questo approccio consente di modificare la codebase del portale ed estendere la funzionalità di base fornita, ad esempio implementare widget personalizzati per l'integrazione con sistemi di terze parti. In questo scenario si è il gestore del portale e si è responsabili dell'aggiornamento del portale alla versione più recente. Per informazioni dettagliate e istruzioni, vedere il [repository GitHub con il codice sorgente del portale][1] e [l'esercitazione sull'implementazione di un widget][3]. L' [esercitazione per la versione gestita](api-management-howto-developer-portal-customize.md) scorre il pannello amministrativo del portale, che è comune per le versioni gestite e indipendenti.

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

## <a name="frequently-asked-questions"></a><a name="faq"></a> Domande frequenti

In questa sezione vengono riportate le risposte alle domande comuni sul portale per sviluppatori, che sono di natura generale. Per domande specifiche per la versione self-hosted, vedere [la sezione wiki del repository GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Come è possibile eseguire la migrazione dalla versione di anteprima del portale?

Quando è stata avviata per la prima volta la versione di anteprima del portale per sviluppatori, è stato effettuato il provisioning della versione di anteprima del contenuto predefinito nel servizio gestione API. Il contenuto predefinito è stato modificato in modo significativo nella versione disponibile a livello generale. Ad esempio, la versione di anteprima del contenuto predefinito non include i pulsanti OAuth nelle pagine di accesso, USA widget diversi per la visualizzazione delle API e si basa su funzionalità limitate per la strutturazione delle pagine del portale per sviluppatori. Anche se sono presenti differenze nel contenuto, il motore del portale (inclusi i widget sottostanti) viene automaticamente aggiornato ogni volta che si pubblica il portale per sviluppatori.

Se il portale è stato personalizzato in modo significativo in base alla versione di anteprima del contenuto, è possibile continuare a usarlo così com'è e posizionare manualmente i nuovi widget nelle pagine del portale. In caso contrario, è consigliabile sostituire il contenuto del portale con il nuovo contenuto predefinito.

Per reimpostare il contenuto in un portale gestito, fare clic su **Reimposta contenuto** nella sezione del menu **operazioni** . Questa operazione rimuoverà tutto il contenuto del portale ed effettuerà il provisioning del nuovo contenuto predefinito. Tutte le personalizzazioni e le modifiche del portale per sviluppatori andranno perse. **Non è possibile annullare questa azione**.

![Reimposta contenuto portale](media/api-management-howto-developer-portal/reset-content.png)

Se si usa la versione self-hosted, eseguire `scripts.v2/cleanup.bat` gli `scripts.v2/generate.bat` script e dal repository GitHub per rimuovere il contenuto esistente ed effettuare il provisioning di nuovo contenuto. Assicurarsi di aggiornare il codice del portale alla versione più recente dal repository GitHub in anticipo.

Se si accede per la prima volta al portale dopo l'annuncio relativo alla disponibilità generale nel 2019 novembre, il nuovo contenuto predefinito dovrebbe essere già disponibile e non è necessaria alcuna azione aggiuntiva.

### <a name="does-the-portal-have-all-the-features-of-the-legacy-portal"></a>Il portale ha tutte le funzionalità del portale legacy?

Il portale per sviluppatori non supporta più *le applicazioni*, i *problemi*e l'integrazione diretta con Facebook, Microsoft, Twitter e Google come provider di identità. è invece possibile usare Azure ad B2C.

### <a name="has-the-legacy-portal-been-deprecated"></a>Il portale legacy è stato deprecato?

I portali legacy per sviluppatori e Publisher sono ora funzionalità *legacy* , che riceveranno solo gli aggiornamenti della sicurezza. Le nuove funzionalità verranno implementate solo nel nuovo portale per sviluppatori.

La deprecazione dei portali legacy verrà annunciata separatamente. In caso di domande, problemi o commenti, è possibile generarli [in un problema dedicato di GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>La funzionalità richiesta non è supportata nel portale

È possibile aprire una [richiesta di funzionalità](https://aka.ms/apimwish) o [implementare manualmente la funzionalità mancante][3]. Se si implementa la funzionalità autonomamente, è possibile ospitare il portale per sviluppatori o aprire una richiesta pull in GitHub per includere le modifiche nella versione gestita.

### <a name="how-can-i-automate-portal-deployments"></a>Come è possibile automatizzare le distribuzioni del portale?

È possibile accedere a livello di codice e gestire il contenuto del portale per sviluppatori tramite l'API REST, indipendentemente dal fatto che si usi una versione gestita o self-hosted.

L'API è documentata nella [sezione wiki del repository GitHub][2]. Può essere usato per automatizzare le migrazioni di contenuto del portale tra ambienti, ad esempio da un ambiente di test all'ambiente di produzione. Altre informazioni su questo processo sono disponibili [in questo articolo della documentazione](https://aka.ms/apimdocs/migrateportal) su GitHub.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Ricerca per categorie passare dalla versione gestita alla versione self-hosted?

Vedere l'articolo dettagliato nella [sezione wiki del repository del portale per sviluppatori su GitHub][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>È possibile avere più portali per sviluppatori in un servizio gestione API?

È possibile avere un portale gestito e più portali self-hosted. Il contenuto di tutti i portali viene archiviato nello stesso servizio gestione API, quindi sarà identico. Se si desidera differenziare l'aspetto e la funzionalità dei portali, è possibile ospitarli autonomamente con widget personalizzati che consentono di personalizzare in modo dinamico le pagine in fase di esecuzione, ad esempio in base all'URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Il portale supporta i modelli di Azure Resource Manager e/o è compatibile con gestione API DevOps Resource Kit?

No.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Il contenuto del portale è salvato con la funzionalità di backup/ripristino in gestione API?

No.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>È necessario abilitare la connettività VNet aggiuntiva per le dipendenze del portale gestito?

Nella maggior parte dei casi-no.

Se il servizio gestione API si trova in una VNet interna, il portale per sviluppatori è accessibile solo dall'interno della rete. Il nome host dell'endpoint di gestione deve essere risolto nell'indirizzo VIP interno del servizio dal computer usato per accedere all'interfaccia amministrativa del portale. Assicurarsi che l'endpoint di gestione sia registrato nel DNS. In caso di errata configurazione, verrà visualizzato un errore: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Se il servizio gestione API si trova in un VNet interno e si accede tramite il gateway applicazione da Internet, assicurarsi di abilitare la connettività al portale per sviluppatori e agli endpoint di gestione di gestione API.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Ho assegnato un dominio personalizzato di gestione API e il portale pubblicato non funziona

Dopo aver aggiornato il dominio, per rendere effettive le modifiche è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) .

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>È stato aggiunto un provider di identità e non è possibile visualizzarlo nel portale

Dopo aver configurato un provider di identità (ad esempio, AAD, AAD B2C), è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) per rendere effettive le modifiche.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Ho configurato la delega e il portale non lo usa

Dopo aver configurato la delega, è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) per rendere effettive le modifiche.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Le altre modifiche di configurazione di gestione API non sono state propagate nel portale per sviluppatori

Per la maggior parte delle modifiche di configurazione, ad esempio VNet, accesso e termini del prodotto, è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Viene ricevuto un errore CORS quando si usa la console interattiva

La console interattiva esegue una richiesta API sul lato client dal browser. Risolvere il problema CORS aggiungendo [un criterio CORS](api-management-cross-domain-policies.md#CORS) sulle API.

È possibile controllare lo stato del criterio CORS nella sezione **Panoramica del portale** del servizio gestione API nella portale di Azure. Una casella di avviso indica un criterio assente o non configurato correttamente.

![Portale per sviluppatori di Gestione API](media/api-management-howto-developer-portal/cors-azure-portal.png)

Applicare automaticamente i criteri di CORS facendo clic sul pulsante **Abilita CORS** .

È anche possibile abilitare CORS manualmente.

1. Fare clic su **applica manualmente** il collegamento a livello globale per visualizzare il codice dei criteri generato.
2. Passare a **tutte le API** nella sezione **API** del servizio gestione API nel portale di Azure.
3. Fare clic sull' **</>** icona nella sezione **elaborazione in ingresso** .
4. Inserire i criteri nella **<inbound>** sezione del file XML. Verificare che il **<origin>** valore corrisponda al dominio del portale per sviluppatori.

> [!NOTE]
> 
> Se si applicano i criteri CORS nell'ambito del prodotto, invece dell'ambito delle API e l'API usa l'autenticazione con chiave di sottoscrizione tramite un'intestazione, la console non funzionerà.
>
> Il browser rilascia automaticamente una richiesta HTTP OPTIONS, che non contiene un'intestazione con la chiave di sottoscrizione. A causa della chiave di sottoscrizione mancante, gestione API non può associare la chiamata OPTIONS a un prodotto, quindi non può applicare i criteri CORS.
>
> Come soluzione alternativa è possibile passare la chiave della sottoscrizione in un parametro di query.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quali autorizzazioni sono necessarie per modificare il portale per sviluppatori?

Se l'errore viene visualizzato `Oops. Something went wrong. Please try again later.` quando si apre il portale in modalità amministrativa, potrebbero mancare le autorizzazioni necessarie (RBAC).

I portali legacy hanno richiesto l'autorizzazione nell' `Microsoft.ApiManagement/service/getssotoken/action` ambito del servizio ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ) per consentire all'amministratore utente di accedere ai portali. Il nuovo portale richiede l'autorizzazione `Microsoft.ApiManagement/service/users/token/action` per l'ambito `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

È possibile usare lo script di PowerShell seguente per creare un ruolo con l'autorizzazione necessaria. Ricordarsi di modificare il `<subscription-id>` parametro. 

```powershell
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
 
Una volta creato, il ruolo può essere concesso a qualsiasi utente dalla sezione **controllo di accesso (IAM)** nel portale di Azure. Assegnando questo ruolo a un utente, verrà assegnata l'autorizzazione nell'ambito del servizio. L'utente sarà in grado di generare token SAS per conto di *qualsiasi* utente nel servizio. Come minimo, questo ruolo deve essere assegnato all'amministratore del servizio. Il comando di PowerShell seguente illustra come assegnare il ruolo a un utente nell' `user1` ambito più basso per evitare di concedere autorizzazioni non necessarie all'utente: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Dopo che le autorizzazioni sono state concesse a un utente, l'utente deve disconnettersi e accedere di nuovo al portale di Azure per rendere effettive le nuove autorizzazioni.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Viene visualizzato l' `Unable to start the portal. See if settings are specified correctly (...)` errore

Questo errore viene visualizzato quando una `GET` chiamata a ha `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` esito negativo. La chiamata viene eseguita dal browser dall'interfaccia amministrativa del portale.

Se il servizio gestione API si trova in una VNet, vedere la domanda di connettività VNet precedente.

L'errore di chiamata può anche essere causato da un certificato TLS/SSL, che è assegnato a un dominio personalizzato e non è considerato attendibile dal browser. Come mitigazione, è possibile rimuovere l'endpoint di gestione. gestione API del dominio personalizzato esegue il fallback all'endpoint predefinito con un certificato attendibile.

### <a name="whats-the-browser-support-for-the-portal"></a>Qual è il supporto del browser per il portale?

| Browser                     | Funzionalità supportata       |
|-----------------------------|-----------------|
| Apple Safari                | Sì<sup>1</sup> |
| Google Chrome               | Sì<sup>1</sup> |
| Microsoft Edge              | Sì<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Sì<sup>1</sup> |

 <small><sup>1</sup> supportato nelle due versioni di produzione più recenti.</small>

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul nuovo portale per sviluppatori:

- [Accedere e personalizzare il portale per sviluppatori gestiti](api-management-howto-developer-portal-customize.md)
- [Configurare la versione self-hosted del portale][2]
- [Implementare il proprio widget][3]

Esplora altre risorse:

- [Repository GitHub con il codice sorgente][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend

---
title: Panoramica del portale per sviluppatori di Gestione API di AzureOverview of Azure API Management developer portal
titleSuffix: Azure API Management
description: Informazioni sul portale per sviluppatori in Gestione API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335902"
---
# <a name="azure-api-management-developer-portal-overview"></a>Panoramica del portale per sviluppatori di Gestione API

Il portale per sviluppatori è un sito Web generato automaticamente e completamente personalizzabile con la documentazione delle API. È qui che i consumer di API possono individuare le API, imparare a usarle, richiedere l'accesso e provarle.

In questo articolo vengono descritte le differenze tra le versioni self-hosted e gestite del portale per sviluppatori in Gestione API. Spiega anche la sua architettura e fornisce risposte alle domande più frequenti.

![Portale per sviluppatori di Gestione API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Versioni gestite e self-hosted

È possibile creare il portale per sviluppatori in due modi:You can build your developer portal in two ways:

- **Versione gestita:** modificando e personalizzando il portale, che è incorporato nell'istanza di Gestione API ed è accessibile tramite l'URL `<your-api-management-instance-name>.developer.azure-api.net`. Fare riferimento a questo articolo della [documentazione](api-management-howto-developer-portal-customize.md) per informazioni su come accedere al portale gestito e personalizzarlo.
- Versione ospitata in modalità **self-hosted:** distribuzione e hosting autonomo del portale all'esterno di un'istanza di Gestione API. Questo approccio consente di modificare la codebase del portale ed estendere le funzionalità di base fornite, ad esempio implementare widget personalizzati per le integrazioni con sistemi di terze parti. In questo scenario, si è il manutentore del portale e si è responsabili per l'aggiornamento del portale alla versione più recente. Per informazioni dettagliate e istruzioni, fare riferimento al [repository GitHub con il codice sorgente del portale][1] e [l'esercitazione sull'implementazione][3]di un widget . [L'esercitazione per la versione gestita](api-management-howto-developer-portal-customize.md) illustra il pannello amministrativo del portale, comune per le versioni gestite e self-hosted.

## <a name="portal-architectural-concepts"></a>Concetti architettonici del portale

I componenti del portale possono essere suddivisi logicamente in due categorie: *codice* e *contenuto*.

*Il codice* viene mantenuto nel repository GitHub e include:Code is maintained in [the GitHub repository][1] and includes:

- Widget: che rappresentano elementi visivi e combinano HTML, JavaScript, capacità di stile, impostazioni e mappatura dei contenuti. Esempi sono un'immagine, un paragrafo di testo, un modulo, un elenco di API e così via.
- Definizioni di stile - che specificano la modalità di applicazione dello stile dei widget
- Motore - che genera pagine Web statiche dal contenuto del portale ed è scritto in JavaScript
- Editor visivo- che consente la personalizzazione all'altro del browser e l'esperienza di creazione

*Il contenuto* è suddiviso in due sottocategorie: *contenuto del portale* e Contenuto di Gestione *API.*

*Il contenuto* del portale è specifico del portale e include:

- Pagine, ad esempio pagina di destinazione, esercitazioni API, post di blog
- Elementi multimediali: immagini, animazioni e altri contenuti basati su file
- Layout: modelli corrispondenti a un URL e che definiscono la modalità di visualizzazione delle pagine
- Stili - valori per le definizioni di stile, ad esempio font, colori, bordi
- Impostazioni - configurazione, ad esempio favicon, metadati del sito Web

*Il contenuto del portale,* ad eccezione dei supporti, viene espresso come documenti JSON.

*Il contenuto di Gestione API* include entità quali API, Operazioni, Prodotti, Sottoscrizioni.API Management content includes entities such as APIs, Operations, Products, Subscriptions.

Il portale si basa su una forcella adattata del [quadro Paperbits.](https://paperbits.io/) La funzionalità Paperbits originale è stata estesa per fornire widget specifici di Gestione API (ad esempio, un elenco di API, un elenco di prodotti) e un connettore al servizio Gestione API per il salvataggio e il recupero del contenuto.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Domande frequenti

In questa sezione, rispondiamo a domande comuni sul portale per sviluppatori, che sono di natura generale. Per domande specifiche sulla versione self-hosted, fare riferimento [alla sezione wiki del repository GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>Come è possibile eseguire la migrazione dalla versione di anteprima del portale?

Utilizzando la versione di anteprima del portale per sviluppatori, è stato eseguito il provisioning del contenuto di anteprima nel servizio Gestione API. Il contenuto predefinito è stato modificato in modo significativo nella versione generalmente disponibile per una migliore esperienza utente. Include anche nuovi widget.

Se si usa la versione gestita, reimpostare il contenuto del portale facendo clic su **Reimposta contenuto** nella sezione **Menu Operazioni.** La conferma di questa operazione rimuoverà tutto il contenuto del portale ed eseguirà il provisioning del nuovo contenuto predefinito. Il motore del portale è stato aggiornato automaticamente nel servizio Gestione API.

![Reimpostare il contenuto del portale](media/api-management-howto-developer-portal/reset-content.png)

Se si usa la versione self-hosted, usare `scripts/cleanup.bat` e `scripts/generate.bat` dal repository GitHub per rimuovere il contenuto esistente ed eseguire il provisioning di nuovo contenuto. Assicurarsi di aggiornare in anticipo il codice del portale alla versione più recente dal repository GitHub.

Se non si desidera reimpostare il contenuto del portale, è possibile utilizzare i widget appena disponibili in tutte le pagine. I widget esistenti sono stati aggiornati automaticamente alle versioni più recenti.

Se è stato eseguito il provisioning del portale dopo l'annuncio di disponibilità generale, dovrebbe già presentare il nuovo contenuto predefinito. Non è richiesta alcuna azione da parte tua.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Come è possibile eseguire la migrazione dal portale per sviluppatori precedente al portale per sviluppatori?

I portali sono incompatibili ed è necessario eseguire manualmente la migrazione del contenuto.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Il portale ha tutte le caratteristiche del vecchio portale?

Il portale per sviluppatori non supporta più *applicazioni* e *problemi*.

L'autenticazione con OAuth nella console di sviluppo interattiva non è ancora supportata. È possibile tenere traccia dello stato di avanzamento tramite [il problema GitHub](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Il vecchio portale è stato deprecato?

I portali di pubblicazione e di pubblicazione precedenti sono ora funzionalità *legacy,* che riceveranno solo gli aggiornamenti della sicurezza. Le nuove funzionalità verranno implementate solo nel nuovo portale per sviluppatori.

La deprecazione dei portali legacy verrà annunciata separatamente. Se hai domande, dubbi o commenti, sollevali in un problema dedicato di [GitHub.](https://github.com/Azure/api-management-developer-portal/issues/121)

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>La funzionalità necessaria non è supportata nel portale

È possibile aprire una [richiesta di funzionalità](https://aka.ms/apimwish) o [implementare manualmente la funzionalità mancante.][3] Se si implementa la funzionalità manualmente, è possibile ospitare autonomamente il portale per sviluppatori o aprire una richiesta pull su GitHub per includere le modifiche nella versione gestita.

### <a name="how-can-i-automate-portal-deployments"></a>Come è possibile automatizzare le distribuzioni del portale?

È possibile accedere e gestire il contenuto del portale per sviluppatori a livello di programmazione tramite l'API REST, indipendentemente dal fatto che si usi una versione gestita o self-hosted.

L'API è documentata nella [sezione wiki del repository GitHub.][2] Può essere utilizzato per automatizzare le migrazioni del contenuto del portale tra ambienti, ad esempio da un ambiente di test all'ambiente di produzione. Per altre informazioni su questo processo, vedere questo articolo della [documentazione](https://aka.ms/apimdocs/migrateportal) su GitHub.You can learn more about this process in this documentation article on GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Il portale supporta i modelli di Azure Resource Manager e/o è compatibile con il DevOps Resource Kit di Gestione API?

No.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>È necessario abilitare la connettività vNet aggiuntiva per le dipendenze del portale gestito?

Nella maggior parte dei casi - no.

Se il servizio Gestione API si trova in una rete virtuale interna, il portale per sviluppatori è accessibile solo dall'interno della rete. Il nome host dell'endpoint di gestione deve essere risolto nell'indirizzo VIP interno del servizio dal computer utilizzato per accedere all'interfaccia amministrativa del portale. Assicurarsi che l'endpoint di gestione sia registrato nel DNS. In caso di configurazione errata, verrà `Unable to start the portal. See if settings are specified correctly in the configuration (...)`visualizzato un errore: .

Se il servizio Gestione API si trova in una rete virtuale interna e si accede tramite Il gateway applicazione da Internet, assicurarsi di abilitare la connettività al portale per sviluppatori e agli endpoint di gestione di Gestione API.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Ho assegnato un dominio di Gestione API personalizzato e il portale pubblicato non funziona

Dopo aver aggiornato il dominio, è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) per rendere effettive le modifiche.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>È stato aggiunto un provider di identità e non è possibile visualizzarlo nel portale

Dopo aver configurato un provider di identità, ad esempio AAD, AAD B2C, è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) per rendere effettive le modifiche.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Ho impostato la delega e il portale non la usa

Dopo aver configurato la delega, è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) per rendere effettive le modifiche.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Le altre modifiche di configurazione di Gestione API non sono state propagate nel portale per sviluppatori

La maggior parte delle modifiche alla configurazione (ad esempio, rete virtuale, accesso e termini del prodotto) richiedono [la ripubblicazione del portale](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Viene visualizzato un errore CORS quando si utilizza la console interattiva

La console interattiva effettua una richiesta API lato client dal browser. Risolvere il problema CORS aggiungendo [un criterio CORS](api-management-cross-domain-policies.md#CORS) nelle API.

È possibile controllare lo stato dei criteri CORS nella sezione **Panoramica** del portale del servizio Gestione API nel portale di Azure.You can check the status of the CORS policy in the Portal overview section of your API Management service in the Azure portal. Una finestra di avviso indica un criterio assente o non configurato correttamente.

![Portale per sviluppatori di Gestione API](media/api-management-howto-developer-portal/cors-azure-portal.png)

Applicare automaticamente il criterio CORS facendo clic sul **pulsante Abilita CORS.**

È inoltre possibile abilitare CORS manualmente.

1. Fare clic sul collegamento **Applica manualmente sul livello globale** per visualizzare il codice dei criteri generato.
2. Passare a Tutte le API nella sezione API del servizio Gestione API nel portale di Azure.Navigate to **All APIs** in the **APIs** section of your API Management service in the Azure portal.
3. Fare clic **</>** sull'icona nella sezione **Elaborazione in ingresso.**
4. Inserire il criterio **<inbound>** nella sezione del file XML. Assicurati che **<origin>** il valore corrisponda al dominio del portale per sviluppatori.

> [!NOTE]
> 
> Se si applica il criterio CORS nell'ambito Product, anziché l'ambito API e l'API utilizza l'autenticazione della chiave di sottoscrizione tramite un'intestazione, la console non funzionerà.
>
> Il browser invia automaticamente una richiesta HTTP OPTIONS, che non contiene un'intestazione con la chiave di sottoscrizione. A causa della chiave di sottoscrizione mancante, Gestione API non può associare la chiamata OPTIONS a un prodotto, pertanto non può applicare il criterio CORS.
>
> In alternativa, è possibile passare la chiave di sottoscrizione in un parametro di query.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quali autorizzazioni sono necessarie per modificare il portale per sviluppatori?

Se viene visualizzato `Oops. Something went wrong. Please try again later.` l'errore quando si apre il portale in modalità amministrativa, è possibile che non siano manche le autorizzazioni necessarie (RBAC).

I portali legacy `Microsoft.ApiManagement/service/getssotoken/action` richiedevano l'autorizzazione nell'ambito del servizio (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) per consentire all'amministratore dell'utente l'accesso ai portali. Il nuovo portale `Microsoft.ApiManagement/service/users/token/action` richiede l'autorizzazione nell'ambito `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`.

È possibile usare lo script di PowerShell seguente per creare un ruolo con l'autorizzazione necessaria. Ricordarsi di `<subscription-id>` modificare il parametro. 

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
 
Una volta creato, il ruolo può essere concesso a qualsiasi utente dalla sezione **Controllo di accesso (IAM)** nel portale di Azure.Once the role is created, it can be granted to any user from the Access Control (IAM) section in the Azure portal. L'assegnazione di questo ruolo a un utente assegnerà l'autorizzazione nell'ambito del servizio. L'utente sarà in grado di generare token di firma di accesso condiviso per conto di *qualsiasi* utente nel servizio. Questo ruolo deve essere assegnato almeno all'amministratore del servizio. Il comando di PowerShell seguente illustra come `user1` assegnare il ruolo a un utente nell'ambito più basso per evitare di concedere autorizzazioni non necessarie all'utente:The following PowerShell command demonstrates how to assign the role to a user at the lowest scope to avoid granting unnecessary permissions to the user: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Dopo aver concesso le autorizzazioni a un utente, l'utente deve disconnettersi e accedere nuovamente al portale di Azure per avere effetto sulle nuove autorizzazioni.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Viene visualizzato l'errore `Unable to start the portal. See if settings are specified correctly (...)`

Questo errore viene `GET` visualizzato `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` quando una chiamata a ha esito negativo. La chiamata viene emessa dal browser dall'interfaccia amministrativa del portale.

Se il servizio Gestione API è in una rete virtuale, fare riferimento alla domanda di connettività della rete virtuale precedente.

L'errore di chiamata può anche essere causato da un certificato TLS/SSL, assegnato a un dominio personalizzato e non considerato attendibile dal browser. Come attenuazione, è possibile rimuovere il dominio personalizzato dell'endpoint di gestione: Gestione API eseguirà il rollback all'endpoint predefinito con un certificato attendibile.

### <a name="whats-the-browser-support-for-the-portal"></a>Qual è il supporto del browser per il portale?

| Browser                     | Supportato       |
|-----------------------------|-----------------|
| Apple Safari                | Sì<sup>1</sup> |
| Google Chrome               | Sì<sup>1</sup> |
| Microsoft Edge              | Sì<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Sì<sup>1</sup> |

 <small><sup>1</sup> Supportato nelle due ultime versioni di produzione.</small>

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sul nuovo portale per sviluppatori:

- [Accedere e personalizzare il portale per sviluppatori gestitoAccess and customize the managed developer portal](api-management-howto-developer-portal-customize.md)
- [Configurare la versione ospitata del portale][2]
- [Implementa il tuo widget][3]

Sfoglia altre risorse:

- [Repository GitHub con il codice sorgente][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
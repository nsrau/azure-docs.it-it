---
title: Configurare l'accesso automatico-accelerazione per un'applicazione tramite un criterio Home Realm Discovery | Documenti Microsoft
description: Viene illustrato il tenant di Azure AD e come gestire Azure tramite Azure Active Directory.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: e2e6e5c40dc4a9f67f94c45f8394512db3f777f5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configurare l'accesso automatico-accelerazione per un'applicazione utilizzando un criterio di individuazione dell'area di autenticazione principale

Il seguente documento viene fornita un'introduzione a Home Realm Discovery e accelerazione automaticamente.

## <a name="home-realm-discovery"></a>Individuazione dell'area di autenticazione
Individuazione dell'area di autenticazione principale (HRD) è il processo che consente di Azure Active Directory (Azure AD) per determinare, in fase di accesso, in cui un utente deve eseguire l'autenticazione.  Quando un utente accede a un tenant di Azure AD accedere a una risorsa o di pagina di Azure AD comune Accedi, digita un nome utente (UPN). Azure AD che usa per individuare in cui l'utente deve eseguire l'accesso. 

L'utente potrebbe essere necessario adottare una delle seguenti posizioni devono essere autenticati:

- Il tenant principale dell'utente (potrebbe essere lo stesso tenant come risorsa che l'utente sta tentando di accedere). 

- Account Microsoft.  L'utente è un utente guest nel tenant di risorse.

- Un altro provider di identità federato con tenant di Azure AD.

-  Un provider di identità locale, ad esempio Active Directory Federation Services (ADFS).

## <a name="auto-acceleration"></a>Accelerazione automatica 
Alcune organizzazioni configurare al proprio tenant di Azure Active Directory per la federazione con un altro provider di identità, ad esempio AD FS per l'autenticazione utente.  

In questi casi, quando un utente esegue l'accesso in un'applicazione, viene innanzitutto prima visualizzata con una pagina di accesso di Azure AD. Dopo che è stato digitato il nome dell'entità, viene visualizzata la pagina di accesso provider di identità. In alcuni casi, gli amministratori potrebbero voler indirizzare gli utenti alla pagina di accesso quando si tenti di accedere ad applicazioni specifiche. 

Ciò significa che gli utenti possono ignorare la pagina iniziale di Azure Active Directory. Questo processo è detto "Accedi automaticamente accelerazione."

In casi in cui il tenant è federato a un altro provider di identità per l'accesso automatico accelerazione rende utente Accedi più efficienti.  È possibile configurare l'accelerazione automatica per singole applicazioni.

>[!NOTE]
>Se si configura un'applicazione per l'accelerazione di auto, gli utenti guest non possono accedere. Se si crea un utente direttamente a un provider di identità federato per l'autenticazione, non è possibile in alcun modo per ritornare alla pagina di accesso di Azure Active Directory. Gli utenti Guest, che potrebbero dover essere indirizzati a un provider di identità esterno, ad esempio un account Microsoft o di altri tenant, non è possibile accedere all'applicazione perché il passaggio Home Realm Discovery in cui verrà ignorato.  

Esistono due modi per controllare l'accelerazione automatico per un provider di identità federate:   

- Utilizzare un hint di dominio per le richieste di autenticazione per un'applicazione. 
- Configurare un criterio di individuazione dell'area di autenticazione principale per abilitare l'accelerazione di auto.

## <a name="domain-hints"></a>Hint di dominio 
Gli hint di dominio sono direttive inclusi nella richiesta di autenticazione da un'applicazione. Possono essere utilizzati per accelerare l'utente per la pagina Accedi IdP federativa. Oppure può essere utilizzati da un'applicazione multi-tenant per accelerare l'utente direttamente per il marchio Active Directory di Azure-pagina di accesso relativi al proprio tenant.  

Ad esempio, l'applicazione "largeapp.com" potrebbe consentire ai clienti accedere all'applicazione in un URL personalizzato "contoso.largeapp.com". L'applicazione potrebbe contenere inoltre un hint di dominio per contoso.com nella richiesta di autenticazione. 

Sintassi di hint di dominio varia a seconda del protocollo utilizzata e in genere è configurato nell'applicazione.

**WS-Federation**: whr=contoso.com nella stringa di query.

**SAML**: entrambi una richiesta di autenticazione SAML che contiene un hint di dominio o un whr=contoso.com di stringa di query.

**Aprire l'ID connessione**: un domain_hint=contoso.com di stringa di query. 

Se un hint di dominio è incluso nella richiesta di autenticazione dall'applicazione e il tenant è federato con tale dominio, Azure AD viene effettuato un tentativo di reindirizzare Accedi al provider di identità configurato per tale dominio. 

Se l'hint di dominio non fa riferimento a un dominio federato verificato, viene ignorata e viene richiamato normale Home Realm Discovery.

Per ulteriori informazioni sull'accelerazione automatica mediante gli hint di dominio che sono supportati da Azure Active Directory, vedere il [Enterprise Mobility + Security blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Se un hint di dominio è incluso in una richiesta di autenticazione, la sua presenza sostituisce tutti i criteri che sono impostato per l'applicazione principale.

## <a name="home-realm-discovery-policy"></a>Criteri di individuazione dell'area di autenticazione principali
Alcune applicazioni non forniscono un modo per configurare la richiesta di autenticazione che generano. In questi casi, non è possibile utilizzare gli hint di dominio per controllare accelerazione automaticamente. L'accelerazione automatica può essere configurata tramite criterio per ottenere lo stesso comportamento.  

### <a name="set-hrd-policy"></a>Impostare i criteri HRD
Esistono tre passaggi per l'impostazione di accesso automatico accelerazione in un'applicazione:


1. Creazione di un criterio principale per l'accelerazione di auto.

2. Il principio di servizio a cui collegare il criterio di individuazione.

3. Collegamento del criterio all'entità servizio. Criteri potrebbero essere stati creati in un tenant, ma non ha alcun effetto fino a quando non sono connessi a un'entità. 

Un criterio principale può essere collegato a un'entità servizio e un solo criterio principale può essere attivo in un'entità specificata in qualsiasi momento.  

È possibile utilizzare i cmdlet di Active Directory Graph API di Microsoft Azure direttamente o Azure Active Directory PowerShell per impostare automaticamente accelerazione mediante criteri principale.

L'API Graph che modifica i criteri è descritta nel [operazioni sui criteri](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) articolo in MSDN.

Ecco un esempio di definizione dei criteri principale:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

Il tipo di criteri è "HomeRealmDiscoveryPolicy".

Se **AccelerateToFederatedDomain** è false, i criteri non ha alcun effetto.

**PreferredDomain** deve indicare un dominio a cui si desidera accelerare. Può essere omesso se dispone di un solo dominio federato.  Se viene omesso ed è presente più di una verifica dominio federato, i criteri non ha alcun effetto.

Se **PreferredDomain** è specificato, deve corrispondere a un dominio federato verificato per il tenant. Tutti gli utenti dell'applicazione devono essere in grado di accedere al dominio.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorità e valutazione dei criteri HRD
È possibile creare criteri HRD e assegnarli a specifiche organizzazioni e entità servizio. È quindi possibile che più criteri si applichino a un'applicazione specifica. II criterio HRD che entra in vigore si basa sulle seguenti regole:


- Se un hint di dominio è presente nella richiesta di autenticazione, viene ignorato qualsiasi criterio principale. Viene utilizzato il comportamento specificato dall'hint dominio.

- Altrimenti, se il criterio è assegnato in modo esplicito all'entità servizio, allora viene applicato. 

- Se non vi è alcun hint di dominio e in modo esplicito è assegnato alcun criterio per l'entità servizio, viene applicato un criterio viene assegnato in modo esplicito per l'organizzazione padre dell'entità servizio. 

- Se non è presente alcun hint di dominio e non è stato assegnato alcun criterio all'entità servizio o all'organizzazione, viene usato il comportamento HRD predefinito.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>Esercitazione per l'impostazione di accesso automatico accelerazione in un'applicazione utilizzando un criterio HRD
Cmdlet di Azure AD PowerShell verrà utilizzata per esaminare alcuni scenari, tra cui:


- Configurazione accelerazione automatica per un'applicazione per un tenant con un singolo dominio federato.

- Configurazione accelerazione automatica per un'applicazione a uno dei diversi domini verificati per il tenant.

- Elenco delle applicazioni per cui è stato configurato un criterio.

### <a name="prerequisites"></a>Prerequisiti
Negli esempi seguenti, creare, aggiornare, collegare ed eliminare i criteri per le entità servizio dell'applicazione in Azure AD.

1.  Per iniziare, scaricare l'anteprima di cmdlet di Azure AD PowerShell più recente. 

2.  Dopo avere scaricato i cmdlet di Azure AD PowerShell, eseguire il comando Connetti per accedere ad Azure AD con l'account di amministratore:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Eseguire il comando seguente per visualizzare tutti i criteri dell'organizzazione:

    ``` powershell
    Get-AzureADPolicy
    ```

Se viene restituito nulla, significa che non sono presenti criteri creati nel tenant.

### <a name="example-set-auto-acceleration-for-an-application"></a>Esempio: Set accelerazione automatica per un'applicazione 
In questo esempio, creare un criterio che auto-accelera gli utenti a una schermata di accesso di ADFS che accedono a un'applicazione. Gli utenti possono accedere a AD FS senza dover immettere un nome utente nella pagina di accesso di Azure AD prima. 

#### <a name="step-1-create-an-hrd-policy"></a>Passaggio 1: creazione di un criterio HRD
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Se si dispone di un singolo dominio federato che autentica gli utenti per le applicazioni, è necessario creare un solo criterio principale.  

Per visualizzare il nuovo criterio e di ottenere il relativo **ObjectID**, eseguire il comando seguente:

``` powershell
Get-AzureADPolicy
```


Per abilitare l'accelerazione automaticamente dopo aver creato un criterio HRD, è possibile assegnare più i principi di servizio dell'applicazione.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Passaggio 2: Individuare l'entità servizio a cui assegnare i criteri  
È necessario il **ObjectID** le entità servizio a cui si desidera assegnare il criterio. Esistono diversi modi per trovare il **ObjectID** delle entità servizio.    

È possibile utilizzare il portale oppure è possibile eseguire query [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). È anche possibile scegliere di [strumento Esplora grafico](https://graphexplorer.cloudapp.net/) e accedere al proprio account Azure AD per visualizzare le entità servizio tutti dell'organizzazione. Perché si sta usando PowerShell, è possibile utilizzare il cmdlet get-AzureADServicePrincipal per elencare i principi di servizio e i relativi ID.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Passaggio 3: assegnazione del criterio all'entità servizio  
Dopo aver creato il **ObjectID** dell'entità servizio dell'applicazione per cui si desidera configurare l'accelerazione di auto, eseguire il comando seguente. Questo comando associa i criteri HRD creato nel passaggio 1 con l'entità servizio che trova nel passaggio 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

È possibile ripetere questo comando per ogni entità servizio a cui si desidera aggiungere il criterio.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Passaggio 4: verifica delle entità servizio dell'applicazione a cui è assegnato il criterio di accelerazione automatica
Per controllare quali applicazioni configurati criteri di accelerazione automatica, utilizzare il **Get AzureADPolicyAppliedObject** cmdlet. Passare il **ObjectID** dei criteri che si desidera controllare.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Passaggio 5: operazione completata
Provare l'applicazione per verificare che il nuovo criterio funzioni.

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Esempio: Elenca le applicazioni per cui viene configurato un criterio di accelerazione automatica

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Passaggio 1: Elenco di tutti i criteri che sono stati creati all'interno dell'organizzazione 

``` powershell
Get-AzureADPolicy
```

Si noti il **ObjectID** dei criteri che si desidera per le assegnazioni di elenco.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Passaggio 2: Elencare le entità servizio a cui è assegnato il criterio  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>Esempio: Rimuovere un criterio di accelerazione automatica per un'applicazione
#### <a name="step-1-get-the-objectid"></a>Passaggio 1: Ottenere il valore ObjectID
Utilizzare l'esempio precedente per ottenere il **ObjectID** dei criteri e che dell'entità servizio dell'applicazione da cui si desidera rimuoverlo. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Passaggio 2: Rimuovere l'assegnazione di criterio dall'entità servizio dell'applicazione  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Passaggio 3: Controllare la rimozione, elencando le entità servizio a cui è assegnato il criterio 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni sul funzionamento dell'autenticazione in Azure AD, vedere [scenari di autenticazione per Azure AD](develop/active-directory-authentication-scenarios.md).
- Per ulteriori informazioni su utenti single sign-on, vedere [accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).
- Per una panoramica di tutti i contenuti correlati allo sviluppo, vedere la [Guida per gli sviluppatori di Active Directory](develop/active-directory-developers-guide.md).

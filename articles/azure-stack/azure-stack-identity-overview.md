---
title: "Panoramica dell'identità per lo Stack di Azure | Documenti Microsoft"
description: "Conoscere i sistemi di identità che è possibile utilizzare con lo Stack di Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: deebe5d8ff4c35c4507d2daf5c15025a1810d865
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Panoramica dell'identità per lo Stack di Azure

Stack di Azure richiede Active Directory Federation Services (ADFS), supportato da Active Directory come provider di identità o Azure Active Directory (Azure AD). La scelta di un provider è una decisione monouso apportate durante la distribuzione di Azure Stack. I concetti e i dettagli di autorizzazione in questo articolo consente di scegliere tra i provider di identità. 

La scelta di Azure Active Directory o ADFS può essere determinata dalla modalità in cui si distribuisce Azure Stack: 
- Quando viene distribuito in una modalità connessa, è possibile utilizzare Azure Active Directory o AD FS. 
- Quando si distribuisce in modalità disconnessa, senza una connessione a internet, è supportato solo AD FS.

Per ulteriori informazioni sulle opzioni, che variano a seconda dell'ambiente dello Stack di Azure, vedere gli articoli seguenti:
- Kit di distribuzione Azure dello Stack: [considerazioni identità](azure-stack-datacenter-integration.md#identity-considerations).
- Stack Azure integrati sistemi: [decisioni per lo Stack di Azure la pianificazione della distribuzione integrati sistemi](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Concetti di base per identità
Nelle sezioni successive vengono trattati i concetti comuni sui provider di identità e il relativo utilizzo nello Stack di Azure.

![Terminologia di provider di identità](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Le organizzazioni e i tenant di directory
Una directory è un contenitore che contiene informazioni su *utenti*, *applicazioni*, *gruppi*, e *service entità*.
 
Un tenant di directory è un *organizzazione*, ad esempio Microsoft o la propria società. 
- Azure AD supporta più tenant e può supportare più organizzazioni, ognuno nella propria directory. Se si utilizza Azure AD e si dispone di più tenant, è possibile concedere agli utenti di accesso di un tenant di altri tenant nella stessa directory e di applicazioni.
- ADFS supporta solo un singolo tenant e, pertanto, solo una singola organizzazione. 

### <a name="users-and-groups"></a>Utenti e gruppi
Gli account utente (identità) sono gli account standard che eseguono l'autenticazione di utenti con un ID utente e una password. I gruppi possono includere altri gruppi o utenti. 

Come creare e gestire utenti e gruppi varia a seconda della soluzione di identità che è utilizzare. 

Nello Stack di Azure, gli account utente: 
- Vengono creati nel  *username@domain*  formato. Anche se ADFS viene eseguito il mapping degli account utente a un'istanza di Active Directory, AD FS non supporta l'utilizzo del  *\<dominio >\<alias >* formato. 
- Da impostare per utilizzare l'autenticazione a più fattori. 
- Sono limitate alla directory in cui innanzitutto registrare, ovvero la directory della propria organizzazione.
- Possono essere importati dalla directory locale. Per altre informazioni, vedere [integrare le directory locali con Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect). 

Quando si accede al portale tenant dell'organizzazione, usare il  *https://portal.local.azurestack.external*  URL. 

### <a name="guest-users"></a>Utenti guest
Gli utenti guest sono gli account utente dagli altri tenant di directory che è stato concesso l'accesso alle risorse nella directory. Per supportare gli utenti guest, si utilizza Azure Active Directory e abilitare il supporto per multi-tenancy. Quando il supporto è abilitato, è possibile invitare gli utenti guest di accedere alle risorse nel tenant di directory, che a sua volta consente la collaborazione con organizzazioni esterne. 

Per invitare gli utenti guest, è possono utilizzare utenti e agli operatori cloud [collaborazione B2B di Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Gli utenti invitati ottengono l'accesso a documenti, le risorse e le applicazioni dalla directory e si mantengono il controllo sui propri dati e alle risorse. 

Come utente guest, è possibile accedere al tenant di directory dell'organizzazione. A tale scopo, è aggiungere il nome di directory dell'organizzazione nel portale di URL. Ad esempio, se si desidera eseguire l'accesso alla directory Fabrikam appartenenti all'organizzazione di Contoso, utilizzare https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>APPLICAZIONI
È possibile registrare le applicazioni in Azure Active Directory o ADFS e quindi offrono le applicazioni agli utenti dell'organizzazione. 

Applicazioni:
- **Applicazione Web**: esempi includono il portale di Azure e Azure Resource Manager. Supportano le chiamate all'API Web. 
- **Native client**: esempi di Azure PowerShell, Visual Studio e CLI di Azure.

Le applicazioni possono supportare due tipi di tenancy: 
- **Single-tenant**: supporta gli utenti e i servizi solo dalla stessa directory in cui l'applicazione è registrata. 

  > [!NOTE]    
  > Poiché ADFS supporta solo una singola directory, le applicazioni create in una topologia ADFS sono, per impostazione predefinita, le applicazioni single-tenant.

- **Multi-tenant**: supporta l'utilizzo da utenti e servizi dalla directory in cui viene registrata l'applicazione sia directory tenant aggiuntive. Con le applicazioni multi-tenant, gli utenti di un altro tenant directory (un altro tenant di Azure AD) possono accedono all'applicazione. 

  Per ulteriori informazioni su multi-tenancy, vedere [abilitare multi-tenancy](azure-stack-enable-multitenancy.md). 

  Per ulteriori informazioni sullo sviluppo di un'applicazione multi-tenant, vedere [App multi-tenant](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Quando si registra un'applicazione, si creano due oggetti:

- **Oggetto applicazione**: la rappresentazione globale dell'applicazione tra tutti i tenant. Questa relazione è uno a uno con l'applicazione software ed esiste solo nella directory in cui l'applicazione viene innanzitutto registrata.

- **Oggetto entità servizio**: le credenziali in cui viene creata per un'applicazione nella directory in cui l'applicazione viene innanzitutto registrata. Un'entità servizio viene anche creata nella directory di ogni tenant aggiuntive in cui viene utilizzato tale applicazione. Questa relazione può essere uno-a-molti con l'applicazione software. 

Per ulteriori informazioni sull'applicazione e gli oggetti entità servizio, vedere [dell'applicazione e oggetti entità servizio in Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Entità servizio 
Un'entità servizio è un set di *credenziali* per un'applicazione o servizio che concedono l'accesso alle risorse nello Stack di Azure. L'utilizzo di un'entità servizio consente di separare le autorizzazioni applicazione dalle autorizzazioni dell'utente dell'applicazione.

Un'entità servizio viene creata in ogni tenant in cui viene utilizzata l'applicazione. L'entità servizio consente di stabilire un'identità per l'accesso e l'accesso alle risorse (ad esempio, gli utenti) che sono protetti da tale tenant. 

- Un'applicazione single-tenant dispone solo un'entità di servizio, nella directory in cui viene creato inizialmente. Questa entità servizio viene creata e accetta le condizioni per l'utilizzo durante la registrazione dell'applicazione. 
- Un'applicazione web di multi-tenant o l'API dispone di un'entità servizio che viene creata in ogni tenant in cui un utente di tenant acconsente all'uso dell'applicazione. 

Le credenziali per le entità servizio possono essere una chiave che viene generata tramite il portale di Azure o un certificato. L'utilizzo di un certificato è adatta per l'automazione perché i certificati vengono considerati più sicuri di chiavi. 


> [!NOTE]    
> Quando si usa ADFS con lo Stack di Azure, solo l'amministratore può creare le entità servizio. Con AD FS, le entità servizio richiedono certificati e vengono create tramite l'endpoint con privilegi (PEP). Per altre informazioni, vedere [forniscono l'accesso di applicazioni per Azure Stack](azure-stack-create-service-principals.md).

Per ulteriori informazioni sulle entità servizio per lo Stack di Azure, vedere [creare entità servizio](azure-stack-create-service-principals.md).


### <a name="services"></a>Services
Servizi nello Stack di Azure che interagiscono con il provider di identità sono registrati come le applicazioni con il provider di identità. Quali applicazioni, la registrazione consente a un servizio per l'autenticazione con il sistema di identità. 

Tutti i servizi di Azure usate [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protocolli e [i token Web JSON](/azure/active-directory/develop/active-directory-token-and-claims) per stabilire la propria identità. Poiché Azure AD e AD FS utilizzano protocolli in modo coerente, è possibile usare [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) per l'autenticazione in locale o in Azure (in uno scenario connesso). Con ADAL, è anche possibile utilizzare strumenti quali Azure PowerShell e CLI di Azure per la gestione delle risorse tra cloud e locali. 


### <a name="identities-and-your-identity-system"></a>Identità e il sistema di identità 
Identità per lo Stack di Azure includono gli account utente, gruppi ed entità servizio. 

Quando si installa Azure Stack, diversi servizi e applicazioni predefinite venga registrato automaticamente con il provider di identità nel tenant di directory. Alcuni servizi che registrano vengono utilizzati per l'amministrazione. Altri servizi sono disponibili per gli utenti. Le registrazioni predefinito forniscono le identità di servizi dei componenti di base che possono interagire tra loro e con le identità che verrà aggiunto.

Se si configura Azure AD con multi-tenancy, alcune applicazioni di propagano per le directory nuova. 

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione
 

### <a name="authentication-by-applications-and-users"></a>Autenticazione da parte di utenti e applicazioni
  
![Identità tra i livelli dello Stack di Azure](media/azure-stack-identity-overview/identity-layers.png)

Per utenti e applicazioni, è descritta l'architettura dello Stack di Azure da quattro livelli. Interazioni tra ognuno di questi livelli possono usare diversi tipi di autenticazione.


|Livello    |Autenticazione tra livelli  |
|---------|---------|
|Strumenti e i client, ad esempio il portale di amministrazione     | Per accedere o modificare una risorsa nello Stack di Azure, strumenti e i client usano un [Token Web JSON](/azure/active-directory/develop/active-directory-token-and-claims) per effettuare una chiamata a Gestione risorse di Azure. <br>Gestione risorse di Azure convalida il Token Web JSON e legge il *attestazioni* presenti nel token rilasciato per stimare il livello di autorizzazione di tale utente o dell'entità servizio è nello Stack di Azure. |
|Gestione risorse di Azure e i relativi servizi componenti di base     |Gestione risorse di Azure comunica con i provider di risorse per il trasferimento di comunicazione dagli utenti. <br> Trasferisce utilizzare *imperativo diretto* chiamate o *dichiarativa* chiamata tramite [modelli di Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Provider di risorse     |Le chiamate che vengono passate al provider di risorse sono protetti con l'autenticazione basata su certificato. <br>Gestione risorse di Azure e il provider di risorse rimane quindi in comunicazione attraverso un'API. Per ogni chiamata che viene ricevuto da Gestione risorse di Azure, il provider di risorse convalida la chiamata con tale certificato.|
|Logica di business e infrastruttura     |I provider di risorse comunicano con logica di business e l'infrastruttura usando una modalità di autenticazione di propria scelta. I provider di risorse predefiniti forniti con Stack di Azure usano l'autenticazione di Windows per proteggere questa comunicazione.|

![Informazioni necessarie per l'autenticazione](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>L'autenticazione di Azure Resource Manager
Per l'autenticazione con il provider di identità e ricevere un Token Web JSON, è necessario disporre delle informazioni seguenti: 
1.  **URL per il sistema di identità (Authority)**: l'URL in cui il provider di identità può essere raggiunto. Ad esempio, *https://login.windows.net*. 
2.  **URI ID App per Azure Resource Manager**: l'identificatore univoco per la gestione di risorse di Azure che è registrato con il provider di identità. Inoltre è univoco per ogni installazione dello Stack di Azure.
3.  **Credenziali**: le credenziali utilizzare per l'autenticazione con il provider di identità. 
4.  **URL per Gestione risorse di Azure**: l'URL è il percorso del servizio Gestione risorse di Azure. Ad esempio,  *https://management.azure.com*  o  *https://management.local.azurestack.external* .

Quando un'entità (un client, applicazione o utente) esegue una richiesta di autenticazione per accedere a una risorsa, la richiesta deve includere:
- Credenziali del server principale.
- L'URI di ID della risorsa che l'entità vuole accedere a app. 

Le credenziali vengono convalidati dal provider di identità. Il provider di identità consente inoltre di verificare che l'app URI ID sia per un'applicazione registrata, e che l'entità disponga dei privilegi appropriati per ottenere un token per tale risorsa. Se la richiesta è valida, viene concesso un Token Web JSON. 

Il token necessario quindi passare l'intestazione di una richiesta in Azure Resource Manager. Gestione risorse di Azure effettua quanto segue, in alcun ordine specifico:
- Convalida la *dell'autorità di certificazione* (iss) di attestazione confermare che il token sia dal provider di identità corretto. 
- Convalida la *audience* attestazione (aud) per confermare che il token sia stato rilasciato da Azure Resource Manager. 
- Convalida che il Token Web JSON è firmato con un certificato che viene configurato tramite OpenID è noto in Azure Resource Manager. 
- Esaminare i *ora di emissione* (iat) e *scadenza* (exp) dichiara di confermare che il token è attivo e possono essere accettati. 

Una volta completate tutte le convalide, Gestione risorse di Azure Usa il *opposte* (oid) e il *gruppi* attestazioni creare un elenco di risorse a cui l'entità può accedere. 

![Diagramma del protocollo di scambio di token](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Usare il controllo degli accessi in base al ruolo  
Role-Based Access controllo (RBAC) nello Stack di Azure è coerente con l'implementazione di Microsoft Azure. È possibile gestire l'accesso alle risorse tramite l'assegnazione di ruolo RBAC appropriato per gli utenti, gruppi e applicazioni. Per informazioni su come usare RBAC con lo Stack di Azure, vedere gli articoli seguenti:
- [Introduzione a controllo di accesso basato sui ruoli nel portale di Azure](/azure/active-directory/role-based-access-control-what-is).
- [Utilizzare controllo di accesso basato sui ruoli per gestire l'accesso alle risorse di sottoscrizione di Azure](/azure/active-directory/role-based-access-control-configure).
- [Creare ruoli personalizzati per gestire il controllo di accesso](/azure/active-directory/role-based-access-control-custom-roles).
- [Gestire il controllo di accesso basato sui ruoli](azure-stack-manage-permissions.md) nello Stack di Azure.


### <a name="authenticate-with-azure-powershell"></a>L'autenticazione con Azure PowerShell  
Informazioni sull'uso di Azure PowerShell per l'autenticazione con Azure Stack sono reperibile in [configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>L'autenticazione con CLI di Azure
Per informazioni sull'utilizzo di Azure PowerShell per l'autenticazione con lo Stack di Azure, vedere [installare e configurare CLI di Azure per l'utilizzo con Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>Passaggi successivi
- [Architettura di identità](azure-stack-identity-architecture.md)   
- [Integrazione con Data Center - identità](azure-stack-integrate-identity.md)





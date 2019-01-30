---
title: Panoramica dell'identità per Azure Stack | Microsoft Docs
description: Scopri i sistemi delle identità che è possibile usare con Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 9fc53fd2539a39de4f01758704765392cc7e98a8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246967"
---
# <a name="overview-of-identity-for-azure-stack"></a>Panoramica dell'identità per Azure Stack

Azure Stack richiede Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS), supportato da Active Directory come provider di identità. La scelta di un provider è una decisione una-tantum effettuate durante la distribuzione di Azure Stack. I concetti e i dettagli di autorizzazione in questo articolo consente di scegliere tra i provider di identità.

La scelta di Azure AD o AD FS è determinata dalla modalità in cui si distribuisce Azure Stack:

- Quando viene distribuita in una modalità connessa, è possibile usare Azure AD o AD FS.
- Quando viene distribuito in modalità disconnessa, senza una connessione a internet, è supportato solo da AD FS.

Per altre informazioni sulle opzioni disponibili, che dipendono dall'ambiente Azure Stack, vedere gli articoli seguenti:

- Kit di distribuzione di Azure Stack: [Considerazioni sull'identità](azure-stack-datacenter-integration.md#identity-considerations).
- Sistemi integrati di Azure Stack: [I sistemi integrati di prendere decisioni per Azure Stack di pianificazione della distribuzione](azure-stack-deployment-decisions.md).

## <a name="common-concepts-for-identity"></a>Concetti di base per identity

Le sezioni successive illustrano concetti comuni sui provider di identità e l'uso in Azure Stack.

![Terminologia di provider di identità](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Le organizzazioni e i tenant di directory

Una directory è un contenitore che contiene informazioni relative a *gli utenti*, *applications*, *gruppi*, e *entità servizio*.

Un tenant di directory è un' *organizzazione*, ad esempio Microsoft o la propria società.

- Azure AD supporta più tenant e può supportare più organizzazioni, ognuno nella propria directory. Se si usa Azure AD e si hanno più tenant, è possibile concedere le applicazioni e un solo tenant l'accesso degli utenti ad altri tenant della directory stessa.
- ADFS supporta solo un singolo tenant e, di conseguenza, solo una singola organizzazione.

### <a name="users-and-groups"></a>Utenti e gruppi

Gli account utente (identità) sono gli account standard che eseguono l'autenticazione di singoli utenti con un ID utente e password. I gruppi possono includere utenti o altri gruppi.

Come creare e gestire utenti e gruppi dipende è usare la soluzione di identità.

In Azure Stack, gli account utente:

- Vengono creati nel *username@domain* formato. Anche se ADFS viene eseguito il mapping degli account utente a un'istanza di Active Directory, AD FS non supporta l'utilizzo dei  *\\ \<dominio >\\\<alias >* formato.
- Da impostare per multi-factor Authentication.
- Sono limitate alla directory in cui prima di tutto registrare, ovvero directory della propria organizzazione.
- Possono essere importati dalla directory locale. Per altre informazioni, vedere [integrare le directory locali con Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Quando si accede al portale tenant dell'organizzazione, usare il *https://portal.local.azurestack.external* URL. Quando si accede al portale di Azure Stack da domini diversi da quello usato per registrare Azure Stack, il nome di dominio usato per registrare Azure Stack deve essere aggiunto al portale di url. Ad esempio, se Azure Stack è stato registrato con fabrikam.onmicrosoft.com e l'account utente di accesso viene admin@contoso.com, l'url da usare per accedere al portale per gli utenti sarà: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Utenti guest

Gli utenti guest sono account utente dagli altri tenant di directory che hanno ottenuto l'accesso alle risorse nella directory. Per supportare gli utenti guest, si usa Azure AD e abilitare il supporto per multi-tenancy. Quando è abilitato il supporto, è possibile invitare utenti guest per accedere alle risorse nel tenant di directory, che a sua volta consente la collaborazione con organizzazioni esterne.

Per invitare utenti guest, è possono usare gli operatori cloud e gli utenti [collaborazione B2B di Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Utenti invitati ottengono l'accesso a documenti, risorse e le applicazioni dalla directory e Mantieni il controllo sulle risorse e dati. 

Come utente guest, è possibile accedere al tenant di directory dell'organizzazione. A tale scopo, si accodare il nome di directory dell'organizzazione nel portale di URL. Ad esempio, se si appartenenti all'organizzazione di Contoso e si vuole accedere alla directory di Fabrikam, utilizzare https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>APPLICAZIONI

È possibile registrare le applicazioni in Azure AD o AD FS e quindi distribuire le applicazioni agli utenti nell'organizzazione.

Applicazioni includono:

- **Applicazione Web**: Ad esempio il portale di Azure e Azure Resource Manager. Supportano chiamate all'API Web.
- **Native client**: Sono esempi di Azure PowerShell, Visual Studio e CLI di Azure.

Le applicazioni possono supportare due tipi di tenancy:

- **Tenant singolo**: Supporta gli utenti e i servizi solo dalla stessa directory in cui è registrata l'applicazione.

  > [!NOTE]
  > Poiché AD FS supporta solo una singola directory, le applicazioni create in una topologia di AD FS sono, per impostazione predefinita, le applicazioni a tenant singolo.

- **Multi-tenant**: Supporta l'utilizzo da utenti e di servizi di directory in cui è registrata l'applicazione e le directory di tenant aggiuntivi. Con le applicazioni multi-tenant, gli utenti di un altro tenant directory (un altro tenant di Azure AD) possano accedere all'applicazione. 

  Per altre informazioni su multi-tenancy, vedere [abilitare multi-tenancy](azure-stack-enable-multitenancy.md).

  Per altre informazioni sullo sviluppo di un'app multi-tenant, vedere [App multi-tenant](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Quando si registra un'applicazione, vengono creati due oggetti:

- **Oggetto applicazione**: La rappresentazione globale dell'applicazione in tutti i tenant. Questa relazione è uno a uno con l'applicazione software ed esiste solo nella directory in cui l'applicazione viene inizialmente registrata.

- **Oggetto entità servizio**: Una credenziale che viene creata per un'applicazione nella directory in cui l'applicazione viene inizialmente registrata. Viene anche creata un'entità servizio nella directory di ogni tenant aggiuntivo in cui viene utilizzato tale applicazione. Questa relazione può essere uno-a-molti con l'applicazione software.

Per altre informazioni sull'applicazione e oggetti entità servizio, vedere [dell'applicazione e oggetti entità servizio in Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Entità servizio

Un'entità servizio è un set di *credenziali* per un'applicazione o servizio che concedono l'accesso alle risorse in Azure Stack. L'uso di un'entità servizio consente di separare le autorizzazioni all'applicazione dalle autorizzazioni dell'utente dell'applicazione.

In ogni tenant in cui viene utilizzata l'applicazione viene creata un'entità servizio. L'entità servizio consente di stabilire un'identità per l'accesso e l'accesso alle risorse protette da tale tenant (ad esempio, gli utenti).

- Un'applicazione single-tenant con una sola entità servizio, nella directory in cui viene creato inizialmente. Questa entità servizio viene creata e accetta le condizioni per l'utilizzo durante la registrazione dell'applicazione.
- Un'applicazione web di multi-tenant o l'API è un'entità servizio creata in ogni tenant in cui un utente dal tenant acconsente all'uso dell'applicazione.

Le credenziali per le entità servizio possono essere una chiave che viene generata tramite il portale di Azure o un certificato. L'uso di un certificato è adatto per l'automazione perché i certificati sono considerati più sicuro delle chiavi. 

> [!NOTE]
> Quando si usa AD FS con Azure Stack, solo l'amministratore può creare le entità servizio. Con AD FS, le entità servizio richiedono i certificati e vengono create tramite l'endpoint con privilegi (PEP). Per altre informazioni, vedere [offrono alle applicazioni di accedere ad Azure Stack](azure-stack-create-service-principals.md).

Per altre informazioni sulle entità servizio per Azure Stack, vedere [creano entità servizio](azure-stack-create-service-principals.md).

### <a name="services"></a>Servizi

Servizi di Azure Stack che interagiscono con il provider di identità vengono registrati come le applicazioni con il provider di identità. Ad esempio applicazioni, la registrazione consente a un servizio per l'autenticazione con il sistema di identità.

Tutti i servizi di Azure usano [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protocolli e [token Web JSON](/azure/active-directory/develop/active-directory-token-and-claims) per definire la propria identità. Perché Azure AD e AD FS usare protocolli in modo coerente, è possibile usare [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) per eseguire l'autenticazione in locale o in Azure (in uno scenario connesso). Con ADAL, è anche possibile usare strumenti come Azure PowerShell e CLI di Azure per la gestione delle risorse tra cloud e locali.

### <a name="identities-and-your-identity-system"></a>Le identità e il sistema di identità

Le identità per Azure Stack includono gli account utente, gruppi ed entità servizio.

Quando si installa Azure Stack, diversi servizi e applicazioni predefinite automaticamente registrati con il provider di identità nel tenant di directory. Alcuni servizi registrati vengono usati per l'amministrazione. Altri servizi sono disponibili per gli utenti. Le registrazioni per l'impostazione predefinita assegnare le identità dei servizi core che possono interagire tra loro e con le identità che aggiungono più tardi.

Se si configura Azure AD con multi-tenancy, alcune applicazioni verranno propagate alle directory di nuovo.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

### <a name="authentication-by-applications-and-users"></a>Autenticazione da parte di utenti e applicazioni

![Identità tra i livelli di Azure Stack](media/azure-stack-identity-overview/identity-layers.png)

Per le applicazioni e gli utenti, l'architettura di Azure Stack è descritta da quattro livelli. Interazioni tra ciascuno di questi livelli è possono usare diversi tipi di autenticazione.

|Livello    |Autenticazione tra i livelli  |
|---------|---------|
|Strumenti e i client, ad esempio il portale di amministrazione     | Per accedere o modificare una risorsa in Azure Stack, strumenti e i client usano un [Token JSON Web](/azure/active-directory/develop/active-directory-token-and-claims) effettuare una chiamata ad Azure Resource Manager. <br>Convalida il Token JSON Web di Azure Resource Manager e visualizza i *attestazioni* nel token rilasciato per stimare il livello di autorizzazione utente o entità servizio è in Azure Stack. |
|Azure Resource Manager e i relativi servizi di base     |Azure Resource Manager comunica con i provider di risorse per il trasferimento di comunicazione degli utenti. <br> Trasferisce uso *imperativo diretto* chiamate o *dichiarativa* chiama tramite [modelli Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates).|
|Provider di risorse     |Chiamate che vengono passate al provider di risorse sono protette con l'autenticazione basata su certificato. <br>Azure Resource Manager e il provider di risorse rimangono quindi in comunicazione tramite un'API. Per ogni chiamata che viene ricevuto da Azure Resource Manager, il provider di risorse convalida la chiamata con tale certificato.|
|Per la logica di business e infrastruttura     |I provider di risorse comunicano con l'infrastruttura e la logica di business utilizzando una modalità di autenticazione di propria scelta. I provider di risorse predefiniti forniti con Azure Stack usano l'autenticazione di Windows per proteggere questa comunicazione.|

![Informazioni necessarie per l'autenticazione](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Eseguire l'autenticazione ad Azure Resource Manager

Per eseguire l'autenticazione con il provider di identità e ricevere un Token Web JSON, è necessario disporre le informazioni seguenti:

1. **URL per il sistema di identità (autorità)**: L'URL in cui il provider di identità può essere raggiunto. Ad esempio, *https://login.windows.net*.
2. **URI ID App per Azure Resource Manager**: Identificatore univoco per Azure Resource Manager che viene registrato con il provider di identità. Inoltre è univoco per ogni installazione di Azure Stack.
3. **Credenziali**: Le credenziali che utilizzare per eseguire l'autenticazione con il provider di identità.
4. **URL per Azure Resource Manager**: L'URL è il percorso del servizio di Azure Resource Manager. Ad esempio, *https://management.azure.com* oppure *https://management.local.azurestack.external*.

Quando un'entità (un client, applicazione o utente) effettua una richiesta di autenticazione per accedere a una risorsa, la richiesta deve includere:

- Le credenziali dell'entità.
- L'app ID URI della risorsa che voglia accedere all'entità.

Le credenziali vengono convalidate dal provider di identità. Il provider di identità convalida inoltre che l'app ID URI sia per un'applicazione registrata e che l'entità disponga dei privilegi appropriati per ottenere un token per tale risorsa. Se la richiesta è valida, viene concesso un Token Web JSON.

Il token deve quindi passare l'intestazione di una richiesta ad Azure Resource Manager. Azure Resource Manager esegue le operazioni seguenti, in nessun ordine specifico:

- Convalida la *issuer* (iss) di attestazione per confermare che il token sia dal provider di identità corretta.
- Convalida la *audience* attestazione (aud) per confermare che il token sia stato rilasciato ad Azure Resource Manager.
- Verifica che il Token JSON Web sia firmato con un certificato che viene configurato tramite OpenID è nota ad Azure Resource Manager.
- Rivedere le *ora di emissione* (iat) e *scadenza* (exp) di attestazioni confermare che il token sia attivo e può essere accettato.

Dopo aver complete tutte le convalide, Azure Resource Manager usa la *contestato* (oid) e il *gruppi* attestazioni rendere un elenco di risorse che può accedere all'entità.

![Diagramma del protocollo di scambio di token](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Dopo la distribuzione, l'autorizzazione di amministratore globale di Azure Active Directory non è necessaria. Tuttavia, alcune operazioni potrebbero richiedere le credenziali di amministratore globale. Ad esempio, uno script di programma di installazione di provider di risorse o una nuova funzionalità che richiedono un'autorizzazione da concedere. È possibile temporaneamente riattivare le autorizzazioni di amministratore globale dell'account o utilizzare un account di amministratore globale separata che è un proprietario del *predefinita sottoscrizione provider*.

### <a name="use-role-based-access-control"></a>Usare il controllo degli accessi in base al ruolo

In base al ruolo di controllo di accesso (RBAC) in Azure Stack è coerente con l'implementazione in Microsoft Azure. È possibile gestire l'accesso alle risorse assegnando i ruoli RBAC appropriati a utenti, gruppi e applicazioni.
Per informazioni su come usare RBAC con Azure Stack, vedere gli articoli seguenti:

- [Introduzione al Controllo degli accessi in base al ruolo di Azure nel portale di Azure](/azure/role-based-access-control/overview).
- [Usare controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](/azure/role-based-access-control/role-assignments-portal).
- [Creare ruoli personalizzati per il controllo di accesso](/azure/role-based-access-control/custom-roles).
- [Gestire controllo degli accessi in base al ruolo](azure-stack-manage-permissions.md) nello Stack di Azure.

### <a name="authenticate-with-azure-powershell"></a>Eseguire l'autenticazione con Azure PowerShell

Vedere i dettagli sull'uso di Azure PowerShell per eseguire l'autenticazione con Azure Stack [configurare l'ambiente PowerShell dell'utente di Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Eseguire l'autenticazione con Azure CLI

Per informazioni sull'uso di Azure PowerShell per eseguire l'autenticazione con Azure Stack, vedere [installare e configurare Azure CLI per l'uso con Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli).

## <a name="next-steps"></a>Passaggi successivi

- [Architettura Identità](azure-stack-identity-architecture.md)
- [Datacenter integration - identità](azure-stack-integrate-identity.md)

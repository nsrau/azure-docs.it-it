---
title: "Panoramica dell'identità per lo Stack di Azure | Documenti Microsoft"
description: "Informazioni sui sistemi di identità che è possibile utilizzare con lo Stack di Azure."
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
ms.openlocfilehash: 609ea3300806f3cfed4a3285a096f59be491c684
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Panoramica dell'identità per lo Stack di Azure

Stack di Azure richiede Azure AD o Active Directory Federated Services (ADFS) supportata da Active Directory (AD) come provider di identità. I seguenti concetti e le informazioni di autorizzazione consentono di adottare la scelta tra i provider di identità. La scelta di un provider è una decisione monouso apportate quando si distribuisce prima dello Stack di Azure.  

La scelta tra Azure Active Directory e AD FS potrebbe essere limitato dalla modalità in cui si distribuisce Azure Stack: 
- Quando si distribuisce in una modalità connessa, è possibile utilizzare Azure Active Directory o AD FS. 
- Quando si distribuisce in modalità disconnessa senza una connessione a Internet, è supportato solo AD FS.

Per ulteriori informazioni su queste opzioni, vedere gli articoli seguenti a seconda dell'ambiente Azure Stack:
- Kit di distribuzione di Azure Stack, vedere [considerazioni identità](azure-stack-datacenter-integration.md#identity-considerations).
- Sistemi Azure Stack integrato, vedere [decisioni per lo Stack di Azure la pianificazione della distribuzione integrati sistemi](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Concetti di base per l'identità
Le sezioni seguenti illustrano i concetti comuni per i provider di identità e il relativo utilizzo nello Stack di Azure.
![Terminologia di provider di identità](media/azure-stack-identity-overview/terminology.png)


### <a name="directories-tenants-and-organizations"></a>Le organizzazioni e i tenant di directory
Una directory è un contenitore che contiene informazioni su *utenti*, *applicazioni*, *gruppi*, e *service entità*.
 
Un tenant di directory è un *organizzazione*, ad esempio Microsoft o società. 
- Azure AD supporta più tenant e può supportare più organizzazioni, ognuno nella propria directory. Se si utilizza Azure AD e si dispone di più tenant, è possibile concedere agli utenti di accesso di un tenant di altri tenant nella stessa directory e di applicazioni.
- ADFS supporta solo un singolo tenant e pertanto una singola organizzazione. 

### <a name="users-and-groups"></a>Utenti e gruppi
Gli account utente (identità) sono gli account standard che eseguono l'autenticazione di utenti con un ID utente e una password. I gruppi possono includere altri gruppi o utenti. 

Come creare e gestire utenti e gruppi varia a seconda della soluzione di identità che è utilizzare. 

Nello Stack di Azure, gli account utente: 
- Vengono creati nel  *username@domain*  formato. Sebbene ADFS esegue il mapping degli account utente a un server Active Directory, ADFS non supporta l'utilizzo del  _&lt;dominio >\<alias >_ formato. 
- Da impostare per utilizzare l'autenticazione a più fattori. 
- Sono limitate alla directory in cui innanzitutto registrare, ovvero la directory delle organizzazioni.
- Possono essere importati dalla directory locale. Per ulteriori informazioni, vedere [integrare le directory locali con Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect) nella documentazione di Azure.  

Quando vengono registrati nel portale tenant di organizzazioni, utilizzare https://portal.local.azurestack.external. 

### <a name="guest-users"></a>Utenti guest
Gli utenti guest sono gli account utente dagli altri tenant di directory che è stato concesso l'accesso alle risorse nella directory. Per supportare l'utente guest, è necessario usare Azure Active Directory e abilitare il supporto per multi-tenancy. Se supportato, è possibile invitare un utente guest per accedere alle risorse nel tenant di directory, che consente la collaborazione con organizzazioni esterne. 

Per invitare gli utenti guest, è possono utilizzare utenti e agli operatori cloud [collaborazione B2B di Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Invito di utenti ottenere l'accesso a documenti, risorse e applicazioni da directory mentre si mantiene il controllo su risorse e dati. 

Come utente guest, è possibile accedere a un altro tenant di directory le organizzazioni. A tale scopo, è necessario aggiungere tale nome di directory aziendale nel portale di URL.  Ad esempio se si desidera registrare nella directory di Fabrikam appartengono a contoso.com, utilizzare https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>APPLICAZIONI
È possibile registrare le applicazioni in Azure Active Directory o ADFS e quindi offrire agli utenti dell'organizzazione. 

Applicazioni:
- **Applicazione Web** : il portale di Azure e Azure Resource Manager esempi.  Supportano le chiamate all'API Web. 
- **Native client** : esempi di PowerShell di Azure, Visual Studio e Azure interfaccia della riga di comando (CLI).

Le applicazioni possono supportare due tipi di tenancy: 
- **Singolo tenant** applicazioni supportano gli utenti e i servizi solo dalla stessa directory in cui l'applicazione è registrata. 

  > [!NOTE]    
  > Poiché ADFS supporta solo una singola directory, le applicazioni create in una topologia ADFS sono in base alla progettazione applicazioni single-tenant.
- **Multi-tenant** applicazioni supportano l'utilizzo per gli utenti e i servizi da entrambe le directory in cui l'applicazione è registrata e dalle directory tenant aggiuntive.  Con le applicazioni multi-tenant, gli utenti di un altro tenant directory (un altro tenant di Azure AD) possono accedono all'applicazione.     

  Per ulteriori informazioni su multi-tenancy, vedere [abilitare multi-tenancy](azure-stack-enable-multitenancy.md).  

  Per ulteriori informazioni sullo sviluppo di un'applicazione multi-tenant, vedere [App multi-tenant](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Quando si registra un'applicazione, vengono creati due oggetti:
- **Oggetto applicazione** : l'oggetto applicazione è la rappresentazione dell'applicazione globale tra tutti i tenant. Questa relazione è uno a uno con l'applicazione software e solo nella directory in cui l'applicazione è primo registri.

 
   
- **Oggetto entità servizio** : un'entità servizio è una credenziale che viene creata per un'applicazione nella directory in cui l'applicazione viene innanzitutto registrata. Un'entità servizio viene anche creata nella directory di ogni tenant aggiuntive in cui viene utilizzato tale applicazione. Questa relazione può essere un uno-a-molti con l'applicazione software.   

Per ulteriori informazioni sull'applicazione e oggetti entità servizio, vedere [applicazione e oggetti entità servizio in Azure Active Directory (Azure AD)](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Entità servizio 
Un'entità servizio è un set di *credenziali* per un'applicazione o servizio che concedono l'accesso alle risorse nello Stack di Azure. Utilizzo di un'entità servizio separa le autorizzazioni dell'applicazione per le autorizzazioni dell'utente che utilizza l'applicazione.

Un'entità servizio viene creata in ogni tenant in cui viene utilizzata l'applicazione. L'entità servizio stabilisce un'identità per l'accesso e l'accesso alle risorse (ad esempio, gli utenti) che sono protetti da tale tenant.   

- Un'applicazione single-tenant dispone solo un'entità di servizio, nella directory in cui viene creato inizialmente.  Questa entità servizio viene creata e accetta le condizioni da utilizzare durante la registrazione dell'applicazione. 
- Un'applicazione multi-tenant o l'API dispone di un'entità di servizio creata in ogni tenant in cui un utente di tenant acconsente all'uso dell'applicazione.  

Le credenziali per le entità servizio possono essere un certificato o chiave (generato tramite il portale di Azure).  Utilizzo di un certificato è adatta per l'automazione perché i certificati vengono considerati più sicuri rispetto all'utilizzo delle chiavi. 


> [!NOTE]    
> Quando si usa ADFS con lo Stack di Azure, solo l'amministratore può creare le entità servizio. Con AD FS, le entità servizio richiedono certificati e vengono create tramite l'endpoint con privilegi (PEP). Per ulteriori informazioni, vedere, [fornire alle applicazioni di accedere a Azure Stack](azure-stack-create-service-principals.md).

Per ulteriori informazioni sulle entità servizio per lo Stack di Azure, vedere [creare entità servizio](azure-stack-create-service-principals.md).


### <a name="services"></a>Services
Servizi nello Stack di Azure che interagiscono con il provider di identità, vengono registrati come applicazioni con il provider di identità. Quali applicazioni, la registrazione consente a un servizio per l'autenticazione con il sistema di identità. 

Tutti i servizi di Azure utilizzano [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protocolli e [token Web JSON](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) per stabilire la propria identità. A causa di un utilizzo coerenza dei protocolli da Azure AD e AD FS, è possibile utilizzare Azure [Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) per l'autenticazione locale o in Azure (in uno scenario connesso). ADAL inoltre consente di utilizzare strumenti come Azure PowerShell e CLI per tra cloud e la gestione delle risorse locali. 


### <a name="identities-and-your-identity-system"></a>Identità e il sistema di identità 
Identità per lo Stack di Azure includono gli account utente, gruppi ed entità servizio. Quando si installa Azure Stack, diversi servizi e applicazioni predefinite venga registrato automaticamente con il provider di identità nel tenant di directory. Alcuni servizi che registrano vengono utilizzati per l'amministrazione. Altri servizi sono disponibili per gli utenti. Le registrazioni predefinito assegnare identità servizi fondamentali in grado di interagire tra loro e con identità aggiungere in un secondo momento.
Se si configura Azure AD con multi-tenancy, alcune applicazioni di propagano per le directory nuova.  

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione
 

### <a name="authentication-by-applications-and-users"></a>Autenticazione da parte di utenti e applicazioni
  
![Identità tra i livelli dello Stack di Azure](media/azure-stack-identity-overview/identity-layers.png)

Per utenti e applicazioni, è descritta l'architettura dello Stack di Azure da quattro livelli. Interazioni tra ognuno di questi livelli possono usare diversi tipi di autenticazione.


|Livello    |Autenticazione tra livelli  |
|---------|---------|
|Strumenti e i client, come il portale di amministrazione     | Per accedere o modificare una risorsa nello Stack di Azure, strumenti e i client usano un [token Web JSON](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) per effettuare una chiamata per la gestione risorse di Azure.  <br><br> Azure Resource Manager convalida il token JWT e legge il *attestazioni* nel token rilasciato per stimare il livello di autorizzazione di tale utente o dell'entità servizio è nello Stack di Azure.        |
|Gestione risorse di Azure e i relativi servizi componenti di base     |Azure Resource Manager comunica con i provider di risorse per il trasferimento di comunicazione da utenti. <br><br> Trasferisce utilizzare *imperativo diretto* chiamate o *dichiarativa* chiamata tramite [modelli di Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Provider di risorse     |Chiamate passate al provider di risorse sono protetti con l'autenticazione basata su certificato. <br><br>Gestione risorse di Azure e il Provider di risorse quindi mantenuta nella comunicazione attraverso un'API. Per ogni chiamata ricevuto da Gestione risorse di Azure, il Provider di risorse convalida la chiamata con tale certificato.|
|Infrastruttura e logica di Business     |I provider di risorse di comunicare con logica di business e infrastruttura con una modalità di autenticazione di propria scelta. L'impostazione predefinita i provider di risorse forniti con lo Stack di Azure usare l'autenticazione di Windows per proteggere questa comunicazione.|

![Informazioni necessarie per l'autenticazione](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>L'autenticazione di Azure Resource Manager
Per l'autenticazione con il provider di identità e la ricezione di un token JWT, è necessario disporre delle informazioni seguenti: 
1.  **URL per il sistema di identità (autorità)** : l'URL in cui il provider di identità può essere raggiunto. Ad esempio,  *&lt;https://login.windows.net>*. 
2.  **URI ID App per Gestione risorse di Azure** : l'identificatore univoco per Gestione risorse di Azure che è univoco per ogni installazione di Azure Stack e registrati con il provider di identità.
3.  **Credenziali** – questo le credenziali utilizzate per l'autenticazione con il provider di identità.  
4.  **URL per Gestione risorse di Azure** : l'URL è il percorso del servizio di gestione risorse di Azure. Ad esempio,  *&lt;https://management.azure.com>* o  *&lt;https://management.local.azurestack.external>*.

Quando un'entità (un client, applicazione o utente) esegue una richiesta di autenticazione per accedere a una risorsa, è necessario che la richiesta:
- Credenziali del server principale.
- L'URI ID App della risorsa a cui si desidera accedere.  

Le credenziali vengono convalidati dal provider di identità. Il provider di identità consente inoltre di verificare che l'URI ID App sia per un'applicazione registrata, e che l'entità disponga dei privilegi appropriati per ottenere un token per tale risorsa. Se la richiesta è valida, viene concesso un Token Web JSON. 

Il token passa quindi nell'intestazione di una richiesta di gestione risorse di Azure. Gestione risorse di Azure esegue le convalide seguenti in alcun ordine specifico:
- Convalidare il *dell'autorità di certificazione* (iss) di attestazione confermare che il token sia dal provider di identità corretto. 
- Convalidare il *destinatari* attestazione (aud) per confermare che il token è stato emesso per Gestione risorse di Azure. 
- Verificare che il token JWT è firmato con un certificato che viene configurato tramite OpenID e che è noto in Azure Resource Manager. 
- Esaminare il *rilasciato al* (iat) e *scadenza* (exp) dichiara di confermare che il token è attivo e possono essere accettati. 

Una volta completate tutte le convalide, Gestione risorse di Azure Usa il *opposte* (oid) e *gruppi* attestazioni creare un elenco di risorse che l'entità può accedere. 

![Diagramma del protocollo di scambio di token](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Usare il controllo degli accessi in base al ruolo  
Role-Based Access controllo (RBAC) nello Stack di Azure è coerente con l'implementazione di Microsoft Azure.  È possibile gestire l'accesso alle risorse tramite l'assegnazione di ruolo RBAC appropriato per gli utenti, gruppi e applicazioni. Per informazioni su come usare RBAC con lo Stack di Azure, vedere gli articoli seguenti:
- [Introduzione a controllo di accesso basato sui ruoli nel portale di Azure](/azure/active-directory/role-based-access-control-what-is).
- [Utilizzare controllo di accesso basato sui ruoli per gestire l'accesso alle risorse di sottoscrizione di Azure](/azure/active-directory/role-based-access-control-configure).
- [Creare ruoli personalizzati per gestire il controllo di accesso](/azure/active-directory/role-based-access-control-custom-roles).
- [Gestire il controllo di accesso basato sui ruoli](azure-stack-manage-permissions.md) nello Stack di Azure.


### <a name="authenticate-with-azure-powershell"></a>L'autenticazione con Azure PowerShell  
Informazioni sull'uso di Azure PowerShell per l'autenticazione con Azure Stack sono reperibile in [configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>L'autenticazione con CLI di Azure
Informazioni sull'uso di Azure PowerShell per l'autenticazione con Azure Stack sono reperibile in [installare e configurare CLI per l'utilizzo con Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>Passaggi successivi
- [Architettura di identità](azure-stack-identity-architecture.md)   
- [Integrazione di Data Center - identità](azure-stack-integrate-identity.md)





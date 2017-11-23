---
title: Configurare l'accelerazione automatica dell'accesso usando i criteri di individuazione dell'area di autenticazione principale | Documentazione Microsoft
description: Illustra il significato di tenant di Azure AD e come gestire Azure con Azure Active Directory
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>Configurare l'accelerazione automatica dell'accesso usando i criteri di individuazione dell'area di autenticazione principale (home realm discovery, HRD)

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>Introduzione all'individuazione dell'area di autenticazione principale e all'accelerazione automatica
Il seguente documento fornisce un'introduzione all'individuazione dell'area di autenticazione principale e all'accelerazione automatica.

### <a name="home-realm-discovery"></a>Individuazione dell'area di autenticazione principale
L'individuazione dell'area di autenticazione principale è il processo che consente a Azure Active Directory di determinare, al momento dell'accesso, dove deve eseguire l'autenticazione l'utente.  Quando si effettua l'accesso a un tenant di Azure AD per accedere a una risorsa, o nella pagina di accesso comune di Azure AD, l'utente digita un nome utente (UPN).  Azure AD lo usa per individuare dove l'utente deve effettuare l'accesso.   L'utente potrebbe dover accedere a uno degli elementi seguenti per poter essere autenticato:

- Il tenant principale dell'utente (può essere lo stesso tenant della risorsa a cui l'utente sta cercando di effettuare l'accesso). 
- Account Microsoft.   L'utente è un ospite nel tenant della risorsa
- Un altro provider di identità federato con il tenant di Azure AD.  Un provider di identità locale, come per esempio AD FS.

### <a name="auto-acceleration"></a>Accelerazione automatica 
Alcune organizzazioni configurano il proprio tenant di Azure Active Directory in modo che esegua la federazione con un altro IdP, come AD FS, per l'autenticazione dell'utente.  In questi casi, quando si effettua l'accesso a un'applicazione, l'utente visualizza innanzitutto una pagina di accesso di Azure AD e, dopo aver digitato il proprio UPN, è indirizzato alla pagina di accesso dell'IdP.  In circostanze in cui questa pratica è adeguata, gli amministratori possono decidere di indirizzare direttamente gli utenti alla pagina di accesso quando effettuano l'accesso ad applicazioni specifiche, saltando la pagina iniziale di Azure Active Directory. Questa pratica è nota come "accelerazione automatica dell'accesso".

Nei casi in cui il tenant è federato con un altro IdP per l'accesso, consentendo l'accelerazione automatica si semplifica l'accesso dell'utente quando si è a conoscenza del fatto che tutti coloro che effettuano l'accesso possono essere autenticati da tale IdP.  È possibile configurare l'accelerazione automatica per singole applicazioni.

>[!NOTE]
>Se si configura l'accelerazione automatica per un'applicazione, gli utenti guest non possono effettuare l'accesso. Indirizzare l'utente direttamente a un IdP federato per l'autenticazione è una strada a senso unico poiché non c'è modo di tornare alla pagina di accesso di Azure Active Directory.  Gli utenti guest, che potrebbero dover essere indirizzati ad altri tenant o a un IdP esterno come un account Microsoft al fine dell'autenticazione, non potranno effettuare l'accesso a tale applicazione poiché il passaggio dell'individuazione dell'area di autenticazione principale viene saltato.  

Esistono due modi per controllare l'accelerazione automatica a un IdP federato.  È possibile:    

- Usare un hint di dominio o richieste di autenticazione per un'applicazione 
- Configurare un criterio di individuazione dell'area di autenticazione principale (home realm discovery, HRD) per consentire l'accelerazione automatica

## <a name="domain-hints"></a>Hint di dominio 
Gli hint di dominio sono direttive incluse nella richiesta di autenticazione da un'applicazione.  Possono essere usate per accelerare l'indirizzamento dell'utente alla rispettiva pagina di accesso dell'IdP federato oppure possono essere usate da un'applicazione multi-tenant per accelerare l'utente direttamente verso la pagina di accesso di Azure AD personalizzata per il suo tenant.  Per esempio, un'applicazione largeapp.com potrebbe consentire ai suoi utenti di accedere all'applicazione tramite un URL personalizzato contoso.largeapp.com e includere un hint di dominio a Contoso.com nella richiesta di autenticazione. La sintassi dell'hint di dominio varia in base al protocollo in uso ed è generalmente configurata nell'applicazione.

**WS-Federation**:  whr=contoso.com nella stringa della query

**SAML**:  una richiesta di autenticazione SAML contenente un hint di dominio, oppure una stringa di query whr=contoso.com

**OpenID Connect**: una stringa di query domain_hint=contoso.com 

Se viene incluso un hint di dominio nella richiesta di autenticazione dall'applicazione e il tenant è federato con il dominio, Azure AD tenta di reindirizzare l'accesso all'IdP configurato per tale dominio.  Se l'hint di dominio non fa riferimento a un dominio federato verificato, è ignorato e viene richiamata la normale individuazione dell'area di autenticazione principale.

Consultare questo [post del blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/) per maggiori informazioni sull'accelerazione automatica supportata da Azure Active Directory.

>[!NOTE]
>Se viene incluso un hint di dominio in una richiesta di autenticazione, la sua presenza sostituisce qualunque criterio HRD impostato per l'applicazione.

## <a name="home-realm-discovery-hrd-policy"></a>Criterio di individuazione dell'area di autenticazione principale (Home realm discovery, HRD)
Alcune applicazioni non forniscono un modo per configurare la richiesta di autenticazione che emettono e in questi casi non è possibile usare gli hint di dominio per controllare l'accelerazione automatica.   L'accelerazione automatica può essere configurata tramite criterio per ottenere lo stesso comportamento.  

### <a name="setting-hrd-policy"></a>Impostazione del criterio HRD
Esistono tre passaggi per la configurazione dell'accelerazione automatica dell'accesso in un'applicazione


1. Creazione di un criterio HRD per l'accelerazione automatica
2. Individuazione dell'entità servizio alla quale collegare il criterio
3. Collegamento del criterio all'entità servizio.  È possibile che siano stati creati dei criteri in un tenant ma questi non hanno alcun effetto fino a che non sono collegati a un'entità.  Un criterio HRD può essere collegato a un'entità servizio e un solo criterio HRD può essere attivo in una data entità in un determinato momento.  

È possibile usare direttamente l'API Graph oppure i cmdlet PowerShell di Microsoft Azure Active Directory per configurare l'accelerazione automatica usando il criterio HRD

L'API Graph che manipola il criterio è descritta [qui](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations).

Qui è riportato un esempio di definizione di criterio HRD:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

Il tipo di criterio è "HomeRealmDiscoveryPolicy".

Se **AccelerateToFederatedDomain** è falso, allora il criterio non ha effetto **PreferredDomain** deve indicare un dominio verso cui eseguire l'accelerazione e può essere omesso nel caso in cui il tenant abbia unicamente un dominio federato.  Se è omesso ed esiste più di un dominio federato verificato, il criterio non ha effetto.

Se si specifica **PreferredDomain**, questo deve corrispondere a un dominio federato verificato per il tenant e tutti gli utenti dell'applicazione in questione devono essere in grado di accedere a tale dominio.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorità e valutazione dei criteri HRD
È possibile creare criteri HRD e assegnarli a specifiche organizzazioni e entità servizio. È quindi possibile che più criteri si applichino a un'applicazione specifica. II criterio HRD che entra in vigore si basa sulle seguenti regole:


- Se è presente un hint di dominio nella richiesta di autenticazione, allora qualunque politica HRD è ignorata e viene usato il comportamento specificato dall'hint di dominio.
- Altrimenti, se il criterio è assegnato in modo esplicito all'entità servizio, allora viene applicato. 
- Se non è presente alcun hint di dominio e all'entità servizio non sono assegnati criteri in modo esplicito, vengono applicati i criteri assegnati in modo esplicito all'elemento organizzazione padre dell'entità servizio. 
- Se non è presente alcun hint di dominio e non è stato assegnato alcun criterio all'entità servizio o all'organizzazione, viene usato il comportamento HRD predefinito.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>Esercitazione per l'impostazione dell'accelerazione automatica dell'accesso in un'applicazione che usa un criterio HRD con cmdlet Azure Active Directory PowerShell
Verranno descritti alcuni scenari, tra cui:


- Configurazione dell'accelerazione automatica per un'applicazione per un tenant con un unico dominio federato
- Configurazione dell'accelerazione automatica per un'applicazione per uno dei diversi domini verificati per il tenant
- Elenco delle applicazioni per cui è configurato un criterio

### <a name="prerequisites"></a>Prerequisiti
Negli esempi di seguito è possibile creare, aggiornare, collegare ed eliminare criteri su entità servizio di un'applicazione in Azure AD.

1.  Per iniziare, scaricare la versione di anteprima dei cmdlet Azure AD PowerShell più recente. 
2.  Quando si hanno a disposizione i cmdlet Azure AD PowerShell, eseguire il comando Connect per accedere all'account amministratore di Azure AD.

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Eseguire questo comando per visualizzare tutti i criteri dell'organizzazione.

    ``` powershell
    Get-AzureADPolicy
    ```

Se non viene restituito alcun risultato, non sono presenti criteri creati nel tenant

### <a name="example-setting-auto-acceleration-for-an-application"></a>Esempio: configurazione dell'accelerazione automatica per un'applicazione 
In questo esempio, è possibile creare un criterio che consente l'accelerazione automatica degli utenti a una schermata di accesso di AD FS quando si effettua l'accesso a un'applicazione.  È possibile eseguire questa operazione senza che gli utenti immettano prima un nome utente nella pagina di accesso di Azure AD. 

#### <a name="step-1-create-an-hrd-policy"></a>Passaggio 1: creazione di un criterio HRD
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Se si dispone di un unico dominio federato che autentica gli utenti per le applicazioni, è necessario creare solo un criterio HRD.  
Per visualizzare i nuovi criteri e ottenere il relativo ObjectID, eseguire il comando indicato di seguito.

``` powershell
Get-AzureADPolicy
```


Quando si dispone di un criterio HRD, al fine di consentire l'accelerazione automatica è possibile assegnarlo a diverse entità servizio dell'applicazione.

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>Passaggio 2: individuazione dell'entità servizio a cui assegnare il criterio.  
È necessario disporre dell'ObjectId delle entità servizio alle quali si desidera assegnare il criterio. Esistono diversi modi per trovare l'ID oggetto delle entità servizio.    

È possibile utilizzare il portale, eseguire una query di [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) o aprire lo strumento [Graph Explorer](https://graphexplorer.cloudapp.net/) e accedere all'account di Azure AD per visualizzare tutte le entità servizio dell'organizzazione o, dato che si sta usando PowerShell, è possibile utilizzare il cmdlet get-AzureADServicePrincipal per elencare le entità servizio e i relativi ID.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Passaggio 3: assegnazione del criterio all'entità servizio  
Dopo aver ottenuto l'ObjectId dell'entità servizio dell'applicazione per cui si desidera configurare l'accelerazione automatica, eseguire il seguente comando per associare il criterio HRD creato al passaggio 1 all'entità servizio individuata al passaggio 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

È possibile ripetere questo comando per ciascuna entità servizio alla quale si desidera aggiungere il criterio.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Passaggio 4: verifica delle entità servizio dell'applicazione a cui è assegnato il criterio di accelerazione automatica
Per verificare quali applicazioni hanno un criterio di accelerazione automatica configurato, usare il cmdlet Get-AzureADPolicyAppliedObject e passare l'ObjectId del criterio che si desidera verificare.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Passaggio 5: operazione completata
Provare l'applicazione per verificare che il nuovo criterio funzioni.

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Esempio: elenco delle applicazioni per cui è configurato un criterio di accelerazione automatica

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>Passaggio 1: elenco di tutti i criteri creati nell'organizzazione 

``` powershell
Get-AzureADPolicy
```

Individuare l'**ID oggetto** del criterio per cui si desidera elencare le assegnazioni.

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>Passaggio 2: elencare le entità servizio a cui è assegnato il criterio.  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>Esempio: rimozione di un criterio di accelerazione automatica per un'applicazione
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>Passaggio 1: usare l'esempio precedente per ottenere l'ObjectId del criterio e quello dell'entità servizio dell'applicazione dalla quale si desidera rimuoverlo
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Passaggio 2: rimuovere l'assegnazione del criterio dall'entità servizio dell'applicazione.  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>Passaggio 3: verificare la rimozione elencando le entità servizio a cui è assegnato il criterio 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sul funzionamento dell'autenticazione in Azure AD, vedere [Scenari di autenticazione per Azure AD](develop/active-directory-authentication-scenarios.md).
- Per maggiori informazioni sul Single Sign-On per l'utente, consultare [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-enterprise-apps-manage-sso.md)
- Per una panoramica di tutti i contenuti correlati allo sviluppo, vedere la [Guida per gli sviluppatori di Active Directory](develop/active-directory-developers-guide.md).

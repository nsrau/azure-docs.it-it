---
title: Configurare l'accelerazione automatica dell'accesso usando criteri di individuazione dell'area di autenticazione principale | Microsoft Docs
description: Illustra il significato di tenant di Azure AD e come gestire Azure con Azure Active Directory.
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
ms.openlocfilehash: deaa52a062eb01450f760324e01e520fcbe894e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configurare l'accelerazione automatica dell'accesso usando criteri di individuazione dell'area di autenticazione principale

Il documento seguente fornisce un'introduzione all'individuazione dell'area di autenticazione principale e all'accelerazione automatica.

## <a name="home-realm-discovery"></a>Individuazione dell'area di autenticazione principale
L'individuazione dell'area di autenticazione principale (HRD) è il processo che consente a Azure Active Directory (Azure AD) di determinare, al momento dell'accesso, dove deve eseguire l'autenticazione l'utente.  Quando un utente effettua l'accesso a un tenant di Azure AD per accedere a una risorsa, o nella pagina di accesso comune di Azure AD, l'utente digita un nome utente (UPN). Azure AD lo usa per individuare dove l'utente deve effettuare l'accesso. 

L'utente potrebbe dover accedere a uno dei percorsi seguenti per poter essere autenticato:

- Il tenant principale dell'utente (potrebbe essere lo stesso tenant della risorsa a cui l'utente sta cercando di effettuare l'accesso). 

- Account Microsoft.  L'utente è un ospite nel tenant della risorsa.

- Un altro provider di identità federato con il tenant di Azure AD.

-  Un provider di identità locale, ad esempio Active Directory Federation Services (AD FS).

## <a name="auto-acceleration"></a>Accelerazione automatica 
Alcune organizzazioni configurano il proprio tenant di Azure Active Directory in modo che esegua la federazione con un altro IdP, come AD FS, per l'autenticazione dell'utente.  

In questi casi, quando un utente accede a un'applicazione, viene innanzitutto visualizzata una pagina di accesso di Azure AD. Dopo aver digitato il nome dell'entità utente, viene visualizzata la pagina di accesso del provider di identità. In alcuni casi, è consigliabile per gli amministratori indirizzare gli utenti alla pagina di accesso quando accedono ad applicazioni specifiche. 

Ciò significa che gli utenti possono ignorare la pagina iniziale di Azure Active Directory. Questo processo è noto come "accelerazione automatica dell'accesso".

Nei casi in cui il tenant è federato a un altro provider di identità per l'accesso, l'accelerazione automatica semplifica maggiormente l'accesso.  È possibile configurare l'accelerazione automatica per singole applicazioni.

>[!NOTE]
>Se si configura l'accelerazione automatica per un'applicazione, gli utenti guest non possono effettuare l'accesso. Se si indirizza un utente direttamente a un IdP federato per l'autenticazione non potrà più tornare alla pagina di accesso di Azure Active Directory. Gli utenti guest, che potrebbero dover essere indirizzati ad altri tenant o a un provider di identità esterno, ad esempio un account Microsoft, non potranno effettuare l'accesso a tale applicazione poiché il passaggio dell'individuazione dell'area di autenticazione principale viene saltato.  

Esistono due modi per controllare l'accelerazione automatica a un IdP federato:   

- Usare un hint di dominio o richieste di autenticazione per un'applicazione. 
- Configurare criteri di individuazione dell'area di autenticazione principale per consentire l'accelerazione automatica.

## <a name="domain-hints"></a>Hint di dominio 
Gli hint di dominio sono direttive incluse nella richiesta di autenticazione da un'applicazione. Possono essere usate per accelerare l'indirizzamento dell'utente alla rispettiva pagina di accesso dell'IdP federato. Oppure possono essere usati da un'applicazione multi-tenant per accelerare l'utente direttamente verso la pagina di accesso di Azure AD personalizzata per il suo tenant.  

Ad esempio, l'applicazione "largeapp.com" potrebbe consentire ai suoi utenti di accedere all'applicazione tramite un URL personalizzato "contoso.largeapp.com". L'applicazione potrebbe anche includere un hint di dominio a Contoso.com nella richiesta di autenticazione. 

La sintassi dell'hint di dominio varia in base al protocollo usato ed è generalmente configurata nell'applicazione.

**WS-Federation**: whr=contoso.com nella stringa della query.

**SAML**: una richiesta di autenticazione SAML che contiene un hint di dominio oppure una stringa di query whr=contoso.com.

**OpenID Connect**: una stringa di query domain_hint=contoso.com. 

Se viene incluso un hint di dominio nella richiesta di autenticazione dall'applicazione e il tenant è federato con il dominio, Azure AD tenta di reindirizzare l'accesso all'IdP configurato per tale dominio. 

Se l'hint di dominio non fa riferimento a un dominio federato verificato, è ignorato e viene richiamata la normale individuazione dell'area di autenticazione principale.

Per altre informazioni sull'accelerazione automatica mediante gli hint di dominio supportati da Azure Active Directory, vedere il [blog su Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Se viene incluso un hint di dominio in una richiesta di autenticazione, la sua presenza sostituisce i criteri HRD impostati per l'applicazione.

## <a name="home-realm-discovery-policy"></a>Criteri di individuazione dell'area di autenticazione principale
Alcune applicazioni non forniscono un modo per configurare la richiesta di autenticazione che generano. In questi casi, non è possibile usare gli hint di dominio per controllare l'accelerazione automaticamente. L'accelerazione automatica può essere configurata tramite criterio per ottenere lo stesso comportamento.  

### <a name="set-hrd-policy"></a>Impostare i criteri HRD
Esistono tre passaggi per la configurazione dell'accelerazione automatica dell'accesso in un'applicazione:


1. Creazione di criteri HRD per l'accelerazione automatica.

2. Individuazione dell'entità servizio a cui collegare i criteri.

3. Collegamento del criterio all'entità servizio. È possibile che siano stati creati dei criteri in un tenant ma questi non hanno alcun effetto fino a che non sono collegati a un'entità. 

I criteri HRD possono essere collegati a un'entità servizio e un solo dei criteri HRD può essere attivo in una data entità in un determinato momento.  

È possibile usare direttamente l'API Graph oppure i cmdlet PowerShell di Microsoft Azure Active Directory per configurare l'accelerazione automatica usando i criteri HRD.

L'API Graph che modifica i criteri è descritta nell'articolo [Operazioni sui criteri](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) in MSDN.

Di seguito è riportato un esempio di definizione di criteri HRD:
    
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

Se **AccelerateToFederatedDomain** è falso, i criteri non hanno alcun effetto.

**PreferredDomain** deve indicare un dominio al quale accelerare. Può essere omesso se il tenant dispone di un solo dominio federato.  Se è omesso ed esiste più di un dominio federato verificato, i criteri non hanno effetto.

Se **PreferredDomain** è specificato, deve corrispondere a un dominio federato verificato per il tenant. Tutti gli utenti dell'applicazione devono essere in grado di accedere a tale dominio.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorità e valutazione dei criteri HRD
È possibile creare criteri HRD e assegnarli a specifiche organizzazioni e entità servizio. È quindi possibile che più criteri si applichino a un'applicazione specifica. II criterio HRD che entra in vigore si basa sulle seguenti regole:


- Se è presente un hint di dominio nella richiesta di autenticazione, tutti i criteri HRD vengono ignorati. Viene usato il comportamento specificato dall'hint di dominio.

- Altrimenti, se il criterio è assegnato in modo esplicito all'entità servizio, allora viene applicato. 

- Se non è presente alcun hint di dominio e all'entità servizio non sono assegnati criteri in modo esplicito, vengono applicati i criteri assegnati in modo esplicito all'elemento organizzazione padre dell'entità servizio. 

- Se non è presente alcun hint di dominio e non è stato assegnato alcun criterio all'entità servizio o all'organizzazione, viene usato il comportamento HRD predefinito.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>Esercitazione per l'impostazione dell'accelerazione automatica dell'accesso in un'applicazione usando criteri HRD
Verrà usato un cmdlet Azure AD PowerShell per esaminare in modo dettagliato alcuni scenari, tra cui:


- Configurazione dell'accelerazione automatica per un'applicazione per un tenant con un unico dominio federato.

- Configurazione dell'accelerazione automatica per un'applicazione per uno dei diversi domini verificati per il tenant.

- Elenco delle applicazioni per cui sono configurati i criteri.

### <a name="prerequisites"></a>prerequisiti
Negli esempi seguenti è possibile creare, aggiornare, collegare ed eliminare criteri su entità servizio di un'applicazione in Azure AD.

1.  Per iniziare, scaricare la versione di anteprima dei cmdlet Azure AD PowerShell più recente. 

2.  Dopo aver scaricato i cmdlet Azure AD PowerShell, eseguire il comando Connect per accedere all'account amministratore di Azure AD:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Eseguire questo comando per visualizzare tutti i criteri dell'organizzazione:

    ``` powershell
    Get-AzureADPolicy
    ```

Se non viene restituito alcun risultato, significa che non sono presenti criteri creati nel tenant.

### <a name="example-set-auto-acceleration-for-an-application"></a>Esempio: configurare l'accelerazione automatica per un'applicazione 
In questo esempio, è possibile creare criteri che consentono l'accelerazione automatica degli utenti a una schermata di accesso di AD FS quando si effettua l'accesso a un'applicazione. Gli utenti possono accedere ad AD FS senza dover immettere prima un nome utente nella pagina di accesso di Azure AD. 

#### <a name="step-1-create-an-hrd-policy"></a>Passaggio 1: creazione di un criterio HRD
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Se si dispone di un unico dominio federato che autentica gli utenti per le applicazioni, è necessario creare solo uno dei criteri HRD.  

Per visualizzare i nuovi criteri e ottenere il relativo **ObjectID**, eseguire questo comando:

``` powershell
Get-AzureADPolicy
```


Quando si dispone di criteri HRD, al fine di consentire l'accelerazione automatica è possibile assegnarli a diverse entità servizio dell'applicazione.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Passaggio 2: individuazione dell'entità servizio a cui assegnare i criteri  
È necessario disporre dell'**ObjectID** delle entità servizio alle quali si intende assegnare i criteri. Esistono diversi modi per trovare l'**ObjectID** delle entità servizio.    

È possibile usare il portale oppure è possibile eseguire una query [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Per visualizzare tutte le entità servizio dell'organizzazione, è inoltre possibile passare allo [strumento Graph Explorer](https://graphexplorer.cloudapp.net/) e accedere all'account Azure AD. Poiché si usa PowerShell, è possibile elencare le entità servizio e i relativi ID mediante il cmdlet get-AzureADServicePrincipal.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Passaggio 3: assegnazione del criterio all'entità servizio  
Dopo aver creato l'**ObjectID** dell'entità servizio dell'applicazione per la quale si intende configurare l'accelerazione automatica, eseguire questo comando. Questo comando associa i criteri HRD creati nel passaggio 1 con l'entità servizio individuata nel passaggio 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

È possibile ripetere questo comando per ciascuna entità servizio alla quale si intende aggiungere i criteri.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Passaggio 4: verifica delle entità servizio dell'applicazione a cui è assegnato il criterio di accelerazione automatica
Per verificare quali applicazioni hanno criteri di accelerazione automatica configurato, usare il cmdlet **Get-AzureADPolicyAppliedObject**. Passare l'**ObjectID** dei criteri che si intende verificare.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Passaggio 5: operazione completata
Provare l'applicazione per verificare che il nuovo criterio funzioni.

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Esempio: elencare le applicazioni per cui sono configurati criteri di accelerazione automatica

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Passaggio 1: elencare tutti i criteri creati nell'organizzazione 

``` powershell
Get-AzureADPolicy
```

Individuare l'**ID oggetto** dei criteri per cui si intende elencare le assegnazioni.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Passaggio 2: elencare le entità servizio a cui sono assegnati i criteri  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>Esempio: rimuovere i criteri di accelerazione automatica per un'applicazione
#### <a name="step-1-get-the-objectid"></a>Passaggio 1: ottenere l'ObjectID
Usare l'esempio precedente per ottenere l'**ObjectID** dei criteri e quello dell'entità servizio dell'applicazione dalla quale si intende rimuoverli. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Passaggio 2: rimuovere l'assegnazione dei criteri dall'entità servizio dell'applicazione  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Passaggio 3: verificare la rimozione elencando le entità servizio a cui sono assegnati i criteri 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sul funzionamento dell'autenticazione in Azure AD, vedere [Scenari di autenticazione per Azure AD](develop/active-directory-authentication-scenarios.md).
- Per altre informazioni sul Single Sign-On per l'utente, consultare [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).
- Per una panoramica di tutti i contenuti correlati allo sviluppo, vedere la [Guida per gli sviluppatori di Active Directory](develop/active-directory-developers-guide.md).

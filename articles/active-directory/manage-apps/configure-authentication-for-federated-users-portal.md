---
title: Configurare l'accelerazione automatica dell'accesso usando criteri di individuazione dell'area di autenticazione principale | Microsoft Docs
description: Illustra il significato di tenant di Azure AD e come gestire Azure con Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: barbkess
ms.openlocfilehash: f24be44b00f9c4e789e8d4797f6a0516dcfe940f
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494058"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configurare i comportamenti delle informazioni di accesso di Azure Active Directory per un'applicazione usando criteri di individuazione dell'area di autenticazione principale

In questo documento viene fornita un'introduzione alla configurazione del comportamento di autenticazione di Azure Active Directory per gli utenti federati.   Viene illustrata la configurazione delle restrizioni di accelerazione automatica e di autenticazione per gli utenti di domini federati.

## <a name="home-realm-discovery"></a>Individuazione dell'area di autenticazione principale
L'individuazione dell'area di autenticazione principale (HRD) è il processo che consente ad Azure Active Directory (Azure AD) di determinare, al momento dell'accesso, dove deve eseguire l'autenticazione l'utente.  Quando un utente effettua l'accesso a un tenant di Azure AD per accedere a una risorsa, o nella pagina di accesso comune di Azure AD, l'utente digita un nome utente (UPN). Azure AD lo usa per individuare dove l'utente deve effettuare l'accesso. 

L'utente potrebbe dover accedere a uno dei percorsi seguenti per poter essere autenticato:

- Il tenant principale dell'utente (potrebbe essere lo stesso tenant della risorsa a cui l'utente sta cercando di effettuare l'accesso). 

- Account Microsoft.  L'utente è un ospite nel tenant della risorsa.

-  Un provider di identità locale, ad esempio Active Directory Federation Services (AD FS).

- Un altro provider di identità federato con il tenant di Azure AD.

## <a name="auto-acceleration"></a>Accelerazione automatica 
Alcune organizzazioni configurano i domini di Azure Active Directory in modo da eseguire la federazione con un altro IdP, come AD FS, per l'autenticazione dell'utente.  

Quando un utente accede a un'applicazione, viene innanzitutto visualizzata una pagina di accesso di Azure AD. Dopo aver digitato il nome dell'entità utente, se ci si trova in un dominio federato verrà visualizzata la pagina di accesso del provider di identità del dominio. In alcuni casi, è consigliabile per gli amministratori indirizzare gli utenti alla pagina di accesso quando accedono ad applicazioni specifiche. 

Ciò significa che gli utenti possono ignorare la pagina iniziale di Azure Active Directory. Questo processo è noto come "accelerazione automatica dell'accesso".

Nei casi in cui il tenant è federato a un altro provider di identità per l'accesso, l'accelerazione automatica semplifica maggiormente l'accesso.  È possibile configurare l'accelerazione automatica per singole applicazioni.

>[!NOTE]
>Se si configura l'accelerazione automatica per un'applicazione, gli utenti guest non possono effettuare l'accesso. Se si indirizza un utente direttamente a un IdP federato per l'autenticazione non potrà più tornare alla pagina di accesso di Azure Active Directory. Gli utenti guest, che potrebbero dover essere indirizzati ad altri tenant o a un provider di identità esterno, ad esempio un account Microsoft, non potranno effettuare l'accesso a tale applicazione poiché il passaggio dell'individuazione dell'area di autenticazione principale viene saltato.  

Esistono due modi per controllare l'accelerazione automatica a un IdP federato:   

- Usare un hint di dominio o richieste di autenticazione per un'applicazione. 
- Configurare criteri di individuazione dell'area di autenticazione principale per consentire l'accelerazione automatica.

### <a name="domain-hints"></a>Hint di dominio    
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
>Se viene incluso un hint di dominio in una richiesta di autenticazione, la sua presenza sostituisce l'accelerazione automatica impostata per l'applicazione nei criteri HRD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Criteri di individuazione dell'area di autenticazione principale per l'accelerazione automatica
Alcune applicazioni non forniscono un modo per configurare la richiesta di autenticazione che generano. In questi casi, non è possibile usare gli hint di dominio per controllare l'accelerazione automaticamente. L'accelerazione automatica può essere configurata tramite criterio per ottenere lo stesso comportamento.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Abilitare l'autenticazione diretta per le applicazioni legacy
Per le applicazioni, è consigliabile usare le librerie di Azure AD e l'accesso interattivo per autenticare gli utenti. Le librerie si occupano dei flussi dell'utente federato.  Le applicazioni legacy in alcuni casi non vengono scritte per comprendere la federazione. Queste non eseguono l'individuazione dell'area di autenticazione e non interagiscono con il corretto endpoint federato per autenticare un utente. Se si desidera, è possibile usare criteri HRD per attivare applicazioni legacy specifiche che consentono di inviare le credenziali di nome utente/password per l'autenticazione diretta con Azure Active Directory. La sincronizzazione degli hash delle password deve essere abilitata. 

> [!IMPORTANT]
> Abilitare l'autenticazione diretta solo se è stata attivata la sincronizzazione degli hash password e se è nota la possibilità di eseguire l'autenticazione di questa applicazione senza eventuali criteri implementati dal provider di identità in locale. Se si disattiva la sincronizzazione degli hash password o si disattiva la sincronizzazione della directory con Active Directory Connect per qualsiasi motivo, è necessario rimuovere questo criterio per impedire l'insorgere di autenticazione diretta usando un hash della password non aggiornata.

## <a name="set-hrd-policy"></a>Impostare i criteri HRD
Esistono tre passaggi per l'impostazione dei criteri HRD su un'applicazione per l'accelerazione automatica federata di accesso o altre applicazioni dirette basate su cloud:

1. Creazione di un criterio HRD.

2. Individuazione dell'entità servizio a cui collegare i criteri.

3. Collegamento del criterio all'entità servizio. 

I criteri diventano effettivi per una specifica applicazione solo quando sono connessi a un'entità servizio. 

Un solo criterio HRD può essere attivo in un'entità servizio in qualsiasi momento.  

È possibile usare direttamente l'API Graph oppure i cmdlet PowerShell di Microsoft Azure Active Directory per creare e gestire i criteri HRD.

L'API Graph che modifica i criteri è descritta nell'articolo [Operazioni sui criteri](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) in MSDN.

Di seguito è riportato un esempio di definizione di criteri HRD:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

Il tipo di criteri è "HomeRealmDiscoveryPolicy".

**AccelerateToFederatedDomain** è facoltativo. Se **AccelerateToFederatedDomain** è falso, i criteri non hanno alcun effetto sull'accelerazione automatica. Se **AccelerateToFederatedDomain** è vero ed esiste solo un dominio verificato e federato nel tenant, gli utenti verranno condotti direttamente al provider di identità federato per l'accesso. Se è vero ed è presente più di un dominio verificato del tenant, **PreferredDomain** deve essere specificato.

**PreferredDomain** è facoltativo. **PreferredDomain** deve indicare un dominio al quale accelerare. Può essere omesso se il tenant dispone di un solo dominio federato.  Se è omesso ed esiste più di un dominio federato verificato, i criteri non hanno effetto.

 Se **PreferredDomain** è specificato, deve corrispondere a un dominio federato verificato per il tenant. Tutti gli utenti dell'applicazione devono essere in grado di accedere a tale dominio.

**AllowCloudPasswordValidation** è facoltativo. Se **AllowCloudPasswordValidation** è vero, l'applicazione può autenticare un utente federato presentando le credenziali di nome utente/password direttamente al token di autenticazione dell'endpoint di Azure Active Directory. Tale operazione viene eseguita solo se la sincronizzazione degli hash delle password è abilitata.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorità e valutazione dei criteri HRD
È possibile creare criteri HRD e assegnarli a specifiche organizzazioni e entità servizio. È quindi possibile che più criteri si applichino a un'applicazione specifica. II criterio HRD che entra in vigore si basa sulle seguenti regole:


- Se è presente un hint di dominio nella richiesta di autenticazione, tutti i criteri HRD vengono ignorati per l'accelerazione automatica. Viene usato il comportamento specificato dall'hint di dominio.

- Altrimenti, se il criterio è assegnato in modo esplicito all'entità servizio, allora viene applicato. 

- Se non è presente alcun hint di dominio e all'entità servizio non sono assegnati criteri in modo esplicito, vengono applicati i criteri assegnati in modo esplicito all'elemento organizzazione padre dell'entità servizio. 

- Se non è presente alcun hint di dominio e non è stato assegnato alcun criterio all'entità servizio o all'organizzazione, viene usato il comportamento HRD predefinito.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Tutorial per l'impostazione dei criteri HRD in un'applicazione 
Verrà usato un cmdlet Azure AD PowerShell per esaminare in modo dettagliato alcuni scenari, tra cui:


- Configurazione del criterio HDR per eseguire l'accelerazione automatica di un'applicazione per un tenant con un unico dominio federato.

- Configurazione del criterio HRD per eseguire l'accelerazione automatica per un'applicazione per uno dei diversi domini verificati per il tenant.

- Impostazione di criteri HRD per abilitare un'applicazione legacy a indirizzare l'autenticazione di nome utente/password in Azure Active Directory per un utente federato.

- Elenco delle applicazioni per cui sono configurati i criteri.


### <a name="prerequisites"></a>Prerequisiti
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

### <a name="example-set-hrd-policy-for-an-application"></a>Esempio: impostare criteri HRD per un'applicazione 

In questo esempio, viene creato un criterio che, se assegnato a un'applicazione: 
- Consente l'accelerazione automatica degli utenti a una schermata di accesso di AD FS quando effettuano l'accesso a un'applicazione qualora sia presente un dominio singolo nel corrispettivo tenant. 
- Consente l'accelerazione automatica degli utenti a una schermata di accesso di AD FS se vi è più di un dominio federato nel corrispettivo tenant.
- Consente l'accesso non interattivo a nome utente/password direttamente ad Azure Active Directory agli utenti federati per le applicazioni a cui è stato assegnato il criterio.

#### <a name="step-1-create-an-hrd-policy"></a>Passaggio 1: creazione di un criterio HRD

Il criterio seguente consente l'accelerazione automatica degli utenti a una schermata di accesso di AD FS quando effettuano l'accesso a un'applicazione qualora sia presente un dominio singolo nel corrispettivo tenant.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Il criterio seguente consente l'accelerazione automatica degli utenti a una schermata di accesso di AD FS se vi è più di un dominio federato nel corrispettivo tenant. Se si dispone di più di un dominio federato che autentica gli utenti per le applicazioni, è necessario specificare il dominio per l'accelerazione automatica.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Per creare un criterio per abilitare l'autenticazione di nome utente/password per gli utenti federati direttamente con Azure Active Directory per applicazioni specifiche, eseguire il comando seguente:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Per visualizzare i nuovi criteri e ottenere il relativo **ObjectID**, eseguire questo comando:

``` powershell
Get-AzureADPolicy
```


Per eseguire i criteri HRD dopo la rispettiva creazione, è possibile assegnarli a diverse entità servizio dell'applicazione.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Passaggio 2: individuazione dell'entità servizio a cui assegnare i criteri  
È necessario disporre dell'**ObjectID** delle entità servizio alle quali si intende assegnare i criteri. Esistono diversi modi per trovare l'**ObjectID** delle entità servizio.    

È possibile usare il portale oppure è possibile eseguire una query [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Per visualizzare tutte le entità servizio dell'organizzazione, è inoltre possibile passare allo [strumento Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) e accedere all'account Azure AD. Poiché si usa PowerShell, è possibile elencare le entità servizio e i relativi ID mediante il cmdlet get-AzureADServicePrincipal.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Passaggio 3: assegnazione del criterio all'entità servizio  
Dopo aver creato l'**ObjectID** dell'entità servizio dell'applicazione per la quale si intende configurare l'accelerazione automatica, eseguire questo comando. Questo comando associa i criteri HRD creati nel passaggio 1 con l'entità servizio individuata nel passaggio 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

È possibile ripetere questo comando per ciascuna entità servizio alla quale si intende aggiungere i criteri.

Nel caso in cui un'applicazione disponga già di un criterio HomeRealmDiscovery assegnato, non sarà possibile aggiungerne un secondo.  In tal caso, modificare la definizione dei criteri di individuazione dell'area di autenticazione principale assegnati all'applicazione per aggiungere altri parametri.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Passaggio 4: verifica delle entità servizio dell'applicazione a cui è assegnato il criterio HRD
Per verificare quali applicazioni hanno criteri HRD configurati, usare il cmdlet **Get-AzureADPolicyAppliedObject**. Passare l'**ObjectID** dei criteri che si intende verificare.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Passaggio 5: operazione completata
Provare l'applicazione per verificare che il nuovo criterio funzioni.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Esempio: elencare le applicazioni per cui sono configurati criteri HRD

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Passaggio 1: elencare tutti i criteri creati nell'organizzazione 

``` powershell
Get-AzureADPolicy
```

Individuare l'**ID oggetto** dei criteri per cui si intende elencare le assegnazioni.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Passaggio 2: elencare le entità servizio a cui sono assegnati i criteri  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Esempio: rimuovere i criteri HRD per un'applicazione
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
- Per altre informazioni sul funzionamento dell'autenticazione in Azure AD, vedere [Scenari di autenticazione per Azure AD](../develop/authentication-scenarios.md).
- Per altre informazioni sul Single Sign-On per l'utente, consultare [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](configure-single-sign-on-portal.md).
- Per una panoramica di tutti i contenuti correlati allo sviluppo, vedere la [Guida per gli sviluppatori di Active Directory](../develop/azure-ad-developers-guide.md).

---
title: Accedere con un indirizzo e-mail come ID di accesso alternativo per Azure Active Directory
description: Informazioni su come configurare e abilitare gli utenti ad accedere ad Azure Active Directory usando il proprio indirizzo e-mail come ID di accesso alternativo (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: ed317039e683ef36054d5ace612e09ca75dfa11e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837310"
---
# <a name="sign-in-to-azure-using-email-as-an-alternate-login-id-preview"></a>Accedere ad Azure usando l'indirizzo e-mail come ID di accesso alternativo (anteprima)

Molte organizzazioni vogliono consentire agli utenti di accedere ad Azure usando le stesse credenziali dell'ambiente della directory locale. Tramite questo approccio, noto come autenticazione ibrida, gli utenti dovranno ricordare solo una coppia di credenziali.

Alcune organizzazioni non sono passate all'autenticazione ibrida per i motivi seguenti:

* Per impostazione predefinita, il nome di accesso UPN di Azure Active Directory (Azure AD) è impostato sullo stesso UPN della directory locale.
* Modificando l'UPN di Azure AD si crea una corrispondenza errata tra gli ambienti locali e di Azure che potrebbe causare problemi con determinate applicazioni e servizi.
* A causa di motivi aziendali o di conformità, l'organizzazione non vuole usare l'UPN locale per accedere ad Azure.

Per favorire il passaggio all'autenticazione ibrida, ora è possibile configurare Azure AD per consentire agli utenti di accedere ad Azure con un indirizzo e-mail nel dominio verificato come ID di accesso alternativo. Ad esempio, se *Contoso* è stato rinominato in *Fabrikam*, anziché continuare ad accedere con l'UPN `balas@contoso.com` precedente ora è possibile usare l'indirizzo e-mail come ID di accesso alternativo. Per accedere a un'applicazione o ai servizi, gli utenti accedono ad Azure usando l'indirizzo e-mail loro assegnato, come `balas@fabrikam.com`.

|     |
| --- |
| Accedere ad Azure AD con l'indirizzo e-mail come ID di accesso alternativo è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Panoramica degli approcci di accesso di Azure AD

I nomi di accesso UPN sono identificatori univoci per un account utente nella directory locale e in Azure AD. Ogni account utente in una directory è rappresentato da un UPN, come `balas@contoso.com`. Per impostazione predefinita, quando si sincronizza un ambiente locale di Active Directory Domain Services con Azure AD, l'UPN di Azure AD deve corrispondere all'UPN locale.

In molte organizzazioni è possibile impostare l'UPN locale e l'UPN di Azure per far sì che corrispondano. Quando gli utenti accedono ad applicazioni e servizi di Azure, usano il proprio UPN di Azure AD. Tuttavia, alcune organizzazioni non possono usare UPN corrispondenti per l'accesso a causa di politiche aziendali o problemi di esperienza utente.

Le organizzazioni che non possono usare UPN corrispondenti in Azure AD hanno diverse opzioni:

* Un approccio consiste nell'impostare l'UPN di Azure AD su un valore diverso in base alle esigenze aziendali, come `balas@fabrikam.com`.
    * Tuttavia, non tutte le applicazioni e i servizi sono compatibili con l'uso di valori diversi per l'UPN locale e l'UPN di Azure AD.
* Un approccio migliore consiste nel verificare che gli UPN locale e di Azure AD siano impostati sullo stesso valore e quindi configurare Azure AD per consentire agli utenti di accedere ad Azure con il proprio indirizzo e-mail come ID di accesso alternativo.

Grazie all'indirizzo e-mail come ID di accesso alternativo, gli utenti possono continuare ad accedere ad Azure immettendo il proprio UPN, ma possono accedere anche usando il proprio indirizzo e-mail. Per supportare la funzione, si specifica un indirizzo e-mail nell'attributo *ProxyAddresses* dell'utente nella directory locale. L'attributo *ProxyAddresses* supporta uno o più indirizzi di posta elettronica.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Sincronizzare gli indirizzi di posta elettronica per accedere ad Azure AD

L'autenticazione tradizionale di Active Directory Domain Services o di Active Directory Federation Services (AD FS) viene eseguita direttamente nella rete e viene gestita dall'infrastruttura di Active Directory Domain Services. Con l'autenticazione ibrida gli utenti possono invece accedere direttamente ad Azure AD.

Per supportare questo approccio di autenticazione ibrida è necessario sincronizzare l'ambiente di Active Directory Domain Services locale con Azure AD usando [Azure AD Connect][azure-ad-connect] e quindi configurarlo per usare la sincronizzazione dell'hash delle password o l'autenticazione pass-through.

In entrambe le opzioni di configurazione, l'utente immette il proprio nome utente e password in Azure AD, che convalida le credenziali ed emette un ticket. Quando gli utenti accedono ad Azure AD, elimina la necessità dell'organizzazione di ospitare e gestire un'infrastruttura di Active Directory Federation Services.

![Diagramma della soluzione ibrida di gestione delle identità di Azure AD con sincronizzazione dell'hash delle password](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagramma della soluzione ibrida di gestione delle identità di Azure AD con autenticazione pass-through](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Uno degli attributi utente che viene automaticamente sincronizzato da Azure AD Connect è *ProxyAddresses*. Se per gli utenti è stato definito un indirizzo e-mail nell'ambiente di Active Directory Domain Services locale come parte dell'attributo *ProxyAddresses*, questo viene automaticamente sincronizzato in Azure AD. L'indirizzo e-mail può quindi essere usato direttamente nel processo di accesso di Azure AD come ID di accesso alternativo.

> [!IMPORTANT]
> Solo gli indirizzi di posta elettronica nei domini verificati per il tenant vengono sincronizzati in Azure AD. Ogni tenant di Azure AD presenta uno o più domini verificati per i quali si può dimostrare la proprietà e che sono associati in modo univoco al tenant.
>
> Per altre informazioni, vedere [Aggiungere e verificare un nome di dominio personalizzato in Azure AD][verify-domain].

Per altre informazioni, vedere [Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità di Azure AD][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Abilitare l'accesso degli utenti con un indirizzo e-mail

Quando gli utenti ai quali è stato applicato l'attributo *ProxyAddresses* vengono sincronizzati su Azure AD usando Azure AD Connect è necessario abilitare la funzione per far sì che gli utenti accedano con il proprio indirizzo e-mail come ID di accesso alternativo per il tenant. Questa funzionalità indica ai server di accesso di Azure AD di non controllare solo il nome di accesso rispetto ai valori UPN, ma anche rispetto ai valori dell'attributo *ProxyAddresses* per l'indirizzo e-mail.

Durante la fase di anteprima ora è possibile abilitare l'accesso tramite posta elettronica solo come funzionalità di ID di accesso alternativo usando PowerShell. Per completare i passaggi seguenti è necessario disporre delle autorizzazioni di *amministratore tenant*:

1. Aprire una sessione di PowerShell come amministratore, installare il modulo *AzureADPreview* usando il cmdlet [Install-Module][Install-Module]:

    ```powershell
    Install-Module AzureADPreview
    ```

    Se richiesto, selezionare **Y** per installare NuGet o per eseguire l'installazione da un repository non attendibile.

1. Accedere al tenant di Azure AD come *amministratore tenant* usando il cmdlet [Connect-AzureAD][Connect-AzureAD]:

    ```powershell
    Connect-AzureAD
    ```

    Il comando restituisce informazioni su account, ambiente e ID del tenant.

1. Verificare se il criterio *HomeRealmDiscoveryPolicy* è già presente nel tenant usando il cmdlet [Get-AzureADPolicy][Get-AzureADPolicy] come segue:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. In assenza di un criterio attualmente configurato, il comando non restituisce alcun valore. Se viene restituito un criterio, ignorare questo passaggio e passare a quello successivo per aggiornare un criterio esistente.

    Per aggiungere il criterio *HomeRealmDiscoveryPolicy* al tenant, usare il cmdlet [New-AzureADPolicy][New-AzureADPolicy] e impostare l'attributo *AlternateIdLogin* su *"Enabled": true* come mostrato nell'esempio seguente:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Dopo aver completato la creazione del criterio, il comando restituisce l'ID del criterio come mostrato nell'output di esempio seguente:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Se è già stato configurato un criterio, verificare che l'attributo  *AlternateIdLogin*  è abilitato, come mostrato nell'output del criterio di esempio seguente:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Se il criterio esiste ma l'attributo *AlternateIdLogin* non è presente o abilitato oppure se sono presenti altri attributi sul criterio che si vuole preservare, aggiornare il criterio esistente usando il cmdlet [Set-AzureADPolicy][Set-AzureADPolicy].

    > [!IMPORTANT]
    > Quando si aggiorna il criterio, assicurarsi di includere eventuali impostazioni precedenti e il nuovo attributo  *AlternateIdLogin*.

    L'esempio seguente aggiunge l'attributo  *AlternateIdLogin* e preserva l'attributo  *AllowCloudPasswordValidation* che può essere già stato impostato:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Confermare che il criterio aggiornato riflette le modifiche e che l'attributo *AlternateIdLogin* sia ora abilitato:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>Testare l'accesso dell'utente con l'indirizzo e-mail

Per verificare che gli utenti possano accedere con il proprio indirizzo e-mail, passare a [https://myprofile.microsoft.com][my-profile] e accedere con un account utente basato sull'indirizzo e-mail dell'utente in questione, come `balas@fabrikam.com`, e non sull'UPN, come `balas@contoso.com`. L'esperienza di accesso deve essere analoga a quella di un evento di accesso basato su UPN.

## <a name="troubleshoot"></a>Risolvere problemi

Se gli utenti hanno problemi con gli eventi di accesso usando il proprio indirizzo e-mail, esaminare i passaggi di risoluzione dei problemi seguenti:

1. Assicurarsi che per l'account utente sia stato impostato l'indirizzo e-mail per l'attributo *ProxyAddresses* nell'ambiente locale di Active Directory Domain Services.
1. Verificare che Azure AD Connect sia configurato e sincronizzi correttamente gli account utente dall'ambiente locale di Active Directory Domain Services ad Azure AD.
1. Confermare che per il criterio di Azure AD *HomeRealmDiscoveryPolicy* l'attributo *AlternateIdLogin* sia impostato su *"Enabled": true*:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'identità ibrida, come il proxy delle app Azure AD o Azure AD Domain Services, vedere [Identità ibrida di Azure AD per l'accesso e la gestione dei carichi di lavoro locali][hybrid-overview].

Per altre informazioni sulle operazioni con l'identità ibrida, vedere [Funzionamento della sincronizzazione dell'hash delle password][phs-overview] o [Funzionamento della sincronizzazione con autenticazione pass-through][pta-overview].

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com

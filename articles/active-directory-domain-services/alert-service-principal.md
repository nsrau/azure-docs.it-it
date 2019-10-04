---
title: Risolvere gli avvisi dell'entità servizio in Azure AD Domain Services | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi agli avvisi di configurazione dell'entità servizio per Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257971"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Problemi noti: Avvisi entità servizio in Azure Active Directory Domain Services

Le [entità servizio](../active-directory/develop/app-objects-and-service-principals.md) sono applicazioni utilizzate dalla piattaforma Azure per gestire, aggiornare e gestire un dominio gestito Azure AD DS. Se viene eliminata un'entità servizio, le funzionalità del dominio gestito Azure AD DS sono interessate.

Questo articolo consente di risolvere i problemi e risolvere gli avvisi di configurazione relativi all'entità servizio.

## <a name="alert-aadds102-service-principal-not-found"></a>Avviso AADDS102: Entità servizio non trovata

### <a name="alert-message"></a>Messaggio di avviso

*Un'entità servizio necessaria per il funzionamento corretto di Azure AD Domain Services è stata eliminata dal tenant di Azure AD. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.*

Se viene eliminata un'entità servizio obbligatoria, la piattaforma Azure non può eseguire attività di gestione automatiche. Il dominio gestito di Azure AD DS potrebbe non applicare correttamente gli aggiornamenti o eseguire i backup.

### <a name="check-for-missing-service-principals"></a>Verificare la presenza delle entità servizio mancanti

Per verificare quale entità servizio manca ed è necessario ricrearla, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare **Azure Active Directory** dal menu di spostamento a sinistra.
1. Selezionare **Applicazioni aziendali**. Scegliere *tutte le applicazioni* dal menu a discesa **tipo di applicazione** , quindi selezionare **applica**.
1. Cercare ogni ID applicazione. Se non viene trovata alcuna applicazione esistente, attenersi alla procedura di *risoluzione* per creare l'entità servizio o registrare nuovamente lo spazio dei nomi.

    | ID applicazione | Risoluzione |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Ricreare un'entità servizio mancante](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrare nuovamente lo spazio dei nomi Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Registrare nuovamente lo spazio dei nomi Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Registrare nuovamente lo spazio dei nomi Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Ricreare un'entità servizio mancante

Se nell'Azure AD directory manca l'ID applicazione *2565bd9d-DA50-47d4-8B85-4c97f669dc36* , usare Azure ad PowerShell per completare i passaggi seguenti. Per altre informazioni, vedere [install Azure ad PowerShell](/powershell/azure/active-directory/install-adv2).

1. Installare il modulo Azure AD PowerShell e importarlo come segue:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. A questo punto, ricreare l'entità servizio usando il cmdlet [New-AzureAdServicePrincipal][New-AzureAdServicePrincipal] :

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

L'integrità del dominio gestito di Azure AD DS si aggiorna automaticamente entro due ore e rimuove l'avviso.

### <a name="re-register-the-microsoft-aad-namespace"></a>Registrare nuovamente lo spazio dei nomi Microsoft AAD

Se nell'Azure AD directory manca l'ID applicazione *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-BC0E-44b0-947A-dc74e5d09022*o *d87dcbc6-A371-462E-88e3-28ad15ec4e64* , completare i passaggi seguenti per registrare di nuovo il provider di risorse *Microsoft. AAD* :

1. Nella portale di Azure cercare e selezionare **sottoscrizioni**.
1. Scegliere la sottoscrizione associata al dominio gestito di Azure AD DS.
1. Dal percorso di spostamento a sinistra scegliere **provider di risorse**.
1. Cercare *Microsoft. AAD*, quindi selezionare **Re-Register**.

L'integrità del dominio gestito di Azure AD DS si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Avviso AADDS105: Applicazione di sincronizzazione delle password non aggiornata

### <a name="alert-message"></a>Messaggio di avviso

*L'entità servizio con ID applicazione "d87dcbc6-a371-462e-88e3-28ad15ec4e64" è stata eliminata e quindi ricreata. La nuova creazione ha generato autorizzazioni non coerenti nelle risorse di Azure AD Domain Services necessarie per la gestione del dominio gestito. È possibile che la sincronizzazione di password nel dominio gestito sia interessata da questo problema.*

Azure AD DS sincronizza automaticamente gli account utente e le credenziali da Azure AD. Se si verifica un problema con l'applicazione Azure AD utilizzata per questo processo, la sincronizzazione delle credenziali tra Azure AD DS e Azure AD ha esito negativo.

### <a name="resolution"></a>Risoluzione

Per ricreare l'applicazione Azure AD usata per la sincronizzazione delle credenziali, usare Azure AD PowerShell per completare i passaggi seguenti. Per altre informazioni, vedere [install Azure ad PowerShell](/powershell/azure/active-directory/install-adv2).

1. Installare il modulo Azure AD PowerShell e importarlo come segue:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. A questo punto eliminare l'applicazione e l'oggetto precedenti usando i cmdlet di PowerShell seguenti:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

Dopo aver eliminato entrambe le applicazioni, la piattaforma Azure le ricrea automaticamente e tenta di riprendere la sincronizzazione delle password. L'integrità del dominio gestito di Azure AD DS si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano ancora problemi, [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal

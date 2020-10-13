---
title: Risolvere gli avvisi dell'entità servizio in Azure AD Domain Services | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi agli avvisi di configurazione dell'entità servizio per Azure Active Directory Domain Services
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: fc980d18a05b18706bb7eeecd907769b80e1b18f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962718"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Problemi noti: Avvisi dell'entità servizio in Azure Active Directory Domain Services

Le [entità servizio](../active-directory/develop/app-objects-and-service-principals.md) sono applicazioni utilizzate dalla piattaforma Azure per gestire, aggiornare e gestire un dominio gestito di Azure Active Directory Domain Services (Azure AD DS). Se viene eliminata un'entità servizio, le funzionalità del dominio gestito sono interessate.

Questo articolo consente di risolvere i problemi e risolvere gli avvisi di configurazione relativi all'entità servizio.

## <a name="alert-aadds102-service-principal-not-found"></a>Avviso AADDS102: Entità servizio non trovata

### <a name="alert-message"></a>Messaggio di avviso

*Un'entità servizio necessaria per il funzionamento corretto di Azure AD Domain Services è stata eliminata dal tenant di Azure AD. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.*

Se viene eliminata un'entità servizio necessaria, la piattaforma Azure non può eseguire attività di gestione automatiche. Il dominio gestito potrebbe non applicare correttamente gli aggiornamenti o eseguire i backup.

### <a name="check-for-missing-service-principals"></a>Verificare la presenza delle entità servizio mancanti

Per verificare quale entità servizio manca e deve essere ricreata, attenersi alla procedura seguente:

1. Nel portale di Azure selezionare **Azure Active Directory** dal menu di spostamento a sinistra.
1. Selezionare **Applicazioni aziendali**. Scegliere *Tutte le applicazioni* dal menu a discesa **Tipo di applicazione** e quindi selezionare **Applica**.
1. Cercare ognuno dei seguenti ID applicazione. Se non viene trovata alcuna applicazione esistente, seguire i passaggi in *Risoluzione* per creare l'entità servizio o registrare nuovamente lo spazio dei nomi.

    | ID applicazione | Risoluzione |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Ricreare un'entità servizio mancante](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Ripetere la registrazione dello spazio dei nomi Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Ripetere la registrazione dello spazio dei nomi Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Ripetere la registrazione dello spazio dei nomi Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Ricreare un'entità servizio mancante

Se nella directory Azure AD manca l'ID applicazione *2565bd9d-da50-47d4-8b85-4c97f669dc36*, usare Azure AD PowerShell per completare i passaggi seguenti. Per altre informazioni, vedere [Azure ad PowerShell](/powershell/azure/active-directory/install-adv2).

1. Se necessario, installare il modulo Azure AD PowerShell e importarlo come segue:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Ricreare ora l'entità servizio usando il cmdlet [New-AzureAdServicePrincipal][New-AzureAdServicePrincipal]:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

L'integrità del dominio gestito si aggiorna automaticamente entro due ore e rimuove l'avviso.

### <a name="re-register-the-microsoft-aad-namespace"></a>Ripetere la registrazione dello spazio dei nomi Microsoft.AAD

Se l'ID applicazione *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022* o *d87dcbc6-a371-462e-88e3-28ad15ec4e64* risulta mancante nella directory Azure AD, seguire questa procedura per registrare nuovamente il provider di risorse *Microsoft.AAD*:

1. Nel portale di Azure cercare e selezionare **Sottoscrizioni**.
1. Scegliere la sottoscrizione associata al dominio gestito.
1. Nel menu di spostamento a sinistra scegliere **Provider di risorse**.
1. Cercare *Microsoft.AAD* e quindi selezionare **Ripeti registrazione**.

L'integrità del dominio gestito si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Avviso AADDS105: Applicazione di sincronizzazione delle password non aggiornata

### <a name="alert-message"></a>Messaggio di avviso

*L'entità servizio con ID applicazione "d87dcbc6-A371-462E-88e3-28ad15ec4e64" è stata eliminata e quindi ricreata. La ricreazione abbandona le autorizzazioni incoerenti per Azure AD Domain Services risorse necessarie per il servizio del dominio gestito. Potrebbe essere interessata la sincronizzazione delle password nel dominio gestito.*

Azure AD DS sincronizza automaticamente gli account utente e le credenziali da Azure AD. In presenza di un problema con l'applicazione Azure AD usata per questo processo, la sincronizzazione delle credenziali tra Azure AD DS e Azure AD ha esito negativo.

### <a name="resolution"></a>Risoluzione

Per ricreare l'applicazione Azure AD usata per la sincronizzazione delle credenziali, usare Azure AD PowerShell per completare i passaggi seguenti. Per altre informazioni, vedere [Installare Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Se necessario, installare il modulo Azure AD PowerShell e importarlo come segue:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Eliminare l'applicazione e l'oggetto precedenti usando i cmdlet di PowerShell seguenti:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

Dopo aver eliminato entrambe le applicazioni, la piattaforma Azure le ricrea automaticamente e tenta di riprendere la sincronizzazione delle password. L'integrità del dominio gestito si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, [aprire una richiesta di supporto tecnico di Azure][azure-support] per richiedere ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal

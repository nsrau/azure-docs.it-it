---
title: Risolvere gli avvisi dell'entità servizio in Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come risolvere i problemi relativi agli avvisi di configurazione dell'entità servizio per Servizi di dominio Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257971"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Problemi noti: Avvisi dell'entità servizio in Servizi di dominio Azure Active DirectoryKnown issues: Service principal alerts in Azure Active Directory Domain Services

Le entità servizio sono applicazioni utilizzate dalla piattaforma Azure per gestire, aggiornare e gestire un dominio gestito di Servizi di dominio Active Directory di Azure.Service [principals](../active-directory/develop/app-objects-and-service-principals.md) are applications that the Azure platform uses to manage, update, and maintain an Azure AD DS managed domain. Se un'entità servizio viene eliminata, la funzionalità nel dominio gestito di Servizi di dominio Active Directory di Azure è interessata.

Questo articolo consente di risolvere i problemi e risolvere gli avvisi di configurazione correlati all'entità servizio.

## <a name="alert-aadds102-service-principal-not-found"></a>Avviso AADDS102: Entità servizio non trovata

### <a name="alert-message"></a>Messaggio di avviso

*Un'entità servizio necessaria per il corretto funzionamento di Servizi di dominio Azure AD è stata eliminata dalla directory di Azure AD. Questa configurazione influisce sulla capacità di Microsoft di monitorare, gestire, applicare patch e sincronizzare il dominio gestito.*

Se un'entità servizio richiesta viene eliminata, la piattaforma Azure non può eseguire attività di gestione automatizzate. Il dominio gestito di Servizi di dominio Active Directory di Azure potrebbe non applicare correttamente gli aggiornamenti o eseguire backup.

### <a name="check-for-missing-service-principals"></a>Verificare la presenza delle entità servizio mancanti

Per verificare quale entità servizio manca e deve essere ricreata, completare i passaggi seguenti:To check which service principal is missing and needs to be recreated, complete the following steps:

1. Nel portale di Azure selezionare **Azure Active Directory** dal menu di spostamento a sinistra.
1. Selezionare **Applicazioni aziendali**. Scegliere *Tutte le applicazioni* dal menu a discesa Tipo di **applicazione,** quindi selezionare **Applica**.
1. Cercare ciascuno degli ID applicazione. Se non viene trovata alcuna applicazione esistente, seguire i passaggi *di risoluzione* per creare l'entità servizio o registrare nuovamente lo spazio dei nomi.

    | ID applicazione | Risoluzione |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Ricreare un'entità servizio mancanteRecreate a missing service principal](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrare nuovamente lo spazio dei nomi Microsoft.AADRe-register the Microsoft.AAD namespace](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Registrare nuovamente lo spazio dei nomi Microsoft.AADRe-register the Microsoft.AAD namespace](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Registrare nuovamente lo spazio dei nomi Microsoft.AADRe-register the Microsoft.AAD namespace](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Ricreare un'entità servizio mancanteRecreate a missing Service Principal

Se l'ID applicazione *2565bd9d-da50-47d4-8b85-4c97f669dc36* non è presente nella directory di Azure AD, usare Azure AD PowerShell per completare i passaggi seguenti. Per altre informazioni, vedere [installare Azure AD PowerShell.](/powershell/azure/active-directory/install-adv2)

1. Installare il modulo Azure AD PowerShell e importarlo come segue:Install the Azure AD PowerShell module and import it as follows:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. A questo punto ricreare l'entità servizio usando il cmdlet [New-AzureAdServicePrincipal:Now][New-AzureAdServicePrincipal] recreate the service principal using the New-AzureAdServicePrincipal cmdlet:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

L'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

### <a name="re-register-the-microsoft-aad-namespace"></a>Registrare nuovamente lo spazio dei nomi Microsoft AADRe-register the Microsoft AAD namespace

Se ID applicazione *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947 a-dc74e5d09022*o *d87dcbc6-a371-462e-88e3-28ad15ec4e64* non è presente nella directory di Azure AD, completare i passaggi seguenti per registrare nuovamente il provider di risorse *Microsoft.AAD:*

1. Nel portale di Azure cercare e selezionare **Sottoscrizioni**.
1. Scegliere la sottoscrizione associata al dominio gestito di Servizi di dominio Active Directory di Azure.
1. Nel riquadro di spostamento sinistro scegliere **Provider di risorse**.
1. Cercare *Microsoft.AAD*, quindi selezionare **Re-register**.

L'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Avviso AADDS105: L'applicazione di sincronizzazione delle password non è aggiornata

### <a name="alert-message"></a>Messaggio di avviso

*L'entità servizio con ID applicazione "d87dcbc6-a371-462e-88e3-28ad15ec4e64" è stata eliminata e quindi ricreata. La ricreazione lascia dietro di sé autorizzazioni incoerenti per le risorse di Servizi di dominio Azure AD necessarie per gestire il dominio gestito. La sincronizzazione delle password nel dominio gestito potrebbe essere compromessa.*

Servizi di dominio Active Directory di Azure sincronizza automaticamente gli account utente e le credenziali da Azure AD. Se si verifica un problema con l'applicazione Azure AD usata per questo processo, la sincronizzazione delle credenziali tra Azure AD DS e Azure AD non riesce.

### <a name="resolution"></a>Risoluzione

Per ricreare l'applicazione Azure AD usata per la sincronizzazione delle credenziali, usare Azure AD PowerShell per completare i passaggi seguenti. Per altre informazioni, vedere [installare Azure AD PowerShell.](/powershell/azure/active-directory/install-adv2)

1. Installare il modulo Azure AD PowerShell e importarlo come segue:Install the Azure AD PowerShell module and import it as follows:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Eliminare ora l'applicazione e l'oggetto precedenti utilizzando i cmdlet di PowerShell seguenti:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

Dopo aver eliminato entrambe le applicazioni, la piattaforma Azure le ricrea automaticamente e tenta di riprendere la sincronizzazione delle password. L'integrità del dominio gestito di Servizi di dominio Active Directory di Azure si aggiorna automaticamente entro due ore e rimuove l'avviso.

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, [aprire una richiesta][azure-support] di supporto di Azure per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal

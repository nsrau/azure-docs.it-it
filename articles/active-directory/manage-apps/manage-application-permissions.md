---
title: Gestione delle autorizzazioni utente e amministratore-Azure Active Directory | Microsoft Docs
description: Informazioni su come rivedere e gestire le autorizzazioni per l'applicazione in Azure AD. Ad esempio, se si desidera revocare tutte le autorizzazioni concesse a un'applicazione.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277646"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Eseguire un'azione su overpriviledged o su un'applicazione sospetta in Azure Active Directory

Informazioni su come rivedere e gestire le autorizzazioni dell'applicazione. In base allo scenario, questo articolo fornirà azioni diverse che è possibile eseguire per proteggere l'applicazione. Questo vale per tutte le applicazioni che sono state aggiunte al tenant di Azure Active Directory (Azure AD) tramite il consenso dell'utente o dell'amministratore.

Per altre informazioni sul consenso alle applicazioni, vedere [framework di consenso di Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prerequisiti

Per poter eseguire le azioni seguenti, è necessario accedere come amministratore globale, amministratore dell'applicazione o amministratore di applicazioni cloud.

Per limitare l'accesso alle applicazioni è necessario richiedere l'assegnazione dell'utente, quindi assegnare utenti o gruppi all'applicazione.  Per altre informazioni, vedere [Metodi per l'assegnazione di utenti e gruppi](methods-for-assigning-users-and-groups.md).

È possibile accedere al portale di Azure AD per ottenere gli script di PowerShell contestuali per eseguire le azioni.
 
1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni aziendali**.
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Autorizzazioni**. Nella barra dei comandi selezionare **Verifica autorizzazioni**.

![Verifica le autorizzazioni](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Si vuole controllare l'accesso a un'applicazione

Per limitare l'accesso a questa applicazione, è consigliabile attivare l'impostazione assegnazione utente.

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni aziendali**.
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Proprietà** e quindi impostare requisito utente necessario per impostare su Sì.
5. Selezionare utenti **e gruppi** e quindi rimuovere gli utenti non desiderati assegnati all'applicazione.
6. Assegnare gli utenti o i gruppi all'applicazione.

Facoltativamente, è possibile rimuovere tutti gli utenti assegnati all'applicazione tramite PowerShell.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Si desidera revocare tutte le autorizzazioni per un'applicazione

L'uso di PowerShell revoca tutte le autorizzazioni concesse all'applicazione.

> [!NOTE]
> La revoca dell'autorizzazione concessa corrente non impedisce agli utenti di reconseing alle applicazioni. Se si vuole impedire agli utenti di concedere il consenso all'applicazione, vedere [configurare il modo in cui gli utenti finali accettano le applicazioni](configure-user-consent.md).

Facoltativamente, è possibile disabilitare l'applicazione per impedire agli utenti di accedere all'app e l'applicazione di accedere ai dati.

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni aziendali**.
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Proprietà** e quindi impostare abilitato per l'accesso degli utenti? su No.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>L'applicazione è sospetta e si desidera esaminarla

Si consiglia di limitare l'accesso a questa applicazione attivando l'impostazione di assegnazione utente ed esaminando le autorizzazioni concesse dall'utente e dagli amministratori all'applicazione.

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
3. Selezionare **Azure Active Directory**  >  **applicazioni aziendali**.
5. Selezionare l'applicazione a cui si vuole limitare l'accesso.
6. Selezionare **Proprietà** e quindi impostare requisito utente necessario per impostare su Sì.
7. Selezionare **autorizzazioni** ed esaminare l'amministratore e le autorizzazioni concesse all'utente.

Facoltativo, è possibile:

- Usando PowerShell, rimuovere tutti gli utenti assegnati per impedirne l'accesso all'applicazione.
- Con PowerShell, invalidare i token di aggiornamento per gli utenti che hanno accesso all'applicazione.
- Tramite PowerShell, revocare tutte le autorizzazioni per l'applicazione
- Disabilitare l'applicazione per bloccare l'accesso degli utenti e arrestare l'accesso alle applicazioni ai dati.


## <a name="application-is-malicious-and-im-compromised"></a>L'applicazione è dannosa e sono compromessi

Si consiglia di disabilitare l'applicazione per impedire agli utenti di accedere all'app e l'applicazione di accedere ai dati. Se invece si elimina l'applicazione, gli utenti finali saranno in grado di riautorizzare l'applicazione e di concedere l'accesso ai dati.

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni aziendali**.
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Proprietà** , quindi copiare l'ID oggetto.

### <a name="powershell-commands"></a>Comandi di PowerShell


Recuperare l'ID oggetto dell'entità servizio

1. Accedere come amministratore globale, amministratore di applicazioni o amministratore di applicazioni cloud nel [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**  >  **applicazioni aziendali**.
3. Selezionare l'applicazione a cui si vuole limitare l'accesso.
4. Selezionare **Proprietà** , quindi copiare l'ID oggetto.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Rimuovere tutti gli utenti assegnati all'applicazione.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Revoca le autorizzazioni concesse all'applicazione

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Invalidare i token di aggiornamento
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Passaggi successivi
- [Gestisci il consenso alle applicazioni e valuta la richiesta di consenso](manage-consent-requests.md)
- [Configurare il consenso utente](configure-user-consent.md)
- [Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)

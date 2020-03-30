---
title: Accesso delegato in Windows Virtual Desktop - AzureDelegated access in Windows Virtual Desktop - Azure
description: Come delegare le funzionalità amministrative in una distribuzione di Windows Virtual Desktop, inclusi esempi.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3e27550ecc5b42c2bf0d947690da09e13d88ea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128025"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Accesso delegato in Desktop virtuale Windows

Windows Virtual Desktop dispone di un modello di accesso delegato che consente di definire la quantità di accesso che un determinato utente può avere assegnando loro un ruolo. Un'assegnazione di ruolo è composta da tre componenti: entità di sicurezza, definizione del ruolo e ambito. Il modello di accesso delegato di Windows Virtual Desktop è basato sul modello di controllo degli accessi in base al ruolo di Azure.The Windows Virtual Desktop delegated access model is based on the Azure RBAC model. Per altre informazioni sulle assegnazioni di ruolo specifiche e sui relativi componenti, vedere Panoramica del controllo degli accessi basato sui ruoli di Azure.To learn more about specific role assignments and their components, see [the Azure role-based access control overview](../role-based-access-control/built-in-roles.md).

L'accesso delegato di Windows Virtual Desktop supporta i valori seguenti per ogni elemento dell'assegnazione di ruolo:

* Entità di sicurezza
    * Utenti
    * Entità servizio
* Definizione di ruolo
    * Ruoli predefiniti
* Scope
    * Gruppi di tenant
    * Tenant
    * Pool host
    * Gruppi di app

## <a name="built-in-roles"></a>Ruoli predefiniti

L'accesso delegato in Windows Virtual Desktop include diverse definizioni di ruolo predefinite che è possibile assegnare a utenti ed entità servizio.

* Un proprietario di Servizi Desktop remoto può gestire tutto, incluso l'accesso alle risorse.
* Un collaboratore RDS può gestire tutto tranne l'accesso alle risorse.
* Un lettore RDS può visualizzare tutto, ma non può apportare modifiche.
* Un operatore RDS può visualizzare le attività di diagnostica.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlet di PowerShell per le assegnazioni di ruolo

È possibile eseguire i cmdlet seguenti per creare, visualizzare e rimuovere assegnazioni di ruolo:

* **Get-RdsRoleAssignment** visualizza un elenco di assegnazioni di ruolo.
* **New-RdsRoleAssignment** crea una nuova assegnazione di ruolo.
* **Remove-RdsRoleAssignment** elimina le assegnazioni di ruolo.

### <a name="accepted-parameters"></a>Parametri accettati

È possibile modificare i tre cmdlet di base con i parametri seguenti:

* **AadTenantId**: specifica l'ID tenant di Azure Active Directory di cui l'entità servizio è membro.
* **AppGroupName:** nome del gruppo di app Desktop remoto.
* **Diagnostica**: indica l'ambito di diagnostica. (Deve essere associato ai parametri **Infrastruttura** o **Tenant.)**
* **HostPoolName:** nome del pool host di Desktop remoto.
* **Infrastruttura**: indica l'ambito dell'infrastruttura.
* **RoleDefinitionName:** nome del ruolo di controllo di accesso basato sui ruoli di Servizi Desktop remoto assegnato all'utente, al gruppo o all'app. Ad esempio, Proprietario Servizi Desktop remoto, Lettore Servizi Desktop remoto e così via.
* **ServerPrincipleName:** nome dell'applicazione Azure Active Directory.
* **SignInName:** indirizzo di posta elettronica o nome dell'entità utente dell'utente.
* **TenantName:** nome del tenant di Desktop remoto.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco più completo dei cmdlet di PowerShell che ogni ruolo può utilizzare, vedere le informazioni di riferimento su [PowerShell.](/powershell/windows-virtual-desktop/overview)

Per istruzioni su come configurare un ambiente Windows Virtual Desktop, vedere [Ambiente Windows Virtual Desktop](environment-setup.md).

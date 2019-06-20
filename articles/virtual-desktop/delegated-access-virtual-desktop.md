---
title: Delega per l'accesso in anteprima Desktop virtuale Windows - Azure
description: Come delegare le funzionalità amministrative in una distribuzione di anteprima di Desktop virtuale Windows, inclusi gli esempi.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 41cf5f8bcc69e181350a63d215fb0d78d43dcfdf
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272823"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Delega per l'accesso in anteprima di Desktop virtuale Windows

Anteprima di Desktop virtuale Windows ha un modello di accesso delegato che consente di definire il livello di accesso di che un determinato utente è consentito avere assegnandole un ruolo. Un'assegnazione di ruolo include tre componenti: entità di sicurezza, definizione di ruolo e ambito. Il modello di delega per l'accesso Desktop virtuale Windows è basato sul modello di Azure RBAC. Per altre informazioni sulle assegnazioni di ruolo specifico e i relativi componenti, vedere [di Cenni preliminari sul controllo degli accessi in base al ruolo Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

Windows Desktop virtuale delegati access supporta i valori seguenti per ogni elemento dell'assegnazione di ruolo:

* Entità di sicurezza
    * Utenti
    * Entità servizio
* Definizione di ruolo
    * Ruoli predefiniti
* `Scope`
    * Gruppi del tenant
    * Tenant
    * Pool di host
    * Gruppi di App

## <a name="built-in-roles"></a>Ruoli predefiniti

L'accesso delegato nel Desktop virtuale Windows ha diverse definizioni di ruolo predefinite, che è possibile assegnare a utenti ed entità servizio.

* Un proprietario di servizi desktop remoto può gestire tutto, incluso l'accesso alle risorse.
* Un collaboratore di servizi desktop remoto può gestire tutti gli elementi tranne l'accesso alle risorse.
* Un lettore di servizi desktop remoto possono visualizzare tutti gli elementi ma non è possibile apportare modifiche.
* Un operatore di servizi desktop remoto possono visualizzare le attività di diagnostica.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlet di PowerShell per le assegnazioni di ruolo

È possibile eseguire i cmdlet seguenti per creare, visualizzare e rimuovere le assegnazioni di ruolo:

* **Get-RdsRoleAssignment** Visualizza un elenco di assegnazioni di ruolo.
* **Nuovo RdsRoleAssignment** crea una nuova assegnazione di ruolo.
* **Remove-RdsRoleAssignment** consente di eliminare le assegnazioni di ruolo.

### <a name="accepted-parameters"></a>Parametri accettati

È possibile modificare i tre cmdlet base con i parametri seguenti:

* **AadTenantId**: specifica l'ID tenant di Azure Active Directory da cui l'entità servizio è un membro.
* **AppGroupName**: nome del gruppo di app Desktop remoto.
* **Diagnostica**: indica l'ambito di diagnostica. (Deve essere abbinato con il **infrastruttura** oppure **Tenant** parametri.)
* **HostPoolName**: nome del pool di host di Desktop remoto.
* **Infrastruttura**: indica l'ambito dell'infrastruttura.
* **RoleDefinitionName**: nome del ruolo di controllo di accesso basato sui ruoli Servizi Desktop remoto assegnato per l'utente, gruppo o un'app. (Ad esempio, Remote Desktop Services proprietario, lettore di Servizi Desktop remoto e così via.)
* **ServerPrincipleName**: nome dell'applicazione Azure Active Directory.
* **SignInName**: indirizzo di posta elettronica dell'utente o nome dell'entità utente.
* **TenantName**: nome del tenant di Desktop remoto.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco più completo di ogni ruolo può usare i cmdlet di PowerShell, vedere la [riferimento a PowerShell](/powershell/windows-virtual-desktop/overview).

Per linee guida per informazioni su come configurare un ambiente di Desktop virtuale Windows, vedere [ambiente di anteprima di Desktop virtuale Windows](environment-setup.md).

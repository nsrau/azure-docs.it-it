---
title: 'Accesso delegato nel desktop virtuale di Windows: 2019-Azure'
description: Come delegare le funzionalità amministrative in una distribuzione di desktop virtuali Windows, inclusi esempi.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91be20de49f549f3530e6c817906f1abf8f106b6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527558"
---
# <a name="delegated-access-in-windows-virtual-desktop-fall-2019"></a>Accesso delegato nel desktop virtuale di Windows 2019

>[!IMPORTANT]
>Questo contenuto si applica alla versione Autunno 2019 che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si sta tentando di gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows introdotti nell'aggiornamento di Primavera 2020, vedere [questo articolo](../delegated-access-virtual-desktop.md).

Desktop virtuale di Windows dispone di un modello di accesso delegato che consente di definire la quantità di accesso consentito a un determinato utente assegnando loro un ruolo. Un'assegnazione di ruolo dispone di tre componenti: entità di sicurezza, definizione del ruolo e ambito. Il modello di accesso delegato di desktop virtuale Windows è basato sul modello di controllo degli accessi in base al ruolo di Azure Per altre informazioni sulle assegnazioni di ruolo specifiche e sui relativi componenti, vedere [Panoramica del controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/built-in-roles.md).

Accesso delegato desktop virtuale Windows supporta i valori seguenti per ogni elemento dell'assegnazione di ruolo:

* Entità di sicurezza
    * Utenti
    * Entità servizio
* Definizione di ruolo
    * Ruoli predefiniti
* Scope
    * Gruppi tenant
    * Tenant
    * Pool host
    * Gruppi di app

## <a name="built-in-roles"></a>Ruoli predefiniti

Per l'accesso delegato nel desktop virtuale di Windows sono disponibili diverse definizioni di ruolo predefinite che è possibile assegnare a utenti ed entità servizio.

* Un proprietario RDS può gestire tutto, incluso l'accesso alle risorse.
* Un collaboratore RDS può gestire tutti gli elementi, ma non può accedere alle risorse.
* Un lettore RDS può visualizzare tutti gli elementi, ma non può apportare alcuna modifica.
* Un operatore RDS può visualizzare le attività di diagnostica.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlet di PowerShell per le assegnazioni di ruolo

Per creare, visualizzare e rimuovere assegnazioni di ruolo, è possibile eseguire i cmdlet seguenti:

* **Get-RdsRoleAssignment** Visualizza un elenco di assegnazioni di ruolo.
* **New-RdsRoleAssignment** crea una nuova assegnazione di ruolo.
* **Remove-RdsRoleAssignment** Elimina le assegnazioni di ruolo.

### <a name="accepted-parameters"></a>Parametri accettati

È possibile modificare i tre cmdlet di base con i parametri seguenti:

* **AadTenantId**: specifica il Azure Active Directory ID tenant da cui l'entità servizio è membro.
* **AppGroupName**: nome del gruppo di app desktop remoto.
* **Diagnostics**: indica l'ambito di diagnostica. (Deve essere abbinato ai parametri dell' **infrastruttura** o del **tenant** ).
* **HostPoolName**: nome del pool di host desktop remoto.
* **Infrastruttura**: indica l'ambito dell'infrastruttura.
* **RoleDefinitionName**: nome del Servizi Desktop remoto ruolo di controllo degli accessi in base al ruolo assegnato all'utente, al gruppo o all'app. Ad esempio Servizi Desktop remoto proprietario, Servizi Desktop remoto Reader e così via.
* **ServerPrincipleName**: nome dell'applicazione Azure Active Directory.
* **SignInName**: indirizzo di posta elettronica dell'utente o nome dell'entità utente.
* **TenantName**: nome del tenant desktop remoto.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco più completo dei cmdlet di PowerShell che possono essere usati da ogni ruolo, vedere le informazioni di [riferimento su PowerShell](/powershell/windows-virtual-desktop/overview).

Per le linee guida su come configurare un ambiente desktop virtuale Windows, vedere [ambiente desktop virtuale di Windows](environment-setup-2019.md).

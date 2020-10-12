---
title: Accesso delegato in desktop virtuale Windows-Azure
description: Come delegare le funzionalità amministrative in una distribuzione di desktop virtuali Windows, inclusi esempi.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2aa2c74704cf89c082d2837b39e82902efa0a62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010056"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Accesso delegato in Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).

Desktop virtuale di Windows dispone di un modello di accesso delegato che consente di definire la quantità di accesso consentito a un determinato utente assegnando loro un ruolo. Un'assegnazione di ruolo dispone di tre componenti: entità di sicurezza, definizione del ruolo e ambito. Il modello di accesso delegato di desktop virtuale Windows è basato sul modello di controllo degli accessi in base al ruolo di Azure Per altre informazioni sulle assegnazioni di ruolo specifiche e sui relativi componenti, vedere [Panoramica del controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md).

Accesso delegato desktop virtuale Windows supporta i valori seguenti per ogni elemento dell'assegnazione di ruolo:

* Entità di sicurezza
    * Utenti
    * Gruppi di utenti
    * Entità servizio
* Definizione di ruolo
    * Ruoli predefiniti
    * Ruoli personalizzati
* Scope
    * Pool host
    * Gruppi di app
    * Aree di lavoro

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlet di PowerShell per le assegnazioni di ruolo

Prima di iniziare, assicurarsi di seguire le istruzioni riportate in [configurare il modulo PowerShell](powershell-module.md) per configurare il modulo PowerShell per desktop virtuale di Windows, se non è già stato fatto.

Desktop virtuale di Windows usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) durante la pubblicazione di gruppi di app per utenti o gruppi di utenti. Il ruolo utente di virtualizzazione desktop viene assegnato all'utente o al gruppo di utenti e l'ambito è il gruppo di app. Questo ruolo concede all'utente l'accesso ai dati speciali per il gruppo di app.

Eseguire il cmdlet seguente per aggiungere Azure Active Directory utenti a un gruppo di app:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Eseguire il cmdlet seguente per aggiungere Azure Active Directory gruppo utenti a un gruppo di app:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>Passaggi successivi

Per un elenco più completo dei cmdlet di PowerShell che possono essere usati da ogni ruolo, vedere le informazioni di [riferimento su PowerShell](/powershell/windows-virtual-desktop/overview).

Per un elenco completo dei ruoli supportati nel controllo degli accessi in base al ruolo di Azure, vedere [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md).

Per le linee guida su come configurare un ambiente desktop virtuale Windows, vedere [ambiente desktop virtuale di Windows](environment-setup.md).

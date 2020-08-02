---
title: Accesso delegato in desktop virtuale Windows-Azure
description: Come delegare le funzionalità amministrative in una distribuzione di desktop virtuali Windows, inclusi esempi.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: df4d1b98eac782641fb36c614d2df9508c6131ad
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498668"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Accesso delegato in Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica al desktop virtuale di Windows con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa desktop virtuale di Windows (classico) senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).

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
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

Eseguire il cmdlet seguente per aggiungere Azure Active Directory gruppo utenti a un gruppo di app:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>Passaggi successivi

Per un elenco più completo dei cmdlet di PowerShell che possono essere usati da ogni ruolo, vedere le informazioni di [riferimento su PowerShell](/powershell/windows-virtual-desktop/overview).

Per un elenco completo dei ruoli supportati nel controllo degli accessi in base al ruolo di Azure, vedere [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md).

Per le linee guida su come configurare un ambiente desktop virtuale Windows, vedere [ambiente desktop virtuale di Windows](environment-setup.md).

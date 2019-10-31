---
title: PowerShell per desktop virtuale Windows-Azure
description: Come risolvere i problemi relativi a PowerShell quando si configura un ambiente tenant di desktop virtuali Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 38d9a2dda945f3a9459aa8e3360012c6ef422608
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163312"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell con Desktop virtuale Windows

Usare questo articolo per risolvere errori e problemi quando si usa PowerShell con desktop virtuale di Windows. Per ulteriori informazioni su Servizi Desktop remoto PowerShell, vedere la pagina relativa al [desktop virtuale di Windows PowerShell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Invia commenti e suggerimenti

Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandi di PowerShell usati durante l'installazione di desktop virtuali Windows

Questa sezione elenca i comandi di PowerShell che vengono in genere utilizzati durante la configurazione di desktop virtuale di Windows e fornisce modi per risolvere i problemi che possono verificarsi durante l'uso.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Errore: comando Add-RdsAppGroupUser--il UserPrincipalName specificato è già assegnato a un gruppo di app RemoteApp nel pool host specificato

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Motivo:** Il nome utente usato è già stato assegnato a un gruppo di app di un tipo diverso. Gli utenti non possono essere assegnati a un desktop remoto e a un gruppo di app Remote nello stesso pool host della sessione.

**Correzione:** Se l'utente necessita sia di app Remote che di desktop remoto, creare pool host diversi o concedere all'utente l'accesso al desktop remoto, che consente l'uso di qualsiasi applicazione nella macchina virtuale host sessione.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Errore: comando Add-RdsAppGroupUser--il UserPrincipalName specificato non esiste nell'Azure Active Directory associato al tenant Desktop remoto

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Motivo:** Impossibile trovare l'utente specificato da-UserPrincipalName nel Azure Active Directory associato al tenant di desktop virtuale di Windows.

**Correzione:** Confermare gli elementi nell'elenco seguente.

- L'utente viene sincronizzato con Azure Active Directory.
- L'utente non è legato a business to consumer (B2C) o commercio business-to-business (B2B).
- Il tenant desktop virtuale di Windows è associato a Azure Active Directory corretti.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Errore: Get-RdsDiagnosticActivities--l'utente non è autorizzato a eseguire una query sul servizio di gestione

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Motivo:** -TenantName parametro

**Correzione:** Eseguire Get-RdsDiagnosticActivities con-TenantName \<> TenantName.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Errore: Get-RdsDiagnosticActivities--l'utente non è autorizzato a eseguire una query sul servizio di gestione

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Motivo:** Con l'opzione-Deployment.

**Correzione:** l'opzione di distribuzione può essere usata solo dagli amministratori della distribuzione. Questi amministratori sono in genere membri del team di desktop virtuali Servizi Desktop remoto/Windows. Sostituire l'opzione-Deployment con-TenantName \<TenantName >.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Errore: New-RdsRoleAssignment--l'utente non è autorizzato a eseguire una query sul servizio di gestione

**Cause 1:** L'account usato non dispone delle autorizzazioni Servizi Desktop remoto proprietario per il tenant.

**Correzione 1:** Un utente con autorizzazioni Servizi Desktop remoto proprietario deve eseguire l'assegnazione di ruolo.

**Motivo 2:** L'account usato ha Servizi Desktop remoto autorizzazioni del proprietario, ma non fa parte del Azure Active Directory del tenant o non ha le autorizzazioni necessarie per eseguire query sul Azure Active Directory in cui si trova l'utente.

**Correzione 2:** Un utente con autorizzazioni Active Directory deve eseguire l'assegnazione di ruolo.

>[!Note]
>New-RdsRoleAssignment non può concedere autorizzazioni a un utente che non esiste nel Azure Active Directory (AD).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a desktop virtuale Windows e alle tracce di escalation, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un tenant e di un pool host in un ambiente desktop virtuale Windows, vedere [creazione di tenant e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [Desktop remoto connessioni client](troubleshoot-client-connection.md).
- Per ulteriori informazioni sul servizio, vedere [ambiente desktop virtuale di Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Per un'esercitazione per la risoluzione dei problemi, vedere [esercitazione: risolvere i problemi relativi alle distribuzioni di modelli gestione risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).

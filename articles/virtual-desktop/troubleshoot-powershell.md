---
title: PowerShell legata ai Desktop virtuali Windows - Azure
description: Come risolvere i problemi con PowerShell quando si configura un ambiente di tenant di Desktop virtuale Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927508"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell con Desktop virtuale Windows

Usare questo articolo per risolvere i problemi e gli errori quando si usa PowerShell con Windows Desktop virtuale. Per altre informazioni su PowerShell di Servizi Desktop remoto, vedere [Windows Powershell di Desktop virtuale](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandi di PowerShell usati durante la configurazione di Desktop virtuale Windows

Questa sezione elenca i comandi di PowerShell che sono in genere usati durante la configurazione di Desktop virtuale Windows e che offre metodi per risolvere i problemi che possono verificarsi durante il loro utilizzo.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Errore: Comando RdsAppGroupUser aggiungere - UserPrincipalName specificato è già assegnato a un gruppo di app di RemoteApp nel Pool di Host specificato

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Causa:** Il nome utente utilizzato è stato già assegnato a un gruppo di app di un tipo diverso. Gli utenti non è possibile assegnare a entrambi un gruppo remoto di app desktop e remote con lo stesso pool di host sessione.

**Fix:** Se l'utente deve App remota e desktop remoto, creare pool di host diverso o concedere l'accesso utente per il desktop remoto, che consente l'uso di qualsiasi applicazione nella macchina virtuale host sessione.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Errore: Comando RdsAppGroupUser aggiungere - UserPrincipalName specificato non esiste in Azure Active Directory associati al tenant di Desktop remoto

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Causa:** Impossibile trovare l'utente specificato da - UserPrincipalName in Azure Active Directory associato al tenant di Desktop virtuale Windows.

**Fix:** Verificare gli elementi nell'elenco seguente.

- L'utente è sincronizzato con Azure Active Directory.
- L'utente non sia legata a business to consumer (B2C) o e-commerce di business-to-business (B2B).
- Il tenant di Desktop virtuale Windows è associato a corretto Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Errore: Get-RdsDiagnosticActivities: L'utente non è autorizzato a eseguire query sul servizio di gestione

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Causa:** Nometenant - parametro

**Fix:** Eseguire Get-RdsDiagnosticActivities con - Nometenant <TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Errore: Get-RdsDiagnosticActivities: l'utente non è autorizzato a eseguire query sul servizio di gestione

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Causa:** Usando - opzione di distribuzione.

**Correzione:** -opzione di distribuzione può essere utilizzata solo dagli amministratori di distribuzione. Questi amministratori vengono in genere i membri del team di Remote Desktop Services/Windows Desktop virtuale. Sostituire - l'opzione di distribuzione con - Nometenant <TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Errore: Nuovo-RdsRoleAssignment: l'utente non è autorizzato a eseguire query sul servizio di gestione

**Causa 1:** L'account utilizzato non dispone delle autorizzazioni di proprietario di Servizi Desktop remoto per il tenant.

**Fix 1:** Un utente con autorizzazioni di proprietario di Servizi Desktop remoto deve eseguire l'assegnazione di ruolo.

**Causa 2:** L'account utilizzato disponga delle autorizzazioni di proprietario di Servizi Desktop remoto, ma non fa parte di Azure Active Directory del tenant o non dispone delle autorizzazioni per eseguire una query di Azure Active Directory in cui si trova l'utente.

**Fix 2:** Un utente con autorizzazioni di Active Directory deve eseguire l'assegnazione di ruolo.

>[!Note]
>Nuovo RdsRoleAssignment non è possibile assegnare le autorizzazioni a un utente che non esiste in Azure Active Directory (AD).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi di Desktop virtuale Windows e le tracce di escalation dei blocchi, vedere [risoluzione dei problemi di supporto, commenti e suggerimenti e panoramica](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un pool di tenant e l'host in un ambiente di Desktop virtuale Windows, vedere [la creazione di pool di Tenant e l'host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Windows Desktop virtuale, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi con le connessioni client di Desktop virtuale Windows, vedere [le connessioni client Desktop remoto](troubleshoot-client-connection.md).
- Per altre informazioni sul servizio di anteprima, vedere [ambiente di anteprima di Desktop Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi di distribuzioni di modelli di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
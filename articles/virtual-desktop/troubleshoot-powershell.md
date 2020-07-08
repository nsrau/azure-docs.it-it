---
title: PowerShell per desktop virtuale Windows-Azure
description: Come risolvere i problemi relativi a PowerShell quando si configura un ambiente desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6e4459eea07f60d90dad692d6625dd45c5038093
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84456964"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell con Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usare questo articolo per risolvere errori e problemi quando si usa PowerShell con desktop virtuale di Windows. Per ulteriori informazioni su Servizi Desktop remoto PowerShell, vedere la pagina relativa al [desktop virtuale di Windows PowerShell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandi di PowerShell usati durante l'installazione di desktop virtuali Windows

Questa sezione elenca i comandi di PowerShell che vengono in genere utilizzati durante la configurazione di desktop virtuale di Windows e fornisce modi per risolvere i problemi che possono verificarsi durante l'uso.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Errore: New-AzRoleAssignment: le informazioni fornite non sono mappate a un ID oggetto AD

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

**Motivo:** L'utente specificato dal parametro *-SignInName* non è stato trovato nel Azure Active Directory associato all'ambiente di desktop virtuale di Windows. 

**Correzione:** Assicurarsi degli elementi seguenti.

- L'utente deve essere sincronizzato con Azure Active Directory.
- L'utente non deve essere collegato al commercio business-to-consumer (B2C) o business-to-business (B2B).
- L'ambiente desktop virtuale di Windows deve essere associato a Azure Active Directory corretti.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Errore: New-AzRoleAssignment: "il client con ID oggetto non dispone dell'autorizzazione per eseguire l'azione sull'ambito (codice: AuthorizationFailed)"

**Cause 1:** L'account utilizzato non dispone delle autorizzazioni di proprietario per la sottoscrizione. 

**Correzione 1:** Un utente con autorizzazioni proprietario deve eseguire l'assegnazione di ruolo. In alternativa, l'utente deve essere assegnato al ruolo amministratore accesso utenti per assegnare un utente a un gruppo di applicazioni.

**Motivo 2:** L'account utilizzato dispone di autorizzazioni proprietario, ma non fa parte del Azure Active Directory dell'ambiente o non dispone delle autorizzazioni necessarie per eseguire query sul Azure Active Directory in cui si trova l'utente.

**Correzione 2:** Un utente con autorizzazioni Active Directory deve eseguire l'assegnazione di ruolo.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Errore: New-AzWvdHostPool--la località non è disponibile per il tipo di risorsa

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'. 
```

Motivo: desktop virtuale Windows supporta la selezione della posizione dei pool host, dei gruppi di applicazioni e delle aree di lavoro per archiviare i metadati del servizio in determinati percorsi. Le opzioni disponibili sono limitate a quelle disponibili per la funzionalità. Questo errore indica che la funzionalità non è disponibile nella località selezionata.

Correzione: nel messaggio di errore viene pubblicato un elenco di aree supportate. Usare invece una delle aree supportate.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Errore: New-AzWvdApplicationGroup deve trovarsi nella stessa posizione del pool host

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Motivo:** Mancata corrispondenza della posizione. Tutti i pool host, i gruppi di applicazioni e le aree di lavoro hanno un percorso in cui archiviare i metadati del servizio. Tutti gli oggetti creati che sono associati tra loro devono trovarsi nella stessa posizione. Se, ad esempio, si trova in un pool di host `eastus` , è necessario creare anche i gruppi di applicazioni in `eastus` . Se si crea un'area di lavoro per registrare questi gruppi di applicazioni in, è necessario che sia presente anche tale area di lavoro `eastus` .

**Correzione:** Recuperare il percorso in cui è stato creato il pool host, quindi assegnare il gruppo di applicazioni che si sta creando nello stesso percorso.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a Desktop virtuale Windows e alle tracce di escalation, consultare [Panoramica della risoluzione dei problemi, feedback e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la configurazione dell'ambiente desktop virtuale Windows e dei pool host, vedere [creazione di ambienti e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Desktop virtuale Windows, consultare [Configurazione di macchine virtuali nell'host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [connessioni al servizio desktop virtuale di Windows](troubleshoot-service-connection.md).
- Per risolvere i problemi relativi ai client di Desktop remoto, vedere [risoluzione dei problemi del client di desktop remoto](troubleshoot-client.md)
- Per ulteriori informazioni sul servizio, vedere [ambiente desktop virtuale di Windows](environment-setup.md).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](../azure-resource-manager/templates/deployment-history.md).

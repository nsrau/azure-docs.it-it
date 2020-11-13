---
title: Registrazione automatica con l'estensione SQL IaaS Agent
description: Informazioni su come abilitare la funzionalità di registrazione automatica per registrare automaticamente tutte le VM SQL Server precedenti e future con l'estensione SQL IaaS Agent usando il portale di Azure.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: a8eb112b0895d1c7a927621e6fb9e5160038692d
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557772"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>Registrazione automatica con l'estensione SQL IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Abilitare la funzionalità di registrazione automatica nel portale di Azure per registrare automaticamente tutti i SQL Server attuali e futuri in macchine virtuali di Azure con l' [estensione SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) in modalità lightweight. 

Questo articolo illustra come abilitare la funzionalità di registrazione automatica. In alternativa, è possibile [registrare una singola macchina virtuale](sql-agent-extension-manually-register-single-vm.md)o [registrare le VM in blocco](sql-agent-extension-manually-register-vms-bulk.md) con l'estensione SQL IaaS Agent. 

## <a name="overview"></a>Panoramica

Registrazione della macchina virtuale SQL Server con l' [estensione SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) per sbloccare un set completo di funzionalità di vantaggi. 

Quando è abilitata la registrazione automatica, un processo viene eseguito ogni giorno per rilevare se SQL Server è installato o meno in tutte le macchine virtuali non registrate nella sottoscrizione. Questa operazione viene eseguita copiando i file binari dell'estensione SQL IaaS Agent nella macchina virtuale, quindi eseguendo un'utilità monouso che verifica la presenza dell'hive del registro di sistema SQL Server. Se viene rilevato il SQL Server hive, la macchina virtuale viene registrata con l'estensione in modalità lightweight. Se nel registro di sistema non è presente alcun hive SQL Server, i file binari vengono rimossi.

Una volta abilitata la registrazione automatica per una sottoscrizione, tutte le VM correnti e future con SQL Server installato verranno registrate con l'estensione SQL IaaS Agent in modalità lightweight. È comunque necessario eseguire [manualmente l'aggiornamento alla modalità di gestibilità completa](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) per sfruttare il set completo di funzionalità. 

> [!IMPORTANT]
> L'estensione SQL IaaS Agent raccoglie i dati per lo scopo esplicito di fornire ai clienti vantaggi facoltativi quando si usa SQL Server all'interno di macchine virtuali di Azure. Microsoft non utilizzerà questi dati per i controlli delle licenze senza il consenso anticipato del cliente. Per ulteriori informazioni, vedere la [SQL Server supplemento sulla privacy](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale SQL Server con l'estensione, è necessario: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/) e le autorizzazioni del [ruolo Collaboratore](../../../role-based-access-control/built-in-roles.md#all) minimo.
- Una macchina virtuale modello di risorse di Azure [Windows Server 2008 R2 (o versione successiva)](../../../virtual-machines/windows/quick-create-portal.md) con [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) distribuita nel cloud pubblico o Azure per enti pubblici. Windows Server 2008 non è supportato. 


## <a name="enable"></a>Abilitare

Per abilitare la registrazione automatica delle macchine virtuali SQL Server nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla pagina delle risorse [**macchine virtuali SQL**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selezionare **registrazione automatica SQL Server VM** per aprire la pagina di **registrazione automatica** . 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="Selezionare la registrazione automatica SQL Server VM per aprire la pagina di registrazione automatica":::

1. Scegliere la sottoscrizione dall'elenco a discesa. 
1. Leggere le condizioni e, se si accettano le condizioni **, selezionare Accetto**. 
1. Selezionare **Register (registra** ) per abilitare la funzionalità e registrare automaticamente tutte le VM SQL Server correnti e future con l'estensione SQL IaaS Agent. Il servizio SQL Server non verrà riavviato in nessuna delle macchine virtuali. 

## <a name="disable"></a>Disabilitazione

Usare l' [interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure o [Azure PowerShell](/powershell/azure/install-az-ps) per disabilitare la funzionalità di registrazione automatica. Quando la funzionalità di registrazione automatica è disabilitata, SQL Server macchine virtuali aggiunte alla sottoscrizione devono essere registrate manualmente con l'estensione SQL IaaS Agent. Non verrà annullata la registrazione di macchine virtuali SQL Server esistenti che sono già state registrate.



# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per disabilitare la registrazione automatica usando l'interfaccia della riga di comando di Azure, eseguire il comando seguente: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Per disabilitare la registrazione automatica usando Azure PowerShell, eseguire il comando seguente: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Abilita per più sottoscrizioni

È possibile abilitare la funzionalità di registrazione automatica per più sottoscrizioni di Azure tramite PowerShell. 

A questo scopo, attenersi alla procedura seguente:

1. Salvare lo [script](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) in un `.ps1` file, ad esempio `EnableBySubscription.ps1` . 
1. Passare al percorso in cui è stato salvato lo script usando un prompt dei comandi amministrativo o una finestra di PowerShell. 
1. Connettersi ad Azure ( `az login` ).
1. Eseguire lo script passando valori SubscriptionId come parametri come   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Ad esempio: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Gli errori di registrazione non riusciti vengono archiviati nella `RegistrationErrors.csv` stessa directory in cui è stato salvato ed eseguito lo `.ps1` script da. 

## <a name="next-steps"></a>Passaggi successivi

Aggiornare la modalità di gestibilità a [full](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) per sfruttare il set di funzionalità completo fornito dall'estensione SQL IaaS Agent. 

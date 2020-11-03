---
title: Registrazione automatica con il provider di risorse VM SQL
description: Informazioni su come abilitare la funzionalità di registrazione automatica per registrare automaticamente tutte le VM SQL Server precedenti e future con il provider di risorse VM SQL usando il portale di Azure.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: 23ecc3bdfb0ca85caf219fc262348937923f53c3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286132"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Registrazione automatica con il provider di risorse VM SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Abilitare la funzionalità di registrazione automatica nel portale di Azure per registrare automaticamente tutti i SQL Server attuali e futuri nelle macchine virtuali di Azure con il provider di risorse VM SQL in modalità lightweight. La registrazione con il provider di risorse VM SQL installa l' [estensione SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md).

Questo articolo illustra come abilitare la funzionalità di registrazione automatica. In alternativa, è possibile [registrare una singola macchina virtuale](sql-vm-resource-provider-register.md)o [registrare le VM in blocco](sql-vm-resource-provider-bulk-register.md) con il provider di risorse della macchina virtuale SQL. 

## <a name="overview"></a>Panoramica

La registrazione della macchina virtuale SQL Server con il provider di risorse VM SQL installa l' [estensione SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). 

Quando è abilitata la registrazione automatica, un processo viene eseguito ogni giorno per rilevare se SQL Server è installato o meno in tutte le macchine virtuali non registrate nella sottoscrizione. Questa operazione viene eseguita copiando i file binari dell'estensione SQL IaaS Agent nella macchina virtuale, quindi eseguendo un'utilità monouso che verifica la presenza dell'hive del registro di sistema SQL Server. Se viene rilevato il SQL Server hive, la macchina virtuale viene registrata con il [provider di risorse VM SQL](sql-vm-resource-provider-register.md) in modalità lightweight. Se nel registro di sistema non è presente alcun hive SQL Server, i file binari vengono rimossi.

Una volta abilitata la registrazione automatica per una sottoscrizione, tutte le VM correnti e future con SQL Server installato verranno registrate con il provider di risorse VM SQL in modalità lightweight. È comunque necessario eseguire [manualmente l'aggiornamento alla modalità di gestibilità completa](sql-vm-resource-provider-register.md#upgrade-to-full) per sfruttare il set completo di funzionalità. 

> [!IMPORTANT]
> L'estensione SQL IaaS Agent raccoglie i dati per lo scopo esplicito di fornire ai clienti vantaggi facoltativi quando si usa SQL Server all'interno di macchine virtuali di Azure. Microsoft non utilizzerà questi dati per i controlli delle licenze senza il consenso anticipato del cliente. Per ulteriori informazioni, vedere la [SQL Server supplemento sulla privacy](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una macchina virtuale modello di risorse di Azure [Windows Server 2008 R2 (o versione successiva)](../../../virtual-machines/windows/quick-create-portal.md) con [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) distribuita nel cloud pubblico o Azure per enti pubblici. Windows Server 2008 non è supportato. 


## <a name="enable"></a>Abilitare

Per abilitare la registrazione automatica delle macchine virtuali SQL Server nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla pagina delle risorse [**macchine virtuali SQL**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selezionare **registrazione automatica SQL Server VM** per aprire la pagina di **registrazione automatica** . 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Selezionare la registrazione automatica SQL Server VM per aprire la pagina di registrazione automatica":::

1. Scegliere la sottoscrizione dall'elenco a discesa. 
1. Leggere le condizioni e, se si accettano le condizioni **, selezionare Accetto**. 
1. Selezionare **Register (registra** ) per abilitare la funzionalità e registrare automaticamente tutte le VM SQL Server correnti e future con il provider di risorse VM SQL. Il servizio SQL Server non verrà riavviato in nessuna delle macchine virtuali. 

## <a name="disable"></a>Disabilita

Usare l' [interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure o [Azure PowerShell](/powershell/azure/install-az-ps) per disabilitare la funzionalità di registrazione automatica. Quando la funzionalità di registrazione automatica è disabilitata, SQL Server macchine virtuali aggiunte alla sottoscrizione devono essere registrate manualmente con il provider di risorse della macchina virtuale SQL. Non verrà annullata la registrazione di macchine virtuali SQL Server esistenti che sono già state registrate.



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

Aggiornare la modalità di gestibilità a [full](sql-vm-resource-provider-register.md#upgrade-to-full) per sfruttare il set di funzionalità completo fornito dal provider di risorse della macchina virtuale SQL. 

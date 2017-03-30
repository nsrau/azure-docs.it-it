---
title: Applicazione automatica delle patch per VM SQL Server (Resource Manager) | Documentazione Microsoft
description: "Viene illustrata la funzionalità di applicazione automatica delle patch per le macchine virtuali di SQL Server in esecuzione in Azure che usano Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: c48e23c1904d824ce2360f83c9df9c8e17d6200a
ms.lasthandoff: 03/25/2017


---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Gestione risorse](virtual-machines-windows-sql-automated-patching.md)
> * [Classico](../classic/sql-automated-patching.md)
> 
> 

L'applicazione automatica delle patch stabilisce un periodo di manutenzione per una macchina virtuale di Azure su cui è in esecuzione SQL Server. Gli aggiornamenti automatici possono essere installati solo durante questo periodo di manutenzione. Per SQL Server, questa restrizione verifica che gli aggiornamenti di sistema e i riavvii associati vengano eseguiti nel momento migliore per il database. L'applicazione automatica delle patch dipende dall' [estensione dell'agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Per visualizzare la versione classica di questo articolo, vedere [Applicazione automatica delle patch per SQL Server in Macchine virtuali di Azure (distribuzione classica)](../classic/sql-automated-patching.md).

## <a name="prerequisites"></a>Prerequisiti
Per usare l'applicazione automatica delle patch, tenere in considerazione i seguenti prerequisiti:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2

**Versione di SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Installare i comandi di Azure PowerShell più recenti](/powershell/azureps-cmdlets-docs) se si prevede di configurare l'applicazione automatica delle patch con PowerShell.

> [!NOTE]
> L'applicazione automatica delle patch si basa sull'estensione dell'agente IaaS di SQL Server. Per impostazione predefinita, le attuali immagini della raccolta di macchine virtuali di SQL aggiungono questa estensione. Per altre informazioni, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Impostazioni
Nella seguente tabella sono descritte le opzioni che possono essere configurate per l'applicazione automatica delle patch. I passaggi di configurazione effettivi variano a seconda che venga usato il portale di Azure o i comandi di Windows PowerShell di Azure.

| Impostazione | Valori possibili | Descrizione |
| --- | --- | --- |
| **Applicazione automatica delle patch** |Enable/Disable (disabilitato) |Abilita o disabilita l'applicazione automatica delle patch per una macchina virtuale di Azure. |
| **Pianificazione della manutenzione** |Ogni giorno, lunedì, martedì, mercoledì, giovedì, venerdì, sabato, domenica |Pianificazione per il download e l'installazione degli aggiornamenti di Windows, SQL Server e Microsoft per la macchina virtuale. |
| **Ora di inizio manutenzione** |0-24 |Ora di inizio locale per aggiornare la macchina virtuale. |
| **Durata dell'intervallo di manutenzione** |30-180 |Numero di minuti consentito per completare il download e l'installazione degli aggiornamenti. |
| **Categoria delle patch** |Importante |Categoria degli aggiornamenti da scaricare e installare. |

## <a name="configuration-in-the-portal"></a>Configurazione nel Portale
È possibile usare il portale di Azure per configurare l'applicazione automatica delle patch durante il provisioning o per le VM esistenti.

### <a name="new-vms"></a>Nuove VM
Usare il portale di Azure per configurare l'applicazione automatica delle patch quando si crea una nuova macchina virtuale di SQL Server nel modello di distribuzione di Resource Manager.

Nel pannello **Impostazioni di SQL Server** selezionare **Applicazione automatica delle patch**. Nella seguente schermata del Portale di Azure viene mostrato il pannello **Applicazione automatica delle patch di SQL** .

![Applicazione automatizzata di patch SQL nel portale di Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Per il contesto, vedere l'argomento completo sul [provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VM esistenti
Per le macchine virtuali SQL Server esistenti, selezionare la macchina virtuale SQL Server. Dopodiché, selezionare la sezione **Configurazione di SQL Server** del pannello **Impostazioni**.

![Applicazione automatizzata di patch SQL per le VM esistenti](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

Nel pannello **Configurazione di SQL Server** fare clic sul pulsante **Modifica** nella sezione delle patch.

![Configurare l'applicazione automatizzata di patch SQL per le VM esistenti](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Al termine, fare clic sul pulsante **OK** in fondo al pannello **Configurazione di SQL Server** per salvare le modifiche.

Se si intende abilitare l'applicazione automatica delle patch per la prima volta, Azure configura l'agente IaaS di SQL Server in background. Durante questo periodo, nel portale di Azure potrebbe non essere visualizzata l'informazione relativa alla configurazione dell'applicazione automatica delle patch. Attendere alcuni minuti per l'installazione e la configurazione dell'agente. A questo punto, nel portale di Azure vengono visualizzate le nuove impostazioni.

> [!NOTE]
> È inoltre possibile configurare l'applicazione automatica delle patch mediante un modello. Per altre informazioni, vedere l'articolo relativo al [modello di avvio rapido di Azure per l'applicazione automatica delle patch](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).
> 
> 

## <a name="configuration-with-powershell"></a>Configurazione con PowerShell
Dopo il provisioning della VM di SQL, usare PowerShell per configurare l'applicazione automatica delle patch.

Nell'esempio seguente, PowerShell viene utilizzato per configurare l'applicazione automatizzata di patch in una macchina virtuale di SQL Server esistente. Il comando **AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig** configura un nuovo periodo di manutenzione per gli aggiornamenti automatici.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

In base a questo esempio, nella tabella seguente vengono descritti gli effetti pratici sulla macchina virtuale di Azure di destinazione:

| Parametro | Effetto |
| --- | --- |
| **DayOfWeek** |Patch installate ogni giovedì. |
| **MaintenanceWindowStartingHour** |Inizio degli aggiornamenti alle ore 11:00. |
| **MaintenanceWindowsDuration** |Le patch devono essere installate entro 120 minuti. In base all'ora di inizio, devono essere completate entro le ore 13:00. |
| **PatchCategory** |L'unica impostazione possibile per questo parametro è **Important**. |

Potrebbero essere necessari diversi minuti per installare e configurare l'agente IaaS di SQL Server.

Per disabilitare l'applicazione automatica delle patch, eseguire lo stesso script senza il parametro **-Enable** per **AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig**. L'assenza del parametro **-Enable** segnala il comando per disabilitare la funzionalità.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre attività di automazione disponibili, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Per altre informazioni sull'esecuzione di SQL Server nelle VM di Azure, vedere [Panoramica di SQL Server nelle macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).



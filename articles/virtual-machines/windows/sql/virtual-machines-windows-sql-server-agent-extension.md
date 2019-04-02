---
title: Automatizzare le attività di gestione in macchine virtuali SQL (Resource Manager) | Microsoft Docs
description: Questo articolo descrive come gestire l'estensione SQL Server Agent, che automatizza attività di amministrazione specifiche di SQL Server. Queste includono il backup automatizzato, l'applicazione automatica delle patch e l'integrazione dell'insieme di credenziali delle chiavi di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 71878d5d033f0005d2c8c36d9f59799e125a19dd
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762702"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizzare le attività di gestione in macchine virtuali SQL con l'estensione SQL Server Agent (Resource Manager)
> [!div class="op_single_selector"]
> * [Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L'estensione SQL Server IaaS Agent (SQLIaaSExtension) viene eseguita su macchine virtuali di Azure per automatizzare le attività di amministrazione. Questo articolo fornisce una panoramica dei servizi supportati dall'estensione e delle istruzioni per l'installazione, lo stato e la rimozione.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Per visualizzare la versione classica di questo articolo, vedere [Estensione Agente IaaS di SQL Server (distribuzione classica)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Servizi supportati
L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | DESCRIZIONE |
| --- | --- |
| **Backup automatico di SQL** |Consente di automatizzare la pianificazione dei backup per tutti i database tra l'istanza predefinita o una [correttamente installato](virtual-machines-windows-sql-server-iaas-faq.md#administration) un'istanza denominata di SQL Server nella macchina virtuale. Per altre informazioni, vedere [Backup automatico per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Applicazione automatica delle patch di SQL** |Consente di configurare una finestra di manutenzione durante la quale è possibile eseguire aggiornamenti importanti di Windows nella macchina virtuale, evitandone l'esecuzione nei periodi di picco del carico di lavoro. Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrazione di Azure Key Vault** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in Macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Dopo averlo installato e messo in esecuzione, l'estensione SQL Server IaaS Agent rende disponibili queste funzionalità di amministrazione nel pannello SQL Server della macchina virtuale nel portale di Azure e tramite Azure PowerShell per le immagini del marketplace di SQL Server e Azure PowerShell per le installazioni manuali dell'estensione. 

## <a name="prerequisites"></a>Prerequisiti
Requisiti per l'uso dell'Estensione Agente IaaS di SQL Server nella VM:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versioni di SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Scaricare e configurare i comandi di Azure PowerShell più recenti](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> A questo punto, l'[Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md) non è supportata per le istanze del cluster di failover di SQL Server in Azure. È consigliabile disinstallare l'estensione dalle VM che fanno parte delle istanze del cluster di failover. Le funzionalità supportate dall'estensione non sono disponibili per le VM di SQL dopo la disinstallazione dell'agente.

## <a name="installation"></a>Installazione
L'Estensione Agente IaaS di SQL Server viene installata automaticamente quando si esegue il provisioning di una delle immagini della galleria di macchine virtuali SQL Server. L'estensione SQL IaaS offre la facilità di gestione per una singola istanza nella VM di SQL Server. Se è presente un'istanza predefinita, l'estensione quindi funzionerà con l'istanza predefinita e non supporterà la gestione di altre istanze. Se è presente alcuna istanza predefinita, ma solo un'istanza denominata, quindi gestiranno l'istanza denominata. Se è presente alcuna istanza predefinita e sono presenti più istanze denominate, quindi l'estensione avrà esito negativo per l'installazione. 



Se è necessario reinstallare manualmente l'estensione in una di queste macchine virtuali di SQL Server, usare il comando PowerShell seguente:

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> Se non è già stata eseguita, l'installazione dell'estensione riavvia il servizio SQL Server. Tuttavia l'aggiornamento dell'estensione SQL IaaS non riavvia il servizio SQL Server. 

> [!NOTE]
> Sebbene sia possibile installare l'estensione agente IaaS di SQL Server per le immagini personalizzate di SQL Server, la funzionalità è attualmente limitata al [modificando il tipo di licenza](virtual-machines-windows-sql-ahb.md). Altre funzionalità fornite dall'estensione SQL IaaS funzionano solo nelle [immagini di VM di SQL Server raccolta](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (pagamento a consumo o bring-your-own-license).

### <a name="use-a-single-named-instance"></a>Usare una singola istanza denominata
L'estensione SQL IaaS funzionerà con un'istanza denominata su un'immagine di SQL Server se l'istanza predefinita viene disinstallato correttamente e se si reinstalla l'estensione IaaS.

Per usare un'istanza denominata di SQL Server, eseguire le operazioni seguenti:
   1. Distribuire una VM di SQL Server dal marketplace. 
   1. Disinstallare l'estensione IaaS dall'interno di [portale di Azure](https://portal.azure.com).
   1. Disinstallare SQL Server completamente all'interno di VM di SQL Server.
   1. Installare SQL Server con un'istanza denominata all'interno della VM di SQL Server. 
   1. Installare l'estensione IaaS dal portale di Azure.  

## <a name="status"></a>Stato
Un modo per verificare che l'estensione sia installata consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **Tutte le impostazioni** nel pannello della macchina virtuale e quindi fare clic su **Estensioni**. L'estensione **SQLIaaSExtension** dovrebbe essere visualizzata nell'elenco.

![Estensione SQL Server IaaS Agent nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

È anche possibile usare il cmdlet **Get-AzVMSqlServerExtension** di Azure PowerShell.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Il comando precedente conferma l'installazione dell'agente e ne fornisce informazioni generali sullo stato. È inoltre possibile ottenere informazioni specifiche sullo stato del backup e dell'applicazione di patch in modalità automatizzata con i comandi seguenti.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Rimozione
Nel portale di Azure, è possibile disinstallare l'estensione facendo clic sui puntini di sospensione sul **estensioni** finestra delle proprietà di macchina virtuale. Fare quindi clic su **Elimina**.

![Disinstallare l'estensione SQL Server IaaS Agent nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

È anche possibile usare il cmdlet **Remove-AzVMSqlServerExtension** di Azure PowerShell.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>Passaggi successivi
Iniziare a usare uno dei servizi supportati dall'estensione. Per altre informazioni, vedere gli articoli citati nella sezione [Servizi supportati](#supported-services) di questo articolo.

Per altre informazioni sull'esecuzione di SQL Server in Macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).


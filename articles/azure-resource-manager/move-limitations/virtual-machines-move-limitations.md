---
title: Spostare le macchine virtuali di Azure alla nuova sottoscrizione o gruppo di risorse | Microsoft Docs
description: Usare Azure Resource Manager per spostare le macchine virtuali in un nuovo gruppo di risorse o sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 095ed1c8d2328b1eb391042125526696ba8cda49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723545"
---
# <a name="move-guidance-for-virtual-machines"></a>Spostare linee guida per le macchine virtuali

Questo articolo descrive gli scenari che non sono attualmente supportati e i passaggi per spostare le macchine virtuali con backup.

## <a name="scenarios-not-supported"></a>Scenari non supportati

Non sono ancora supportati gli scenari seguenti:

* I dischi gestiti in zone di disponibilità non è possibile spostare una sottoscrizione diversa.
* Le macchine virtuali con certificato archiviato in Key Vault possono essere spostate in un nuovo gruppo di risorse nella stessa sottoscrizione, ma non da una sottoscrizione a un'altra.
* Set di scalabilità di macchine virtuali con Load Balancer Standard SKU o un indirizzo IP pubblico dello SKU Standard non può essere spostato.
* Non è possibile spostare da un gruppo di risorse o una sottoscrizione a un'altra macchine virtuali create a partire da risorse Marketplace con piani assegnati. Sottoporre a deprovisioning le macchine virtuali nella sottoscrizione in cui si trovano e distribuirle di nuovo nella nuova sottoscrizione.
* Le macchine virtuali in una rete virtuale esistente, ma è non sono lo spostamento di tutte le risorse nella rete virtuale.

## <a name="virtual-machines-with-azure-backup"></a>Macchine virtuali con Backup di Azure

Per spostare le macchine virtuali configurate con Backup di Azure, usare la soluzione alternativa seguente:

* Individuare la località della macchina virtuale.
* Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<location of your VM>_1`, ad esempio AzureBackupRG_westus2_1
* Nel portale di Azure selezionare quindi l'opzione "Mostra tipi nascosti"
* In PowerShell usare il cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Nella CLI usare il `az resource list -g AzureBackupRG_<location of your VM>_1`
* Trovare la risorsa di tipo `Microsoft.Compute/restorePointCollections` con modello di denominazione `AzureBackup_<name of your VM that you're trying to move>_###########`
* Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.
* Al termine dell'eliminazione, è possibile spostare l'insieme di credenziali e una macchina virtuale per la sottoscrizione di destinazione. Dopo lo spostamento è possibile continuare a eseguire i backup senza alcuna perdita di dati.
* Per informazioni sullo spostamento degli insiemi di credenziali di Servizi di ripristino per il backup, vedere [Limitazioni di Servizi di ripristino](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../resource-group-move-resources.md).

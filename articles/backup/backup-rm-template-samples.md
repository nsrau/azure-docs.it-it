---
title: Modelli di Azure Resource Manager per Backup di Azure | Microsoft Docs
description: Esempi PowerShell di Backup di Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/18/2018
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: b8502e4e3934b36fb4c8ccac00f4fa14565780d9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Modelli di Azure Resource Manager per Backup di Azure

La tabella seguente contiene collegamenti ai modelli di Azure Resource Manager da usare con gli insiemi di credenziali dei servizi di ripristino e le funzionalità di Backup di Azure.

|   |   |
|---|---|
|**Insieme di credenziali dei servizi di ripristino** | |
| [Creare un insieme di credenziali di Servizi di ripristino](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Creare un insieme di credenziali dei servizi di ripristino. L'insieme di credenziali può essere usato per Backup di Azure e Azure Site Recovery. |
|**Eseguire il backup di macchine virtuali**| |
| [Eseguire il backup di macchine virtuali di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms). | Usare l'insieme di credenziali dei servizi di ripristino e i criteri di backup esistenti per eseguire il backup delle macchine virtuali di Resource Manager nello stesso gruppo di risorse.|
| [Eseguire il backup di macchine virtuali IaaS in un insieme di credenziali dei servizi di ripristino](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Modello per il backup di macchine virtuali classiche e di Resource Manager. |
| [Creare criteri di backup settimanale per le macchine virtuali IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Il modello consente di creare un insieme di credenziali dei servizi di ripristino e un criterio di backup settimanale per il backup di macchine virtuali classiche e di Resource Manager.|
| [Creare criteri di backup giornaliero per le macchine virtuali IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Il modello consente di creare un insieme di credenziali dei servizi di ripristino e un criterio di backup giornaliero per il backup di macchine virtuali classiche e di Resource Manager.|
| [Distribuire la macchina virtuale di Windows Server con backup abilitato](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Il modello consente di creare una macchina virtuale di Windows Server e un insieme di credenziali dei servizi di ripristino con il criterio di backup predefinito abilitato.|
|**Monitorare i processi di backup** |  |
| [Usare Log Analytics OMS per monitorare Backup di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Il modello consente di distribuire il monitoraggio di OMS per Backup di Azure, che permette di monitorare i processi di backup e ripristino, gli avvisi di backup e l'archiviazione cloud usata negli insiemi di credenziali dei servizi di ripristino.|  
|   |   |


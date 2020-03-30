---
title: Azure Security Control - Data Recovery
description: Recupero dei dati del controllo di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934500"
---
# <a name="security-control-data-recovery"></a>Controllo di sicurezza: recupero dei dati

Assicurarsi che il backup periodico di tutti i dati, le configurazioni e i segreti di sistema venga eseguito automaticamente.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 9.1 | 10.1 | Customer |

Abilitare Backup di Azure e configurare l'origine di backup (macchine virtuali di Azure, SQL Server o condivisioni file), nonché la frequenza e il periodo di conservazione desiderati.

Come abilitare Backup di Azure:How to enable Azure Backup:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 9.2 | 10.2 | Customer |

Abilitare Backup di Azure e le macchine virtuali di destinazione, nonché i periodi di frequenza e conservazione desiderati. Eseguire il backup delle chiavi gestite dai clienti all'interno dell'insieme di credenziali delle chiavi di Azure.Backup customer managed keys within Azure Key

Come abilitare Backup di Azure:How to enable Azure Backup:

https://docs.microsoft.com/azure/backup/

Come eseguire il backup delle chiavi dell'insieme di credenziali in Azure:How to backup key vault keys in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 9.3 | 10.3 | Customer |

Garantire la possibilità di eseguire periodicamente il ripristino dei dati del contenuto all'interno di Backup di Azure.Ensure ability to periodically perform data restoration of content within Azure Backup. Se necessario, verificare il ripristino su una VLAN isolata. Testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

Come ripristinare i file dal backup della macchina virtuale di Azure:How to recover files from Azure Virtual Machine backup:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Come ripristinare le chiavi dell'insieme di credenziali delle chiavi in Azure:How to restore key vault keys in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 9.4 | 10.4 | Customer |

Per il backup in locale, la crittografia dei dati inattivi viene eseguita con l'uso della passphrase immessa durante il backup in Azure. Per le macchine virtuali di Azure, i dati inattivi sono crittografati usando la crittografia del servizio di archiviazione. È possibile abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi per proteggere le chiavi da eliminazioni accidentali o dannose.

Come abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Passaggi successivi

Vedere il controllo di sicurezza successivo: [Risposta agli incidentiSee](security-control-incident-response.md) the next security control: Incident Response

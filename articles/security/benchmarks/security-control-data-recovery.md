---
title: Controllo di sicurezza di Azure-ripristino dei dati
description: Ripristino dei dati del controllo di sicurezza di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 835e4f681d514bb6b92caa5ee076e3794ed59236
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698819"
---
# <a name="security-control-data-recovery"></a>Controllo di sicurezza: ripristino dei dati

Assicurarsi che tutti i dati di sistema, le configurazioni e i segreti vengano automaticamente sottoposti a backup a intervalli regolari.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 9.1 | 10.1 | Cliente |

Abilitare backup di Azure e configurare l'origine di backup (VM di Azure, SQL Server o condivisioni file), nonché la frequenza e il periodo di memorizzazione desiderati.

- [Come abilitare backup di Azure](../../backup/index.yml)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 9.2 | 10,2 | Cliente |

Abilitare backup di Azure e VM di destinazione, nonché i periodi di conservazione e frequenza desiderati. Eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault.

- [Come abilitare backup di Azure](../../backup/index.yml)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 9.3 | 10.3 | Cliente |

Verificare la possibilità di eseguire periodicamente il ripristino dei dati del contenuto all'interno di backup di Azure. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

- [Come ripristinare i file dal backup della macchina virtuale di Azure](../../backup/backup-azure-restore-files-from-vm.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantire la protezione dei backup e delle chiavi gestite dal cliente

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 9.4 | 10.4 | Cliente |

Per il backup in locale, la crittografia dei dati inattivi viene eseguita con l'uso della passphrase immessa durante il backup in Azure. Per le macchine virtuali di Azure, i dati inattivi sono crittografati usando la crittografia del servizio di archiviazione. Usare il controllo degli accessi in base al ruolo di Azure per proteggere i backup e le chiavi gestite dal cliente.  

Abilitare Soft-Delete ed eliminare la protezione in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.  Se si usa archiviazione di Azure per archiviare i backup, abilitare l'eliminazione temporanea per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot BLOB. 

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)

- [Come abilitare Soft-Delete ed eliminare la protezione in Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo:  [risposta agli eventi imprevisti](security-control-incident-response.md)
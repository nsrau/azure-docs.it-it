---
title: Centro di backup-domande frequenti
description: Questo articolo risponde a domande frequenti su Backup Center
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: c5b23a32b60f651cd3ff91819155d83a7465491a
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173819"
---
# <a name="backup-center---frequently-asked-questions"></a>Centro di backup-domande frequenti

## <a name="management"></a>Gestione

### <a name="can-backup-center-be-used-across-tenants"></a>Il centro di backup può essere usato tra i tenant?

Sì, se si usa [Azure Lighthouse](../lighthouse/overview.md) e si ha l'accesso delegato alle sottoscrizioni in tenant diversi, è possibile usare il centro di backup come un unico riquadro di vetro per gestire i backup tra i tenant.

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>È possibile usare il centro di backup per gestire gli insiemi di credenziali dei servizi di ripristino e gli insiemi di credenziali per il backup?

Sì, il centro di backup può gestire gli insiemi di credenziali [dei servizi di ripristino](./backup-azure-recovery-services-vault-overview.md) e gli insiemi di credenziali di [backup](backup-vault-overview.md).

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>Si è verificato un ritardo prima della superficie dei dati nel centro di backup?

Il centro di backup ha lo scopo di fornire informazioni in tempo reale. Potrebbe verificarsi un ritardo di pochi secondi tra l'ora in cui un'entità viene visualizzata in una singola schermata dell'insieme di credenziali e l'ora in cui viene visualizzata la stessa entità nel centro di backup.

## <a name="configuration"></a>Configurazione

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>È necessario configurare qualsiasi elemento per visualizzare i dati nel centro di backup?

No. Il centro di backup è pronto per l'uso. Tuttavia, per visualizzare i [report di backup](./configure-reports.md) in Backup Center, è necessario configurare la creazione di report per gli insiemi di credenziali.

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>È necessario disporre di autorizzazioni speciali per usare il centro di backup?

Il centro di backup non necessita di nuove autorizzazioni. Fino a quando si dispone del livello corretto di accesso RBAC di Azure per le risorse gestite, è possibile usare il centro di backup per queste risorse. Per visualizzare le informazioni sui backup, ad esempio, è necessario l'accesso in **lettura** agli insiemi di credenziali. Per configurare il backup ed eseguire altre azioni correlate al backup, saranno necessari i ruoli **collaboratore backup** o **operatore di backup** . Altre informazioni sui [ruoli di Azure per backup di Azure](./backup-rbac-rs-vault.md).

## <a name="pricing"></a>Prezzi

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>È necessario pagare qualcosa di aggiuntivo per usare Esplora backup?

Attualmente non sono previsti costi aggiuntivi, oltre ai costi di backup, per usare il centro di backup. Tuttavia, se si usano i [report di backup](./configure-reports.md) in Backup Center, l'uso dei log di monitoraggio di Azure per i report di backup [comporta un costo](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="next-steps"></a>Passaggi successivi

Leggere le altre domande frequenti:

* [Domande frequenti sugli insiemi di credenziali dei servizi di ripristino](./backup-azure-backup-faq.md)
* [Domande comuni sui backup di macchine virtuali di Azure](./backup-azure-vm-backup-faq.md)
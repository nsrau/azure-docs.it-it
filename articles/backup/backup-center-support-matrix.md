---
title: Matrice di supporto per il centro di backup
description: In questo articolo vengono riepilogati gli scenari supportati da backup Center per ogni tipo di carico di lavoro
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8effc2514abf1cac55abc28b625b869810536baf
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997001"
---
# <a name="support-matrix-for-backup-center"></a>Matrice di supporto per il centro di backup

Il centro di backup offre un unico riquadro di vetro per le aziende che [governano, monitorano, operano e analizzano i backup su larga scala](backup-center-overview.md). In questo articolo vengono riepilogati gli scenari supportati da backup Center per ogni tipo di carico di lavoro.

## <a name="supported-scenarios"></a>Scenari supportati

| **Categoria** | **Scenario**  | **Carichi di lavoro supportati**  | **Limiti** |
| -------------| ------------- | ----------------------- |------------|
| Monitoraggio   | Visualizza tutti i processi | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | <li> 7 giorni per i processi disponibili. <br> <li> Ogni filtro/elenco a discesa supporta un massimo di 1000 elementi. Quindi, il centro di backup può essere usato per monitorare un massimo di 1000 sottoscrizioni e 1000 insiemi di credenziali tra i tenant. |
| Monitoraggio | Visualizza tutte le istanze di backup | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | Vedere sopra. |
| Monitoraggio | Visualizza tutti i criteri di backup | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | Vedere sopra. |
| Monitoraggio | Visualizza tutti gli insiemi di credenziali | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | Vedere sopra. |
| Azioni | Configurare il backup | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | Vedere le matrici di supporto per il backup di [macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) e il [backup del server database di Azure per PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Azioni | Ripristinare un'istanza di backup | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | Vedere le matrici di supporto per il backup di [macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) e il [backup del server database di Azure per PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Azioni | Create vault | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | Vedere le matrici di supporto per l'insieme di credenziali di [servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support) |
| Azioni | Creare criteri di backup | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | Vedere le matrici di supporto per l'insieme di credenziali di [servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support) |
| Azioni | Eseguire il backup su richiesta per un'istanza di backup | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | Vedere le matrici di supporto per il backup di [macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) e il [backup del server database di Azure per PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Azioni | Arrestare il backup per un'istanza di backup | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | Vedere le matrici di supporto per il backup di [macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) e il [backup del server database di Azure per PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Informazioni dettagliate | Visualizzare i report di backup | <li> Macchina virtuale di Azure <br><br> <li> SQL in una macchina virtuale di Azure <br><br> <li> SAP HANA nella macchina virtuale di Azure <br><br> <li> File di Azure <br><br> <li> System Center Data Protection Manager <br><br> <li> Agente di backup di Azure (MARS) <br><br> <li> Server di backup di Azure (MABS) | Vedere gli [scenari supportati per i report di backup](https://docs.microsoft.com/azure/backup/configure-reports#supported-scenarios) |
| Governance | Visualizzare e assegnare criteri di Azure predefiniti e personalizzati nella categoria ' backup ' | N/D | N/D |
| Governance | Visualizza origini dati non configurate per il backup | <li> Macchina virtuale di Azure <br><br> <li> Database di Azure per il server PostgreSQL | N/D |

## <a name="unsupported-scenarios"></a>Scenari non supportati

| **Categoria** | **Scenario**  |
|--------------|---------------|
| Monitoraggio | Visualizza avvisi su larga scala |
| Azioni | Configurare le impostazioni dell'insieme di credenziali su larga scala |
| Azioni | Eseguire un processo di ripristino tra più aree dal centro di backup |

## <a name="next-steps"></a>Passaggi successivi

* [Esaminare la matrice di supporto per backup di Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix)
* [Esaminare la matrice di supporto per il backup delle macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)
* [Esaminare la matrice di supporto per il backup del server database di Azure per PostgreSQL](backup-azure-database-postgresql.md#support-matrix)

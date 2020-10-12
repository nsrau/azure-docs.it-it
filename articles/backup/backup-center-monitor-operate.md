---
title: Monitorare e gestire i backup con Centro backup
description: Questo articolo illustra come monitorare e gestire i backup su larga scala con backup Center
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 29e09e502e83ea67af290f206ee0e68b847b2069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996674"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Monitorare e gestire i backup con Centro backup

Come amministratore di backup, è possibile usare il centro di backup come un unico riquadro di vetro per monitorare i processi e l'inventario dei backup su base giornaliera. È anche possibile usare il centro di backup per eseguire le normali operazioni, ad esempio la risposta alle richieste di backup su richiesta, il ripristino dei backup, la creazione di criteri di backup e così via.

## <a name="supported-scenarios"></a>Scenari supportati

* Backup Center è attualmente supportato per il backup di macchine virtuali di Azure e per il backup del server database di Azure per PostgreSQL.
* Per un elenco dettagliato degli scenari supportati e non supportati, vedere la [matrice di supporto](backup-center-support-matrix.md) .

## <a name="backup-instances"></a>Istanze di backup

Backup Center consente la ricerca e l'individuabilità semplificate delle istanze di backup nell'intero patrimonio di backup.

Selezionando la scheda **istanze di backup** in Backup Center è possibile visualizzare i dettagli di tutte le istanze di backup a cui si ha accesso.

 È possibile visualizzare le informazioni seguenti su ognuna delle istanze di backup:

* Sottoscrizione DataSource
* Gruppo di risorse DataSource
* Punto di ripristino più recente
* Insieme di credenziali associato all'istanza di backup
* Stato protezione

 È anche possibile filtrare l'elenco delle istanze di backup sui parametri seguenti:

* Sottoscrizione DataSource
* Gruppo di risorse DataSource
* Percorso DataSource
* Tipo di origine dati
* Insiemi di credenziali
* Stato protezione
* Tag DataSource

Facendo clic con il pulsante destro del mouse su uno degli elementi della griglia è possibile eseguire azioni sull'istanza di backup specificata, ad esempio passare alla risorsa, attivare backup su richiesta e ripristini oppure arrestare il backup.

![Centro di backup-istanze](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Processi di backup

Backup Center consente di visualizzare informazioni dettagliate su tutti i processi creati nell'area di backup e di intraprendere le azioni appropriate per i processi non riusciti.

Selezionando la voce di menu **processi di backup** in Backup Center viene fornita una visualizzazione di tutti i processi. Ogni processo contiene le informazioni seguenti:

* Istanza di backup associata al processo
* Sottoscrizione DataSource
* Gruppo di risorse DataSource
* Percorso DataSource
* Operazione del processo
* Stato processo
* Ora di inizio processo
* Durata processo

La selezione di un elemento nella griglia consente di visualizzare altri dettagli sul processo specificato. Facendo clic con il pulsante destro del mouse su un elemento è possibile passare alla risorsa per eseguire un'azione necessaria.

![Centro di backup-processi](./media/backup-center-monitor-operate/backup-center-jobs.png)

Utilizzando la scheda **processi di backup** , è possibile visualizzare i processi negli ultimi sette giorni. Per visualizzare i processi meno recenti, utilizzare i [report di backup](backup-center-obtain-insights.md).

## <a name="vaults"></a>Insiemi di credenziali

Selezionando **la voce** di menu insiemi di credenziali in Backup Center è possibile visualizzare un elenco di tutti gli insiemi di credenziali dei [servizi di ripristino](backup-azure-recovery-services-vault-overview.md) e gli insiemi di credenziali per il [backup](backup-vault-overview.md) a cui si ha accesso. È possibile filtrare l'elenco con i parametri seguenti:

* Sottoscrizione dell'insieme di credenziali
* Gruppo di risorse dell'insieme di credenziali
* Nome dell'insieme di credenziali
* Tipo di origine dati associato ai criteri

Selezionando un elemento nell'elenco è possibile passare a un insieme di credenziali specificato.

![Centro di backup-insiemi di credenziali](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Criteri di backup

Backup Center consente di visualizzare e modificare le informazioni chiave per uno dei criteri di backup.

Selezionando la voce di menu **criteri di backup** è possibile visualizzare tutti i criteri creati nell'area di backup. È possibile filtrare l'elenco in base alla sottoscrizione, al gruppo di risorse, al tipo di origine dati e all'insieme di credenziali. Facendo clic con il pulsante destro del mouse su un elemento della griglia è possibile visualizzare gli elementi associati per tale criterio, modificare il criterio o persino eliminarlo, se necessario.

![Centro di backup-criteri](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Passaggi successivi

* [Governare la proprietà di backup](backup-center-govern-environment.md)
* [Eseguire azioni con backup Center](backup-center-actions.md)
* [Ottenere informazioni dettagliate sui backup](backup-center-obtain-insights.md)

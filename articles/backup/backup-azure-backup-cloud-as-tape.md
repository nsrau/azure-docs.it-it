---
title: Come sostituire l'infrastruttura basata su nastro
description: Informazioni su come backup di Azure offre una semantica di tipo nastro che consente di eseguire il backup e il ripristino dei dati in Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 71bfa86611b3a63fa6a28f60f80608b394cab5a3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514187"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Spostare lo spazio di archiviazione a lungo termine su nastro nel cloud di Azure

I clienti di Backup di Azure e System Center Data Protection Manager possono eseguire le attività seguenti:

* Eseguire il backup dei dati secondo le pianificazioni più adatte alle esigenze dell'organizzazione.
* Mantenere i dati di backup per periodi di tempo più lunghi.
* Includere Azure nelle strategie di conservazione dei dati a lungo termine, in alternativa al backup su nastro.

Questo articolo illustra come abilitare i criteri di backup e di conservazione. I clienti che usano i nastri per soddisfare le esigenze di conservazione a lungo termine dispongono ora di un'alternativa efficace e affidabile. Questa funzionalità è abilitata nella versione più recente di Backup di Azure, disponibile [in questa pagina](https://aka.ms/azurebackup_agent). I clienti di System Center DPM devono effettuare l'aggiornamento a DPM 2012 R2 UR5 almeno, prima di poter usare DPM con il servizio Backup di Azure.

## <a name="what-is-the-backup-schedule"></a>Che cos'è la pianificazione di backup?

La pianificazione del backup indica la frequenza dell'operazione di backup. Ad esempio, le impostazioni nella schermata di seguito indicano che i backup vengono eseguiti ogni giorno alle ore 18.00 e a mezzanotte.

![Pianificazione giornaliera](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

I clienti possono anche pianificare un backup settimanale. Ad esempio, le impostazioni nella schermata di seguito indicano che verranno eseguiti backup a domeniche e mercoledì alterni alle 9:30 e all'1: 00.

![Pianificazione settimanale](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Che cos'è il criterio di conservazione?

I criteri di conservazione specificano il periodo di tempo per cui il backup deve essere archiviato. Anziché specificare solo un "criterio semplice" per tutti i punti di backup, i clienti possono specificare criteri di conservazione diversi in base al momento in cui viene eseguito il backup. Ad esempio, un punto di backup eseguito quotidianamente, che funge da punto di ripristino operativo, viene conservato per 90 giorni. Il punto di backup eseguito alla fine di ogni trimestre per scopi di controllo viene mantenuto per un periodo più lungo.

![Criteri di conservazione](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Il numero totale di "punti di conservazione" specificati in questi criteri è pari a 90 (punti giornalieri) + 40 (uno per ogni trimestre per 10 anni) = 130.

## <a name="example--putting-both-together"></a>Esempio: Uso di entrambi i metodi

![Schermata di esempio](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Criteri di mantenimento giornaliero**: i backup eseguiti quotidianamente vengono archiviati per sette giorni.
2. **Criteri di conservazione settimanale**: i backup eseguiti a mezzanotte e le 18.00 sabato vengono conservati per quattro settimane.
3. **Criteri di conservazione mensili**: i backup eseguiti a mezzanotte e le 18.00 dell'ultimo sabato di ogni mese vengono conservati per 12 mesi.
4. **Criteri di conservazione annuali**: i backup eseguiti a mezzanotte dell'ultimo sabato di ogni mese di marzo vengono conservati per 10 anni.

Il numero totale dei "punti di conservazione" (punti da cui un cliente può ripristinare i dati) riportati nel diagramma precedente viene calcolato nel modo seguente:

* due punti al giorno per sette giorni = 14 punti di ripristino
* due punti a settimana per quattro settimane = 8 punti di ripristino
* due punti al mese per 12 mesi = 24 punti di ripristino
* un punto all'anno per 10 anni = 10 punti di ripristino

Il numero totale dei punti di ripristino è 56.

> [!NOTE]
> Con backup di Azure è possibile creare fino a 9999 punti di ripristino per ogni istanza protetta. Un'istanza protetta è un computer, un server fisico o virtuale o un carico di lavoro che esegue il backup in Azure.
>

## <a name="advanced-configuration"></a>Configurazione avanzata

Facendo clic su **Modifica** nella schermata precedente, i clienti dispongono di un'ulteriore flessibilità nella definizione delle pianificazioni di conservazione.

![Modifica](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul Backup di Azure vedere:

* [Introduzione a Backup di Azure](./backup-overview.md)
* [Valutazione di Backup di Azure](./backup-windows-with-mars-agent.md)

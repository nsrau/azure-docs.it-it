---
title: Usare Backup di Azure per sostituire l'infrastruttura basata su nastro | Documentazione Microsoft
description: Informazioni sulla semantica di Backup di Azure, simile all'archiviazione su nastro, che consente di eseguire il backup e il ripristino dei dati in Azure
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
ms.assetid: 2e1bb67d-986c-4437-8056-3a63169b4214
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/10/2017
ms.author: saurse;trinadhk;markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0f3152daf5f91f7c9e540797bf09b21969d2d33
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Spostare lo spazio di archiviazione a lungo termine su nastro nel cloud di Azure
I clienti di Backup di Azure e System Center Data Protection Manager possono eseguire le attività seguenti:

* Eseguire il backup dei dati secondo le pianificazioni più adatte alle esigenze dell'organizzazione.
* Mantenere i dati di backup per periodi più lunghi
* Includere Azure nelle strategie di conservazione dei dati a lungo termine, in alternativa al backup su nastro.

Questo articolo illustra come abilitare i criteri di backup e di conservazione. I clienti che usano i nastri per soddisfare le esigenze di conservazione a lungo termine dispongono ora di un'alternativa efficace e affidabile. Questa funzionalità è abilitata nella versione più recente di Backup di Azure, disponibile [in questa pagina](http://aka.ms/azurebackup_agent). I clienti di System Center DPM devono effettuare l'aggiornamento a DPM 2012 R2 UR5 almeno, prima di poter usare DPM con il servizio Backup di Azure.

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
2. **Criteri di conservazione settimanale**: i backup eseguiti ogni giorno a mezzanotte e ogni sabato alle 18.00 verranno conservati per quattro settimane
3. **Criteri di mantenimento mensile**: i backup eseguiti a mezzanotte e alle 18.00 dell'ultimo sabato del mese verranno conservati per 12 mesi
4. **Criteri di mantenimento annuale**: i backup eseguiti a mezzanotte dell'ultimo sabato del mese di marzo verranno conservati per 10 anni

Il numero totale dei "punti di conservazione" (punti da cui un cliente può ripristinare i dati) riportati nel diagramma precedente viene calcolato nel modo seguente:

* due punti al giorno per sette giorni = 14 punti di ripristino
* due punti a settimana per quattro settimane = 8 punti di ripristino
* due punti al mese per 12 mesi = 24 punti di ripristino
* un punto all'anno per 10 anni = 10 punti di ripristino

Il numero totale dei punti di ripristino è 56.

> [!NOTE]
> Il backup di Azure non dispone di una restrizione sul numero di punti di ripristino.
>
>

## <a name="advanced-configuration"></a>Configurazione avanzata
Facendo clic su **Modifica** nella schermata precedente, i clienti dispongono di un'ulteriore flessibilità nella definizione delle pianificazioni di conservazione.

![Modifica](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sul Backup di Azure vedere:

* [Introduzione a Backup di Azure](backup-introduction-to-azure-backup.md)
* [Valutazione di Backup di Azure](backup-try-azure-backup-in-10-mins.md)

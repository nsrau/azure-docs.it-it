---
title: Aggiornamento del pacchetto di contenuto per reporting centrale di Backup di Azure
description: Informazioni sugli aggiornamenti del pacchetto di contenuto Backup di Azure in Power BI
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan;
ms.openlocfilehash: 07774234849d96b9a44678b68ca7b13b6b4830f9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39267181"
---
# <a name="updating-azure-backup-central-reporting-content-pack"></a>Aggiornamento del pacchetto di contenuto per reporting centrale di Backup di Azure 

[Il pacchetto di contenuto Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) può essere usato per visualizzare i report sui backup in una posizione centrale. Il pacchetto di contenuto viene aggiornato regolarmente per aggiungere altre funzionalità e correzioni di bug. Questo articolo illustra i passaggi per aggiornare il pacchetto di contenuto, ritardare l'aggiornamento e gli aggiornamenti installati nel corso del tempo.

## <a name="how-to-get-updates-to-the-content-pack"></a>Come ottenere gli aggiornamenti per il pacchetto di contenuto

### <a name="to-get-the-updated-content-pack"></a>Per ottenere il pacchetto di contenuto aggiornato
La copia del pacchetto di contenuto verrà aggiornata automaticamente se non sono state apportate modifiche. Nel caso in cui il pacchetto di contenuto sia stato modificato, si riceverà una notifica in Power BI e una notifica di posta elettronica simili. È possibile scegliere di ricevere il pacchetto di contenuto aggiornato in base alle proprie esigenze. 

### <a name="to-delay-the-update"></a>Per ritardare l'aggiornamento
La procedura consigliata consiste nell'importare il pacchetto di contenuto in un'[area di lavoro personalizzata](https://youtu.be/26zyOtyHPJM?t=1m57s). A questo punto è possibile modificare il report.
Come indicato in precedenza, se il pacchetto di contenuto è stato modificato, viene visualizzata una notifica in Power BI. È possibile scegliere di scaricare il pacchetto di contenuto in un secondo momento. 

## <a name="coming-soon"></a>Imminente
   
Il pacchetto di contenuto Backup di Azure è stato aggiornato per supportare più carichi di lavoro, come SQL nel backup di macchine virtuali IaaS e DPM di System Center, oltre al supporto corrente per MAB e backup di macchine virtuali di Azure. Ciò significa che a breve sarà possibile visualizzare e analizzare tutti i dati di backup in un'unica posizione centrale. I [report possono anche essere personalizzati](https://youtu.be/26zyOtyHPJM) per soddisfare le esigenze dell'organizzazione.

Per rendere i report più significativi per tutti i carichi di lavoro, viene modificato il set di report preconfigurati con il pacchetto di contenuto Backup di Azure. Un'anteprima del futuro set di report è disponibile qui:

### <a name="summary"></a>Summary
   
![Summary](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>Fatturazione

![Fatturazione](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>Conformità

![Conformità](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Archiviazione

![Archiviazione](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Elementi di backup
![Elementi di backup](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Avvisi

![Avvisi](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>Processi

![Processi](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>Passaggi successivi:

* [Condividere i report nell'intera organizzazione](https://youtu.be/26zyOtyHPJM)
* [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

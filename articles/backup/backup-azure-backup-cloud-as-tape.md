<properties
   pageTitle="Usare Backup di Azure per sostituire l'infrastruttura basata su nastro"
   description="Informazioni sulla semantica di Backup di Azure, simile all'archiviazione su nastro, che consente di eseguire il backup e il ripristino dei dati in Azure"
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="03/27/2015"
   ms.author="prvijay"/>

# Usare Backup di Azure per sostituire l'infrastruttura basata su nastro

I clienti di Backup di Azure e System Center Data Protection Manager possono eseguire le attività seguenti:

+ Eseguire il backup dei dati secondo le pianificazioni più adatte alle esigenze dell'organizzazione

+ Mantenere i dati di backup per periodi più lunghi

+ Includere Azure nelle strategie di conservazione dei dati a lungo termine (in alternativa al backup su nastro)

Questo articolo illustra come abilitare i criteri di backup e di conservazione. I clienti che usano i nastri per soddisfare le esigenze di conservazione a lungo termine dispongono ora di un'alternativa efficace e affidabile. Questa funzionalità è abilitata nella versione più recente di Backup di Azure, disponibile [in questa pagina](http://aka.ms/azurebackup_agent). Prima di usare questa funzionalità, i clienti SCDPM dovranno passare a UR5.

## Che cos'è la pianificazione di backup?
La pianificazione di backup indica la frequenza dell'operazione di backup, ad esempio le impostazioni nella schermata riportata di seguito indicano che i backup verranno eseguiti ogni giorno alle 18.00 e a mezzanotte. <br/>

![Pianificazione giornaliera][1]

I clienti possono inoltre pianificare un backup settimanale, ad esempio le impostazioni nella schermata riportata di seguito indicano che i backup verranno eseguiti la domenica e il mercoledì a settimane alterne alle 9.30 e all'1.00. <br/>

![Pianificazione settimanale][2]

## Che cosa sono i criteri di conservazione?
I criteri di conservazione specificano il periodo di tempo per cui il backup deve essere archiviato. Anziché specificare solo un "criterio semplice" per tutti i punti di backup, i clienti possono specificare criteri di conservazione diversi in base al momento in cui viene eseguito il backup. Potrebbe ad esempio essere necessario, a scopo di controllo, conservare il punto di backup eseguito alla fine di ogni trimestre per un periodo di tempo più lungo, mentre il punto di backup eseguito quotidianamente, usato come punto di ripristino operativo, deve essere conservato per 90 giorni. <br/>

![Criteri di conservazione][3]

Il numero totale di "punti di conservazione" specificati in questi criteri è pari a 90 (punti giornalieri) + 40 (uno per ogni trimestre per 10 anni) = 130.

## Esempio: Uso di entrambi i metodi
<br/> ![Schermata di esempio][4]

1. **Criteri di mantenimento giornaliero**: i backup eseguiti quotidianamente vengono archiviati per 7 giorni.
2. **Criteri di mantenimento settimanale**: i backup eseguiti ogni sabato a mezzanotte e alle 18.00 verranno conservati per 4 settimane.
3. **Criteri di mantenimento mensile**: i backup eseguiti a mezzanotte e alle 18.00 dell'ultimo sabato di ogni mese verranno conservati per 12 mesi.
4. **Criteri di mantenimento annuale**: i backup eseguiti a mezzanotte dell'ultimo sabato di ogni mese di marzo verranno conservati per 10 anni.

Il numero totale dei "punti di conservazione" (punti da cui un cliente può ripristinare i dati) riportati nel diagramma precedente viene calcolato nel modo seguente:

+ 2 punti al giorno per 7 giorni = 14 punti di ripristino

+ 2 punti a settimana per 4 settimane = 8 punti di ripristino

+ 2 punti al mese per 12 mesi = 24 punti di ripristino

+ 1 punto all'anno per 10 anni = 10 punti di ripristino

Il numero totale dei punti di ripristino è 56.

## Configurazione avanzata

Facendo clic su **Modifica** nella schermata precedente, i clienti dispongono di un'ulteriore flessibilità nella definizione delle pianificazioni di conservazione. <br/>

![Modifica][5]


<!--Image references-->
[1]: ./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png
[2]: ./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png
[3]: ./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png
[4]: ./media/backup-azure-backup-cloud-as-tape/samplescreen.png
[5]: ./media/backup-azure-backup-cloud-as-tape/modify.png
 

<!---HONumber=62-->
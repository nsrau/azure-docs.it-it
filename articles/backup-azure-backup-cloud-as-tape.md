<properties
	pageTitle="Utilizzare Backup di Azure per sostituire l'infrastruttura nastro"
	description="Scopri come Backup di Azure fornisce semantica simile a nastro che consente di eseguire il backup e ripristino dei dati in Azure"
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

# Utilizzare Backup di Azure per sostituire l'infrastruttura nastro

I clienti di Azure Backup e System Center Data Protection Manager è possibile:

+ Dati di backup nelle pianificazioni che più adatto alle proprie esigenze dell'organizzazione

+ Mantenere i dati di backup per periodi più lunghi

+ Assicurarsi di Azure che necessita di una parte del loro memorizzazione a lungo termine (invece di nastri).

In questo articolo viene illustrato come abilitare i criteri di backup e memorizzazione dei clienti. I clienti che utilizzano i nastri per risolvere i relativi lungo-termine-memorizzazione ora abbiamo bisogno un'alternativa efficace e affidabile con la disponibilità di questa funzionalità. In questa versione di Azure Backup è abilitata la funzionalità (disponibile [qui](http://aka.ms/azurebackup_agent)). I clienti SCDPM sarebbe necessario spostarsi UR5 prima di utilizzare questa funzionalità.

## Che cos'è la pianificazione di Backup?
Pianificazione del backup indica la frequenza (o con quale frequenza) dell'operazione di backup, ad esempio le impostazioni nella schermata riportata di seguito indica che backup ogni giorno alle ore 18.00 e a mezzanotte. <br/>

![Pianificazione giornaliera][1]

I clienti possono inoltre pianificare un backup settimanale, ad esempio, le impostazioni nella schermata riportata di seguito indica che backup ogni domenica alternativo & mercoledì 9:30 am e 01: 00. <br/>

![Pianificazione settimanale][2]

## Che cos'è il criterio di conservazione?
Criteri di conservazione specificano la durata per cui deve essere archiviato il backup. Invece di specificare solo un criterio"flat" per tutti i punti di backup, i clienti possono specificare criteri di conservazione diversi in base a quando viene eseguito il backup. Per il punto di backup eseguito alla fine di ogni trimestre, ad esempio, potrebbe essere necessario deve essere mantenuto per molto tempo per scopi di controllo, mentre il punto di backup eseguito ogni giorno (che viene utilizzata come un ripristino operativo punto) deve essere conservato per 90 giorni. <br/>

![Criteri di conservazione][3]

Il numero totale di "punti di memorizzazione" specificato in questo criterio è 90 (punti giornalieri) + 40 (uno per ogni trimestre per 10 anni) = 130.

## Esempio: realizzazione di entrambi
<br/> ![Schermata di esempio][4]

1. **Criteri di conservazione giornaliera**: vengono archiviati i backup eseguiti ogni giorno per 7 giorni.
2. **Criteri di conservazione settimanale**: i backup eseguiti ogni a mezzanotte e 6 pm sabato verranno mantenuti per 4 settimane
3. **Criteri di conservazione mensile**: i backup eseguiti a mezzanotte e 6 pm dell'ultimo sabato di ogni mese verranno mantenuti per 12months
4. **Criteri di conservazione annuale**: i backup eseguiti a mezzanotte dell'ultimo sabato di ogni mese di marzo verranno mantenuti da 10 anni

Il numero totale di "punti di memorizzazione" (punti da cui un cliente può ripristinare i dati) nel diagramma sopra viene calcolato come segue:

+ 2 punti al giorno per 7 giorni = 14 ripristino punta

+ 2 punti per ogni settimana per 4 settimane = 8 punti di ripristino

+ 2 punti al mese per 12 mesi = 24 punti di ripristino

+ 1 punto all'anno per il ripristino di 10 anni = 10 punti

Il numero totale di punti di ripristino è 56.

## Configurazione avanzata

Facendo clic su **Modifica** nella schermata precedente, i clienti hanno un'ulteriore flessibilità nella definizione di pianificazioni di conservazione. <br/>

![Modificare][5]


<!--Image references-->
[1]: ./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png
[2]: ./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png
[3]: ./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png
[4]: ./media/backup-azure-backup-cloud-as-tape/samplescreen.png
[5]: ./media/backup-azure-backup-cloud-as-tape/modify.png

<!---HONumber=GIT-SubDir-->
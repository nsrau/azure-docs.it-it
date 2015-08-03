<properties 
   pageTitle="Guida alla risoluzione dei problemi di Azure Mobile Engagement - Analytics" 
   description="Risoluzione dei problemi relativi ad analisi, monitoraggio, segmentazione e dashboard in Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="06/18/2015"
   ms.author="piyushjo"/>

# Guida alla risoluzione dei problemi relativi ad analisi, monitoraggio, segmentazione e dashboard

Di seguito sono indicati possibili problemi che relativi al modo in cui Azure Mobile Engagement raccoglie informazioni su applicazioni, dispositivi e utenti.

## Informazioni mancanti o in ritardo

### Problema
- Informazioni visualizzate in ritardo nell'analisi, nella segmentazione o nel dashboard.
- Informazioni mancanti nel monitoraggio.
- Informazioni mancanti nell'analisi, nella segmentazione o nel dashboard.
- Raggiungimento dei limiti di segmentazione.

### Cause

- È possibile usare le API Analytics, Monitor e Segments per verificare se i dati che non vengono visualizzati nell'interfaccia utente sono visibili tramite le API.
- Se Azure Mobile Engagement SDK non è stato integrato correttamente nell'app, non sarà possibile visualizzare le informazioni nell'analisi, nella segmentazione, nel monitoraggio o nei dashboard.
- Una volta creati, i segmenti possono essere soltanto "clonati" (copiati) o "distrutti" (eliminati). I segmenti possono contenere solo 10 criteri.
- Il modo migliore per verificare le informazioni non visualizzate nel monitoraggio consiste nel configurare un dispositivo di test, disinstallare l'app dal dispositivo e/o reinstallarla.
- Le informazioni vengono aggiornate ogni 24 ore per l'analisi, la segmentazione o i dashboard.
- È possibile che le informazioni nei nuovi segmenti vengano visualizzate solo dopo 24 ore dal momento della creazione, anche se il segmento si basa su informazioni precedenti.
- Se i dati di analisi presenti nell'interfaccia utente vengono filtrati, è possibile visualizzare tutti gli esempi di questo tipo, indipendentemente dalla versione dell'app. Ad esempio, se si filtrano gli "arresti anomali" in base al nome, verranno visualizzati quelli delle versioni 1 e 2 dell'app.
- Il periodo per l'analisi si basa sulla data presente nelle impostazioni del dispositivo dell'utente. Pertanto, se nel telefono di un utente la data è impostata in modo errato, è possibile che venga visualizzato il periodo errato.
- Non vengono registrati dati sul lato server quando si usa il pulsante per "testare" i push, vengono registrati solo i dati per le campagne di push reali.

## Impossibile individuare elementi nell'interfaccia utente

### Problema
- Impossibile creare segmenti in base a determinati criteri di tag sulle informazioni dell'app integrati o personalizzati.
- Impossibile trovare criteri di tag sulle informazioni dell'app integrati o personalizzati nell'analisi, nel monitoraggio o nei dashboard.
- Impossibile interpretare i dati presenti nell'analisi, nel monitoraggio, nella segmentazione o nei dashboard.

### Cause

- Alcuni elementi integrati e tag sulle informazioni dell'app sono disponibili soltanto come criteri di inserimento, ma non possono essere aggiunti a un segmento o essere visibili nell'analisi, nel monitoraggio o nel dashboard. 
- Nel caso di elementi integrati e tag sulle informazioni dell'app che non possono essere aggiunti a un segmento, sarà necessario impostare un elenco di criteri di destinazione per ogni campagna, in modo che eseguano la stessa funzione di selezione della destinazione basata su un segmento.
- Visualizzare i menu di scelta rapida nelle sezioni di analisi, monitoraggio e dashboard dell'interfaccia utente di Azure Mobile Engagement per altre informazioni di supporto e procedure.

## Risoluzione dei problemi di arresto anomalo

### Problema
- Gli arresti anomali dell'applicazione vengono visualizzati nelle sezioni di analisi, monitoraggio o dashboard.

### Cause

- Per risolvere i problemi di arresto anomalo dell'applicazione visualizzati nelle sezioni di analisi, monitoraggio o dashboard, controllare le note sulla versione e verificare la presenza di problemi noti nelle versioni precedenti dell'SDK.
- Per risolvere altri problemi di arresto anomalo dell'app, eseguire un evento da un dispositivo di test su cui è installata l'app, quindi cercare l'ID dispositivo nella sezione "Monitor - Eventi" dell'interfaccia utente di Azure Mobile Engagement. Quindi, eseguire l'evento che causa l'arresto anomalo dell'applicazione e cercare altre informazioni nella sezione "Monitor - Arresto anomalo" dell'interfaccia utente di Azure Mobile Engagement. 

 

<!---HONumber=July15_HO4-->
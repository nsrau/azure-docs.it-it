---
title: Pianificazione di attività e flussi di lavoro ricorrenti in App per la logica di AzureScheduling recurring tasks and workflows in Azure Logic Apps
description: Panoramica sulla pianificazione di attività, processi e flussi di lavoro automatici ricorrenti con app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270563"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Pianificare ed eseguire attività, processi e flussi di lavoro automatizzati ricorrenti con le app per la logica di Azure

App per la logica consente di creare ed eseguire attività e processi ricorrenti automatizzati in base a una pianificazione. Creando un flusso di lavoro dell'app per la logica che inizia con un trigger di ricorrenza incorporato o un trigger di finestra scorrevole, che sono trigger di tipo pianificazione, è possibile eseguire le attività immediatamente, in un secondo momento o in un intervallo ricorrente. È possibile chiamare servizi all'interno e all'esterno di Azure, ad esempio endpoint HTTP o HTTPS, inviare messaggi ai servizi di Azure, ad esempio Archiviazione di Azure e bus di servizio di Azure, o ottenere file caricati in una condivisione file. Con il trigger Ricorrenza è inoltre possibile impostare pianificazioni complesse e ricorrenze avanzate per l'esecuzione di attività. Per ulteriori informazioni sui trigger e sulle azioni di pianificazione incorporati, vedere [Pianificare i trigger](#schedule-triggers) e [Pianificare le azioni](#schedule-actions). 

> [!TIP]
> È possibile pianificare ed eseguire carichi di lavoro ricorrenti senza creare un'app per la logica separata per ogni processo pianificato e in esecuzione nel [limite dei flussi di lavoro per area e sottoscrizione.](../logic-apps/logic-apps-limits-and-config.md#definition-limits) È invece possibile usare il modello di app per la logica creato dal modello di Guida introduttiva di [Azure: Utilità di pianificazione processi app per](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/)la logica.
>
> Il modello di utilità di pianificazione dei processi per le app per la logica crea un'app per la logica CreateTimerJob che chiama un'app per la logica TimerJob.The Logic Apps job scheduler template creates a CreateTimerJob logic app that calls a TimerJob logic app. È quindi possibile chiamare l'app per la logica CreateTimerJob come API effettuando una richiesta HTTP e passando una pianificazione come input per la richiesta. Ogni chiamata all'app per la logica CreateTimerJob chiama anche l'app per la logica TimerJob, che crea una nuova istanza TimerJob che viene eseguita continuamente in base alla pianificazione specificata o fino a quando non soddisfa un limite specificato. In questo modo, è possibile eseguire tutte le istanze TimerJob che si desidera senza preoccuparsi dei limiti del flusso di lavoro perché le istanze non sono singole definizioni o risorse del flusso di lavoro dell'app per la logica.

Questo elenco mostra alcune attività di esempio che è possibile eseguire con i trigger predefiniti di pianificazione:This list shows some example tasks that you can run with the Schedule built-in triggers:

* Ottenere dati interni, ad esempio eseguire una stored procedure SQL ogni giorno.

* Ottieni dati esterni, come i bollettini meteo da NOAA ogni 15 minuti.

* Inviare i dati del report, ad esempio inviare tramite posta elettronica un riepilogo per tutti gli ordini superiori a un importo specifico nell'ultima settimana.

* Elaborare i dati, ad esempio comprimere le immagini caricate oggi ogni giorno della settimana durante le ore non di punta.

* Pulire i dati, ad esempio eliminare tutti i tweet più vecchi di tre mesi.

* Archiviare i dati, ad esempio il push delle fatture a un servizio di backup alle ore 1:00 ogni giorno per i successivi nove mesi.

È inoltre possibile utilizzare le azioni predefinite di pianificazione per sospendere il flusso di lavoro prima dell'esecuzione dell'azione successiva, ad esempio:You can also use the Schedule built-in actions to pause your workflow before the next action run, for example:

* Attendere fino a un determinato giorno della settimana per inviare un aggiornamento di stato tramite posta elettronica.

* Ritardare il flusso di lavoro fino a quando una chiamata HTTP viene completata prima di riprendere e recuperare i risultati.

In questo articolo vengono descritte le funzionalità per le azioni e i trigger predefiniti di pianificazione.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Attivare la pianificazione

È possibile avviare il flusso di lavoro dell'app per la logica usando il trigger Ricorrenza o finestra scorrevole, che non è associato ad alcun servizio o sistema specifico, ad esempio Office 365 Outlook o SQL Server.You can start your logic app workflow by using the Recurrence trigger or Sliding Window trigger, which isn't associated with any specific service or system, for example, Office 365 Outlook or SQL Server. Questi trigger avviano ed eseguono il flusso di lavoro in base alla ricorrenza specificata in cui si selezionano l'intervallo e la frequenza, ad esempio il numero di secondi, minuti e ore per entrambi i trigger o il numero di giorni, settimane o mesi per il trigger Ricorrenza. È inoltre possibile impostare la data e l'ora di inizio, nonché il fuso orario. Ogni volta che viene attivato un trigger, App per la logica crea ed esegue una nuova istanza del flusso di lavoro per l'app per la logica.

Di seguito sono riportate le differenze tra questi trigger:Here are the differences between these triggers:

* **Ricorrenza**: Esegue il flusso di lavoro a intervalli di tempo regolari in base alla pianificazione specificata. Se le ricorrenze vengono perse, il trigger Ricorrenza non elabora le ricorrenze perse, ma riavvia le ricorrenze con l'intervallo pianificato successivo. È possibile specificare una data e un'ora di inizio, nonché il fuso orario. Se si seleziona "Giorno", è possibile specificare le ore del giorno e i minuti dell'ora, ad esempio tutti i giorni alle 2:30. Se si seleziona "Settimana", è anche possibile selezionare i giorni della settimana, ad esempio mercoledì e sabato. Per ulteriori informazioni, consultate [Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger Ricorrenza](../connectors/connectors-native-recurrence.md).

* **Finestra scorrevole:** esegue il flusso di lavoro a intervalli di tempo regolari che gestiscono i dati in blocchi continui. Se le ricorrenze vengono perse, il trigger della finestra scorrevole torna indietro ed elabora le ricorrenze perse. È possibile specificare una data e un'ora di inizio, un fuso orario e una durata per ritardare ogni ricorrenza nel flusso di lavoro. Questo trigger non dispone di opzioni per specificare giorni, settimane e mesi, ore del giorno, minuti dell'ora e giorni della settimana. Per ulteriori informazioni, consultate [Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger Finestra scorrevole](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Pianificare le azioni

Dopo qualsiasi azione nel flusso di lavoro dell'app per la logica, è possibile usare le azioni Ritardo e Ritardo fino a attendere il flusso di lavoro prima dell'esecuzione dell'azione successiva.

* **Ritardo:** attendere l'esecuzione dell'azione successiva per il numero specificato di unità di tempo, ad esempio secondi, minuti, ore, giorni, settimane o mesi. Per ulteriori informazioni, consultate [Ritardare l'azione successiva nei flussi di lavoro.](../connectors/connectors-native-delay.md)

* **Ritardo fino**a : Attendere per eseguire l'azione successiva fino alla data e all'ora specificate. Per ulteriori informazioni, consultate [Ritardare l'azione successiva nei flussi di lavoro.](../connectors/connectors-native-delay.md)

## <a name="patterns-for-start-date-and-time"></a>Modelli per la data e l'ora di inizio

<a name="start-time"></a>

Ecco alcuni modelli che mostrano come è possibile controllare la ricorrenza con la data e l'ora di inizio e come il servizio App per la logica esegue queste ricorrenze:Here are some patterns that show how you can control recurrence with the start date and time, and how the Logic Apps service runs these recurrences:

| Ora di inizio | Ricorrenza senza pianificazione | Ricorrenza con pianificazione (solo trigger di ricorrenza) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Esegue immediatamente il primo carico di lavoro. <p>Esegue i carichi di lavoro futuri in base all'ultima esecuzione. | Esegue immediatamente il primo carico di lavoro. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. |
| Ora di inizio nel passato | **Trigger ricorrenza:** calcola i tempi di esecuzione in base all'ora di inizio specificata ed elimina i tempi di esecuzione precedenti. Esegue il primo carico di lavoro all'esecuzione successiva. <p>Esegue i carichi di lavoro futuri in base ai calcoli dell'ultima esecuzione. <p><p>**Trigger finestra scorrevole:** calcola i tempi di esecuzione in base all'ora di inizio specificata e rispetta i tempi di esecuzione passati. <p>Esegue i carichi di lavoro futuri in base ai calcoli dall'ora di inizio specificata. <p><p>Per altri chiarimenti, vedere l'esempio dopo questa tabella. | Esegue il primo carico di lavoro *non prima* dell'ora di inizio, in base alla pianificazione calcolata dall'ora di inizio. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. <p>**Nota:** se si specifica una ricorrenza con una pianificazione, ma non specificano ore o minuti per la pianificazione, le esecuzioni future verranno calcolate rispettivamente in ore o minuti dalla prima esecuzione. |
| Ora di inizio attuale o nel futuro | Esegue il primo carico di lavoro all'ora di inizio specificata. <p>Esegue i carichi di lavoro futuri in base ai calcoli dell'ultima esecuzione. | Esegue il primo carico di lavoro *non prima* dell'ora di inizio, in base alla pianificazione calcolata dall'ora di inizio. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. <p>**Nota:** se si specifica una ricorrenza con una pianificazione, ma non specificano ore o minuti per la pianificazione, le esecuzioni future verranno calcolate rispettivamente in ore o minuti dalla prima esecuzione. |
||||

> [!IMPORTANT]
> Quando le ricorrenze non specificano opzioni di pianificazione avanzate, le ricorrenze future si basano sull'ultima esecuzione.
> I tempi di inizio per queste ricorrenze potrebbero derivare a causa di fattori quali la latenza durante le chiamate di archiviazione. Per assicurarti che l'app per la logica non perda una ricorrenza, specialmente quando la frequenza è in giorni o più, usa una di queste opzioni:
> 
> * Specificare un'ora di inizio per la ricorrenza.
> 
> * Specificare le ore e i minuti per l'esecuzione della ricorrenza utilizzando le proprietà **In queste ore** e **A questi minuti.**
> 
> * Utilizzare il [trigger Finestra scorrevole](../connectors/connectors-native-sliding-window.md), anziché il trigger Ricorrenza .

*Esempio per l'ora di inizio e la ricorrenza passata ma nessuna pianificazione*

Si supponga che la data e l'ora correnti siano l'8 settembre 2017 alle 13:00. Specificare la data e l'ora di inizio come 7 settembre 2017 alle 14:00, che è nel passato, e una ricorrenza che viene eseguita ogni due giorni.

| Ora di inizio | Ora corrente | Ricorrenza | Pianificazione |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-**07** alle 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09-**08** alle 13:00) | Ogni due giorni | {none} |
|||||

Per il trigger Ricorrenza, il motore App per la logica calcola i tempi di esecuzione in base all'ora di inizio, elimina i tempi di esecuzione precedenti, usa l'ora di inizio successiva per la prima esecuzione e calcola le esecuzioni future in base all'ora dell'ultima esecuzione.

Di seguito è illustrato l'aspetto di questa ricorrenza:

| Ora di inizio | Prima esecuzione | Esecuzioni future |
|------------|----------------|------------------|
| 2017-09-**07** alle 14:00 | 2017-09-**09** alle 14:00 | 2017-09-**11** alle 14:00 </br>2017-09-**13** alle 14:00 </br>2017-09-**15** alle 14:00 </br>e così via. |
||||

Quindi, indipendentemente dalla distanza nel passato in cui si specifica l'ora di inizio, ad esempio 2017-09-**05** alle 14:00 o 2017-09-**01** alle 14:00, la prima esecuzione utilizza sempre l'ora di inizio successiva.

Per il trigger della finestra scorrevole, il motore App per la logica calcola i tempi di esecuzione in base all'ora di inizio, rispetta i tempi di esecuzione precedenti, utilizza l'ora di inizio per la prima esecuzione e calcola le esecuzioni future in base all'ora di inizio.

Di seguito è illustrato l'aspetto di questa ricorrenza:

| Ora di inizio | Prima esecuzione | Esecuzioni future |
|------------|----------------|------------------|
| 2017-09-**07** alle 14:00 | 2017-09-**07** alle 14:00 | 2017-09-**09** alle 14:00 </br>2017-09-**11** alle 14:00 </br>2017-09-**13** alle 14:00 </br>2017-09-**15** alle 14:00 </br>e così via. |
||||

Quindi, indipendentemente dalla distanza nel passato in cui si specifica l'ora di inizio, ad esempio 2017-09-**05** alle 14:00 o 2017-09-**01** alle 14:00, la prima esecuzione utilizza sempre l'ora di inizio specificata.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Ricorrenze di esempio

Di seguito sono riportate varie ricorrenze di esempio che è possibile impostare per i trigger che supportano le opzioni:

| Trigger | Ricorrenza | Interval | Frequenza | Ora di inizio | In questi giorni | A queste ore | A questi minuti | Note |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni 15 minuti (senza data e ora di inizio) | 15 | Minuto | {none} | {non disponibile} | {none} | {none} | Questa pianificazione inizia immediatamente, quindi calcola le ricorrenze future in base all'ultima esecuzione. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni 15 minuti (con data e ora di inizio) | 15 | Minuto | *DataInizio*T*OraInizio*Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, quindi calcola le ricorrenze future in base all'ultima esecuzione. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni ora all'inizio dell'ora (con data e ora di inizio) | 1 | Ora | *DataInizio*Thh:00:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate. Le ricorrenze future vengono eseguite ogni ora al minuto "00", calcolato a partire dall'ora di inizio. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni ora ogni giorno (senza data e ora di inizio) | 1 | Ora | {none} | {non disponibile} | {none} | {none} | Questa pianificazione inizia immediatamente, quindi calcola le ricorrenze future in base all'ultima esecuzione. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni ora ogni giorno (con data e ora di inizio) | 1 | Ora | *DataInizio*T*OraInizio*Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, quindi calcola le ricorrenze future in base all'ultima esecuzione. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni 15 minuti dopo l'ora, ogni ora (con data e ora di inizio) | 1 | Ora | *DataInizio*T00:15:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate. Le ricorrenze future vengono eseguite in corrispondenza del contrassegno dei minuti "15", calcolato dall'ora di inizio, quindi alle 00:15, alle 1:15, alle 2:15 e così via. |
| Ricorrenza | Eseguire ogni 15 minuti dopo l'ora, ogni ora (senza data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Questa pianificazione viene eseguita alle 00:15, 1:15, 2:15 e così via. Questa pianificazione è equivalente a una frequenza di "Ora" e un'ora di inizio con "15" minuti. |
| Ricorrenza | Esegui ogni 15 minuti con i segni dei minuti specificati (nessuna data e ora di inizio). | 1 | Giorno | {none} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | La pianificazione non inizia fino al raggiungimento dell'indicatore di 15 minuti successivo. |
| Ricorrenza | Esegui ogni giorno alle 8 del *mattino più* il minuto-mark da quando si salva l'app per la logica | 1 | Giorno | {none} | {non disponibile} | 8 | {none} | Senza una data e un'ora di inizio, questa pianificazione viene eseguita in base all'ora in cui si salva l'app per la logica (operazione PUT). |
| Ricorrenza | Esegui ogni giorno alle 08:00 (con data e ora di inizio) | 1 | Giorno | *DataInizio*T08:00:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate. Le occorrenze future vengono eseguite ogni giorno alle 08:00. | 
| Ricorrenza | Esegui tutti i giorni alle 08:30 (nessuna data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 8 | 30 | Questo programma viene eseguito ogni giorno alle 08:30. |
| Ricorrenza | Esegui tutti i giorni alle 8:30 e alle 16:30 | 1 | Giorno | {none} | {non disponibile} | 8, 16 | 30 | |
| Ricorrenza | Esegui tutti i giorni alle 8:30, alle 8:45, alle 16:30 e alle 16:45 | 1 | Giorno | {none} | {non disponibile} | 8, 16 | 30, 45 | |
| Ricorrenza | Eseguire ogni sabato alle 17:00 (senza data e ora di inizio) | 1 | Week | {none} | "Sabato" | 17 | 00 | Questa pianificazione viene eseguita ogni sabato alle 17:00. |
| Ricorrenza | Eseguire ogni sabato alle 17:00 (con data e ora di inizio) | 1 | Week | *DataInizio*T17:00:00Z | "Sabato" | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, in questo caso il 9 settembre 2017 alle 17:00. Le ricorrenze future verranno eseguite ogni sabato alle 17:00. |
| Ricorrenza | Esegui ogni martedì, giovedì alle 17:00 *più* il minuto-mark da quando si salva l'app per la logica| 1 | Week | {none} | "Martedì", "Giovedì" | 17 | {none} | |
| Ricorrenza | Eseguire ogni ora durante le ore lavorative | 1 | Week | {none} | Selezionare tutti i giorni, ad eccezione di sabato e domenica. | Selezionare le ore del giorno desiderate. | Selezionare i minuti dell'ora desiderati. | Ad esempio, se le ore lavorative sono comprese tra le 8:00 e le 17:00, selezionare "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" come ore del giorno. <p>Se le ore lavorative sono comprese tra le 8:30 e le 17:30, selezionare le ore precedenti del giorno e aggiungere "30" come minuti dell'ora. |
| Ricorrenza | Eseguire una volta al giorno nei fine settimana | 1 | Week | {none} | "Sabato", "Domenica" | Selezionare le ore del giorno desiderate. | Selezionare i minuti dell'ora desiderati. | Questa pianificazione verrà eseguita ogni sabato e domenica alla pianificazione specificata. |
| Ricorrenza | Eseguire ogni 15 minuti ogni 2 settimane solo il lunedì | 2 | Week | {none} | "Lunedì" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Questa pianificazione verrà eseguita un lunedì a settimane alterne, in corrispondenza dell'indicatore dei 15 minuti. |
| Ricorrenza | Esegui ogni mese | 1 | Month | *DataInizio*T*OraInizio*Z | {non disponibile} | {non disponibile} | {non disponibile} | Questa pianificazione non viene *avviata prima* della data e dell'ora di inizio specificate e calcola le ricorrenze future alla data e all'ora di inizio. Se non si specificano una data e un'ora di inizio, questa pianificazione usa la data e l'ora di creazione. |
| Ricorrenza | Eseguire ogni ora per un giorno al mese | 1 | Month | {vedere la nota} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {vedere la nota} | Se non si specificano una data e un'ora di inizio, questa pianificazione usa la data e l'ora di creazione. Per controllare i minuti della pianificazione di ricorrenza, specificare i minuti dell'ora, un'ora di inizio oppure usare l'ora di creazione. Ad esempio, se l'ora di inizio o creazione è 8:25, questa pianificazione verrà eseguita alle 8:25, 9:25, 10:25 e così via. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Esegui una sola volta

Se si vuole eseguire l'app per la logica solo in futuro, è possibile usare il modello **Utilità di pianificazione: Esegui una volta processi.** Dopo aver creato una nuova app per la logica, ma prima di aprire Progettazione app per la logica, nell'elenco **Modelli** della sezione **Modelli** selezionare **Pianificazione**e quindi selezionare il modello seguente:

![Selezionare il modello "Scheduler: Run Once jobs"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

In alternativa, se è possibile avviare l'app per la logica con il trigger **Quando viene ricevuta una richiesta HTTP** - Richiesta e passare l'ora di inizio come parametro per il trigger. Per la prima azione, usare l'azione **Ritarda fino a - Pianifica** e specificare l'ora in cui viene avviata l'esecuzione dell'azione successiva.

## <a name="next-steps"></a>Passaggi successivi

* [Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger Ricorrenza](../connectors/connectors-native-recurrence.md)
* [Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger Finestra scorrevole](../connectors/connectors-native-sliding-window.md)
* [Sospendere i flussi di lavoro con azioni di ritardo](../connectors/connectors-native-delay.md)

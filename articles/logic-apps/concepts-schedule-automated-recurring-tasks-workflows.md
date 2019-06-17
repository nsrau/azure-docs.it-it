---
title: Pianificazione di attività ricorrenti e flussi di lavoro nelle App per la logica di Azure
description: Una panoramica sulla pianificazione di attività automatizzate ricorrente, processi e flussi di lavoro con App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 7f15dc5b28a44dc8405e2f0524913e6012ebe380
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66356040"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Pianificare ed eseguire le attività automatizzate ricorrente, processi e flussi di lavoro con le App per la logica di Azure

App per la logica consente di creare ed eseguire le attività ricorrenti automatizzate e i processi in base a una pianificazione. Tramite la creazione di un flusso di lavoro app per la logica che inizia con un trigger predefinito ricorrenza o finestra temporale scorrevole, ovvero i trigger di tipo di pianificazione, è possibile eseguire le attività immediatamente, in un secondo momento o a intervalli ricorrenti. È possibile chiamare i servizi all'interno e all'esterno di Azure, ad esempio gli endpoint HTTP o HTTPS, inviare messaggi a servizi di Azure come archiviazione di Azure e Bus di servizio di Azure oppure ottenere i file caricati in una condivisione file. Con il trigger di ricorrenza, è anche possibile configurare le pianificazioni complesse e ricorrenze per l'esecuzione di attività avanzate. Per altre informazioni sulle azioni e trigger di pianificazione predefinite, vedere [pianificazione e ricorrente automatizzato di esecuzione, attività e flussi di lavoro con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Di seguito sono riportati alcuni esempi che illustrano i tipi di attività che è possibile eseguire:

* Recupero dei dati interni, ad esempio eseguire una stored procedure SQL ogni giorno.

* Carica dati esterni, ad esempio pull meteorologici dalla NOAA ogni 15 minuti.

* Inviare i dati del report, ad esempio posta elettronica un riepilogo di tutti gli ordini superiori a una quantità specifica nella settimana precedente.

* Elaborare dati, ad esempio compress oggi ha caricato ogni giorno feriale durante gli orari di immagini.

* Pulire i dati, ad esempio elimina tutti i TWEET più vecchi di tre mesi.

* Archiviazione dei dati, ad esempio le fatture di push per un servizio di backup in 1:00 AM ogni giorno per i nove mesi successivi.

È anche possibile usare le azioni predefinite di pianificazione per sospendere il flusso di lavoro prima di eseguita l'azione successiva, ad esempio:

* Attendere fino a un determinato giorno della settimana per inviare un aggiornamento di stato tramite posta elettronica.

* Ritardare il flusso di lavoro fino a quando una chiamata HTTP viene completata prima di riprendere e recuperare i risultati.

Questo articolo descrive le funzionalità per i trigger di pianificazione predefinite e le azioni.

## <a name="schedule-triggers"></a>Trigger di pianificazione

È possibile avviare il flusso di lavoro di app per la logica usando il trigger di ricorrenza o un trigger di finestra temporale scorrevole, che non sono associati a qualsiasi servizio specifico o un sistema, ad esempio, Office 365 Outlook o SQL Server. Questi trigger avviare ed eseguire il flusso di lavoro di base della ricorrenza specificata in cui si seleziona l'intervallo e frequenza, ad esempio il numero di secondi, minuti e ore per entrambi i trigger o il numero di giorni, settimane o mesi per il trigger di ricorrenza. È anche possibile impostare la data di inizio e ora, nonché il fuso orario. Ogni volta che viene attivato un trigger, le App per la logica crea ed esegue una nuova istanza del flusso di lavoro per app per la logica.

Di seguito sono le differenze tra questi trigger:

* **Ricorrenza**: Esegue il flusso di lavoro a intervalli regolari in base alla pianificazione specificata. Se le ricorrenze risultano mancanti, il trigger di ricorrenza non elabora le occorrenze mancanti ma riavvia ricorrenze con il successivo intervallo pianificato. È possibile specificare una data di inizio e ora, nonché il fuso orario. Se si seleziona "Giorno", è possibile specificare ore del giorno e i minuti dell'ora, ad esempio, ogni giorno alle 2:30. Se si seleziona "Settimana", è anche possibile selezionare i giorni della settimana, ad esempio mercoledì e domenica. Per altre informazioni, vedere [Create, pianificazione ed esecuzione di attività ricorrenti e flussi di lavoro con il trigger di ricorrenza](../connectors/connectors-native-recurrence.md).

* **Finestra temporale scorrevole**: Esegue il flusso di lavoro a intervalli regolari che gestiscono i dati in blocchi continua. Se le ricorrenze risultano mancanti, il trigger di finestra temporale scorrevole viene reimpostato ed elabora le occorrenze mancanti. È possibile specificare una data di inizio e l'ora, fuso orario e una durata per ritardare ogni ricorrenza del flusso di lavoro. Questo trigger non ha opzioni da specificare i giorni, settimane e mesi, ora del giorno, i minuti dell'ora e giorni della settimana. Per altre informazioni, vedere [Create, pianificazione e attività ricorrenti di esecuzione e i flussi di lavoro con il trigger di finestra temporale scorrevole](../connectors/connectors-native-sliding-window.md).

## <a name="schedule-actions"></a>Azioni di pianificazione

Dopo qualsiasi azione del flusso di lavoro di app per la logica, è possibile usare le azioni di ritardo e ritardo fino a quando per rendere il flusso di lavoro di attesa prima di eseguire l'azione successiva.

* **ritardo**: Attendere per eseguire l'azione successiva per il numero specificato di unità di tempo, ad esempio secondi, minuti, ore, giorni, settimane o mesi. Per altre informazioni, vedere [ritardare l'azione successiva nei flussi di lavoro](../connectors/connectors-native-delay.md).

* **Ritarda fino a**: Attendere per eseguire l'azione successiva finché non la data e ora specificate. Per altre informazioni, vedere [ritardare l'azione successiva nei flussi di lavoro](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>I modelli di data e ora di inizio

<a name="start-time"></a>

Ecco alcuni modelli che indicano come controllare la ricorrenza con la data di inizio e l'ora e come i servizi di App per la logica esegue queste ricorrenze:

| Ora di inizio | Ricorrenza senza pianificazione | Ricorrenza con pianificazione (solo per i trigger di ricorrenza) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Esegue immediatamente il primo carico di lavoro. <p>Esegue i carichi di lavoro futuri in base all'ultima esecuzione. | Esegue immediatamente il primo carico di lavoro. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. |
| Ora di inizio nel passato | **Ricorrenza** trigger: Calcola le esecuzioni in base all'ora di inizio specificata e rimuove le esecuzioni precedenti. Esegue il primo carico di lavoro all'esecuzione successiva. <p>Esegue i carichi di lavoro futuri in base ai calcoli dell'ultima esecuzione. <p><p>**Finestra temporale scorrevole** trigger: Calcola le esecuzioni basate sull'ora di inizio specificata e rispetta precedenti eseguire volte. <p>Esegue i carichi di lavoro futuri in base a calcoli dall'ora di inizio specificata. <p><p>Per altri chiarimenti, vedere l'esempio dopo questa tabella. | Esegue il primo carico di lavoro *non prima* dell'ora di inizio, in base alla pianificazione calcolata dall'ora di inizio. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. <p>**Nota:** se si specifica una ricorrenza con una pianificazione, ma non si specificano ore o minuti per la pianificazione, le esecuzioni future verranno calcolate rispettivamente in ore o minuti dalla prima esecuzione. |
| Ora di inizio attuale o nel futuro | Esegue il primo carico di lavoro all'ora di inizio specificata. <p>Esegue i carichi di lavoro futuri in base ai calcoli dell'ultima esecuzione. | Esegue il primo carico di lavoro *non prima* dell'ora di inizio, in base alla pianificazione calcolata dall'ora di inizio. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. <p>**Nota:** se si specifica una ricorrenza con una pianificazione, ma non si specificano ore o minuti per la pianificazione, le esecuzioni future verranno calcolate rispettivamente in ore o minuti dalla prima esecuzione. |
||||

*Esempio per ultimi ora di inizio e ricorrenza, ma nessuna pianificazione*

Si supponga che la data corrente e l'ora sia 8 settembre 2017 a 1:00 PM. Specificate la data di inizio e l'ora come il 7 settembre 2017 alle 14:00, ovvero in passato e una ricorrenza che viene eseguito ogni 2 giorni.

| Ora di inizio | Ora corrente | Ricorrenza | Pianificazione |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09 -**07** in 2:00 PM) | 2017-09-**08**T13:00:00Z <br>(2017-09 -**08** in 1:00 PM) | Ogni 2 giorni | {none} |
|||||

Per il trigger di ricorrenza, le App per la logica calcola motore esecuzioni in base all'ora di inizio, rimuove precedenti esecuzioni, utilizzi successiva ora di inizio per la prima esecuzione e calcola futuro eseguita in base all'ultima esecuzione.

Di seguito è illustrato l'aspetto di questa ricorrenza:

| Ora di inizio | Prima esecuzione | Esecuzioni future |
|------------|----------------|------------------|
| 2017-09-**07** alle 14:00 | 2017-09-**09** alle 14:00 | 2017-09-**11** alle 14:00 </br>2017-09-**13** alle 14:00 </br>2017-09-**15** alle 14:00 </br>e così via. |
||||

In questo caso, non importa quanto in passato si specifica l'ora di inizio, ad esempio, 2017-09 -**05** alle 14:00 o 2017-09 -**01** alle 14:00, la prima esecuzione sempre Usa ora di inizio successiva.

Per il trigger di finestra temporale scorrevole, l'App per la logica calcola motore esecuzioni in base all'ora di inizio, rispetta ultime esecuzioni, Usa l'ora di inizio per la prima esecuzione e calcola le esecuzioni future in base all'ora di inizio.

Di seguito è illustrato l'aspetto di questa ricorrenza:

| Ora di inizio | Prima esecuzione | Esecuzioni future |
|------------|----------------|------------------|
| 2017-09-**07** alle 14:00 | 2017-09-**07** alle 14:00 | 2017-09-**09** alle 14:00 </br>2017-09-**11** alle 14:00 </br>2017-09-**13** alle 14:00 </br>2017-09-**15** alle 14:00 </br>e così via. |
||||

In questo caso, non importa quanto in passato si specifica l'ora di inizio, ad esempio, 2017-09 -**05** alle 14:00 o 2017-09 -**01** alle 14:00, la prima fase sempre Usa specificato ora di inizio.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Ricorrenze di esempio

Di seguito sono varie le ricorrenze di esempio che è possibile configurare per i trigger che supportano le opzioni:

| Trigger | Ricorrenza | Interval | Frequenza | Ora di inizio | In questi giorni | A queste ore | A questi minuti | Note |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Ricorrenza, <br>Finestra temporale scorrevole | Eseguire ogni 15 minuti (senza data e ora di inizio) | 15 | Minuto | {none} | {non disponibile} | {none} | {none} | Questa pianificazione inizia immediatamente, quindi calcola le ricorrenze future in base all'ultima esecuzione. |
| Ricorrenza, <br>Finestra temporale scorrevole | Eseguire ogni 15 minuti (con data e ora di inizio) | 15 | Minuto | *DataInizio*T*OraInizio*Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, quindi calcola le ricorrenze future in base all'ultima esecuzione. |
| Ricorrenza, <br>Finestra temporale scorrevole | Eseguire ogni ora all'inizio dell'ora (con data e ora di inizio) | 1 | Ora | *DataInizio*Thh:00:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate. Ricorrenze future vengono eseguite ogni ora in corrispondenza del contrassegno relativo ai minuti "00", che viene calcolata dall'ora di inizio. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. |
| Ricorrenza, <br>Finestra temporale scorrevole | Eseguire ogni ora ogni giorno (senza data e ora di inizio) | 1 | Ora | {none} | {non disponibile} | {none} | {none} | Questa pianificazione inizia immediatamente, quindi calcola le ricorrenze future in base all'ultima esecuzione. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. |
| Ricorrenza, <br>Finestra temporale scorrevole | Eseguire ogni ora ogni giorno (con data e ora di inizio) | 1 | Ora | *DataInizio*T*OraInizio*Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, quindi calcola le ricorrenze future in base all'ultima esecuzione. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. |
| Ricorrenza, <br>Finestra temporale scorrevole | Eseguire ogni 15 minuti dopo l'ora, ogni ora (con data e ora di inizio) | 1 | Ora | *DataInizio*T00:15:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate. Le ricorrenze future in esecuzione in corrispondenza del contrassegno relativo ai minuti "15", che viene calcolato dall'inizio di tempo, pertanto in 00:15, 1 5:15 di Mattina, 2 5:15 di Mattina e così via. |
| Ricorrenza | Eseguire ogni 15 minuti dopo l'ora, ogni ora (senza data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Questa pianificazione viene eseguita alle 00:15, 1:15, 2:15 e così via. Questa pianificazione è equivalente a una frequenza di "Ora" e un'ora di inizio con "15" minuti. |
| Ricorrenza | I punti di minuti specificato (Nessuna data di inizio e ora) viene eseguito ogni 15 minuti. | 1 | Giorno | {none} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | La pianificazione non inizia fino al raggiungimento dell'indicatore di 15 minuti successivo. |
| Ricorrenza | Eseguire alle 8:00 ogni giorno (senza data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 8 | {none} | Questa pianificazione viene eseguita alle 8:00 di ogni giorno, in base alla pianificazione specificata. |
| Ricorrenza | Eseguire alle 8:00 ogni giorno (con data e ora di inizio) | 1 | Giorno | *DataInizio*T08:00:00Z | {non disponibile} | {none} | {none} | Questa pianificazione viene eseguita alle 8:00 di ogni giorno, in base all'ora di inizio specificata. | 
| Ricorrenza | Eseguire alle 8:30 ogni giorno (senza data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 8 | 30 | Questa pianificazione viene eseguita alle 8:30 di ogni giorno, in base alla pianificazione specificata. |
| Ricorrenza | Eseguire alle 8:30 ogni giorno (con data e ora di inizio) | 1 | Giorno | *DataInizio*T08:30:00Z | {non disponibile} | {none} | {none} | Questa pianificazione inizia alla data di inizio specificata alle 8:30. |
| Ricorrenza | Eseguire alle 8:30 e alle 16:30 ogni giorno | 1 | Giorno | {none} | {non disponibile} | 8, 16 | 30 | |
| Ricorrenza | Eseguire alle 8:30, alle 8:45 e alle 16:30 e alle 16:45 ogni giorno | 1 | Giorno | {none} | {non disponibile} | 8, 16 | 30, 45 | |
| Ricorrenza | Eseguire ogni sabato alle 17:00 (senza data e ora di inizio) | 1 | Settimana | {none} | "Sabato" | 17 | 00 | Questa pianificazione viene eseguita ogni sabato alle 17:00. |
| Ricorrenza | Eseguire ogni sabato alle 17:00 (con data e ora di inizio) | 1 | Settimana | *DataInizio*T17:00:00Z | "Sabato" | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, in questo caso il 9 settembre 2017 alle 17:00. Le ricorrenze future verranno eseguite ogni sabato alle 17:00. |
| Ricorrenza | Eseguire ogni martedì e giovedì alle 17:00 | 1 | Settimana | {none} | "Martedì", "Giovedì" | 17 | {none} | Questa pianificazione verrà eseguita ogni martedì e giovedì alle 17:00. |
| Ricorrenza | Eseguire ogni ora durante le ore lavorative | 1 | Settimana | {none} | Selezionare tutti i giorni, ad eccezione di sabato e domenica. | Selezionare le ore del giorno desiderate. | Selezionare i minuti dell'ora desiderati. | Ad esempio, se le ore lavorative sono comprese tra le 8:00 e le 17:00, selezionare "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" come ore del giorno. <p>Se le ore lavorative sono comprese tra le 8:30 e le 17:30, selezionare le ore precedenti del giorno e aggiungere "30" come minuti dell'ora. |
| Ricorrenza | Eseguire una volta al giorno nei fine settimana | 1 | Settimana | {none} | "Sabato", "Domenica" | Selezionare le ore del giorno desiderate. | Selezionare i minuti dell'ora desiderati. | Questa pianificazione verrà eseguita ogni sabato e domenica alla pianificazione specificata. |
| Ricorrenza | Eseguire ogni 15 minuti ogni 2 settimane solo il lunedì | 2 | Settimana | {none} | "Lunedì" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Questa pianificazione verrà eseguita un lunedì a settimane alterne, in corrispondenza dell'indicatore dei 15 minuti. |
| Ricorrenza | Eseguire ogni mese | 1 | Mese | *DataInizio*T*OraInizio*Z | {non disponibile} | {non disponibile} | {non disponibile} | Questa pianificazione non inizia *prima* specificato data e ora di inizio e calcola le ricorrenze future alla data di inizio e ora. Se non si specificano una data e un'ora di inizio, questa pianificazione usa la data e l'ora di creazione. |
| Ricorrenza | Eseguire ogni ora per un giorno al mese | 1 | Mese | {vedere la nota} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {vedere la nota} | Se non si specificano una data e un'ora di inizio, questa pianificazione usa la data e l'ora di creazione. Per controllare i minuti della pianificazione di ricorrenza, specificare i minuti dell'ora, un'ora di inizio oppure usare l'ora di creazione. Ad esempio, se l'ora di inizio o creazione è 8:25, questa pianificazione verrà eseguita alle 8:25, 9:25, 10:25 e così via. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Eseguire una sola volta

Se si desidera eseguire l'app per la logica solo in una sola volta in futuro, è possibile usare il **dell'utilità di pianificazione: Run once jobs** (Utilità di pianificazione: esegui i processi una sola volta). Dopo aver creato una nuova app per la logica, ma prima di aprire la finestra di progettazione di App per la logica, sotto il **modelli** sezione dalle **categoria** elenco, selezionare **pianificazione**e quindi selezionare Questo modello:

![Selezionare il modello "Scheduler: Run once jobs (Utilità di pianificazione: esegui i processi una sola volta)](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Oppure, se è possibile avviare l'app per la logica con il **viene ricevuta quando una richiesta HTTP - richiesta** attivano e passare l'ora di avvio come parametro per il trigger. Per la prima azione, usare il **Ritarda fino a - pianificare** azione e fornire il tempo per quando l'azione successiva viene avviata l'esecuzione.

## <a name="next-steps"></a>Passaggi successivi

* [Creare, pianificare ed eseguire le attività ricorrenti e flussi di lavoro con il trigger di ricorrenza](../connectors/connectors-native-recurrence.md)
* [Creare, pianificare ed eseguire le attività ricorrenti e flussi di lavoro con il trigger di finestra temporale scorrevole](../connectors/connectors-native-sliding-window.md)
* [Flussi di lavoro di pausa con azioni di ritardo](../connectors/connectors-native-delay.md)
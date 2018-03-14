---
title: "Pianificare attività e flussi di lavoro regolarmente in esecuzione - App per la logica di Azure - Microsoft Docs"
description: "Creare e pianificare attività, azioni, flussi di lavoro, processi e carichi di lavoro regolarmente in esecuzione con le app per la logica"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0dead955f9eb723dfa232d3ce751498a09ce1b29
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="create-and-schedule-regularly-running-tasks-with-azure-logic-apps"></a>Creare e pianificare attività eseguite regolarmente con App per la logica di Azure

Per pianificare attività, azioni, carichi di lavoro o processi regolarmente in esecuzione, è possibile creare un flusso di lavoro delle app per la logica che ha inizio con il [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) **Pianificazione - Ricorrenza**. Con questo trigger è possibile impostare una data e un'ora per l'inizio della ricorrenza e una pianificazione di ricorrenza per l'esecuzione di attività, come quelle riportate in questi esempi e altre ancora:

* Recupero dei dati interni: [eseguire una stored procedure SQL](../connectors/connectors-create-api-sqlazure.md) ogni giorno.
* Recupero dei dati esterni: eseguire il pull dei bollettini meteo da NOAA ogni 15 minuti.
* Segnalazione dei dati: inviare tramite e-mail un riepilogo di tutti gli ordini maggiori di una quantità specifica nella settimana precedente.
* Elaborazione dei dati: comprimere quotidianamente le immagini caricate del giorno corrente in orari di scarso traffico.
* Pulizia dei dati: eliminare tutti i tweet precedenti a tre mesi.
* Archiviazione dei dati: eseguire il push delle fatture a un servizio di backup ogni mese.

Questo trigger supporta numerosi criteri, tra cui:

* Esecuzione immediata e ripetizione ogni *n* secondi, minuti, ore, giorni, settimane o mesi.
* Inizio a un orario specifico ed esecuzione e ripetizione ogni *n* secondi, minuti, ore, giorni, settimane o mesi.
* Esecuzione e ripetizione a uno o più orari ogni giorno, ad esempio alle 8:00 e alle 17:00.
* Esecuzione e ripetizione ogni settimana, ma solo in giorni specifici, ad esempio sabato e domenica.
* Esecuzione e ripetizione ogni settimana, ma solo in ore e giorni specifici, ad esempio dal lunedì al venerdì alle 8:00 e alle 17:00.

Ogni volta che viene attivato il trigger di ricorrenza, App per la logica crea ed esegue una nuova istanza del flusso di lavoro delle app per la logica.

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, è possibile [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Aggiungere un trigger di ricorrenza all'app per la logica

1. Accedere al [portale di Azure](https://portal.azure.com). Creare un'app per la logica vuota o acquisire informazioni su [come creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Quando Progettazione app per la logica viene visualizzato, nella casella di ricerca immettere "ricorrenza" come filtro. Selezionare il trigger **Pianificazione - Ricorrenza**. 

   ![Trigger Pianificazione - Ricorrenza](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Questo trigger rappresenta ora il primo passaggio nell'app per la logica.

3. Impostare l'intervallo e la frequenza per la ricorrenza. In questo esempio impostare queste proprietà per eseguire il flusso di lavoro ogni settimana. 

   ![Impostare intervallo e frequenza](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Per altre opzioni di pianificazione, scegliere **Mostra opzioni avanzate**. 

   ![Altre opzioni](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. È ora possibile impostare queste opzioni: 

   * Impostare una data e un'ora di inizio per l'attivazione del trigger. 
   Se si specificano una data e un'ora di inizio, è anche possibile applicare un fuso orario. 

   * Se si seleziona "Giorno" o "Settimana" per la frequenza, è possibile selezionare orari specifici per la ricorrenza. 

   * Se si seleziona "Settimana", è possibile selezionare anche giorni specifici della settimana.
   
   ![Opzioni di pianificazione avanzata](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Ad esempio, si supponga che sia lunedì 4 settembre 2017. 
   Il trigger di ricorrenza seguente non viene attivato *prima* della data e dell'ora di inizio, ovvero lunedì 18 settembre 2017 alle 8:00 PST. 
   Tuttavia, la pianificazione di ricorrenza è impostata solo per i lunedì alle 10:30, 12:30 e 14:30. Il trigger viene pertanto attivato e crea un'istanza del flusso di lavoro dell'app per la logica per la prima volta alle 10:30. 
   Per altre informazioni sul funzionamento degli orari di inizio, vedere questi [esempi](#start-time).
   Le esecuzioni future avranno luogo alle 12:30 e 14:30 nella stessa giornata. 
   Ogni ricorrenza crea la propria istanza del flusso di lavoro. L'intera pianificazione verrà quindi ripetuta da capo il lunedì successivo. 
   [*Quali sono altre occorrenze di esempio?*](#example-recurrences)

   ![Esempio di pianificazione avanzata](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Il trigger mostra un'anteprima della ricorrenza specificata solo quando si seleziona "Giorno" o "Settimana" come frequenza.
   
6. Creare ora il flusso di lavoro rimanente con azioni o istruzioni del controllo di flusso. Per altre azioni che è possibile aggiungere, vedere [Connettori](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Dettagli del trigger

È possibile configurare queste proprietà per il trigger di ricorrenza.

| NOME | Obbligatoria | Nome proprietà | type | DESCRIZIONE | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Frequenza** | Sì | frequency | string | L'unità di tempo per la ricorrenza: **Secondo**, **Minuto**, **Ora**, **Giorno**, **Settimana** o **Mese** | 
| **Interval** | Sì | interval | Integer | Numero intero positivo che indica l'intervallo con cui viene eseguito il flusso di lavoro in base alla frequenza. <p>L'intervallo predefinito è 1. Di seguito sono riportati gli intervalli minimi e massimi: <p>- Mese: 1-16 mesi </br>- Giorno: 1-500 giorni </br>- Ora: 1-12.000 ore </br>- Minuto: 1-72.000 minuti </br>- Secondo: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Mese", la ricorrenza è ogni 6 mesi. | 
| **Fuso orario** | No  | timeZone | string | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Selezionare il fuso orario che si desidera applicare. | 
| **Ora di inizio** | No  | startTime | string | Specificare un'ora di inizio nel formato seguente: <p>AAAA-MM-GGThh:mm:ss se si seleziona un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si seleziona un fuso orario <p>Ad esempio, per il 18 settembre 2017 alle 14:00, specificare "2017-09-18T14:00:00" e selezionare un fuso orario, ad esempio Pacifico. In alternativa, specificare "2017-09-18T14:00:00Z" senza un fuso orario. <p>**Nota:** l'ora di inizio deve seguire la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza una [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Se non si seleziona un fuso orario, è necessario aggiungere la lettera "Z" alla fine, senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. <p>Per le pianificazioni semplici l'ora di inizio è la prima occorrenza, mentre per le pianificazioni complesse il trigger non si attiva prima dell'ora di inizio. [*In quali modi posso usare la data e l'ora di inizio?*](#start-time) | 
| **In questi giorni** | No  | weekDays | Stringa o matrice di stringhe | Se si seleziona "Settimana", è possibile selezionare uno o più giorni in cui eseguire il flusso di lavoro: **Lunedì**, **Martedì**, **Mercoledì**, **Giovedì**, **Venerdì**, **Sabato** e **Domenica** | 
| **A queste ore** | No  | hours | Intero o matrice di intero | Se si seleziona "Giorno" o "Settimana", è possibile selezionare uno o più numeri interi da 0 a 23 come ore del giorno in cui si desidera eseguire il flusso di lavoro. <p>Ad esempio, se si specificano "10", "12" e "14", 10:00, 12:00 e 14:00 rappresentano gli indicatori di ora. | 
| **A questi minuti** | No  | minutes | Intero o matrice di intero | Se si seleziona "Giorno" o "Settimana", è possibile selezionare uno o più numeri interi da 0 a 59 come minuti dell'ora in cui si desidera eseguire il flusso di lavoro. <p>Ad esempio, è possibile specificare "30" come indicatore dei minuti e, usando l'esempio precedente per le ore del giorno, si otterranno le ore 10.30, 12.30 e 14.30. | 
||||| 

## <a name="json-example"></a>Esempio di JSON

Di seguito è riportato un esempio di [definizione di trigger di ricorrenza](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

``` json
{
    "triggers": {
        "Recurrence": {
            "type": "Recurrence",
            "recurrence": {
                "frequency": "Week",
                "interval": 1,
                "schedule": {
                    "hours": [
                        10,
                        12,
                        14
                    ],
                    "minutes": [
                        30
                    ],
                    "weekDays": [
                        "Monday"
                    ]
                },
               "startTime": "2017-09-07T14:00:00",
               "timeZone": "Pacific Standard Time"
            }
        }
    }
}
```

## <a name="faq"></a>Domande frequenti

<a name="example-recurrences"></a>

**D:** Quali sono altri esempi di pianificazioni di ricorrenza? </br>
**R:** Di seguito sono riportati altri esempi:

| Ricorrenza | Interval | Frequenza | Ora di inizio | In questi giorni | A queste ore | A questi minuti | Note |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Eseguire ogni 15 minuti (senza data e ora di inizio) | 15 | Minuto | {none} | {non disponibile} | {none} | {none} | Questa pianificazione inizia immediatamente, quindi calcola le ricorrenze future in base all'ultima esecuzione. | 
| Eseguire ogni 15 minuti (con data e ora di inizio) | 15 | Minuto | *DataInizio*T*OraInizio*Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, quindi calcola le ricorrenze future in base all'ultima esecuzione. | 
| Eseguire ogni ora all'inizio dell'ora (con data e ora di inizio) | 1 | Hour | *DataInizio*Thh:00:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate. Le ricorrenze future vengono eseguite ogni ora in corrispondenza dell'indicatore dei minuti "00". <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. | 
| Eseguire ogni ora ogni giorno (senza data e ora di inizio) | 1 | Hour | {none} | {non disponibile} | {none} | {none} | Questa pianificazione inizia immediatamente, quindi calcola le ricorrenze future in base all'ultima esecuzione. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. | 
| Eseguire ogni ora ogni giorno (con data e ora di inizio) | 1 | Hour | *DataInizio*T*OraInizio*Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, quindi calcola le ricorrenze future in base all'ultima esecuzione. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. | 
| Eseguire ogni 15 minuti dopo l'ora, ogni ora (con data e ora di inizio) | 1 | Hour | *DataInizio*T00:15:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate e viene eseguita alle ore 00:15, 1:15, 2:15 e così via. | 
| Eseguire ogni 15 minuti dopo l'ora, ogni ora (senza data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Questa pianificazione viene eseguita alle 00:15, 1:15, 2:15 e così via. Questa pianificazione è equivalente a una frequenza di "Ora" e un'ora di inizio con "15" minuti. | 
| Eseguire ogni 15 minuti in corrispondenza dell'indicatore dei 15 minuti (senza data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | La pianificazione non inizia fino al raggiungimento dell'indicatore di 15 minuti successivo. | 
| Eseguire alle 8:00 ogni giorno (senza data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 8 | {none} | Questa pianificazione viene eseguita alle 8:00 di ogni giorno, in base alla pianificazione specificata. | 
| Eseguire alle 8:00 ogni giorno (con data e ora di inizio) | 1 | Giorno | *DataInizio*T08:00:00Z | {non disponibile} | {none} | {none} | Questa pianificazione viene eseguita alle 8:00 di ogni giorno, in base all'ora di inizio specificata. | 
| Eseguire alle 8:30 ogni giorno (senza data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 8 | 30 | Questa pianificazione viene eseguita alle 8:30 di ogni giorno, in base alla pianificazione specificata. | 
| Eseguire alle 8:30 ogni giorno (con data e ora di inizio) | 1 | Giorno | *DataInizio*T08:30:00Z | {non disponibile} | {none} | {none} | Questa pianificazione inizia alla data di inizio specificata alle 8:30. | 
| Eseguire alle 8:30 e alle 16:30 ogni giorno | 1 | Giorno | {none} | {non disponibile} | 8, 16 | 30 | | 
| Eseguire alle 8:30, alle 8:45 e alle 16:30 e alle 16:45 ogni giorno | 1 | Giorno | {none} | {non disponibile} | 8, 16 | 30, 45 | | 
| Eseguire ogni sabato alle 17:00 (senza data e ora di inizio) | 1 | Settimana | {none} | "Sabato" | 17 | 00 | Questa pianificazione viene eseguita ogni sabato alle 17:00. | 
| Eseguire ogni sabato alle 17:00 (con data e ora di inizio) | 1 | Settimana | *DataInizio*T17:00:00Z | "Sabato" | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, in questo caso il 9 settembre 2017 alle 17:00. Le ricorrenze future verranno eseguite ogni sabato alle 17:00. | 
| Eseguire ogni martedì e giovedì alle 17:00 | 1 | Settimana | {none} | "Martedì", "Giovedì" | 17 | {none} | Questa pianificazione verrà eseguita ogni martedì e giovedì alle 17:00. | 
| Eseguire ogni ora durante le ore lavorative | 1 | Settimana | {none} | Selezionare tutti i giorni, ad eccezione di sabato e domenica. | Selezionare le ore del giorno desiderate. | Selezionare i minuti dell'ora desiderati. | Ad esempio, se le ore lavorative sono comprese tra le 8:00 e le 17:00, selezionare "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" come ore del giorno. <p>Se le ore lavorative sono comprese tra le 8:30 e le 17:30, selezionare le ore precedenti del giorno e aggiungere "30" come minuti dell'ora. | 
| Eseguire una volta al giorno nei fine settimana | 1 | Settimana | {none} | "Sabato", "Domenica" | Selezionare le ore del giorno desiderate. | Selezionare i minuti dell'ora desiderati. | Questa pianificazione verrà eseguita ogni sabato e domenica alla pianificazione specificata. | 
| Eseguire ogni 15 minuti ogni 2 settimane solo il lunedì | 2 | Settimana | {none} | "Lunedì" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Questa pianificazione verrà eseguita un lunedì a settimane alterne, in corrispondenza dell'indicatore dei 15 minuti. | 
| Eseguire ogni ora per un giorno al mese | 1 | Mese | {vedere la nota} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {vedere la nota} | Se non si specificano una data e un'ora di inizio, questa pianificazione usa la data e l'ora di creazione. Per controllare i minuti della pianificazione di ricorrenza, specificare i minuti dell'ora, un'ora di inizio oppure usare l'ora di creazione. Ad esempio, se l'ora di inizio o creazione è 8:25, questa pianificazione verrà eseguita alle 8:25, 9:25, 10:25 e così via. | 
||||||||| 

<a name="start-time"></a>

**D:** In quali modi posso usare la data e l'ora di inizio? </br>
**R:** Di seguito sono riportati alcuni formati che indicano come controllare la ricorrenza con la data e l'ora di inizio e in che modo il motore App per la logica esegue queste ricorrenze:

| Ora di inizio | Ricorrenza senza pianificazione | Ricorrenza con pianificazione | 
| ---------- | --------------------------- | ------------------------ | 
| {none} | Esegue immediatamente il primo carico di lavoro. <p>Esegue i carichi di lavoro futuri in base all'ultima esecuzione. | Esegue immediatamente il primo carico di lavoro. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. | 
| Ora di inizio nel passato | Calcola le esecuzioni in base all'ora di inizio specificata e rimuove le esecuzioni precedenti. Esegue il primo carico di lavoro all'esecuzione successiva. <p>Esegue i carichi di lavoro futuri in base ai calcoli dell'ultima esecuzione. <p>Per altri chiarimenti, vedere l'esempio dopo questa tabella. | Esegue il primo carico di lavoro *non prima* dell'ora di inizio, in base alla pianificazione calcolata dall'ora di inizio. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. <p>**Nota:** se si specifica una ricorrenza con una pianificazione, ma non specificano ore o minuti per la pianificazione, le esecuzioni future verranno calcolate rispettivamente in ore o minuti dalla prima esecuzione. | 
| Ora di inizio attuale o nel futuro | Esegue il primo carico di lavoro all'ora di inizio specificata. <p>Esegue i carichi di lavoro futuri in base ai calcoli dell'ultima esecuzione. | Esegue il primo carico di lavoro *non prima* dell'ora di inizio, in base alla pianificazione calcolata dall'ora di inizio. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. <p>**Nota:** se si specifica una ricorrenza con una pianificazione, ma non specificano ore o minuti per la pianificazione, le esecuzioni future verranno calcolate rispettivamente in ore o minuti dalla prima esecuzione. | 
||||

**Esempio di un'ora di inizio precedente con ricorrenza, ma senza pianificazione** 

| Ora di inizio | Ora corrente | Ricorrenza | Pianificazione |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | Ogni 2 giorni | {none} | 
||||| 

In questo scenario, il motore App per la logica calcola le esecuzioni in base all'ora di inizio, rimuove le esecuzioni precedenti e usa l'ora di inizio successiva per la prima esecuzione. Dopo la prima esecuzione, le esecuzioni future si baseranno sulla pianificazione calcolata dall'ora di inizio. Di seguito è illustrato l'aspetto di questa ricorrenza:

| Ora di inizio | Prima esecuzione | Esecuzioni future | 
| ---------- | ------------ | ---------- | 
| 2017-09-**07** alle 14:00 | 2017-09-**09** alle 14:00 | 2017-09-**11** alle 14:00 </br>2017-09-**13** alle 14:00 </br>2017-09-**15** alle 14:00 </br>e così via.
||||| 

Pertanto, per questo scenario, indipendentemente dall'ora di inizio specificata nel passato, ad esempio 2017-09-**05** alle 14:00 o 2017-09-**01** alle 14:00, la prima esecuzione è la stessa.

## <a name="next-steps"></a>Passaggi successivi

* [Azioni e trigger del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Connettori](../connectors/apis-list.md)

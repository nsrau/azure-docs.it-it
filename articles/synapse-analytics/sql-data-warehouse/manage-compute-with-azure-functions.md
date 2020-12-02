---
title: 'Esercitazione: gestire le risorse di calcolo con funzioni di Azure'
description: Come usare funzioni di Azure per gestire il calcolo del pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f0731f0deaf46ec419cfe43037804e10f2b73fd4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448388"
---
# <a name="use-azure-functions-to-manage-compute-resources-for-your-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Usare funzioni di Azure per gestire le risorse di calcolo per il pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics

Questa esercitazione usa funzioni di Azure per gestire le risorse di calcolo per un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics.

Per usare un app per le funzioni di Azure con un pool SQL dedicato (in precedenza SQL DW), è necessario creare un [account dell'entità servizio](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). L'account dell'entità servizio necessita dell'accesso come collaboratore nella stessa sottoscrizione dell'istanza del pool SQL dedicato (in precedenza SQL DW).

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Distribuire il ridimensionamento basato su timer con un modello di Azure Resource Manager

Per distribuire il modello sono necessarie le informazioni seguenti:

- Nome del gruppo di risorse in cui si trova l'istanza del pool SQL dedicato (in precedenza SQL DW)
- Nome del server in cui si trova l'istanza del pool SQL dedicato (in precedenza SQL DW)
- Nome dell'istanza del pool SQL dedicato (in precedenza SQL DW)
- ID tenant (ID directory) di Azure Active Directory
- ID sottoscrizione
- ID applicazione dell'entità servizio
- Chiave privata dell'entità servizio

Dopo aver recuperato le informazioni riportate sopra, distribuire questo modello:

[![Immagine che mostra il pulsante "Distribuisci in Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

Al termine della distribuzione del modello, dovrebbero essere presenti tre nuove risorse: un piano di servizio app di Azure gratuito, un piano di app per le funzioni a consumo e un account di archiviazione che gestisce la registrazione e la coda delle operazioni. Per informazioni su come modificare le funzioni distribuite in base alle esigenze, vedere le altre sezioni.

## <a name="change-the-compute-level"></a>Modificare il livello di calcolo

1. Passare al servizio app per le funzioni. Se il modello è stato distribuito con i valori predefiniti, il servizio sarà denominato *DWOperations*. Dopo aver aperto l'app per le funzioni, si noterà che nel servizio app per le funzioni sono distribuite cinque funzioni.

   ![Funzioni distribuite con il modello](./media/manage-compute-with-azure-functions/five-functions.png)

2. Selezionare *DWScaleDownTrigger* o *DWScaleUpTrigger* per aumentare o ridurre la dimensione. Nel menu a discesa selezionare Integrazione.

   ![Selezionare Integrazione per la funzione](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Il valore attualmente visualizzato dovrebbe essere *%ScaleDownTime%* o *%ScaleUpTime%*. Questi valori indicano che la pianificazione è basata sui valori definiti nelle [impostazioni dell'applicazione](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Per il momento è possibile ignorare questo valore e modificare la pianificazione impostando la data e l'ora preferite in base ai passaggi successivi.

4. Nell'area pianificazione aggiungere l'espressione CRON da considerare con quale frequenza si vuole aumentare la scalabilità di Azure sinapsi Analytics.

   ![Modificare la pianificazione della funzione](./media/manage-compute-with-azure-functions/change-schedule.png)

   Il valore di `schedule` è un'[espressione CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) che include i 6 campi seguenti:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Ad esempio, *"0 30 9 * * 1-5"* corrisponde a un trigger alle 9.30 di ogni giorno feriale. Per altre informazioni, vedere gli [esempi di pianificazione](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) di Funzioni di Azure.

## <a name="change-the-time-of-the-scale-operation"></a>Modificare la data e l'ora dell'operazione di ridimensionamento

1. Passare al servizio app per le funzioni. Se il modello è stato distribuito con i valori predefiniti, il servizio sarà denominato *DWOperations*. Dopo aver aperto l'app per le funzioni, si noterà che nel servizio app per le funzioni sono distribuite cinque funzioni.

2. Selezionare *DWScaleDownTrigger* o *DWScaleUpTrigger* per aumentare o ridurre il valore di calcolo. Quando si selezionano le funzioni, nel riquadro dovrebbe essere visualizzato il file *index.js*.

   ![Modificare il livello di calcolo del trigger della funzione](././media/manage-compute-with-azure-functions/index-js.png)

3. Modificare il valore di *obiettivo* con il livello desiderato e selezionare Save (Salva). *Obiettivo* è il livello di calcolo a cui verrà ridimensionata l'istanza di data warehouse in base alla pianificazione definita nella sezione integrazione.

## <a name="use-pause-or-resume-instead-of-scale"></a>Usare la sospensione o la ripresa invece del ridimensionamento

Attualmente, le funzioni attivate per impostazione predefinita sono *DWScaleDownTrigger* e *DWScaleUpTrigger*. Se si preferisce invece sospendere e riprendere le funzionalità, è possibile abilitare *DWPauseTrigger* o *DWResumeTrigger*.

1. Passare al riquadro Funzioni.

   ![Riquadro Funzioni](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Selezionare l'interruttore scorrevole per i trigger corrispondenti che si vuole abilitare.

3. Passare alle schede *Integrazione* per i rispettivi trigger per modificarne la pianificazione.

   > [!NOTE]
   > La differenza funzionale fra i trigger di ridimensionamento e i trigger di sospensione o ripresa è data dal messaggio inviato alla coda. Per altre informazioni, vedere [Aggiungere una nuova funzione trigger](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Aggiungere una nuova funzione trigger

Attualmente, nel modello sono incluse solo due funzioni di ridimensionamento. Con queste funzioni, nel corso di un giorno è possibile eseguire solo una riduzione e un aumento delle prestazioni. Per un controllo più granulare, ad esempio per ridurre le prestazioni più volte al giorno o impostare un comportamento di ridimensionamento diverso nei fine settimana, è necessario aggiungere un altro trigger.

1. Creare una nuova funzione vuota. Selezionare il *+* pulsante accanto al percorso funzioni per visualizzare il riquadro modello di funzione.

   ![Screenshot che mostra il menu "app per le funzioni" con l'icona "più" accanto a "Functions" selezionato.](./media/manage-compute-with-azure-functions/create-new-function.png)

2. In lingua selezionare *JavaScript*, quindi selezionare *TimerTrigger*.

   ![Creare una nuova funzione](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Assegnare un nome alla funzione e impostare la pianificazione. L'immagine mostra come è possibile attivare la funzione ogni sabato a mezzanotte (ossia nella tarda serata di venerdì).

   ![Ridurre le prestazioni di sabato](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copiare il contenuto di *index.js* da una delle altre funzioni trigger.

   ![Copiare index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Impostare la variabile operation sul comportamento desiderato come illustrato di seguito:

   ```JavaScript
   // Resume the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the dedicated SQL pool (formerly SQL DW)l instance to DW600c
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600c"
   }
   ```

## <a name="complex-scheduling"></a>Pianificazione complessa

Questa sezione illustra brevemente gli elementi necessari per ottenere una pianificazione più complessa delle funzionalità di sospensione, ripresa e ridimensionamento.

### <a name="example-1"></a>Esempio 1

Scalabilità giornaliera fino a 8 a DW600c e riduzione alle 20.00 a DW200c.

| Funzione  | Pianifica     | Operazione                                |
| :-------- | :----------- | :--------------------------------------- |
| Funzione 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600c"}` |
| Funzione 2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-2"></a>Esempio 2

Scalabilità giornaliera fino a 8 a compreso dw1000c, riduzione delle prestazioni una volta al DW600 alle 16.00 e riduzione delle ore 10:00 a DW200c.

| Funzione  | Pianifica     | Operazione                                |
| :-------- | :----------- | :--------------------------------------- |
| Funzione 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Funzione 2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Funzione 3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-3"></a>Esempio 3

Scalabilità verticale a compreso dw1000c, con scalabilità verticale una volta DW600c alle 16.00 nei giorni feriali. nonché sospendere alle ore 23 di venerdì e riprendere alle ore 7 di lunedì.

| Funzione  | Pianifica       | Operazione                                |
| :-------- | :------------- | :--------------------------------------- |
| Funzione 1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Funzione 2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Funzione 3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Funzione 4 | 0 0 7 * * 1    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle funzioni di Azure per il [trigger timer](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

Vedere il [repository di esempi](https://github.com/Microsoft/sql-data-warehouse-samples)di pool SQL dedicato (in precedenza SQL DW).

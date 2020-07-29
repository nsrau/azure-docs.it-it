---
title: Pianificare e gestire i costi per Azure Data Factory
description: Questo articolo illustra come pianificare e gestire i costi per Azure Data Factory
documentationcenter: ''
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: ca76563475dfbf8d35595c1de3cdee37f80e3ce1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690237"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Pianificare e gestire i costi per Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory è un servizio di integrazione dei dati serverless ed elastico creato per la scalabilità al cloud.  Questo significa che, invece di pianificare risorse di calcolo di dimensioni fisse per i picchi di carico, è sufficiente specificare quante risorse allocare su richiesta per operazione, cosa che consente di progettare i processi ETL in un modo molto più scalabile. Inoltre, Azure Data Factory viene fatturato in base a un piano a consumo, con il quale si paga solo per le risorse usate.

Questo articolo illustra come pianificare e gestire i costi per Azure Data Factory.

*   Per prima cosa, all'inizio del progetto ETL, occorre eseguire un modello di verifica e usare in combinazione i dati di consumo per pipeline e il calcolatore dei prezzi per stimare i costi.
*   Dopo aver distribuito le pipeline in ambiente di produzione, si usano le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e identificare le tendenze di spesa.
*   Inoltre, è possibile visualizzare le informazioni sul consumo per pipeline e per attività per individuare le pipeline e le attività più costose e identificare i candidati per la riduzione dei costi.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>Stimare i costi usando le informazioni sul consumo per pipeline e per attività e il calcolatore prezzi

È possibile usare il [calcolatore prezzi di Azure Data Factory](https://azure.microsoft.com/pricing/calculator/?service=data-factory) per ottenere una stima dei costi di esecuzione del carico di lavoro ETL in Azure Data Factory.  Per usare il calcolatore è necessario immettere dettagli come il numero di esecuzioni attività, il numero di ore di unità di integrazione dei dati, il tipo di calcolo usato per il flusso di dati, il numero di core, il numero di istanze, la durata dell'esecuzione e così via.

Una delle domande più frequenti sul calcolatore prezzi riguarda i valori da usare come input.  Durante la fase di verifica, è possibile avviare esecuzioni di prova usando set di dati di esempio per comprendere il consumo per diversi contatori di Azure Data Factory.  Quindi, in base al consumo del set di dati di esempio, è possibile proiettare il consumo per il set di dati completo e la pianificazione dell'operazionalizzazione.

> [!NOTE]
> I prezzi usati negli esempi seguenti sono ipotetici e non suggeriscono in alcun modo i prezzi effettivi.

Ad esempio, si immagini di dover spostare ogni giorno 1 TB di dati da AWS S3 ad Azure Data Lake Gen2.  È possibile eseguire un modello di verifica del trasferimento di 100 GB di dati per misurare la velocità effettiva di inserimento dati e comprendere il consumo di fatturazione corrispondente.

Di seguito sono riportati i dettagli dell'esecuzione di un'attività di copia di esempio (il chilometraggio effettivo varia a seconda della forma del set di dati specifico, della velocità di rete, dei limiti in uscita per l'account S3, dei limiti di ingresso per ADLS Gen2 e di altri fattori).

![Esecuzione della copia S3](media/plan-manage-costs/s3-copy-run-details.png)

Sfruttando il [monitoraggio del consumo al livello di esecuzione della pipeline](#monitor-consumption-at-pipeline-run-level), è possibile visualizzare le quantità di consumo corrispondenti del contatore di spostamento dati:

![Consumo della pipeline della copia S3](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Pertanto, il numero totale di ore DIU necessarie per spostare 1 TB al giorno per l'intero mese è:

1,2667 (ore DIU) * (1 TB/100 GB) * 30 (giorni in un mese) = 380 ore DIU

Ora è possibile inserire 30 esecuzioni attività e 380 ore DIU nel calcolatore prezzi di Azure Data Factory per ottenere una stima della fattura mensile:

![Calcolatore prezzi della copia S3](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Usare budget e avvisi relativi ai costi

È possibile creare [budget](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets) per gestire i costi e creare avvisi per informare automaticamente le persone interessate in caso di anomalie di spesa e rischi di costi eccessivi.  Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi.  È possibile creare un budget a livello di sottoscrizione o a un livello di granularità inferiore aggiungendo altri filtri, come l'ID risorsa e il nome del contatore.  Non è però possibile creare budget per singole pipeline all'interno di una factory.

## <a name="monitor-costs-at-factory-level"></a>Monitorare i costi a livello di factory

Quando si inizia a usare Azure Data Factory, è possibile visualizzare i costi sostenuti nel riquadro [Analisi dei costi](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis) nel portale di Azure.

1. Per visualizzare il riquadro [Analisi dei costi](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis), aprire la finestra **Gestione dei costi e fatturazione**, scegliere **Gestione costi** dal menu e quindi selezionare **Apri l'analisi dei costi**.
2. La visualizzazione predefinita mostra i costi accumulati per il mese corrente.  È possibile passare a un intervallo di tempo diverso e a un livello di granularità diverso, ad esempio giornaliero o mensile.
3. Per restringere i costi a un solo servizio come Azure Data Factory, selezionare **Aggiungi filtro** e quindi **Nome servizio**.  Selezionare quindi **Azure Data Factory v2** dall'elenco.
4. È possibile aggiungere altri filtri per analizzare i costi per un'istanza di factory specifica e uno specifico livello di granularità del contatore di Azure Data Factory.

   ![Analisi dei costi](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Monitorare il consumo a livello di esecuzione della pipeline

A seconda dei tipi di attività incluse nella pipeline, della quantità di dati da spostare e trasformare e della complessità della trasformazione, l'esecuzione di una pipeline comporterà la selezione di contatori di fatturazione diversi in Azure Data Factory.

È possibile visualizzare la quantità di consumo per contatori diversi in relazione a singole esecuzioni di pipeline nell'esperienza utente di Azure Data Factory. Per aprire l'esperienza di monitoraggio, selezionare il riquadro **Monitoraggio e gestione** nel pannello Data factory del [portale di Azure](https://portal.azure.com/). Se si è già all'interno dell'esperienza utente di Azure Data Factory, fare clic sull'icona di **monitoraggio** sulla barra laterale sinistra. La visualizzazione di monitoraggio predefinita è l'elenco delle esecuzioni di pipeline.

Facendo clic sul pulsante **Consumo** accanto al nome della pipeline verrà visualizzata una finestra popup che mostra il consumo dell'esecuzione della pipeline aggregata in tutte le attività all'interno della pipeline.

![Consumo dell'esecuzione della pipeline](media/plan-manage-costs/pipeline-run-consumption.png)

![Dettagli del consumo della pipeline](media/plan-manage-costs/pipeline-consumption-details.png)

La visualizzazione del consumo dell'esecuzione della pipeline mostra la quantità utilizzata per ogni contatore di Azure Data Factory per l'esecuzione della pipeline specifica, ma non mostra il prezzo effettivo addebitato, perché l'importo addebitato dipende dal tipo di account di Azure e dal tipo di valuta usati.  Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

## <a name="monitor-consumption-at-activity-run-level"></a>Monitorare l'utilizzo a livello di esecuzione dell'attività
Una volta compreso il consumo aggregato a livello di esecuzione della pipeline, esistono alcune situazioni in cui è necessario eseguire ulteriormente il drill-down per identificare l'attività più costosa all'interno della pipeline.

Per visualizzare il consumo al livello di esecuzione dell'attività, selezionare il riquadro **Crea e monitora** nel pannello Data factory. Nella scheda **Monitoraggio** in cui viene visualizzato un elenco di esecuzioni di pipeline fare clic sul collegamento **nome pipeline** per accedere all'elenco delle esecuzioni di attività nell'esecuzione della pipeline.  Fare clic sul pulsante **Output** accanto al nome dell'attività e cercare la proprietà **billableDuration** nell'output JSON:

Ecco un esempio di esecuzione di un'attività di copia:

![Output della copia](media/plan-manage-costs/copy-output.png)

Ed ecco un esempio di esecuzione di un'attività di flusso di dati per mapping:

![Output del flusso di dati](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui prezzi in Azure Data Factory, vedere gli articoli seguenti:

- [Pagina dei prezzi di Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Determinazione dei prezzi di Azure Data Factory ed esempi](https://docs.microsoft.com/azure/data-factory/pricing-concepts)
- [Calcolatore prezzi di Azure Data Factory](https://azure.microsoft.com/pricing/calculator/?service=data-factory)

---
title: Configurare Personalizza esperienze
description: La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219357"
---
# <a name="configure-personalizer-learning-loop"></a>Configurare il ciclo di apprendimento del personalizer

La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.

Configurare il ciclo di apprendimento nella pagina Configurazione nel portale di Azure per la risorsa Personalizer.Configure the learning loop on the **Configuration** page, in the Azure portal for that Personalizer resource.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>Configurare i premi per il ciclo di feedback

Configura il servizio per l'uso delle ricompense da parte del tuo ciclo di apprendimento. Le modifiche ai seguenti valori resetteranno il modello Personalizer corrente e lo riaddestrano con gli ultimi 2 giorni di dati.

> [!div class="mx-imgBorder"]
> ![Configurare i valori di ricompensa per il ciclo di feedback](media/settings/configure-model-reward-settings.png)

|valore|Scopo|
|--|--|
|Reward wait time (Tempo di attesa ricompense)|Imposta l'intervallo di tempo durante il quale Personalizza esperienze raccoglierà i valori delle ricompense per una chiamata a Classifica, a partire dal momento in cui si verifica tale chiamata. Questo valore viene impostato chiedendo: "Per quanto tempo il Personalizer deve attendere le chiamate di ricompensa?" Qualsiasi ricompensa arrivi dopo questo intervallo verrà registrata ma non verrà usata per l'apprendimento.|
|Default reward (Ricompensa predefinita)|Se Personalizza esperienze non riceve chiamate per le ricompense durante l'intervallo di Reward Wait Time associato a una chiamata a Classifica, verrà assegnata la ricompensa predefinita. Per impostazione predefinita e nella maggior parte degli scenari, la ricompensa predefinita è zero (0).|
|Reward aggregation (Aggregazione ricompense)|Se si ricevono più ricompense per la stessa chiamata all'API Classifica, viene usato questo metodo di aggregazione: **somma** o **più vecchio**. Con quest'ultimo metodo, viene considerato il punteggio più vecchio ricevuto e viene ignorato il resto. Ciò è utile se si desidera una ricompensa unica tra le chiamate eventualmente duplicate. |

Dopo aver modificato questi valori, assicurarsi di selezionare **Salva**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Configurare l'esplorazione per consentire al ciclo di apprendimento di adattarsi

La personalizzazione è in grado di individuare nuovi modelli e adattarsi alle modifiche del comportamento degli utenti nel tempo esplorando alternative anziché usare la stima del modello sottoposto a training. Il valore **Esplorazione** determina la percentuale di chiamate Rank a cui viene data risposta con l'esplorazione.

Le modifiche apportate a questo valore reimposteranno il modello Personalizer corrente e lo riaddestrano con gli ultimi 2 giorni di dati.

![Il valore di esplorazione determina quale percentuale di classi Rank viene risolta con l'esplorazione](media/settings/configure-exploration-setting.png)

Dopo aver modificato questo valore, assicurarsi di selezionare **Salva**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Configurare la frequenza di aggiornamento del modello per il training del modelloConfigure model update frequency for model training

La frequenza di aggiornamento del modello imposta la **frequenza** di training del modello.

|Impostazione della frequenza|Scopo|
|--|--|
|1 minuto|Le frequenze di aggiornamento di un minuto sono utili quando si **esegue il debug** del codice di un'applicazione usando Personalizer, eseguendo demo o testando in modo interattivo gli aspetti di apprendimento automatico.|
|15 minuti|Le frequenze di aggiornamento del modello elevato sono utili per le situazioni in cui si desidera **tenere traccia da vicino delle modifiche** nei comportamenti degli utenti. ad esempio nei siti basati su notizie live, contenuto virale oppure offerte su prodotti in tempo reale. In questi scenari si può usare una frequenza di 15 minuti. |
|1 ora|Nella maggior parte dei casi d'uso, una frequenza di aggiornamento inferiore è efficace.|

![Model update frequency imposta la frequenza con cui viene ripetuto il training di un nuovo modello di Personalizza esperienze.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Dopo aver modificato questo valore, assicurarsi di selezionare **Salva**.

## <a name="data-retention"></a>Conservazione dei dati

**Data retention period** (Periodo di conservazione dati) imposta il numero di giorni in cui Personalizza esperienze mantiene i log dei dati. I log dei dati passati sono necessari per eseguire le [valutazioni offline](concepts-offline-evaluation.md), usate per misurare l'efficacia di Personalizza esperienze e per ottimizzare i criteri di apprendimento.

Dopo aver modificato questo valore, assicurarsi di selezionare **Salva**.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Impostazioni che includono la reimpostazione del modello

Le azioni seguenti includono un'immediata riqualificazione del modello con gli ultimi 2 giorni di dati.

* Benefici
* Esplorazione

Per [cancellare](how-to-manage-model.md) tutti i dati, utilizzare la pagina " Modello e impostazioni di apprendimento ".

## <a name="next-steps"></a>Passaggi successivi

[Scopri come gestire il tuo modello](how-to-manage-model.md)

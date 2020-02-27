---
title: Configurare Personalizza esperienze
description: La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623715"
---
# <a name="configure-personalizer-learning-loop"></a>Configurare il ciclo di apprendimento della personalizzazione

La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.

Configurare il ciclo di apprendimento nella pagina di **configurazione** nel portale di Azure per la risorsa di personalizzazione.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>Configurare i premi per il ciclo di feedback

Configurare il servizio per l'uso dei premi del ciclo di apprendimento. Le modifiche apportate ai valori seguenti reimpostano il modello di personalizzazione corrente e ne rieseguiranno il training negli ultimi 2 giorni di dati.

> [!div class="mx-imgBorder"]
> ![configurare i valori Reward per il ciclo di feedback](media/settings/configure-model-reward-settings.png)

|Valore|Scopo|
|--|--|
|Reward wait time (Tempo di attesa ricompense)|Imposta l'intervallo di tempo durante il quale Personalizza esperienze raccoglierà i valori delle ricompense per una chiamata a Classifica, a partire dal momento in cui si verifica tale chiamata. Questo valore viene impostato chiedendo: "per quanto tempo è necessario che il Personalizzatore attenda le chiamate dei premi?" Qualsiasi ricompensa arrivi dopo questo intervallo verrà registrata ma non verrà usata per l'apprendimento.|
|Default reward (Ricompensa predefinita)|Se Personalizza esperienze non riceve chiamate per le ricompense durante l'intervallo di Reward Wait Time associato a una chiamata a Classifica, verrà assegnata la ricompensa predefinita. Per impostazione predefinita, e nella maggior parte degli scenari, la ricompensa predefinita è zero (0).|
|Reward aggregation (Aggregazione ricompense)|Se si ricevono più ricompense per la stessa chiamata all'API Classifica, viene usato questo metodo di aggregazione: **somma** o **più vecchio**. Con quest'ultimo metodo, viene considerato il punteggio più vecchio ricevuto e viene ignorato il resto. Questa opzione è utile se si vuole ottenere una ricompensa univoca tra le chiamate duplicate possibili. |

Dopo aver modificato questi valori, assicurarsi di selezionare **Salva**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Configurare l'esplorazione per consentire l'adattamento del ciclo di apprendimento

La personalizzazione è in grado di individuare nuovi modelli e adattarsi alle modifiche del comportamento dell'utente nel tempo esplorando le alternative anziché utilizzare la stima del modello sottoposto a training. Il valore di **esplorazione** determina la percentuale di chiamate di rango a cui viene fornita una risposta con l'esplorazione.

Le modifiche apportate a questo valore reimpostano il modello di personalizzazione corrente e ne rieseguiranno il training negli ultimi 2 giorni di dati.

![Il valore di esplorazione determina la percentuale di chiamate di rango a cui viene fornita una risposta con l'esplorazione](media/settings/configure-exploration-setting.png)

Dopo avere modificato questo valore, assicurarsi di selezionare **Salva**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Configurare la frequenza di aggiornamento del modello per il training del modello

La **frequenza di aggiornamento del modello** determina la frequenza con cui viene eseguito il training del modello.

|Impostazione frequenza|Scopo|
|--|--|
|1 minuto|Le frequenze di aggiornamento di un minuto sono utili quando si **esegue il debug** del codice di un'applicazione usando il personalizzatore, facendo demo o testando in modo interattivo gli aspetti di machine learning.|
|15 minuti|Le frequenze di aggiornamento del modello elevato sono utili per le situazioni in cui si desidera **tenere traccia delle modifiche** ai comportamenti degli utenti. ad esempio nei siti basati su notizie live, contenuto virale oppure offerte su prodotti in tempo reale. In questi scenari si può usare una frequenza di 15 minuti. |
|1 ora|Nella maggior parte dei casi d'uso, una frequenza di aggiornamento inferiore è efficace.|

![Model update frequency imposta la frequenza con cui viene ripetuto il training di un nuovo modello di Personalizza esperienze.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Dopo avere modificato questo valore, assicurarsi di selezionare **Salva**.

## <a name="data-retention"></a>Mantenimento dei dati

**Data retention period** (Periodo di conservazione dati) imposta il numero di giorni in cui Personalizza esperienze mantiene i log dei dati. I log dei dati passati sono necessari per eseguire le [valutazioni offline](concepts-offline-evaluation.md), usate per misurare l'efficacia di Personalizza esperienze e per ottimizzare i criteri di apprendimento.

Dopo avere modificato questo valore, assicurarsi di selezionare **Salva**.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Impostazioni che includono la reimpostazione del modello

Le azioni seguenti includono una ripetizione immediata del training del modello con gli ultimi 2 giorni di dati.

* Benefici
* Esplorazione

Per [cancellare](how-to-manage-model.md) tutti i dati, usare la pagina * * Impostazioni modello e apprendimento * *.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come gestire il modello](how-to-manage-model.md)

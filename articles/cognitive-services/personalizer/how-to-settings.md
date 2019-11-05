---
title: Configurare Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 4ab1dcf4f3554c941107ec653f717b3680543da2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490729"
---
# <a name="configure-personalizer"></a>Configurare Personalizza esperienze

La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.

## <a name="create-personalizer-resource"></a>Creare la risorsa di Personalizza esperienze

Creare una risorsa di Personalizza esperienze per ogni ciclo di feedback. 

1. Accedere al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Il collegamento precedente consente di accedere alla pagina **Crea** per il servizio Personalizza esperienze. 
1. Immettere il nome del servizio, selezionare una sottoscrizione, una località, un piano tariffario e un gruppo di risorse.
1. Selezionare la conferma e fare clic su **Crea**.

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>Configurare il servizio nel portale di Azure

1. Accedere al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Trovare la risorsa di Personalizza esperienze. 
1. Nella sezione **Resource Management** selezionare Configuration ( **configurazione**).

    Prima di uscire dal portale di Azure, copiare una delle chiavi di risorsa dalla pagina **Chiavi**. Tale chiave sarà necessaria per usare [Personalizer SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>Configurare la ricompensa per il ciclo di commenti e suggerimenti in base al caso d'uso

Configurare il servizio per l'uso dei premi del ciclo di feedback. Le modifiche apportate ai valori seguenti reimpostano il modello di personalizzazione corrente e ne rieseguiranno il training negli ultimi 2 giorni di dati:

![Configurare i valori Reward per il ciclo feedback](media/settings/configure-model-reward-settings.png)

|Valore|Scopo|
|--|--|
|Reward wait time (Tempo di attesa ricompense)|Imposta l'intervallo di tempo durante il quale Personalizza esperienze raccoglierà i valori delle ricompense per una chiamata a Classifica, a partire dal momento in cui si verifica tale chiamata. Questo valore viene impostato chiedendo: "per quanto tempo è necessario che il Personalizzatore attenda le chiamate dei premi?" Qualsiasi ricompensa arrivi dopo questo intervallo verrà registrata ma non verrà usata per l'apprendimento.|
|Default reward (Ricompensa predefinita)|Se Personalizza esperienze non riceve chiamate per le ricompense durante l'intervallo di Reward Wait Time associato a una chiamata a Classifica, verrà assegnata la ricompensa predefinita. Per impostazione predefinita e nella maggior parte degli scenari la ricompensa predefinita è zero.|
|Reward aggregation (Aggregazione ricompense)|Se si ricevono più ricompense per la stessa chiamata all'API Classifica, viene usato questo metodo di aggregazione: **somma** o **più vecchio**. Con quest'ultimo metodo, viene considerato il punteggio più vecchio ricevuto e viene ignorato il resto. Questo approccio è utile se si vuole avere una ricompensa univoca tra possibili chiamate duplicate. |

Dopo aver modificato questi valori, assicurarsi di selezionare **Salva**.

### <a name="configure-exploration"></a>Configura esplorazione 

Personalizza esperienze è in grado di individuare nuovi modelli e di adattarsi ai cambiamenti del comportamento degli utenti nel corso del tempo esplorando le alternative. Il valore di **esplorazione** determina la percentuale di chiamate di rango a cui viene fornita una risposta con l'esplorazione. 

Le modifiche apportate a questo valore reimpostano il modello di personalizzazione corrente e ne rieseguiranno il training negli ultimi 2 giorni di dati.

![Il valore di esplorazione determina la percentuale di chiamate di rango a cui viene fornita una risposta con l'esplorazione](media/settings/configure-exploration-setting.png)

Dopo avere modificato questo valore, assicurarsi di selezionare **Salva**.

### <a name="model-update-frequency"></a>Model update frequency (Frequenza di aggiornamento del modello)

Il modello più recente, sottoposto a training dalle chiamate all'API per le ricompense da tutti gli eventi attivi, non viene usato automaticamente dalla chiamata a Classifica di Personalizza esperienze. **Frequenza di aggiornamento del modello** imposta la frequenza con cui viene aggiornato il modello usato dalla chiamata a Classifica. 

Frequenze di aggiornamento del modello elevate sono utili nelle situazioni in cui si vuole tenere accuratamente traccia delle modifiche nei comportamenti degli utenti, ad esempio nei siti basati su notizie live, contenuto virale oppure offerte su prodotti in tempo reale. In questi scenari si può usare una frequenza di 15 minuti. Nella maggior parte dei casi d'uso, una frequenza di aggiornamento inferiore è efficace. Frequenze di aggiornamento di un minuto sono utili durante il debug del codice di un'applicazione con Personalizza esperienze, l'esecuzione di demo o i test interattivi di aspetti di Machine Learning.

![Model update frequency imposta la frequenza con cui viene ripetuto il training di un nuovo modello di Personalizza esperienze.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Dopo avere modificato questo valore, assicurarsi di selezionare **Salva**.

### <a name="data-retention"></a>Conservazione dei dati

**Data retention period** (Periodo di conservazione dati) imposta il numero di giorni in cui Personalizza esperienze mantiene i log dei dati. I log dei dati passati sono necessari per eseguire le [valutazioni offline](concepts-offline-evaluation.md), usate per misurare l'efficacia di Personalizza esperienze e per ottimizzare i criteri di apprendimento.

Dopo avere modificato questo valore, assicurarsi di selezionare **Salva**.

## <a name="export-the-personalizer-model"></a>Esportare il modello di Personalizza esperienze

Dalla sezione relativa alla gestione delle risorse per **le impostazioni relative a modelli e apprendimento**esaminare la creazione del modello e la data dell'ultimo aggiornamento ed esportare il modello corrente. È possibile usare il portale di Azure o le API di Personalizza esperienze per esportare un file di modello a scopo di archiviazione. 

![Esportare il modello corrente di Personalizza esperienze](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importare ed esportare criteri di apprendimento

Dalla sezione relativa alla gestione delle risorse per **le impostazioni relative a modelli e apprendimento**importare un nuovo criterio di apprendimento o esportare i criteri di apprendimento correnti.
È possibile ottenere i file dei criteri di apprendimento dalle esportazioni precedenti oppure scaricare i criteri ottimizzati individuati durante le valutazioni offline. Apportare modifiche manuali a questi file influirà sulle prestazioni e sull'accuratezza delle valutazioni offline e Microsoft non è in grado di garantire l'accuratezza di machine learning e valutazioni oppure le eccezioni dei servizi derivanti da criteri modificati manualmente.

## <a name="clear-data-for-your-learning-loop"></a>Cancellare i dati per il ciclo di apprendimento

1. Nel portale di Azure, per la risorsa di personalizzazione, nella pagina **Impostazioni modello e apprendimento** selezionare **Cancella dati**.
1. Per cancellare tutti i dati e reimpostare il ciclo di apprendimento sullo stato originale, selezionare tutte e tre le caselle di controllo.

    ![In portale di Azure cancellare i dati dalla risorsa di personalizzazione.](./media/settings/clear-data-from-personalizer-resource.png)

    |Valore|Scopo|
    |--|--|
    |Personalizzazione e dati di ricompensa registrati.|I dati di registrazione vengono usati nelle valutazioni offline. Cancellare i dati se si sta reimpostando la risorsa.|
    |Reimpostare il modello di personalizzazione.|Questo modello viene modificato a ogni ripetizione del training. Questa frequenza di training è specificata in **caricare la frequenza del modello** nella pagina di **configurazione** . |
    |Impostare i criteri di apprendimento su predefinito.|Se sono stati modificati i criteri di apprendimento come parte di una valutazione offline, questo Reimposta i criteri di apprendimento originali.|

1. Selezionare **Cancella dati selezionati** per avviare il processo di cancellazione. Lo stato viene segnalato nelle notifiche di Azure, nella barra di spostamento in alto a destra. 

## <a name="next-steps"></a>Passaggi successivi


[Informazioni sulla disponibilità in base all'area geografica](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

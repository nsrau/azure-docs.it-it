---
title: Configurare le impostazioni
titleSuffix: Azure Cognitive Services
description: La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: bce260d2a6b897d3e45b231df4eecd7ecb31ba31
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143252"
---
# <a name="personalizer-settings"></a>Impostazioni di Personalizza esperienze

La configurazione del servizio include il modo in cui vengono trattate le ricompense, la frequenza delle esplorazioni, la frequenza di ripetizione del training del modello e la quantità di dati archiviati.

## <a name="create-personalizer-resource"></a>Creare la risorsa di Personalizza esperienze

Creare una risorsa di Personalizza esperienze per ogni ciclo di feedback. 

1. Accedere al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Il collegamento precedente consente di accedere alla pagina **Crea** per il servizio Personalizza esperienze. 
1. Immettere il nome del servizio, selezionare una sottoscrizione, una località, un piano tariffario e un gruppo di risorse.
1. Selezionare la conferma e fare clic su **Crea**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Configurare le impostazioni del servizio nel portale di Azure

1. Accedere al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Trovare la risorsa di Personalizza esperienze. 
1. Nella sezione **Gestione risorse** selezionare **Impostazioni**.

    Prima di uscire dal portale di Azure, copiare una delle chiavi di risorsa dalla pagina **Chiavi**. Tale chiave sarà necessaria per usare [Personalizer SDK](https://aka.ms/personalizer-dotnet-sdk-docs).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Configurare le impostazioni delle ricompense per il ciclo di feedback in base al caso d'uso

Configurare le impostazioni del servizio per l'uso delle ricompense del ciclo di feedback. Le modifiche apportate alle impostazioni correnti reimpostano il modello corrente di Personalizza esperienze e ne ripetono il training con almeno 2 giorni di dati:

![Configurare le impostazioni delle ricompense per il ciclo di feedback](media/settings/configure-model-reward-settings.png)

|Impostazione|Scopo|
|--|--|
|Reward wait time (Tempo di attesa ricompense)|Imposta l'intervallo di tempo durante il quale Personalizza esperienze raccoglierà i valori delle ricompense per una chiamata a Classifica, a partire dal momento in cui si verifica tale chiamata. Questo valore viene impostato domandando: "Per quanto tempo Personalizza esperienze dovrà aspettare le chiamate per le ricompense?" Qualsiasi ricompensa arrivi dopo questo intervallo verrà registrata ma non verrà usata per l'apprendimento.|
|Default reward (Ricompensa predefinita)|Se Personalizza esperienze non riceve chiamate per le ricompense durante l'intervallo di Reward Wait Time associato a una chiamata a Classifica, verrà assegnata la ricompensa predefinita. Per impostazione predefinita e nella maggior parte degli scenari la ricompensa predefinita è zero.|
|Reward aggregation (Aggregazione ricompense)|Se si ricevono più ricompense per la stessa chiamata all'API Classifica, viene usato questo metodo di aggregazione: **somma** o **più vecchio**. Con quest'ultimo metodo, viene considerato il punteggio più vecchio ricevuto e viene ignorato il resto. Questo approccio è utile se si vuole avere una ricompensa univoca tra possibili chiamate duplicate. |

Dopo aver apportato queste modifiche, assicurarsi di selezionare **Salva**.

### <a name="exploration-setting"></a>Impostazione dell'esplorazione 

Personalizza esperienze è in grado di individuare nuovi modelli e di adattarsi ai cambiamenti del comportamento degli utenti nel corso del tempo esplorando le alternative. L'impostazione **Exploration** (Esplorazione) determina la percentuale di chiamate a Classifica a cui viene data una risposta con l'esplorazione. 

Le modifiche apportate a questa impostazione reimpostano il modello corrente di Personalizza esperienze e ne ripetono il training con almeno 2 giorni di dati.

![L'impostazione dell'esplorazione determina la percentuale di chiamate a Classifica a cui viene data una risposta con l'esplorazione](media/settings/configure-exploration-setting.png)

Dopo aver cambiato questa impostazione, assicurarsi di selezionare **Salva**.

### <a name="model-update-frequency"></a>Model update frequency (Frequenza di aggiornamento del modello)

**Model update frequency** imposta la frequenza con cui viene ripetuto il training di un nuovo modello di Personalizza esperienze. 

![Model update frequency imposta la frequenza con cui viene ripetuto il training di un nuovo modello di Personalizza esperienze.](media/settings/configure-model-update-frequency-settings.png)

Dopo aver cambiato questa impostazione, assicurarsi di selezionare **Salva**.

### <a name="data-retention"></a>Conservazione dei dati

**Data retention period** (Periodo di conservazione dati) imposta il numero di giorni in cui Personalizza esperienze mantiene i log dei dati. I log dei dati passati sono necessari per eseguire le [valutazioni offline](concepts-offline-evaluation.md), usate per misurare l'efficacia di Personalizza esperienze e per ottimizzare i criteri di apprendimento.

Dopo aver cambiato questa impostazione, assicurarsi di selezionare **Salva**.

## <a name="export-the-personalizer-model"></a>Esportare il modello di Personalizza esperienze

Nella sezione **Model and Policy** (Modello e criteri) di Gestione risorse esaminare la data di creazione e dell'ultimo aggiornamento del modello ed esportare il modello corrente.

![Esportare il modello corrente di Personalizza esperienze](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importare ed esportare criteri di apprendimento

Nella sezione **Model and Policy** (Modello e criteri) di Gestione risorse importare un nuovo criterio di apprendimento oppure esportare quello corrente.

## <a name="next-steps"></a>Passaggi successivi

[Come usare il contenitore di Personalizza esperienze](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)

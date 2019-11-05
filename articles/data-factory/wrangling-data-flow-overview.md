---
title: Dispute dei flussi di dati in Azure Data Factory | Microsoft Docs
description: Cenni preliminari sulla disputa dei flussi di dati in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 05119adfa5b254297fd656854a027ede05c5ef26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518580"
---
# <a name="what-are-wrangling-data-flows"></a>Che cosa sono i flussi di dati?

Le organizzazioni devono eseguire la preparazione dei dati e la disputa per un'analisi accurata dei dati complessi che continuano a crescere ogni giorno. La preparazione dei dati è necessaria in modo che le organizzazioni possano usare i dati in vari processi aziendali e ridurre il tempo di valore.

La disputa dei flussi di dati in Azure Data Factory consente di eseguire in modo iterativo la preparazione dei dati senza codice a livello di cloud. L'integrazione di flussi di dati con [Power query online](https://docs.microsoft.com/power-query/) e rende disponibili funzioni Power query M per gli utenti di data factory.

La verifica del flusso di dati converte i M generati dall'editor di mashup di Power Query online in codice Spark per l'esecuzione su scala cloud.

La disputa dei flussi di dati è particolarmente utile per gli ingegneri di dati o per gli integratori di dati Citizen.

## <a name="use-cases"></a>Casi d'uso

### <a name="fast-interactive-data-exploration-and-preparation"></a>Esplorazione e preparazione rapida di dati interattivi

Più data Engineers e gli integratori di dati Citizen possono esplorare e preparare in modo interattivo i set di dati a livello di cloud. Con l'aumentare del volume, della varietà e della velocità dei dati nei data Lake, gli utenti devono disporre di un modo efficace per esplorare e preparare i set di dati. Potrebbe essere necessario, ad esempio, creare un set di dati che "disponga di tutte le informazioni demografiche sui clienti per i nuovi clienti dal 2017". Non è possibile eseguire il mapping a una destinazione nota. Si stanno esplorando, litigando e preparando i set di impostazioni per soddisfare un requisito prima di pubblicarlo nel Lake. La disputa dei flussi di dati viene spesso usata per scenari di analisi meno formali. I set di impostazioni possono essere usati per eseguire trasformazioni e operazioni di machine learning a valle.

### <a name="code-free-agile-data-preparation"></a>Preparazione dei dati agile senza codice

Gli integratori di dati Citizen dedicano più del 60% del tempo alla ricerca e alla preparazione dei dati. Si sta cercando di eseguire questa operazione in un codice libero per migliorare la produttività operativa. Consentire agli integratori di dati dei cittadini di arricchire, modellare e pubblicare i dati usando strumenti noti come Power Query online in modo scalabile, migliora notevolmente la produttività. Il flusso di dati in Azure Data Factory consente all'editor di mashup familiare Power Query online di consentire agli integratori di dati Citizen di correggere rapidamente gli errori, standardizzare i dati e produrre dati di alta qualità per supportare le decisioni aziendali.

### <a name="data-validation"></a>Convalida dei dati

Analizza visivamente i dati in modo senza codice per rimuovere eventuali outlier, anomalie e conformi a una forma per l'analisi veloce.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un flusso di dati](wrangling-data-flow-tutorial.md)in confronto.
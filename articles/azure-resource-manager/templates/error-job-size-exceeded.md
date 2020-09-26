---
title: Errore durante il superamento delle dimensioni del processo
description: Viene descritto come risolvere gli errori quando la dimensione o il modello del processo è troppo grande.
ms.topic: troubleshooting
ms.date: 09/25/2020
ms.openlocfilehash: 06645561964d9634d93061b3be4d100a578cc7e7
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91373176"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Risoluzione degli errori per la dimensione del processo superata

Questo articolo descrive come risolvere gli errori di **JobSizeExceededException** e **DeploymentSizeExceededException** .

## <a name="symptom"></a>Sintomo

Quando si distribuisce un modello, viene visualizzato un errore che informa che la distribuzione ha superato i limiti.

## <a name="cause"></a>Causa

Questo errore può essere ottenuto quando la dimensione del modello supera i 4 MB. Il limite di 4 MB si applica allo stato finale del modello dopo che è stato espanso per le definizioni di risorse che usano [Copy](copy-resources.md) per creare molte istanze. Lo stato finale include anche i valori risolti per variabili e parametri.

Il processo di distribuzione include anche i metadati sulla richiesta. Per i modelli di grandi dimensioni, i metadati combinati con il modello possono superare le dimensioni consentite per un processo.

Altri limiti per il modello sono:

* 256 parametri
* 256 variabili
* 800 risorse (incluso il conteggio copie)
* 64 valori di output
* 24.576 caratteri in un'espressione di modello

## <a name="solution-1---simplify-template"></a>Soluzione 1-semplificare il modello

La prima opzione consiste nel semplificare il modello. Questa opzione funziona quando il modello distribuisce molti tipi di risorse diversi. Provare a dividere il modello in [modelli collegati](linked-templates.md). Dividere i tipi di risorse in gruppi logici e aggiungere un modello collegato per ogni gruppo. Ad esempio, se è necessario distribuire numerose risorse di rete, è possibile spostare tali risorse in un modello collegato.

È possibile impostare altre risorse come dipendenti dal modello collegato e [ottenere i valori dall'output del modello collegato](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---use-serial-copy"></a>Soluzione 2: usare la copia seriale

La seconda opzione consiste nel modificare il ciclo di copia da [parallelo a elaborazione seriale](copy-resources.md#serial-or-parallel). Usare questa opzione solo quando si ritiene che l'errore provenga dalla distribuzione di un numero elevato di risorse tramite copia. Questa modifica può aumentare significativamente il tempo di distribuzione perché le risorse non vengono distribuite in parallelo.

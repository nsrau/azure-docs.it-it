---
title: Deviazione dello schema dei flussi di dati di mapping di Azure Data Factory
description: Compilare flussi di dati resilienti in Azure Data Factory con la deviazione dello schema
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 6fd610afc0a21a97a8544b9e4b173f207f5fb50f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722876"
---
# <a name="mapping-data-flow-schema-drift"></a>Deviazione dello schema dei flussi di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Il fenomeno di deviazione dello schema avviene quando i metadati delle origini cambiano spesso. Campi, colonne, tipi e così via possono essere aggiunti, rimossi o modificati in tempo reale. Senza la gestione per la deviazione dello schema, il flusso di dati diventa vulnerabile alle modifiche nell'origine dati upstream. Quando colonne e campi immessi vengono modificati, i modelli ETL tipici non riescono, perché tendono a essere associati ai nomi di origine.

Per proteggere l'ambiente dalla deviazione dello schema, è importante avere a disposizione le capacità di uno strumento per i flussi di dati che permettano agli sviluppatori di dati di:

* Definire le origini che hanno nomi di campo, tipi di dati, valori e dimensioni variabili
* Definire i parametri di trasformazione che possono funzionare con i modelli di dati invece di valori e campi impostati come hardcoded
* Definire le espressioni in grado di riconoscere i criteri per la corrispondenza dei campi in ingresso, invece di usare campi denominati

## <a name="how-to-implement-schema-drift"></a>Come implementare deviazione schema

* Scegliere "Allow Schema Drift" (Consenti deviazione schema) nella trasformazione origine

<img src="media/data-flow/schemadrift001.png" width="400">

* Dopo aver selezionato questa opzione, tutti i campi in ingresso verranno letti dall'origine a ogni esecuzione del flusso di dati e verranno passati attraverso l'intero flusso al sink.

* Assicurarsi di usare "Mapping automatico" per eseguire il mapping di tutti i nuovi campi nella trasformazione Sink in modo che tutti i nuovi campi ottengano raccoglie e visualizzati nella destinazione.

<img src="media/data-flow/automap.png" width="400">

* Tutto funzionerà correttamente quando i nuovi campi vengono introdotti in questo scenario con un semplice mapping origine -> sink (noto anche come copia).

* Per aggiungere trasformazioni nel flusso di lavoro che gestisce la deviazione dello schema, è possibile usare criteri di ricerca per trovare le colonne corrispondenti in base a nome, tipo e valore.

* Fare clic su "Add Column Pattern" (Aggiungi criteri colonna) nella trasformazione aggregazione o colonna derivata se si vuole creare una trasformazione in grado di riconoscere la deviazione dello schema.

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Nel flusso di dati è necessario prendere una decisione dal punto di vista dell'architettura riguardo a se accettare la deviazione dello schema in tutto il flusso. In questo modo, è possibile proteggersi da modifiche dello schema rispetto alle origini. Andrà tuttavia perduta l'associazione anticipata delle colonne e dei tipi in tutto il flusso di dati. Poiché Azure Data Factory considera i flussi con deviazione dello schema come flussi con associazione anticipata, quando si creano trasformazioni i nomi di colonna non sono disponibili nelle visualizzazioni dello schema in tutto il flusso.

<img src="media/data-flow/taxidrift1.png" width="400">

Nel flusso di dati di esempio Taxi Demo è presente una deviazione dello schema nel flusso di dati inferiore con l'origine TripFare. Nella trasformazione aggregazione notare che viene usata la progettazione basata sui "criteri di ricerca colonna" per i campi di aggregazione. Invece di denominare colonne specifiche o cercare colonne in base alla posizione, si presuppone che i dati possano cambiare e non essere visualizzati nello stesso ordine tra le esecuzioni.

In questo esempio di gestione della deviazione dello schema del flusso di dati di Azure Data Factory è stata creata un'aggregazione che esegue l'analisi per individuare le colonne di tipo "double", sapendo che il dominio dei dati contiene i prezzi per ogni corsa. È quindi possibile eseguire un calcolo matematico di aggregazione su tutti i campi double nell'origine, indipendentemente da dove appare la colonna e dalla denominazione della colonna.

La sintassi del flusso di dati di Azure Data Factory usa $$ per rappresentare ogni colonna corrispondente in base al criterio di ricerca. È anche possibile trovare la corrispondenza in base ai nomi di colonna usando una ricerca di stringhe complessa e funzioni di espressione regolare. In questo caso, si creerà un nuovo nome di campo aggregato in base a ogni corrispondenza di un tipo di colonna "double" e verrà aggiunto il testo ```_total``` a ognuno dei nomi corrispondenti: 

```concat($$, '_total')```

Verranno quindi arrotondati e sommati i valori per ognuna delle colonne corrispondenti:

```round(sum ($$))```

È possibile verificare tutto questo con l'esempio di flusso di dati di Azure Data Factory "Taxi Demo". Attivare la sessione di debug usando l'interruttore Debug nella parte superiore dell'area di progettazione del flusso di dati in modo da visualizzare i risultati in modo interattivo:

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Accesso le nuove colonne a valle

Quando si generano nuove colonne usando modelli di colonna, è possibile accedere a tali nuove colonne in un secondo momento nelle trasformazioni del flusso di dati utilizzando la funzione di espressione "byName".

## <a name="next-steps"></a>Passaggi successivi

Nel [linguaggio di espressioni del flusso di dati](data-flow-expression-functions.md) per modelli di colonna e deviazione schema incluso "byName" e "byPosition" sono disponibili funzionalità aggiuntive.

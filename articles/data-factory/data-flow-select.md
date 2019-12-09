---
title: Trasformazione Selezione flusso di dati mapping
description: Trasformazione Selezione flusso di dati mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: a255d89aa4850d1249f1af9bdd0cb43b0826914f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930237"
---
# <a name="mapping-data-flow-select-transformation"></a>Trasformazione Selezione flusso di dati mapping


Usare questa trasformazione per la selettività delle colonne (riducendo il numero di colonne), le colonne alias e i nomi di flusso e riordinare le colonne.

## <a name="how-to-use-select-transformation"></a>Come usare selezione trasformazione
La trasformazione Selezione consente di creare un alias per un intero flusso, o per le colonne in tale flusso, di assegnare nomi diversi (alias) e quindi di fare riferimento a questi nuovi nomi in un secondo momento nel flusso di dati. Questa trasformazione è utile per scenari di self-join. Per implementare un self join in un flusso di dati di Azure Data Factory, prendere un flusso, creare un ramo del flusso con "New Branch"(Nuovo ramo) e quindi aggiungere subito dopo una trasformazione "Select" (Selezione). Tale flusso avrà ora un nuovo nome che è possibile usare per il join con il flusso originale, creando così un self join:

![Self-join](media/data-flow/selfjoin.png "Self-join")

Nel diagramma precedente la trasformazione Selezione si trova nella parte superiore. Viene usata per creare un alias del flusso originale in "OrigSourceBatting". Nella trasformazione di join evidenziata sotto di essa si noterà che questo flusso di alias SELECT viene usato come join a destra, consentendo di fare riferimento alla stessa chiave sia nella parte sinistra & destra del Inner join.

È possibile usare la trasformazione Selezione anche come modo per deselezionare le colonne dal flusso di dati. Ad esempio, se nel sink sono definite 6 colonne, ma se ne vogliono selezionare solo 3 specifiche per la trasformazione e per poi inserirle nel flusso per il sink, è possibile selezionare solo quelle 3 tramite la trasformazione Selezione.

![Selezione trasformazione](media/data-flow/newselect1.png "Seleziona alias")

## <a name="options"></a>Opzioni
* L'impostazione predefinita per "Select" (Selezione) prevede l'inclusione di tutte le colonne in ingresso mantenendo i nomi originali. È possibile definire un alias per il flusso impostando il nome della trasformazione Selezione.
* Per impostare un alias per singole colonne, deselezionare "Select All" (Seleziona tutto) e usare il mapping delle colonne nella parte inferiore.
* Scegliere Ignora duplicati per eliminare le colonne duplicate dai metadati di input o di output.

![Ignora duplicati](media/data-flow/select-skip-dup.png "Ignora duplicati")

* Quando si sceglie di ignorare i duplicati, i risultati saranno visibili nella scheda controlla. ADF manterrà la prima occorrenza della colonna e si noterà che ogni occorrenza successiva della stessa colonna è stata rimossa dal flusso.

> [!NOTE]
> Per cancellare le regole di mapping, fare clic sul pulsante **Reimposta** .

## <a name="mapping"></a>Mapping
Per impostazione predefinita, la trasformazione Select eseguirà automaticamente il mapping di tutte le colonne, che passeranno tutte le colonne in ingresso allo stesso nome nell'output. Il nome del flusso di output impostato in Seleziona impostazioni definirà un nuovo nome di alias per il flusso. Se si mantiene il set di selezione per la mappa automatica, è possibile usare l'alias dell'intero flusso con tutte le colonne uguali.

![Selezionare le regole di trasformazione](media/data-flow/rule2.png "Mapping basato su regole")

Se si desidera assegnare alias, rimuovere, rinominare o riordinare le colonne, è necessario innanzitutto disattivare "mappa automatica". Per impostazione predefinita, viene visualizzata una regola predefinita immessa per il nome "tutte le colonne di input". È possibile lasciare invariata questa regola se si intende consentire sempre il mapping di tutte le colonne in ingresso allo stesso nome nell'output.

Tuttavia, se si desidera aggiungere regole personalizzate, sarà necessario fare clic su "Aggiungi mapping". Il mapping dei campi fornirà un elenco di nomi di colonna in entrata e in uscita da mappare e alias. Scegliere "mapping basato su regole" per creare regole di corrispondenza dei criteri.

## <a name="rule-based-mapping"></a>Mapping basato su regole
Quando si sceglie il mapping basato su regole, si indica ad ADF di valutare l'espressione corrispondente in modo che corrisponda alle regole dei criteri in ingresso e di definire i nomi dei campi in uscita. È possibile aggiungere qualsiasi combinazione di mapping di campi e basati su regole. I nomi dei campi vengono quindi generati in fase di esecuzione da ADF in base ai metadati in ingresso provenienti dall'origine. È possibile visualizzare i nomi dei campi generati durante il debug e utilizzando il riquadro di anteprima dei dati.

Altre informazioni sui criteri di ricerca sono disponibili nella [documentazione relativa ai modelli di colonna](concepts-data-flow-column-pattern.md).

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver utilizzato SELECT per rinominare, riordinare e utilizzare le colonne alias, utilizzare la [trasformazione sink](data-flow-sink.md) per inserire i dati in un archivio dati.

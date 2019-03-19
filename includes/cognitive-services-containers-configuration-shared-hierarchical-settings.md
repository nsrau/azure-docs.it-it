---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 03ec8740a4cf36bf3d09dade8a24b155c09d1299
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740964"
---
Le impostazioni per il contenitore sono gerarchiche e tutti i contenitori nel computer host usano una gerarchia condivisa.

Per specificare le impostazioni è possibile usare una delle tecniche seguenti:

* [Variabili di ambiente](#environment-variable-settings)
* [Argomenti della riga di comando](#command-line-argument-settings)

I valori delle variabili di ambiente sostituiscono i valori degli argomenti della riga di comando, che a loro volta sostituiscono i valori predefiniti per l'immagine del contenitore. Se si specificano valori diversi in una variabile di ambiente e in un argomento della riga di comando per la stessa impostazione di configurazione per il contenitore di cui è stata creata l'istanza viene usato il valore nella variabile di ambiente.

|Precedenza|Impostazione della posizione|
|--|--|
|1|Variabile di ambiente| 
|2|Riga di comando|
|3|Valore predefinito dell'immagine del contenitore|

### <a name="environment-variable-settings"></a>Impostazioni della variabile di ambiente

I vantaggi dell'uso delle variabili di ambiente sono:

* È possibile configurare più impostazioni.
* Più contenitori possono usare le stesse impostazioni.

### <a name="command-line-argument-settings"></a>Impostazioni degli argomenti della riga di comando

Il vantaggio di usare gli argomenti della riga di comando è che ogni contenitore può usare impostazioni diverse.

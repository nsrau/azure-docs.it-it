---
author: baanders
description: file di inclusione per le operazioni di query di Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 450ea0b8024192f6f351b4893fe6e8e07db236f7
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931980"
---
## <a name="query-language-features"></a>Funzionalità del linguaggio di query

I dispositivi gemelli digitali di Azure forniscono funzionalità di query complete rispetto al grafo gemello. Le query vengono descritte usando la sintassi simile a SQL, in un linguaggio di query simile al [linguaggio di query dell'hub](../articles/iot-hub/iot-hub-devguide-query-language.md) Internet con molte funzionalità confrontabili.

> [!NOTE]
> Tutte le operazioni di query di Azure Digital Twins fanno distinzione tra maiuscole e minuscole.

Di seguito sono riportate le operazioni disponibili nel linguaggio di query di Azure Digital gemelli.

Ottenere i gemelli digitali per...
* modello ( `IS_OF_MODEL` operatore using)
* Proprietà (incluse le [proprietà del tag](../articles/digital-twins/how-to-use-tags.md))
* interfaces
* relationships
  - Proprietà delle relazioni

È possibile migliorare ulteriormente le query con le operazioni seguenti:
* Ottenere i gemelli su più tipi di relazione ( `JOIN` query). 
  - Durante l'anteprima, `JOIN` sono consentiti fino a cinque livelli.
* Seleziona solo i primi risultati della query ( `Select TOP` operatore)
* Contare il numero di elementi in un set di risultati utilizzando `Select COUNT`
* Usare funzioni scalari: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTSWITH` , `ENDSWITH` .
* Utilizzare gli operatori di confronto delle query: `IN` / `NIN` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Usare qualsiasi combinazione ( `AND` , `OR` , `NOT` operatore) di `IS_OF_MODEL` , funzioni scalari e operatori di confronto.
* Use continuation: viene creata un'istanza dell'oggetto query con una dimensione di pagina (fino a 100). È possibile recuperare i gemelli digitali una pagina alla volta specificando il token di continuazione nelle chiamate successive all'API.
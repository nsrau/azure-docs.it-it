---
author: baanders
description: file di inclusione per le operazioni di query di Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 82639a19ef728c22a74381d24754992e0f647976
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562472"
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
  - Esistono limitazioni sul numero di istanze `JOIN` consentite (un livello per l'anteprima pubblica).
* Seleziona solo i primi risultati della query ( `Select TOP` operatore)
* Usare funzioni scalari: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTSWITH` , `ENDSWITH` .
* Utilizzare gli operatori di confronto delle query: `IN` / `NIN` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Usare qualsiasi combinazione ( `AND` , `OR` , `NOT` operatore) di `IS_OF_MODEL` , funzioni scalari e operatori di confronto.
* Use continuation: viene creata un'istanza dell'oggetto query con una dimensione di pagina (fino a 100). È possibile recuperare i gemelli digitali una pagina alla volta specificando il token di continuazione nelle chiamate successive all'API.
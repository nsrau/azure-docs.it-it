---
title: Linguaggio di query
titleSuffix: Azure Digital Twins
description: Informazioni sulle nozioni di base del linguaggio Query Store di Azure Digital gemelli.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3196004015046b4d3d2789745c80d323bacdced9
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985242"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Informazioni sul linguaggio di query per i dispositivi gemelli digitali di Azure

Tenere presente che il centro dei dispositivi gemelli digitali di Azure è il [**grafo gemello**](concepts-twins-graph.md), costruito dai **gemelli digitali** e dalle **relazioni**. È possibile eseguire query su questo grafico per ottenere informazioni sui gemelli digitali e sulle relazioni in esso contenute. Queste query sono scritte in un linguaggio di query personalizzato simile a SQL denominato **Azure Digital gemells query Store linguaggio**.

Per inviare una query al servizio da un'app client, si userà l' **API di query**dei dispositivi digitali gemelli di Azure. Questo consente agli sviluppatori di scrivere query e applicare filtri per trovare set di dispositivi gemelli digitali nel grafico gemello e altre informazioni sullo scenario dei gemelli digitali di Azure.

## <a name="query-language-features"></a>Funzionalità del linguaggio di query

I dispositivi gemelli digitali di Azure forniscono funzionalità di query complete rispetto al grafo gemello. Le query vengono descritte usando la sintassi simile a SQL, in un linguaggio di query simile al [linguaggio di query dell'hub](../iot-hub/iot-hub-devguide-query-language.md) Internet con molte funzionalità confrontabili.

Di seguito sono riportate le operazioni disponibili in Azure Digital Gemells Query Store lingua:
* Ottiene i gemelli dalle proprietà dei gemelli digitali.
* Ottiene i dispositivi gemelli per le interfacce dei gemelli digitali.
* Ottiene i gemelli in base alle proprietà della relazione.
* Ottenere i gemelli su più tipi di relazione ( `JOIN` query). Esistono limitazioni sul numero di istanze `JOIN` consentite (un livello per l'anteprima pubblica).
* Usare la funzione personalizzata `IS_OF_MODEL(twinCollection, twinTypeName)` , che consente di filtrare in base al [modello](concepts-models.md)del gemello. Supporta l'ereditarietà.
* Usare qualsiasi combinazione ( `AND` , `OR` , `NOT` operatore) della versione precedente.
* Usare funzioni scalari: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTS_WITH` , `ENDS_WITH` .
* Utilizzare gli operatori di confronto delle query: `AND` / `OR` / `NOT` , `IN` / `NOT IN` , `STARTSWITH` / `ENDSWITH` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Use continuation: viene creata un'istanza dell'oggetto query con una dimensione di pagina (fino a 100). È possibile recuperare i gemelli digitali una pagina alla volta, ripetendo le chiamate al `nextAsTwin` metodo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come scrivere query e vedere esempi di codice client in [procedura: eseguire una query sul grafico gemello](how-to-query-graph.md).
---
title: Interfaccia API Web - API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Usare l'interfaccia API Web per creare un'esperienza di ricerca semantica avanzata nell'API Knowledge Exploration Service (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 970db5984eedebf98bbb087cfd0b4a35a21a0f54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814425"
---
# <a name="web-api-interface"></a>Interfaccia API Web

I file di modello compilati da Knowledge Exploration Service possono essere ospitati e sono accessibili tramite un set di API Web.  Le API possono essere ospitate nel computer locale usando il comando [`host_service`](CommandLine.md#host_service-command) o possono essere distribuite in un servizio cloud di Azure usando il comando [`deploy_service`](CommandLine.md#deploy_service-command).  Entrambe le tecniche espongono gli endpoint API seguenti:

* [*interpret*](interpretMethod.md): interpreta una stringa di query in linguaggio naturale. Restituisce interpretazioni con note per consentire esperienze avanzate di completamento automatico della casella di ricerca, in grado di prevedere ciò che l'utente sta digitando.
* [*evaluate*](evaluateMethod.md): valuta e restituisce l'output di un'espressione di query strutturata.
* [*calchistogram*](calchistogramMethod.md): calcola un istogramma dei valori degli attributi per gli oggetti restituiti da un'espressione di query strutturata.

Usati insieme, questi metodi API consentono la creazione di un'esperienza di ricerca semantica avanzata.  Con una stringa di query in linguaggio naturale, il metodo *interpret* fornisce le versioni con annotazioni della query di input con le espressioni di query strutturate, in base alla grammatica e ai dati di indice sottostanti.  Il metodo *evaluate* valuta l'espressione di query strutturata e restituisce gli oggetti indice corrispondenti da visualizzare.  Il metodo *calchistogram* elabora le distribuzioni dei valori degli attributi per abilitare l'applicazione di filtri e il perfezionamento.

**Esempio**

In un dominio di pubblicazioni accademiche, se un utente digita la stringa "latent s", il metodo *interpret* può fornire un set di interpretazioni classificate, il che suggerisce che l'utente stia cercando la parola chiave "latent semantic analysis", il titolo "latent structure analysis" o altre espressioni che iniziano con "latent s".  Queste informazioni possono essere usate per consentire all'utente di arrivare rapidamente ai risultati della ricerca desiderati.

Per questo dominio, il metodo *evaluate* può essere usato per recuperare dall'indice accademico un set di pubblicazioni corrispondenti, mentre il metodo*calchistogram* può essere usato per calcolare la distribuzione dei valori degli attributi per le pubblicazioni corrispondenti, che è possibile usare per filtrare e affinare ulteriormente i risultati della ricerca.

Si noti che, per migliorare la leggibilità degli esempi, le chiamate alle API REST contengono caratteri (ad esempio, spazi) senza codifica URL. Il codice dovrà applicare le codifiche URL appropriate.

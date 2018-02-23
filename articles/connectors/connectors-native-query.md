---
title: Aggiungere l'azione di query alle app per la logica | Documentazione Microsoft
description: Panoramica dell'azione di query per l'esecuzione di azioni come Filtra matrice.
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 05dd4ae3c4ee439d66401a3f5595f9104051f8ee
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-query-action"></a>Introduzione all'azione di query
Con l'azione di query è possibile usare batch e matrici per poter eseguire flussi di lavoro per:

* Creare un'attività per tutti i record ad alta priorità di un database.
* Salvare tutti gli allegati PDF per i messaggi di posta elettronica in un BLOB di Azure.

Per iniziare a usare l'azione di query in un'app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Usare l'azione di query
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](connectors-overview.md).  

L'azione di query ha attualmente un'operazione, denominata Filtra matrice, esposta nella finestra di progettazione. Questa consente di interrogare una matrice e restituire un set di risultati filtrati.

Ecco come è possibile aggiungerla in un'app per la logica:

1. Fare clic sul pulsante **Nuovo passaggio** .
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di ricerca azione digitare **filtro** per elencare l'azione **Filtra matrice**.
   
    ![Selezionare l'azione di query](./media/connectors-native-query/using-action-1.png)
4. Selezionare una matrice da filtrare. Lo screenshot seguente illustra la matrice di risultati ottenuta da una ricerca in Twitter.
5. Creare una condizione da valutare per ogni elemento. Lo screenshot seguente filtra i tweet degli utenti con più di 100 follower.
   
    ![Completare l'azione di query](./media/connectors-native-query/using-action-2.png)
   
    L'azione genera una nuova matrice che contiene solo i risultati che soddisfano i requisiti del filtro.
6. Fare clic sull'angolo in alto a sinistra della barra degli strumenti per salvare e pubblicare (attivare) l'app per la logica.

\* Se si esegue la chiamata a un endpoint HTTP e si riceve una risposta JSON, usare l'azione _Analizza JSON_ per analizzare la risposta JSON. Senza questo passaggio, l'azione _Filtra matrice_ vedrà solo il corpo e non comprenderà la struttura del payload JSON.

## <a name="query-action"></a>Azione di query
Ecco i dettagli per l'azione supportata da questo connettore. Il connettore supporta una sola azione possibile.

| Azione | DESCRIZIONE |
| --- | --- |
| Filtra matrice |Valuta una condizione per ogni elemento in una matrice e restituisce i risultati |

## <a name="action-details"></a>Informazioni dettagliate sulle azioni
L'azione di query supporta un'azione possibile. La tabella seguente descrive i campi di input obbligatori e facoltativi per l'azione e i dettagli di output corrispondenti associati all'uso dell'azione.

### <a name="filter-array"></a>Filtra matrice
Di seguito sono riportati campi di input per l'azione, che esegue una richiesta HTTP in uscita.
Un asterisco (*) indica che è un campo obbligatorio.

| Nome visualizzato | Nome proprietà | DESCRIZIONE |
| --- | --- | --- |
| Da* |from |La matrice da filtrare |
| Condizione* |dove |La condizione da valutare per ogni elemento |

<br>

### <a name="output-details"></a>Dettagli dell'output
Di seguito sono riportati i dettagli di output per la risposta HTTP.

| Nome proprietà | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| Matrice filtrata |array |Matrice che contiene un oggetto per ogni risultato filtrato |

## <a name="next-steps"></a>Passaggi successivi
Provare ora a usare la piattaforma e [creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l' [elenco di API](apis-list.md).


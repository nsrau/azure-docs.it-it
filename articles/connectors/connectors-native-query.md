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
ms.translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: a992fa17a07d6167297c4cf5fe9fb3b58181d7df
ms.contentlocale: it-it
ms.lasthandoff: 01/20/2017

---
# <a name="get-started-with-the-query-action"></a>Introduzione all'azione di query
Con l'azione di query è possibile usare batch e matrici per poter eseguire flussi di lavoro per:

* Creare un'attività per tutti i record ad alta priorità di un database.
* Salvare tutti gli allegati PDF per i messaggi di posta elettronica in un BLOB di Azure.

Per iniziare a usare l'azione di query in un'app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

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

## <a name="query-action"></a>Azione di query
Ecco i dettagli per l'azione supportata da questo connettore. Il connettore supporta una sola azione possibile.

| Azione | Descrizione |
| --- | --- |
| Filtra matrice |Valuta una condizione per ogni elemento in una matrice e restituisce i risultati |

## <a name="action-details"></a>Informazioni dettagliate sulle azioni
L'azione di query supporta un'azione possibile. La tabella seguente descrive i campi di input obbligatori e facoltativi per l'azione e i dettagli di output corrispondenti associati all'uso dell'azione.

### <a name="filter-array"></a>Filtra matrice
Di seguito sono riportati campi di input per l'azione, che esegue una richiesta HTTP in uscita.
Un asterisco (*) indica che è un campo obbligatorio.

| Nome visualizzato | Nome proprietà | Descrizione |
| --- | --- | --- |
| Da* |from |La matrice da filtrare |
| Condizione* |dove |La condizione da valutare per ogni elemento |

<br>

### <a name="output-details"></a>Dettagli dell'output
Di seguito sono riportati i dettagli di output per la risposta HTTP.

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| Matrice filtrata |array |Matrice che contiene un oggetto per ogni risultato filtrato |

## <a name="next-steps"></a>Passaggi successivi
Provare ora a usare la piattaforma e [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l' [elenco di API](apis-list.md).



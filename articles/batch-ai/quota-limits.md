---
title: Quote e limiti del servizio per Azure Batch per intelligenza artificiale | Microsoft Docs
description: Informazioni su vincoli, limiti e quote predefiniti di Azure Batch per intelligenza artificiale e su come richiedere incrementi di quota
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 16032ec5ba1e613462f92b86281ce93153b70923
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409718"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Quote e limiti del servizio Batch per intelligenza artificiale

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Come con altri servizi di Azure, esistono limiti su specifiche risorse associate al servizio Batch per intelligenza artificiale. Con Batch per intelligenza artificiale, questi limiti sono quote predefinite applicate a livello di sottoscrizione per ogni area in cui il servizio è [disponibile](https://azure.microsoft.com/global-infrastructure/services/). Questo articolo illustra i valori predefiniti e come è possibile richiedere aumenti di quota.

Tenere presenti queste quote quando si progettano e aumentano le risorse Batch per intelligenza artificiale. Se, ad esempio, il cluster non raggiunge il numero previsto di nodi specificato, potrebbe essere stato raggiunto un limite di core di Batch per intelligenza artificiale per la sottoscrizione.

Se si prevede di eseguire carichi di lavoro di produzione con Batch per intelligenza artificiale, potrebbe essere necessario incrementare il valore predefinito di una o più quote.

> [!NOTE]
> Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.
> 
> 

## <a name="resource-quotas"></a>Quote di risorse

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>Altri limiti

I seguenti sono limiti rigidi che non possono essere superati una volta raggiunti.

| **Risorsa** | **Limite massimo** |
| --- | --- |
| Aree di lavoro massime per gruppo di risorse | 800 |
| Dimensioni massime del cluster | 100 nodi |
| Processi MPI GPU massimi per nodo | 1-4 |
| Numero massimo di ruoli di lavoro GPU per nodo | 1-4 |
| Durata massima del processo | 7 giorni<sup>1</sup> |
| Parametri massimi dei server per nodo | 1 |

<sup>1</sup> La durata massima equivale al tempo in cui un processo inizia e completa l'esecuzione. I processi completati persistono per un tempo illimitato. Non è possibile accedere ai dati per i processi non completati entro la durata massima.

## <a name="view-batch-ai-quotas"></a>Visualizzare le quote Batch per intelligenza artificiale

Visualizzare le quote di sottoscrizione correnti di Batch per intelligenza artificiale nel [portale di Azure][portal].

1. Nel riquadro a sinistra selezionare **Tutti i servizi**. Quindi cercare **Batch per intelligenza artificiale** e fare clic per aprire il servizio.
2. Fare clic su **Utilizzo + quote** nel menu di Batch per intelligenza artificiale.
3. Selezionare la sottoscrizione per visualizzare i limiti di quota.

## <a name="increase-a-batch-ai-cores-quota"></a>Aumentare una quota di core Batch per intelligenza artificiale

Per richiedere un aumento di quota per la sottoscrizione a Batch per intelligenza artificiale usando il [portale di Azure][portal], seguire questa procedura. 

1. Nel riquadro a sinistra selezionare **Tutti i servizi**. Quindi cercare **Batch per intelligenza artificiale** e fare clic per aprire il servizio.
2. Fare clic su **Nuova richiesta di supporto** nel menu di Batch per intelligenza artificiale.
3. In **Nozioni di base**:
   
    a. **Tipo di problema** > **Quota**
   
    b. **Sottoscrizione** > Selezionare la sottoscrizione.
   
    c. **Tipo di quota** > **Batch per intelligenza artificiale**
   
    d. **Piano di supporto** > Selezionare il piano di supporto.

    Fare clic su **Avanti**.
4. In **Problema**:
   
    a. Selezionare una **Gravità** in base all'[impatto sull'attività aziendale][support_sev].
   
    b. Nei **Dettagli quota**, specificare la posizione, il tipo di quota e il tipo di risorsa. Specificare il nuovo limite che si desidera richiedere. Fare clic su **Salva e continua**.

    c. Facoltativo: caricare tutti i file pertinenti con altre informazioni riguardanti il motivo dell'aumento.
   
    Fare clic su **Avanti**.
5. In **Informazioni contatto**:
   
    a. Selezionare il **metodo di contatto preferito**.
   
    b. Verificare e immettere i dettagli di contatto richiesti.
   
    Fare clic su **Crea** per inviare la richiesta di supporto.

Dopo aver inviato la richiesta di supporto, si verrà contattati dal supporto tecnico di Azure. Per completare la richiesta possono essere necessari fino a 2 giorni lavorativi.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con i limiti di quota, vedere gli articoli seguenti per iniziare a usare Batch per intelligenza artificiale.

> [!div class="nextstepaction"]
> [Esercitazione introduttiva di Batch per intelligenza artificiale](quickstart-tensorflow-training-cli.md)
> [Ricette di Batch per intelligenza artificiale](https://github.com/Azure/BatchAI/tree/master/recipes)
> [Altre informazioni sulle risorse di Batch per intelligenza artificiale](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: http://aka.ms/supportseverity
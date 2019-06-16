---
title: Ottenere più dati, gli elementi o i record con paginazione - App per la logica di Azure
description: Configurare la paginazione di superare il limite di dimensioni di pagina predefinito per azioni del connettore App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64476542"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Ottenere più dati, gli elementi o i record usando la paginazione nelle App per la logica di Azure

Quando si recupera dati, gli elementi o i record usando un'azione connettore nel [Azure Logic Apps](../logic-apps/logic-apps-overview.md), si potrebbero ottenere set di risultati così grande che l'azione non restituisce tutti i risultati nello stesso momento. Con alcune azioni, il numero di risultati può superare dimensioni di pagina predefinite del connettore. In questo caso, l'azione restituisce solo la prima pagina di risultati. Ad esempio, le dimensioni di pagina predefinite per il connettore di SQL Server **Ottieni righe** azione è 2048, ma potrebbe variare in base alle altre impostazioni.

Alcune azioni consentono di attivare un *paginazione* impostazione in modo che l'app per la logica può recuperare più risultati, fino al limite di paginazione, ma restituire i risultati come un singolo messaggio al termine dell'azione. Quando si usa la paginazione, è necessario specificare una *soglia* valore, ovvero il numero di risultati che si desidera l'azione da restituire. L'azione recupera risultati fino a raggiungere la soglia specificata. Quando il numero totale di elementi è inferiore alla soglia specificata, l'azione recupera tutti i risultati.

Attivando l'impostazione di impaginazione Recupera pagine di risultati in base alle dimensioni di pagina del connettore. Questo comportamento significa che in alcuni casi, si potrebbe essere più risultati rispetto alla soglia specificata. Ad esempio, quando si usa SQL Server **Ottieni righe** azione, che supporta la paginazione impostazione:

* Dimensioni predefinite della pagina dell'azione sono 2048 record per ogni pagina.
* Si supponga che vi sono 10.000 record e specificare 5000 record come valore minimo.
* Paginazione recupera pagine di record, in modo da ottenere almeno il valore minimo specificato, l'azione restituisce 6144 record (3 pagine x 2048 record), non 5000 record.

Ecco un elenco con solo alcuni dei connettori in cui è possibile superare le dimensioni di pagina predefinite per azioni specifiche:

* [Archiviazione BLOB di Azure](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'app per la logica e l'azione in cui si desidera attivare la paginazione. Se non si dispone di un'app per la logica, vedere [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Attivare la paginazione

Per determinare se un'azione supporta la paginazione nella finestra di progettazione di App per la logica, controllare le impostazioni dell'azione per il **paginazione** impostazione. Questo esempio viene illustrato come attivare la paginazione in del Server SQL **Ottieni righe** azione.

1. Nell'angolo superiore destro dell'azione, scegliere i puntini di sospensione ( **...** ) e selezionare **impostazioni**.

   ![Aprire le impostazioni dell'azione](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se l'azione supporta la paginazione, l'azione viene illustrato il **paginazione** impostazione.

1. Modifica il **paginazione** impostazione dal **Off** al **su**. Nel **soglia** proprietà, specificare un valore intero per il numero di risultati che si vuole che l'azione da restituire.

   ![Specificare il numero minimo di risultati da restituire](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Al termine, scegliere **Fine**.

## <a name="workflow-definition---pagination"></a>Definizione del flusso di lavoro - paginazione

Quando si attiva la paginazione per un'azione che supporta questa funzionalità, definizione di flusso di lavoro dell'app per la logica include il `"paginationPolicy"` proprietà con il `"minimumItemCount"` proprietà in tale azione `"runtimeConfiguration"` proprietà, ad esempio:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Supporto

In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

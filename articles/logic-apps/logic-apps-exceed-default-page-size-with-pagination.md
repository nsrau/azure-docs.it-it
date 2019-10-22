---
title: Ottenere più elementi o record con la paginazione-app per la logica di Azure
description: Configurare la paginazione per superare il limite di dimensioni di pagina predefinito per le azioni del connettore nelle app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: e86600312490c77ed492cb28a359add0fed90596
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679883"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Ottenere più dati, elementi o record usando l'impaginazione in app per la logica di Azure

Quando si recuperano dati, elementi o record usando un'azione del connettore in [app](../logic-apps/logic-apps-overview.md)per la logica di Azure, è possibile ottenere set di risultati talmente grandi che l'azione non restituisce tutti i risultati nello stesso momento. Con alcune azioni, il numero di risultati può superare le dimensioni di pagina predefinite del connettore. In questo caso, l'azione restituisce solo la prima pagina di risultati. Ad esempio, la dimensione di pagina predefinita per l'azione **Get rows** del connettore SQL Server è 2048, ma può variare in base ad altre impostazioni.

Alcune azioni consentono di attivare un'impostazione di *paginazione* in modo che l'app per la logica possa recuperare più risultati fino al limite di paginazione, ma restituisce tali risultati come un unico messaggio al termine dell'azione. Quando si usa la paginazione, è necessario specificare un valore *soglia* , ovvero il numero di risultati di destinazione che si vuole che l'azione restituisca. L'azione recupera i risultati fino a raggiungere la soglia specificata. Quando il numero totale di elementi è inferiore alla soglia specificata, l'azione recupera tutti i risultati.

Quando si attiva l'impostazione di paginazione, vengono recuperate le pagine di risultati in base alle dimensioni di pagina di un connettore. Questo comportamento significa che talvolta è possibile ottenere più risultati rispetto alla soglia specificata. Ad esempio, quando si usa l'azione SQL Server **Ottieni righe** , che supporta l'impostazione di paginazione:

* Le dimensioni di pagina predefinite dell'azione sono pari a 2048 record per pagina.
* Si supponga di avere 10.000 record e di specificare come minimo i record 5000.
* La paginazione ottiene pagine di record, quindi per ottenere almeno il valore minimo specificato, l'azione restituisce 6144 record (3 pagine x 2048 record), non 5000 record.

Ecco un elenco con solo alcuni dei connettori in cui è possibile superare le dimensioni di pagina predefinite per azioni specifiche:

* [Archivio BLOB di Azure](https://docs.microsoft.com/connectors/azureblob/)
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

* L'app per la logica e l'azione in cui si desidera attivare l'impaginazione. Se non si dispone di un'app per la logica, vedere [Guida introduttiva: creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Attiva paginazione

Per determinare se un'azione supporta la paginazione nella finestra di progettazione dell'app per la logica, controllare le impostazioni dell'azione per l'impostazione di **paginazione** . Questo esempio illustra come attivare l'impaginazione nell'azione **Get rows** del SQL Server.

1. Nell'angolo superiore destro dell'azione scegliere il pulsante con i puntini di sospensione ( **...** ) e selezionare **Impostazioni**.

   ![Aprire le impostazioni dell'azione](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se l'azione supporta la paginazione, l'azione Visualizza l'impostazione di **paginazione** .

1. Modificare l'impostazione di **impaginazione** da **off** a **on**. Nella proprietà **soglia** specificare un valore intero per il numero di risultati di destinazione che si desidera venga restituito dall'azione.

   ![Specificare il numero minimo di risultati da restituire](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Al termine, scegliere **Fine**.

## <a name="workflow-definition---pagination"></a>Definizione del flusso di lavoro-paginazione

Quando si attiva la paginazione per un'azione che supporta questa funzionalità, la definizione del flusso di lavoro dell'app per la logica include la proprietà `"paginationPolicy"` insieme alla proprietà `"minimumItemCount"` nella proprietà `"runtimeConfiguration"` di tale azione, ad esempio:

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

## <a name="get-support"></a>Ottenere supporto

In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

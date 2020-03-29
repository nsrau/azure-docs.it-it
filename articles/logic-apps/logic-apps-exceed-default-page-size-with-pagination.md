---
title: Ottenere più elementi o record con l'impaginazione
description: Configurare l'impaginazione per superare il limite predefinito delle dimensioni di pagina per le azioni del connettore in App per la logica di AzureSet up pagination to exceed the default page size limit for connector actions in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792119"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Ottenere più dati, elementi o record usando l'impaginazione nelle app per la logica di AzureGet more data, items, or records by using pagination in Azure Logic Apps

Quando si recuperano dati, elementi o record usando un'azione del connettore in [App per](../logic-apps/logic-apps-overview.md)la logica di Azure , è possibile ottenere set di risultati così grandi che l'azione non restituisce tutti i risultati contemporaneamente. Con alcune azioni, il numero di risultati potrebbe superare le dimensioni di pagina predefinite del connettore. In questo caso, l'azione restituisce solo la prima pagina di risultati. Ad esempio, le dimensioni di pagina predefinite per l'azione **Ottieni righe** del connettore SQL Server sono 2048, ma possono variare in base ad altre impostazioni.

Alcune azioni consentono di attivare un'impostazione di *paginazione* in modo che l'app per la logica possa recuperare più risultati fino al limite di impaginazione, ma restituire tali risultati come singolo messaggio al termine dell'azione. Quando si utilizza l'impaginazione, è necessario specificare un valore di *soglia,* ovvero il numero di risultati di destinazione che si desidera venga restituito dall'azione. L'azione recupera i risultati fino al raggiungimento della soglia specificata. Quando il numero totale di elementi è inferiore alla soglia specificata, l'azione recupera tutti i risultati.

L'attivazione dell'impostazione di impaginazione consente di recuperare pagine di risultati in base alle dimensioni della pagina di un connettore. Questo comportamento significa che a volte è possibile ottenere più risultati rispetto alla soglia specificata. Ad esempio, quando si utilizza l'azione Ottieni righe di SQL Server, che supporta l'impostazione di impaginazione:For example, when using the SQL Server **Get rows** action, which supports pagination setting:

* Le dimensioni di pagina predefinite dell'azione sono 2048 record per pagina.
* Si supponga di disporre di 10.000 record e di specificare 5000 record come minimo.
* L'impaginazione ottiene pagine di record, in modo da ottenere almeno il minimo specificato, l'azione restituisce 6144 record (3 pagine x record 2048), non 5000 record.

Ecco un elenco con solo alcuni dei connettori in cui è possibile superare le dimensioni di pagina predefinite per azioni specifiche:

* [Archiviazione BLOB di AzureAzure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
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

* L'app per la logica e l'azione in cui vuoi attivare l'impaginazione. Se non si dispone di un'app per la logica, vedere [Guida introduttiva: Creare la prima app per la logica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="turn-on-pagination"></a>Attivare l'impaginazione

Per determinare se un'azione supporta l'impaginazione in Progettazione app per la logica, controllare le impostazioni dell'azione per l'impostazione **Paginazione.** In questo esempio viene illustrato come attivare l'impaginazione nell'azione **Ottieni righe** di SQL Server.

1. Nell'angolo superiore destro dell'azione scegliere il pulsante con i puntini di sospensione (**...**) e selezionare **Impostazioni**.

   ![Aprire le impostazioni dell'azione](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se l'azione supporta l'impaginazione, l'azione mostra l'impostazione **Paginazione.**

1. Modificare l'impostazione **Impaginazione** da **Disattivato** a **Attivato**. Nella proprietà **Soglia** specificare un valore intero per il numero di risultati di destinazione che si desidera venga restituito dall'azione.

   ![Specificare il numero minimo di risultati da restituire](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Al termine, scegliere **Fine**.

## <a name="workflow-definition---pagination"></a>Definizione del flusso di lavoro - impaginazione

Quando attivi l'impaginazione per un'azione che supporta questa funzionalità, `"paginationPolicy"` la definizione `"minimumItemCount"` del flusso di `"runtimeConfiguration"` lavoro dell'app per la logica include la proprietà insieme alla proprietà nella proprietà dell'azione, ad esempio:

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

---
title: File di inclusione
description: File di inclusione
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678274"
---
### <a name="set-up-pagination"></a>Configurare la paginazione

Per alcuni connettori e le relative azioni che consentono di recuperare più elementi, i risultati potrebbero superare le dimensioni di pagina predefinite del connettore. In questo caso, l'azione restituisce solo la prima pagina di risultati. Ad esempio, le dimensioni di pagina predefinite per l'azione **SQL Server - Ottieni righe** sono 2048, ma potrebbero essere diverse in base alle altre impostazioni. Per assicurarsi di ottenere tutti i record, attivare l'impostazione **Paginazione** per l'azione. Con questa impostazione, l'app per la logica richiede al connettore i rimanenti record, ma restituisce tutti i risultati come singolo messaggio al termine dell'azione. 

Ecco solo alcuni dei connettori in cui è possibile attivare la paginazione per azioni specifiche: 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Oracle Database</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

Di seguito è riportato un esempio per l'azione **Ottieni righe**:

1. Per verificare se l'azione supporta la paginazione, aprire le **Impostazioni** dell'azione. 

   ![Aprire "Impostazioni" per l'azione](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. Se l'azione supporta la paginazione, modificare l'impostazione **Paginazione** da **No** a **Sì**. Per assicurarsi che l'azione restituisca un set minimo di risultati, specificare un valore per **Limite**.

   ![Specificare un numero minimo di risultati per l'azione](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. Al termine, scegliere **Fine**.
---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180663"
---
## <a name="append-other-apis"></a>Aggiungere altre API

Un'API può essere composta da API esposte da servizi diversi, tra cui la specifica OpenAPI, un'API SOAP, la funzionalità app per le API di Servizio app di Azure, app per le funzioni di Azure, App per la logica di Azure e Azure Service Fabric.

![Importare un'API](./media/api-management-append-apis/import.png)

Per aggiungere un'API diversa all'API esistente, seguire questa procedura. Quando si importa un'altra API, le operazioni vengono aggiunte all'API corrente.

1. Passare all'istanza di Gestione API di Azure nel portale di Azure.
2. Selezionare **API** nel menu a sinistra.
3. Fare clic su **...** accanto all'API a cui si vuole aggiungere un'altra API.
4. Nel menu a discesa selezionare **Importa**.
5. Selezionare un servizio da cui importare un'API.
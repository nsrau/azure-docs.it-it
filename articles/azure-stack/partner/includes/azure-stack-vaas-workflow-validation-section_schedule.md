---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 11/26/2018
ms.author: mabrigg
ms.openlocfilehash: be9b3b3a9cbb39386d13f76c595ce5e34688adac
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336072"
---
Nei flussi di lavoro di convalida **pianificazione** un test Usa i parametri comuni del flusso di lavoro a livello specificato durante la creazione del flusso di lavoro (vedere [parametri comuni del flusso di lavoro per la convalida dello Stack di Azure come servizio](../azure-stack-vaas-parameters.md)). Se uno qualsiasi dei valori dei parametri di test diventano non valido, è necessario resupply li come indicato nelle [modificare i parametri del flusso di lavoro](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Pianificazione di un test di convalida su un'istanza esistente creerà una nuova istanza al posto di istanza precedente nel portale. I log per l'istanza precedente verranno mantenuti ma non sono accessibili dal portale.  
Una volta che un test è stata completata, il **pianificazione** azione viene disabilitata.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Selezionare **pianificazione** dal menu di scelta rapida per aprire un prompt dei comandi per la pianificazione dell'istanza di test.

1. Esaminare i parametri di test e quindi selezionare **Submit** per il test per l'esecuzione.
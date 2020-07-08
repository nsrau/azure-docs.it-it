---
title: includere il file
description: includere file
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 4dc85988d904fdec72e1e6d92f03582a2a8f1427
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85121609"
---
1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.

1. Nella pagina **Nuovo**, selezionare **Calcolo** > **App per le funzioni**.

1. Nella pagina **nozioni di base** usare le impostazioni dell'app per le funzioni come specificato nella tabella seguente:

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova app per le funzioni. |
    | **[Gruppo di risorse](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome del nuovo gruppo di risorse in cui creare l'app per le funzioni. |
    | **Nome dell'app per le funzioni** | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono `a-z` (senza distinzione tra maiuscole e minuscole), `0-9` e `-`.  |
    |**Pubblica**| Codice | Opzione per la pubblicazione di file di codice o di un contenitore Docker. |
    | **Stack di runtime** | Lingua preferita | Scegliere un runtime che supporti il linguaggio di programmazione della funzione preferito. Scegliere **.NET** per le funzioni C# e F #. |
    |**Area**| Area preferita | Scegliere un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |

    ![Pagina Informazioni di base](./media/functions-premium-create/function-app-create-basics.png)

1. Selezionare **Avanti: hosting**. Nella pagina **hosting** immettere le impostazioni seguenti:

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Account di archiviazione](../articles/storage/common/storage-account-create.md)** |  Nome globalmente univoco |  Creare un account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È anche possibile usare un account esistente, che deve soddisfare i [requisiti dell'account di archiviazione](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Sistema operativo**| Sistema operativo preferito | Viene preselezionato automaticamente un sistema operativo in base alla selezione dello stack di runtime, ma, se necessario, è possibile modificare l'impostazione. Python è supportato solo in Linux. |
    | **[Piano](../articles/azure-functions/functions-scale.md)** | Premium | Piano di hosting che definisce come vengono allocate le risorse all'app per le funzioni. Selezionare **Premium**. Per impostazione predefinita, viene creato un nuovo piano di servizio app. Lo **SKU e la dimensione** predefiniti sono **EP1**, dove EP è l'acronimo di _Elastic Premium_. Per altre informazioni, vedere l' [elenco degli SKU Premium](../articles/azure-functions/functions-premium-plan.md#available-instance-skus).<br/>Quando si eseguono funzioni JavaScript in un piano Premium, è consigliabile scegliere un'istanza con un minor numero di vCPU. Per altre informazioni, vedere [scegliere i piani Premium a core singolo](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions).  |

    ![Pagina Hosting](./media/functions-premium-create/function-app-premium-create-hosting.png)

1. Selezionare **Avanti: monitoraggio**. Nella pagina **monitoraggio** immettere le impostazioni seguenti:

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinito | Crea una risorsa di Application Insights con lo stesso *nome di app* nell'area più vicina supportata. Espandendo questa impostazione, è possibile modificare il **nome della nuova risorsa** o scegliere un **percorso** diverso in una [geografia di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) per archiviare i dati. |

    ![Pagina di monitoraggio](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Selezionare **Rivedi e crea** per rivedere le selezioni effettuate per la configurazione dell'app.

1. Nella pagina **Rivedi e crea** esaminare le impostazioni e quindi selezionare **Crea** per effettuare il provisioning e distribuire l'app per le funzioni.

1. Selezionare l'icona **Notifiche** nell'angolo superiore destro del portale e attendere che venga visualizzato il messaggio **La distribuzione è riuscita**.

1. Selezionare **Vai alla risorsa** per visualizzare la nuova app per le funzioni. È anche possibile selezionare **Aggiungi al dashboard**. In questo modo è più facile tornare in questa risorsa di app per le funzioni dal dashboard.

    ![Notifica della distribuzione](./media/functions-premium-create/function-app-create-notification2.png)

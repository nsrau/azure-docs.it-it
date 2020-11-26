---
title: Creare un'app per le funzioni in Linux dal portale di Azure
description: Informazioni su come creare la prima funzione di Azure in Linux usando il portale di Azure.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 20390239ed58e42749e9a3bae472a2f3f6324bb2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181255"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Creare un'app per le funzioni in Linux in un piano di servizio app di Azure

Funzioni di Azure consente di ospitare le funzioni in Linux in un contenitore di Servizio app di Azure predefinito. Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per creare un'app per le funzioni ospitata in Linux che viene eseguita in un [piano di servizio app](functions-scale.md#app-service-plan). È anche possibile [usare un contenitore personalizzato](functions-create-function-linux-custom-image.md).

![Creare un'app per le funzioni nel portale di Azure](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com> con il proprio account Azure.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni in Linux, è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente per l'esecuzione del codice delle funzioni. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione, il ridimensionamento e la condivisione delle risorse. In questo articolo si crea un piano di servizio app durante la creazione dell'app per le funzioni.

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.

1. Nella pagina **Nuovo**, selezionare **Calcolo** > **App per le funzioni**.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Creare un'app per le funzioni nel portale di Azure":::

1. Nella pagina **Informazioni di base** usare le impostazioni dell'app per le funzioni specificate nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova app per le funzioni. |
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome del nuovo gruppo di risorse in cui creare l'app per le funzioni. |
    | **Nome dell'app per le funzioni** | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono `a-z` (senza distinzione tra maiuscole e minuscole), `0-9` e `-`.  |
    |**Pubblica**| **Codice** (impostazione predefinita) | Opzione per la pubblicazione di file di codice o di un contenitore Docker. |
    | **Stack di runtime** | Lingua preferita | Scegliere un runtime che supporti il linguaggio di programmazione della funzione preferito. Scegliere **.NET Core** per le funzioni C# e F#. |
    |**Versione**| Numero di versione | Scegliere la versione del runtime installato.  |
    |**Area**| Area preferita | Scegliere un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Pagina Informazioni di base":::

1. Selezionare **Avanti: Hosting**. Nella pagina **Hosting** immettere le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Account di archiviazione](../storage/common/storage-account-create.md)** |  Nome globalmente univoco |  Creare un account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È anche possibile usare un account esistente, che deve soddisfare i [requisiti dell'account di archiviazione](../azure-functions/functions-scale.md#storage-account-requirements). |
    |**Sistema operativo**| **Linux** | Viene preselezionato automaticamente un sistema operativo in base alla selezione dello stack di runtime, ma, se necessario, è possibile modificare l'impostazione. |
    | **[Piano](../azure-functions/functions-scale.md)** | **Consumo (serverless)** | Piano di hosting che definisce come vengono allocate le risorse all'app per le funzioni. Nel **piano a consumo** predefinito le risorse vengono aggiunte dinamicamente in base alle esigenze delle funzioni. In questo hosting [serverless](https://azure.microsoft.com/overview/serverless-computing/) si paga solo per il periodo in cui le funzioni sono in esecuzione. In caso di esecuzione in un piano di servizio app, è necessario gestire il [ridimensionamento dell'app per le funzioni](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Pagina Hosting":::

1. Selezionare **Avanti: Monitoraggio**. Nella pagina **Monitoraggio** immettere le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Sì** (impostazione predefinita) | Crea una risorsa di Application Insights con lo stesso *nome di app* nell'area più vicina supportata. Espandendo questa impostazione o selezionando **Crea nuovo**, è possibile cambiare il nome Application Insights oppure scegliere una regione diversa in un'[area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui archiviare i dati. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Pagina di monitoraggio":::

1. Selezionare **Rivedi e crea** per rivedere le selezioni effettuate per la configurazione dell'app.

1. Nella pagina **Rivedi e crea** esaminare le impostazioni e quindi selezionare **Crea** per effettuare il provisioning e distribuire l'app per le funzioni.

1. Selezionare l'icona **Notifiche** nell'angolo superiore destro del portale e attendere che venga visualizzato il messaggio **La distribuzione è riuscita**.

1. Selezionare **Vai alla risorsa** per visualizzare la nuova app per le funzioni. È anche possibile selezionare **Aggiungi al dashboard**. In questo modo è più facile tornare in questa risorsa di app per le funzioni dal dashboard.

    ![Notifica della distribuzione](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Anche dopo che l'app per le funzioni è disponibile, l'inizializzazione completa potrebbe richiedere alcuni minuti.

Si creerà ora una funzione nella nuova app per le funzioni.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Creare una funzione Trigger HTTP

Questa sezione illustra come creare una funzione nella nuova app per le funzioni nel portale.

> [!NOTE]
> L'esperienza di sviluppo nel portale può essere utile per provare Funzioni di Azure. Per la maggior parte degli scenari è consigliabile sviluppare le funzioni in locale e pubblicare il progetto nell'app per le funzioni usando [Visual Studio Code](./create-first-function-vs-code-csharp.md#create-an-azure-functions-project) o [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. Nel menu a sinistra della finestra **Funzioni**, selezionare **Funzioni**, quindi **Aggiungi** nel menu in alto. 
 
1. Nella finestra **Nuova funzione**, selezionare **Trigger HTTP**.

    ![Scegliere la funzione Trigger HTTP](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. Nella finestra **Nuova funzione**, accettare il nome predefinito per la **nuova funzione** oppure immettere un nuovo nome. 

1. Scegliere **Anonimo** nell'elenco a discesa **Livello di autorizzazione**, quindi selezionare **Crea funzione**.

    A questo punto Azure crea la funzione Trigger HTTP. Ora è possibile eseguire la nuova funzione inviando una richiesta HTTP.

## <a name="test-the-function"></a>Testare la funzione

1. Nella nuova funzione Trigger HTTP, selezionare **Codice + test** dal menu a sinistra, quindi selezionare **Ottieni URL funzione** dal menu in alto.

    ![Selezionare Ottieni URL funzione](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. Nella finestra di dialogo **Ottieni URL funzione**, selezionare **Predefinito** dall'elenco a discesa, quindi l'icona **Copia negli Appunti**. 

    ![Creare l'URL della funzione dal portale di Azure](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Incollare l'URL della funzione nella barra degli indirizzi del browser. Aggiungere il valore della stringa di query `?name=<your_name>` alla fine dell'URL e premere INVIO per eseguire la richiesta. 

    L'esempio seguente mostra la risposta nel browser:

    ![Risposta della funzione nel browser.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    L'URL della richiesta include una chiave necessaria per impostazione predefinita per accedere a una funzione tramite HTTP.

1. Quando viene eseguita la funzione, vengono scritte nei log informazioni di traccia. Per visualizzare l'output di traccia, tornare alla pagina **Codice + test** nel portale ed espandere la freccia **Log** nella parte inferiore della pagina.

   ![Visualizzatore log di Funzioni nel portale di Azure.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata creata un'app per le funzioni con una funzione Trigger HTTP semplice.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Per altre informazioni, vedere [Associazioni HTTP e webhook in Funzioni di Azure](functions-bindings-http-webhook.md).
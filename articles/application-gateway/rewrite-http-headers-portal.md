---
title: Riscrivere le intestazioni di richiesta e risposta HTTP con il Gateway applicazione di Azure - portale di Azure | Microsoft Docs
description: Informazioni su come usare il portale di Azure per configurare un Gateway applicazione di Azure per riscrivere le intestazioni HTTP nelle richieste e risposte passa attraverso il gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947154"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Riscrivere le intestazioni di richiesta e risposta HTTP con il Gateway applicazione di Azure - portale di Azure

Questo articolo descrive come usare il portale di Azure per configurare un [SKU di Gateway applicazione v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) istanza riscrivere le intestazioni HTTP nelle richieste e risposte.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario disporre di un'istanza SKU di Gateway applicazione v2 per completare la procedura descritta in questo articolo. Riscrivere le intestazioni non è supportato nello SKU v1. Se non è disponibile lo SKU v2, creare un [SKU di Gateway applicazione v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) istanza prima di iniziare.

## <a name="create-required-objects"></a>Creare gli oggetti richiesti

Per configurare riscrittura dell'intestazione HTTP, è necessario completare questi passaggi.

1. Creare gli oggetti che sono necessari per la riscrittura di intestazione HTTP:

   - **Riscrivere azione**: Utilizzato per specificare la richiesta e campi di intestazione di richiesta che si prevede di riscrittura e il nuovo valore per le intestazioni. È possibile associare uno o più condizioni con un'azione di riscrittura di riscrittura.

   - **Riscrivere condizione**: Configurazione facoltativa. Le condizioni di riscrittura di valutare il contenuto di richieste HTTP (S) e risposte. L'azione di riscrittura verificherà se la richiesta HTTP (S) o la risposta soddisfa la condizione di riscrittura.

     Se si associa più di una condizione con un'azione, l'azione si verifica solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione con AND logica.

   - **Regola di riscrittura**: Contiene più azioni di riscrittura / riscrivere le combinazioni di condizione.

   - **Sequenza di regole**: Consente di determinare l'ordine in cui vengono eseguite le regole di riscrittura. Questa configurazione è utile quando sono presenti più regole di riscrittura in un set di riscrittura. Una regola di riscrittura con un valore di sequenza più basso di regole viene eseguito per prima. Se si assegna lo stesso valore di sequenza di regole da due regole di riscrittura, l'ordine di esecuzione è non deterministico.

   - **Riscrivere set**: Contiene più regole di riscrittura degli indirizzi che verranno associate a una regola di routing di richiesta.

2. Collegare la riscrittura impostata su una regola di routing. La configurazione di riscrittura è allegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura di intestazione è associata a un listener di origine ed è una riscrittura intestazione globale. Quando si usa una regola di routing basato sul percorso, la configurazione di riscrittura di intestazione è definita nel mapping del percorso URL. In tal caso, si applica solo all'area di percorso specifico di un sito.

È possibile creare più set di riscrittura dell'intestazione HTTP e applicare ogni riscrittura impostato su più listener. Ma è possibile applicare solo una riscrittura impostato su un listener specifico.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al [portale di Azure](https://portal.azure.com/) con il proprio account Azure.

## <a name="configure-header-rewrite"></a>Configurare riscrittura di intestazione

In questo esempio, modifichiamo un URL di reindirizzamento riscrivendo l'intestazione location nella risposta HTTP inviata da un'applicazione back-end.

1. Selezionare **tutte le risorse**e quindi selezionare il gateway applicazione.

2. Selezionare **riscrive** nel riquadro sinistro.

3. Selezionare **riscrivere set**:

   ![Aggiungi set di riscrittura](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Specificare un nome per il set di riscrittura e associarlo a una regola di routing:

   - Immettere un nome per la riscrittura impostato **nome** casella.
   - Selezionare uno o più delle regole elencate nella **associate regole di routing** elenco. È possibile selezionare solo le regole che non sono associate con gli altri set di riscrittura. Vengono visualizzati in grigio le regole che sono già state associate con altri set di riscrittura.
   - Selezionare **Avanti**.
   
     ![Aggiungere nome e l'associazione](media/rewrite-http-headers-portal/name-and-association.png)

5. Creare una regola di riscrittura degli indirizzi:

   - Selezionare **Aggiungi regola di riscrittura**.

     ![Aggiungi regola di riscrittura](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Immettere un nome per la regola di riscrittura nel **nome della regola di riscrittura** casella. Immettere un numero nel **regola sequenza** casella.

     ![Aggiungere il nome di regola di riscrittura](media/rewrite-http-headers-portal/rule-name.png)

6. In questo esempio, si sarà riscrivere l'intestazione location solo quando contiene un riferimento a azurewebsites.net. A tale scopo, aggiungere una condizione da valutare se l'intestazione location nella risposta contiene azurewebsites.net:

   - Selezionare **Aggiungi condizione** e quindi selezionare la casella che contiene il **se** istruzioni per espanderlo.

     ![Aggiungere una condizione](media/rewrite-http-headers-portal/add-condition.png)

   - Nel **tipo di variabile da controllare** elenco, selezionare **intestazione HTTP**.

   - Nel **tipo di intestazione** elenco, selezionare **risposta**.

   - Poiché in questo esempio vengono valutati l'intestazione location, che è un'intestazione comune, selezionare **intestazione Common** sotto **nome intestazione**.

   - Nel **intestazione Common** elenco, selezionare **posizione**.

   - Sotto **distinzione maiuscole/minuscole**, selezionare **No**.

   - Nel **Operator** elenco, selezionare **uguale (=)**.

   - Immettere un modello di espressione regolare. In questo esempio, si userà il modello `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Selezionare **OK**.

     ![Configurare un blocco If condizione](media/rewrite-http-headers-portal/condition.png)

7. Aggiungere un'azione per riscrivere l'intestazione location:

   - Nel **tipo di azione** elenco, selezionare **impostare**.

   - Nel **tipo di intestazione** elenco, selezionare **risposta**.

   - Sotto **nome dell'intestazione**, selezionare **intestazione Common**.

   - Nel **intestazione Common** elenco, selezionare **posizione**.

   - Immettere il valore dell'intestazione. In questo esempio, si userà `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come il valore dell'intestazione. Questo valore sostituirà *azurewebsites.net* con *contoso.com* nell'intestazione location.

   - Selezionare **OK**.

     ![Aggiungere un'azione](media/rewrite-http-headers-portal/action.png)

8. Selezionare **Create** per creare la riscrittura set:

   ![Selezionare Crea](media/rewrite-http-headers-portal/create.png)

9. Verrà aperta la visualizzazione del set di riscrittura. Verificare che il set di riscrittura creato sia nell'elenco dei set di riscrittura degli indirizzi:

   ![Visualizzazione del set di riscrittura](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come configurare alcuni casi d'uso comuni, vedere [intestazione common riscrivere scenari](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).
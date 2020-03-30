---
title: Riscrivere le intestazioni di richiesta e risposta HTTP nel portale - Gateway applicazione di AzureRewrite HTTP request and response headers in portal - Azure Application Gateway
description: Informazioni su come usare il portale di Azure per configurare un gateway applicazione di Azure per riscrivere le intestazioni HTTP nelle richieste e nelle risposte che passano attraverso il gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74012849"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Riscrivere le intestazioni di richiesta e risposta HTTP con il gateway applicazione di Azure - Portale di AzureRewrite HTTP request and response headers with Azure Application Gateway - Azure portal

Questo articolo descrive come usare il portale di Azure per configurare un'istanza [dello SKU del gateway applicazione v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) per riscrivere le intestazioni HTTP nelle richieste e nelle risposte.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario disporre di un'istanza SKU del gateway applicazione v2 per completare i passaggi descritti in questo articolo. La riscrittura delle intestazioni non è supportata nello SKU v1. Se non si dispone dello SKU v2, creare un'istanza [dello SKU del gateway applicazione v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) prima di iniziare.

## <a name="create-required-objects"></a>Creare gli oggetti necessari

Per configurare la riscrittura dell'intestazione HTTP, è necessario completare questi passaggi.

1. Creare gli oggetti necessari per la riscrittura dell'intestazione HTTP:Create the objects that are required for HTTP header rewrite:

   - **Azione di riscrittura:** utilizzata per specificare i campi di richiesta e intestazione richiesta che si desidera riscrivere e il nuovo valore per le intestazioni. È possibile associare una o più condizioni di riscrittura a un'azione di riscrittura.

   - **Riscrittura: una**configurazione facoltativa. Le condizioni di riscrittura valutano il contenuto delle richieste e delle risposte HTTP(S). L'azione di riscrittura si verificherà se la richiesta o la risposta HTTP(S) corrisponde alla condizione di riscrittura.

     Se si associano più condizioni a un'azione, l'azione viene eseguita solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione AND logica.

   - **Regola di riscrittura**: Contiene più combinazioni di azioni/condizioni di riscrittura.

   - **Sequenza delle**regole : consente di determinare l'ordine di esecuzione delle regole di riscrittura. Questa configurazione è utile quando si dispone di più regole di riscrittura in un set di riscrittura. Una regola di riscrittura con un valore di sequenza di regole inferiore viene eseguita per prima. Se si assegna lo stesso valore di sequenza di regole a due regole di riscrittura, l'ordine di esecuzione non è deterministico.

   - **Rewrite set**: Contiene più regole di riscrittura che verranno associate a una regola di routing delle richieste.

2. Collegare il set di riscrittura a una regola di routing. La configurazione di riscrittura viene collegata al listener di origine tramite la regola di routing. Quando si utilizza una regola di routing di base, la configurazione di riscrittura dell'intestazione è associata a un listener di origine ed è una riscrittura dell'intestazione globale. Quando si utilizza una regola di routing basata sul percorso, la configurazione di riscrittura dell'intestazione viene definita nella mappa del percorso URL. In tal caso, si applica solo all'area del percorso specifico di un sito.

È possibile creare più set di riscrittura dell'intestazione HTTP e applicare ogni set di riscrittura a più listener. È tuttavia possibile applicare un solo set di riscrittura a un listener specifico.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/) con il proprio account Azure.

## <a name="configure-header-rewrite"></a>Configurare la riscrittura dell'intestazione

In questo esempio verrà modificato un URL di reindirizzamento riscrivendo l'intestazione del percorso nella risposta HTTP inviata da un'applicazione back-end.

1. Selezionare **Tutte le risorse**e quindi selezionare il gateway applicazione.

2. Selezionare **Riscritture** nel riquadro sinistro.

3. Selezionare **Riscrivi set**:

   ![Aggiungi set di riscrittura](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Specificare un nome per il set di riscrittura e associarlo a una regola di routing:Provide a name for the rewrite set and associate it with a routing rule:

   - Immettere il nome per il set di riscrittura nella casella **Nome.**
   - Selezionare una o più regole elencate nell'elenco **Regole di routing associate.** È possibile selezionare solo le regole che non sono state associate ad altri set di riscrittura. Le regole che sono già state associate ad altri set di riscrittura sono disattivate.
   - Fare clic su **Avanti**.
   
     ![Aggiungi nome e associazione](media/rewrite-http-headers-portal/name-and-association.png)

5. Creare una regola di riscrittura:Create a rewrite rule:

   - Selezionare Aggiungi regola di **riscrittura**.

     ![Aggiungi regola di riscrittura](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Immettere un nome per la regola di riscrittura nella casella Nome regola di **riscrittura.** Immettere un numero nella casella **Sequenza regole.**

     ![Aggiungere il nome della regola di riscrittura](media/rewrite-http-headers-portal/rule-name.png)

6. In questo esempio, riscriveremo l'intestazione della posizione solo quando contiene un riferimento a azurewebsites.net. A tale scopo, aggiungere una condizione per valutare se l'intestazione di posizione nella risposta contiene azurewebsites.net:

   - Selezionare **Aggiungi condizione,** quindi selezionare la casella contenente le istruzioni **If** per espanderla.

     ![Aggiungere una condizione](media/rewrite-http-headers-portal/add-condition.png)

   - Nell'elenco **Tipo di variabile da controllare** selezionare **Intestazione HTTP**.

   - Nell'elenco **Tipo di intestazione** selezionare **Risposta**.

   - Poiché in questo esempio viene valutata l'intestazione della posizione, che è un'intestazione comune, selezionare **Intestazione comune** in **Nome intestazione**.

   - Nell'elenco **Intestazione comune** selezionare **Posizione**.

   - In **Maiuscole/minuscole**selezionare **No**.

   - Nell'elenco **Operatore** selezionare **uguale (-)**.

   - Immettere un modello di espressione regolare. In questo esempio, useremo `(https?):\/\/.*azurewebsites\.net(.*)$`il modello .

   - Selezionare **OK**.

     ![Configurare una condizione If](media/rewrite-http-headers-portal/condition.png)

7. Aggiungere un'azione per riscrivere l'intestazione della posizione:Add an action to rewrite the location header:

   - Nell'elenco **Tipo di azione** selezionare **Imposta**.

   - Nell'elenco **Tipo di intestazione** selezionare **Risposta**.

   - In **Nome intestazione**selezionare **Intestazione comune**.

   - Nell'elenco **Intestazione comune** selezionare **Posizione**.

   - Immettere il valore dell'intestazione. In questo esempio, useremo `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come valore dell'intestazione. Questo valore sostituirà *azurewebsites.net* con *contoso.com* nell'intestazione dell'ubicazione.

   - Selezionare **OK**.

     ![Aggiungere un'azione](media/rewrite-http-headers-portal/action.png)

8. Selezionare Crea per creare il set di riscrittura:Select **Create** to create the rewrite set:

   ![Selezionare Crea](media/rewrite-http-headers-portal/create.png)

9. Si aprirà la vista Del set di riscrittura. Verificare che il set di riscrittura creato sia presente nell'elenco dei set di riscrittura:

   ![Riscrivere la visualizzazione set](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come impostare alcuni casi d'uso comuni, vedere Scenari comuni di [riscrittura delle intestazioni](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).
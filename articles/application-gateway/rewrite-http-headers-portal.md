---
title: Riscrivere le intestazioni di richiesta e risposta HTTP nel portale applicazione Azure gateway
description: Informazioni su come usare il portale di Azure per configurare un gateway applicazione Azure per riscrivere le intestazioni HTTP nelle richieste e nelle risposte che passano attraverso il gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012849"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Riscrivere le intestazioni di richiesta e risposta HTTP con applicazione Azure gateway-portale di Azure

Questo articolo descrive come usare la portale di Azure per configurare un'istanza di [SKU del gateway applicazione V2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) per riscrivere le intestazioni HTTP in richieste e risposte.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare i passaggi di questo articolo, è necessario disporre di un'istanza di SKU del gateway applicazione V2. La riscrittura delle intestazioni non è supportata nello SKU V1. Se non si ha lo SKU V2, creare un'istanza di [SKU del gateway applicazione V2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) prima di iniziare.

## <a name="create-required-objects"></a>Creazione di oggetti necessari

Per configurare la riscrittura dell'intestazione HTTP, è necessario completare questi passaggi.

1. Creare gli oggetti necessari per la riscrittura dell'intestazione HTTP:

   - **Azione di riscrittura**: utilizzata per specificare i campi di richiesta e di intestazione della richiesta che si desidera riscrivere e il nuovo valore per le intestazioni. È possibile associare una o più condizioni di riscrittura con un'azione di riscrittura.

   - **Condizione di riscrittura**: una configurazione facoltativa. Le condizioni di riscrittura valutano il contenuto delle richieste e delle risposte HTTP (S). L'azione di riscrittura si verificherà se la richiesta o la risposta HTTP (S) corrisponde alla condizione di riscrittura.

     Se si associa più di una condizione a un'azione, l'azione si verifica solo quando vengono soddisfatte tutte le condizioni. In altre parole, l'operazione è un'operazione AND logica.

   - **Regola di riscrittura**: contiene più combinazioni di operazioni di riscrittura/riscrittura delle condizioni.

   - **Sequenza di regole**: consente di determinare l'ordine in cui vengono eseguite le regole di riscrittura. Questa configurazione è utile quando si dispone di più regole di riscrittura in un set di riscrittura. Viene eseguita prima una regola di riscrittura con un valore di sequenza di regole inferiore. Se si assegna lo stesso valore di sequenza della regola a due regole di riscrittura, l'ordine di esecuzione è non deterministico.

   - **Rewrite set**: contiene più regole di riscrittura che saranno associate a una regola di routing delle richieste.

2. Alleghi il set di riscrittura a una regola di routing. La configurazione di riscrittura è collegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione dell'intestazione di riscrittura è associata a un listener di origine ed è una riscrittura dell'intestazione globale. Quando si usa una regola di routing basata sul percorso, la configurazione dell'intestazione di riscrittura è definita nella mappa del percorso URL. In tal caso, si applica solo all'area del percorso specifica di un sito.

È possibile creare più set di riscrittura dell'intestazione HTTP e applicare ciascun set di riscrittura a più listener. È tuttavia possibile applicare un solo set di riscrittura a un listener specifico.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/) con il proprio account Azure.

## <a name="configure-header-rewrite"></a>Configura riscrittura intestazione

In questo esempio verrà modificato un URL di reindirizzamento riscrivendo l'intestazione Location nella risposta HTTP inviata da un'applicazione back-end.

1. Selezionare **tutte le risorse**e quindi selezionare il gateway applicazione.

2. Selezionare **riscritture** nel riquadro sinistro.

3. Selezionare il **set di riscrittura**:

   ![Aggiungi riscrittura set](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Specificare un nome per il set di riscrittura e associarlo a una regola di routing:

   - Immettere il nome del set di riscrittura nella casella **nome** .
   - Selezionare una o più regole elencate nell'elenco regole di **routing associate** . È possibile selezionare solo le regole che non sono state associate ad altri set di riscrittura. Le regole già associate ad altri set di riscrittura sono visualizzate in grigio.
   - Selezionare **Avanti**.
   
     ![Aggiungi nome e associazione](media/rewrite-http-headers-portal/name-and-association.png)

5. Creare una regola di riscrittura:

   - Selezionare **Aggiungi regola di riscrittura**.

     ![Aggiungi regola di riscrittura](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Immettere un nome per la regola di riscrittura nella casella **Riscrivi nome regola** . Immettere un numero nella casella **sequenza regole** .

     ![Aggiungi nome regola di riscrittura](media/rewrite-http-headers-portal/rule-name.png)

6. In questo esempio l'intestazione Location verrà riscritta solo quando contiene un riferimento a azurewebsites.net. A tale scopo, aggiungere una condizione per valutare se l'intestazione Location nella risposta contiene azurewebsites.net:

   - Selezionare **Aggiungi condizione** , quindi selezionare la casella contenente le istruzioni **if** per espanderla.

     ![Add a condition](media/rewrite-http-headers-portal/add-condition.png)

   - Nell'elenco **tipo di variabile da controllare** selezionare **intestazione HTTP**.

   - Nell'elenco **tipo di intestazione** selezionare **risposta**.

   - Poiché in questo esempio si sta valutando l'intestazione Location, che è un'intestazione comune, selezionare **intestazione comune** in **nome intestazione**.

   - Nell'elenco di **intestazioni comuni** selezionare **location**.

   - In **distinzione tra maiuscole**e minuscole selezionare **No**.

   - Nell'elenco **operatore** selezionare uguale a **(=)** .

   - Immettere un modello di espressione regolare. In questo esempio verrà usato il modello `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Selezionare **OK**.

     ![Configurare una condizione If](media/rewrite-http-headers-portal/condition.png)

7. Aggiungere un'azione per riscrivere l'intestazione del percorso:

   - Nell'elenco **tipo di azione** selezionare **imposta**.

   - Nell'elenco **tipo di intestazione** selezionare **risposta**.

   - In **nome intestazione**selezionare **intestazione comune**.

   - Nell'elenco di **intestazioni comuni** selezionare **location**.

   - Immettere il valore dell'intestazione. In questo esempio si userà `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come valore di intestazione. Questo valore sostituirà *azurewebsites.NET* con *contoso.com* nell'intestazione Location.

   - Selezionare **OK**.

     ![Aggiungere un'azione](media/rewrite-http-headers-portal/action.png)

8. Selezionare **Crea** per creare il set di riscrittura:

   ![Selezionare Crea](media/rewrite-http-headers-portal/create.png)

9. Viene visualizzata la visualizzazione set di riscrittura. Verificare che il set di riscrittura creato sia presente nell'elenco dei set di riscrittura:

   ![Riscrivi visualizzazione set](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come configurare alcuni casi d'uso comuni, vedere [scenari comuni di riscrittura dell'intestazione](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).
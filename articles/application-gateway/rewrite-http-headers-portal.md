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
ms.openlocfilehash: 16f59ecaf934813890cd25e89896182378f376ed
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549068"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Riscrivere le intestazioni di richiesta e risposta HTTP con il Gateway applicazione di Azure - portale di Azure

Questo articolo illustra come usare il portale di Azure per configurare un [SKU di Gateway applicazione v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) riscrivere le intestazioni HTTP nelle richieste e risposte.

> [!IMPORTANT]
> Lo SKU del gateway applicazione con scalabilità automatica e ridondanza della zona è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario avere un v2 Application Gateway SKU poiché l'intestazione della funzionalità di riscrittura non è supportato per lo SKU v1. Se non è disponibile lo SKU v2, creare un [SKU di Gateway applicazione v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) prima di iniziare.

## <a name="what-is-required-to-rewrite-a-header"></a>Che cosa è necessaria riscrivere un'intestazione

Per configurare riscrittura dell'intestazione HTTP, è necessario:

1. Creare i nuovi oggetti necessari per riscrivere le intestazioni http:

   - **Riscrivere azione**: consente di specificare la richiesta e campi di intestazione di richiesta che si prevede di riscrittura e il nuovo valore che dovranno essere riscritte per le intestazioni originale. È possibile scegliere di associare uno o più condizioni di riscrittura con un'azione di riscrittura.

   - **Riscrivere condizione**: È una configurazione facoltativa. Se viene aggiunta una condizione di riscrittura, valuterà il contenuto delle richieste HTTP (S) e le risposte. La decisione di eseguire l'azione di riscrittura associato alla condizione di riscrittura si baseranno se la richiesta HTTP (S) o la risposta corrispondente con la condizione di riscrittura. 

     Se più condizioni sono associati a un'azione, quindi l'azione verrà eseguita solo quando vengono soddisfatte tutte le condizioni, ad esempio, verrà eseguita un'operazione con AND logica.

   - **Regola di riscrittura**: regola di riscrittura contiene più azioni di riscrittura - riscrive le combinazioni di condizione.

   - **Sequenza di regole**: consente di determinare l'ordine in cui le diverse regole di riscrittura venga eseguito. Ciò risulta utile quando sono presenti più regole di riscrittura in un set di riscrittura. La regola di riscrittura con valore di sequenza minore regola Ottiene eseguita per prime. Se si fornisce la stessa sequenza di regole da due regole di riscrittura, l'ordine di esecuzione sarà non deterministica.

   - **Riscrivere Set**: contiene più regole di riscrittura degli indirizzi che verrà associate a una regola di routing di richiesta.

2. È necessario collegare la riscrittura impostato con una regola di routing. Questo avviene perché la configurazione di riscrittura è allegata al listener di origine tramite la regola di routing. Quando si usa una regola di routing di base, la configurazione di riscrittura delle intestazioni viene associata a un listener di origine e la riscrittura è globale. Quando viene usata una regola di routing basata sul percorso, la configurazione di riscrittura delle intestazioni è definita sulla mappa del percorso URL. Pertanto, si applica solo all'area del percorso specifico di un sito.

È possibile creare più set di riscrittura dell'intestazione http e ogni set di riscrittura degli indirizzi può essere applicato a più listener. Tuttavia, è possibile applicare solo una riscrittura impostato su un listener specifico.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al [portale di Azure](https://portal.azure.com/) con il proprio account Azure.

## <a name="configure-header-rewrite"></a>Configurare riscrittura di intestazione

In questo esempio, si modificherà l'URL di reindirizzamento riscrivendo l'intestazione location nella risposta http inviata dall'applicazione back-end. 

1. Selezionare **tutte le risorse**e quindi selezionare il gateway applicazione.

2. Selezionare **riscrive** nel menu a sinistra.

3. Fare clic su **+ riscrivere set**. 

   ![Aggiungi set di riscrittura](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Specificare il nome da set di riscrittura e associarlo a una regola di routing:

   - Immettere il nome della riscrittura di cui il **nome** nella casella di testo.
   - Selezionare una o più regole elencate nella **associate regole di routing** elenco. È possibile selezionare solo le regole che non sono state associate con altri set di riscrittura. Le regole che sono già state associate con altri set di riscrittura verranno disattivate.
   - Fare clic su Avanti.
   
     ![Aggiungere nome e l'associazione](media/rewrite-http-headers-portal/name-and-association.png)

5. Creare una regola di riscrittura degli indirizzi:

   - Fare clic su **+ Aggiungi regola di riscrittura**.![ Aggiungi regola di riscrittura](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - Specificare un nome alla regola di riscrittura nella casella di testo Nome regola riscrittura e fornire una sequenza di regole.![Aggiungere il nome della regola](media/rewrite-http-headers-portal/rule-name.png)

6. In questo esempio, abbiamo riscriverà l'intestazione location solo quando contiene un riferimento a "azurewebsites.net". A tale scopo, aggiungere una condizione da valutare se l'intestazione location nella risposta contiene azurewebsites.net:

   - Fare clic su **+ Aggiungi condizione** e quindi fare clic sulla sezione con i **se** istruzioni a esso. espandere![ Aggiungere il nome della regola](media/rewrite-http-headers-portal/add-condition.png)

   - Selezionare **intestazione HTTP** dalle **tipo di variabile da controllare** elenco a discesa. 

   - Selezionare **tipo di intestazione** come **risposta**.

   - Poiché in questo esempio, si sta valutando l'intestazione location che in questo caso da un'intestazione comune, seleziona **intestazione Common** pulsante di opzione come le **nome intestazione**.

   - Selezionare **ubicazione** dalle **intestazione Common** elenco a discesa.

   - Selezionare **No** come la **distinzione maiuscole/minuscole** impostazione.

   - Selezionare **uguale (=)** dalle **operatore** elenco a discesa.

   - Immettere il criterio di espressione regolare. In questo esempio, si userà il modello `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Fare clic su **OK**.

     ![Modificare intestazione location](media/rewrite-http-headers-portal/condition.png)

7. Aggiungere un'azione per riscrivere l'intestazione location:

   - Selezionare **impostata** come la **tipo di azione**.

   - Selezionare **risposta** come la **tipo di intestazione**.

   - Selezionare **intestazione Common** come la **nome intestazione**.

   - Selezionare **ubicazione** dalle **intestazione Common** elenco a discesa.

   - Immettere il valore dell'intestazione. In questo esempio, si userà `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` come il valore dell'intestazione. In questo sostituirà *azurewebsites.net* con *contoso.com* nell'intestazione location.

   - Fare clic su **OK**.

     ![Modificare intestazione location](media/rewrite-http-headers-portal/action.png)

8. Fare clic su **Create** per creare la riscrittura set.

   ![Modificare intestazione location](media/rewrite-http-headers-portal/create.png)

9. Si verrà reindirizzati alla visualizzazione di set di riscrittura. Verificare che il set di riscrittura creato in precedenza è presente nell'elenco dei set di riscrittura.

   ![Modificare intestazione location](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla configurazione necessaria per eseguire alcuni dei casi d'uso, vedere [intestazione common riscrivere scenari](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

   

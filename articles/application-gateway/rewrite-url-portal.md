---
title: Riscrivere l'URL e la stringa di query con applicazione Azure gateway-portale di Azure
description: Informazioni su come usare la portale di Azure per configurare un gateway applicazione Azure per riscrivere l'URL e la stringa di query
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 7/16/2020
ms.author: surmb
ms.openlocfilehash: ec58c6f97efdbcb91071bcea98bbbc614833246d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215774"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal-preview"></a>Riscrivere l'URL con applicazione Azure gateway-portale di Azure (anteprima)

Questo articolo descrive come usare la portale di Azure per configurare un'istanza di [SKU del gateway applicazione V2](application-gateway-autoscaling-zone-redundant.md) per riscrivere l'URL.

>[!NOTE]
> La funzionalità di riscrittura URL è in anteprima ed è disponibile solo per Standard_v2 e WAF_v2 SKU del gateway applicazione. Non è consigliato per l'uso nell'ambiente di produzione. Per ulteriori informazioni sulle anteprime, vedere [le condizioni per l'utilizzo qui](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare i passaggi di questo articolo, è necessario disporre di un'istanza di SKU del gateway applicazione V2. La riscrittura dell'URL non è supportata nello SKU V1. Se non si ha lo SKU V2, creare un'istanza di [SKU del gateway applicazione V2](tutorial-autoscale-ps.md) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/) con il proprio account Azure.

## <a name="configure-url-rewrite"></a>Configurare la riscrittura dell'URL

Nell'esempio seguente, ogni volta che l'URL della richiesta contiene */article*, il percorso URL e la stringa di query dell'URL vengono riscritti

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. Selezionare **tutte le risorse**e quindi selezionare il gateway applicazione.

2. Selezionare **riscritture** nel riquadro sinistro.

3. Selezionare il **set di riscrittura**:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="Aggiungi riscrittura set":::

4. Specificare un nome per il set di riscrittura e associarlo a una regola di routing:

    a. Immettere il nome del set di riscrittura nella casella **nome** .
    
    b. Selezionare una o più regole elencate nell'elenco regole di **routing associate** . Viene utilizzato per associare la configurazione di riscrittura al listener di origine tramite la regola di routing. È possibile selezionare solo le regole di routing che non sono state associate ad altri set di riscrittura. Le regole già associate ad altri set di riscrittura sono disattivate.
    
    c. Selezionare **Avanti**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="Aggiungi riscrittura set":::

5. Creare una regola di riscrittura:

    a. Selezionare **Aggiungi regola di riscrittura**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="Aggiungi riscrittura set":::
    
    b. Immettere un nome per la regola di riscrittura nella casella **Riscrivi nome regola** . Immettere un numero nella casella **sequenza regole** .

6. In questo esempio verrà riscritta la stringa di query URL e percorso URL solo quando il percorso contiene */article*. A tale scopo, aggiungere una condizione per valutare se il percorso URL contiene */article*

    a. Selezionare **Aggiungi condizione** , quindi selezionare la casella contenente le istruzioni **if** per espanderla.
    
    b. Poiché in questo esempio si vuole controllare il pattern */article* nel percorso URL, selezionare **variabile server**nell'elenco **tipo di variabile da verificare** .
    
    c. Nell'elenco **variabile server** selezionare uri_path
    
    d. In **distinzione tra maiuscole**e minuscole selezionare **No**.
    
    e. Nell'elenco **operatore** selezionare uguale a **(=)**.
    
    f. Immettere un modello di espressione regolare. In questo esempio verrà usato il modello `.*article/(.*)/(.*)`
    
      () viene usato per acquisire la sottostringa per un uso successivo nella composizione dell'espressione per la riscrittura del percorso URL. Per altre informazioni, vedere [qui](rewrite-http-headers-url.md#capturing).

    g. Selezionare **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Aggiungi riscrittura set":::

 

7. Aggiungere un'azione per riscrivere l'URL e il percorso URL

   a. Nell'elenco **Riscrivi tipo** selezionare **URL**.

   b. Nell'elenco **tipo di azione** selezionare **imposta**.

   c. In **componenti**selezionare il **percorso URL e la stringa di query URL**

   d. Nel **valore percorso URL**immettere il nuovo valore del percorso. In questo esempio si userà **/article.aspx** 

   e. Nel **valore stringa di query URL**immettere il nuovo valore della stringa di query dell'URL. In questo esempio si userà **ID = {var_uri_path_1} &title = {var_uri_path_2}**
    
    `{var_uri_path_1}` e `{var_uri_path_1}` vengono utilizzati per recuperare le sottostringhe acquisite durante la valutazione della condizione in questa espressione `.*article/(.*)/(.*)`
    
   f. Selezionare **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="Aggiungi riscrittura set":::

8. Fare clic su **Crea** per creare il set di riscrittura.

9. Verificare che il nuovo set di riscrittura venga visualizzato nell'elenco dei set di riscrittura

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="Aggiungi riscrittura set":::

## <a name="verify-url-rewrite-through-access-logs"></a>Verificare la riscrittura URL tramite log di accesso

Osservare i campi seguenti nei log di accesso per verificare se la riscrittura dell'URL è avvenuta in base alla previsione.

* **originalRequestUriWithArgs**: questo campo contiene l'URL della richiesta originale
* **requestUri**: questo campo contiene l'URL dopo l'operazione di riscrittura sul gateway applicazione

Per ulteriori informazioni su tutti i campi nei log di accesso, vedere [qui](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku).

##  <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come configurare riscritture per alcuni casi d'uso comuni, vedere [scenari di riscrittura comuni](rewrite-http-headers.md).

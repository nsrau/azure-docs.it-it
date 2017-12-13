---
title: Come impostare o modificare i criteri di Gestione API di Azure | Microsoft Docs
description: Questo argomento illustra come impostare o modificare i criteri di Gestione API di Azure.
services: api-management
documentationcenter: 
author: Juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 409069cbc382610a48139df75f0f64b1682d8ee6
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Come impostare o modificare i criteri di Gestione API di Azure

La definizione dei criteri è un documento XML che descrive una sequenza di istruzioni in ingresso e in uscita. Il codice XML può essere modificato direttamente nella finestra della definizione. È anche possibile selezionare un criterio predefinito nell'elenco visualizzato sulla destra della finestra dei criteri. Le istruzioni applicabili all'ambito corrente sono abilitate ed evidenziate. Facendo clic su un'istruzione abilitata, il codice XML appropriato viene aggiunto in corrispondenza del cursore nella visualizzazione della definizione. 

Per informazioni dettagliate sui criteri, vedere [Criteri in Gestione API di Azure](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Impostare o modificare un criterio

Per impostare o modificare un criterio, seguire questa procedura:

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare all'istanza di Gestione API.
3. Fare clic sulla scheda **API**.
4. Selezionare una delle API importate in precedenza.
5. Selezionare la scheda **Progettazione**.
6. Selezionare un'operazione a cui si vuole applicare il criterio. Per applicare il criterio a tutte le operazioni, selezionare **Tutte le operazioni**.
7. Fare clic sul triangolo accanto alla matita per l'elaborazione **in ingresso** o **in uscita**.
8. Selezionare la voce **Editor di codice**.

    ![Modificare i criteri](./media/set-edit-policies/set-edit-policies01.png)

9. Incollare il codice del criterio desiderato in uno dei blocchi appropriati.
         
        <policies>
             <inbound>
                 <base />
             </inbound>
             <backend>
                 <base />
             </backend>
             <outbound>
                 <base />
             </outbound>
             <on-error>
                 <base />
             </on-error>
         </policies>
 
## <a name="configure-scope"></a>Configurare l'ambito

I criteri possono essere configurati a livello globale o nell'ambito di un prodotto, un'API o un'operazione. Per iniziare a configurare un criterio, è prima necessario selezionare l'ambito in cui dovrà essere applicato.

Gli ambiti dei criteri vengono valutati nell'ordine seguente:

1. Ambito globale
2. Ambito del prodotto
3. Ambito dell’API
4. Ambito dell'operazione

Le istruzioni all'interno dei criteri vengono valutate in base alla posizione dell'elemento `base`, se presente. Con i criteri globali non sono disponibili criteri padre e l'uso dell'elemento `<base>` in tali criteri non produce alcun effetto.

Per visualizzare i criteri nell'ambito corrente nell'editor dei criteri, fare clic su **Ricalcolare il criterio effettivo per l'ambito selezionato**.

### <a name="global-scope"></a>Ambito globale

L'ambito globale è configurato per **tutte le API** nell'istanza di Gestione API.

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'istanza di Gestione API.
2. Fare clic su **Tutte le API**.

    ![Ambito globale](./media/api-management-howto-policies/global-scope.png)

3. Fare clic sull'icona a forma di triangolo.
4. Selezionare **Editor di codice**.
5. Aggiungere o modificare criteri.
6. Premere **Salva**. 

    Le modifiche vengono propagate immediatamente al gateway di Gestione API.

### <a name="product-scope"></a>Ambito del prodotto

L'ambito del prodotto è configurato per il prodotto selezionato.

1. Fare clic su **Prodotti**.

    ![Ambito del prodotto](./media/api-management-howto-policies/product-scope.png)

2. Selezionare il prodotto a cui si vogliono applicare i criteri.
3. Fare clic su **Criteri**.
4. Aggiungere o modificare criteri.
5. Premere **Salva**. 

### <a name="api-scope"></a>Ambito dell’API

L'ambito dell'API è configurato per **tutte le operazioni** dell'API selezionata.

1. Selezionare l'**API** a cui si vogliono applicare i criteri.

    ![Ambito dell’API](./media/api-management-howto-policies/api-scope.png)

2. Selezionare **Tutte le operazioni**.
3. Fare clic sull'icona a forma di triangolo.
4. Selezionare **Editor di codice**.
5. Aggiungere o modificare criteri.
6. Premere **Salva**. 

### <a name="operation-scope"></a>Ambito dell'operazione 

L'ambito dell'operazione è configurato per l'operazione selezionata.

1. Selezionare un'**API**.
2. Selezionare l'operazione a cui si vogliono applicare i criteri.

    ![Ambito dell'operazione](./media/api-management-howto-policies/operation-scope.png)

3. Fare clic sull'icona a forma di triangolo.
4. Selezionare **Editor di codice**.
5. Aggiungere o modificare criteri.
6. Premere **Salva**. 

## <a name="next-steps"></a>Passaggi successivi

Vedere gli argomenti correlati seguenti:

+ [Trasformare le API](transform-api.md)
+ [Informazioni di riferimento sui criteri](api-management-policy-reference.md) per un elenco completo delle istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](policy-samples.md)
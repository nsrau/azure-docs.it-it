---
title: "Esercitazione: Pubblicare versioni dell'API con Gestione API di Azure"
description: Le procedure illustrate in questa esercitazione descrivono come pubblicare più versioni API in Gestione API.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: e6afa26c65f097683a5b471dc34621cca38c01e6
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377397"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Esercitazione: Pubblicare più versioni dell'API 

Talvolta, può risultare poco pratico che tutti i chiamanti dell'API usino esattamente la stessa versione. Quando i chiamanti sono pronti per passare a una versione successiva dell'API, preferiscono farlo adottando un approccio facile da comprendere. Come illustrato in questa esercitazione, è possibile fornire più *versioni* in Gestione API di Azure. 

Per i concetti di base, vedere [Versioni e revisioni](https://azure.microsoft.com/blog/versions-revisions/).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere una nuova versione a un'API esistente
> * Scegliere uno schema di versione
> * Aggiungere la versione a un prodotto
> * Passare al portale per sviluppatori per visualizzare la versione

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Versione mostrata nel portale di Azure":::

## <a name="prerequisites"></a>Prerequisites

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md).

## <a name="add-a-new-version"></a>Aggiungere una nuova versione

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. Selezionare **API**.
1. Selezionare **Demo Conference API** (API Demo Conference) nell'elenco di API. 
1. Selezionare il menu di scelta rapida ( **...** ) accanto a **Demo Conference API**.
1. Selezionare **Aggiungi versione**.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="Menu di scelta rapida dell'API - Aggiungi versione":::


> [!TIP]
> È anche possibile abilitare le versioni quando si crea una nuova API. Nella schermata **Aggiungi API** selezionare **Assegnare un numero di versione a questa API?** .

## <a name="choose-a-versioning-scheme"></a>Scegliere uno schema di controllo delle versioni

In Gestione API di Azure è possibile scegliere in che modo i chiamanti specificano la versione dell'API selezionando uno *schema di controllo delle versioni*: **percorso, intestazione** o **stringa di query**. L'esempio seguente mostra l'uso dello schema di controllo delle versioni di tipo *percorso*.

Immettere i valori dalla tabella seguente. Fare quindi clic su **Crea** per creare la versione.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Finestra Aggiungi versione":::



|Impostazione   |valore  |Descrizione  |
|---------|---------|---------|
|**Nome**     |  *demo-conference-api-v1*       |  Nome univoco nell'istanza di Gestione API.<br/><br/>Poiché una versione è in realtà una nuova API basata sulla [revisione](api-management-get-started-revise-api.md) di un'API, questa impostazione è il nome della nuova API.   |
|**Schema di controllo delle versioni**     |  **Percorso**       |  Modo in cui i chiamanti specificano la versione dell'API.     |
|**Identificatore della versione**     |  *v1*       |  Indicatore della versione specifico dello schema. Per **Percorso**, il suffisso per il percorso dell'URL dell'API. <br/><br/> Se si seleziona **Intestazione** o **Stringa di query** come schema di controllo delle versioni, è necessario immettere un valore aggiuntivo, ovvero il nome dell'intestazione o il parametro della stringa di query.<br/><br/> Viene visualizzato un esempio di utilizzo.        |
|**Prodotti**     |  **Illimitato**       |  Facoltativamente, uno o più prodotti a cui è associata la versione dell'API. Per pubblicare l'API, è necessario associarla a un prodotto. È anche possibile [aggiungere la versione a un prodotto](#add-the-version-to-a-product) in un secondo momento.      |

Una volta creata, la versione viene visualizzata sotto **Demo Conference API** nell'elenco di API. A questo punto, sono visualizzate due API: **Originale** e **v1**.

![Versioni elencate per un'API nel portale di Azure](media/api-management-getstarted-publish-versions/version-list.png)

È ora possibile modificare e configurare la versione **v1** come API distinta dalla versione **Originale**. Le modifiche apportate a una versione non influiscono sulle altre.

> [!Note]
> Se si aggiunge una versione a un'API senza controllo delle versioni, viene automaticamente creata una versione **Originale**, che risponde dall'URL predefinito. La creazione di una versione originale garantisce che i chiamanti esistenti non subiscano interruzioni a causa della procedura di aggiunta della versione. Se si crea una nuova API con le versioni abilitate sin dall'inizio, non viene creata alcuna versione originale.

## <a name="add-the-version-to-a-product"></a>Aggiungere la versione a un prodotto

Perché i chiamanti possano visualizzare la nuova versione, questa deve essere aggiunta a un *prodotto*. Se ancora non è stata aggiunta la versione a un prodotto, è possibile aggiungerla in qualsiasi momento.

Ad esempio, per aggiungere la versione al prodotto **Illimitato**:
1. Nel portale di Azure accedere all'istanza di Gestione API.
1. Selezionare **Prodotti** > **Illimitato** > **API** >  **+ Aggiungi**.
1. Selezionare **Demo Conference API** versione **v1**.
1. Fare clic su **Seleziona**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Aggiungere la versione al prodotto":::

## <a name="browse-the-developer-portal-to-see-the-version"></a>Passare al portale per sviluppatori per visualizzare la versione

Nel [portale per sviluppatori](api-management-howto-developer-portal-customize.md) è possibile visualizzare le versioni dell'API.

1. Selezionare **Portale per sviluppatori** dal menu principale.
2. Selezionare **API**, quindi **Demo Conference API** (API Demo Conference).
3. Verrà visualizzato un elenco a discesa con più versioni accanto al nome dell'API.
4. Selezionare **v1**.
5. Si noti l'**URL della richiesta** della prima operazione nell'elenco. Mostra che il percorso dell'URL dell'API include **v1**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere una nuova versione a un'API esistente
> * Scegliere uno schema di versione 
> * Aggiungere la versione a un prodotto
> * Passare al portale per sviluppatori per visualizzare la versione

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Personalizzare lo stile delle pagine del portale per sviluppatori](api-management-customize-styles.md)

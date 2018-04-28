---
title: Pubblicare versioni dell'API con Gestione API di Azure | Microsoft Docs
description: Eseguire le procedure di questa esercitazione per comprendere come pubblicare più versioni con Gestione API.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 7d61fa25f29f1380fe58069dbc25ed9ce1fff53c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="publish-multiple-versions-of-your-api"></a>Pubblicare più versioni dell'API 

Può risultare poco pratico che tutti i chiamanti dell'API usino esattamente la stessa versione. Quando i chiamanti sono pronti per passare a una versione successiva dell'API, preferiscono farlo adottando un approccio facile da comprendere. A questo scopo, è possibile usare le **versioni** in Gestione API di Azure. Per altre informazioni, vedere [Versions & revisions](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) (Versioni e revisioni).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere una nuova versione a un'API esistente
> * Scegliere uno schema di versione
> * Aggiungere la versione a un prodotto
> * Passare al portale per sviluppatori per visualizzare la versione

![Versione visualizzata sul portale per sviluppatori](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>prerequisiti

* Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
* Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md).

## <a name="add-a-new-version"></a>Aggiungere una nuova versione

![Menu di scelta rapida delle API - Aggiungere versione](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Selezionare **Conference API** (API Conferenza) nell'elenco di API.
2. Selezionare il menu di scelta rapida (**...**).
3. Selezionare **+ Aggiungi versione**.

    > [!TIP]
    > È possibile abilitare le versioni anche quando si crea una nuova API. A tal fine, nella schermata **Aggiungi API** selezionare **Assegnare un numero di versione a questa API?**.

## <a name="choose-a-versioning-scheme"></a>Scegliere uno schema di controllo delle versioni

In Gestione API di Azure è possibile scegliere come consentire ai chiamanti di specificare quale versione dell'API usare. Specificare la versione API da usare selezionando uno **schema di controllo delle versioni**. Può trattarsi di uno schema **stringa di query, percorso o intestazione**. Nell'esempio seguente viene usato uno schema percorso come schema di controllo delle versioni.

![Aggiungere una schermata di versione](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Lasciare **percorso** selezionato come lo **schema di controllo delle versioni**.
2. Aggiungere **v1** come **identificatore di versione**.

    > [!TIP]
    > Se si seleziona **intestazione** o **stringa di query** come schema di controllo delle versioni, è necessario fornire un altro valore, ovvero il nome dell'intestazione o il parametro della stringa di query.

3. Aggiungere un'eventuale descrizione.
4. Selezionare **Crea** per impostare la nuova versione.
5. Nell'elenco delle API al di sotto di **Big Conference API** (API Grande conferenza), verranno visualizzate due API distinte - **Originale**, e **v1**.

    ![Versioni elencate per un'API nel portale di Azure](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Se si aggiunge una versione a un'API senza versione, viene automaticamente creata una versione **originale**, che risponde dall'URL predefinito. Ciò garantisce che i chiamanti esistenti non subiscano interruzioni a causa della procedura di aggiunta della versione. Se si crea una nuova API con le versioni abilitate sin dall'inizio, non viene creato alcun originale.

6. È ora possibile modificare e configurare **v1** come API distinta dall'**originale**. Le modifiche apportate a una versione non influiscono sulle altre.

## <a name="add-the-version-to-a-product"></a>Aggiungere la versione a un prodotto

Perché i chiamanti possano visualizzare la nuova versione, questa deve essere aggiunta a un **prodotto**.

1. Selezionare **Prodotti** nella pagina del modello di distribuzione classica.
2. Selezionare **Illimitato**.
3. Selezionare **API**.
4. Selezionare **Aggiungi**.
5. Selezionare **Conference API, Version v1** (API Conferenza, Versione v1).
6. Passare alla pagina di gestione del servizio e selezionare **API**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Passare al portale per sviluppatori per visualizzare la versione

1. Selezionare **Portale per sviluppatori** dal menu principale.
2. Selezionare **API** e notare che **Conference API** (API Conferenza) visualizza sia la versione **Original** (Originale), sia la versione **v1**.
3. Selezionare **v1**.
4. Si noti l'**URL della richiesta** della prima operazione nell'elenco. Mostra che il percorso dell'URL dell'API include **v1**.

    ![Menu di scelta rapida delle API - Aggiungere versione](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere una nuova versione a un'API esistente
> * Scegliere uno schema di versione 
> * Aggiungere la versione a un prodotto
> * Passare al portale per sviluppatori per visualizzare la versione

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Aggiornare e ridimensionare](upgrade-and-scale.md)
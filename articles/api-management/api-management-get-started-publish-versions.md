---
title: Pubblicare versioni dell'API con Gestione API di Azure | Microsoft Docs
description: "Eseguire le procedure di questa esercitazione per comprendere come pubblicare più versioni con Gestione API."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>Pubblicazione di più versioni dell'API in modo prevedibile
Questa esercitazione descrive come impostare le versioni dell'API e scegliere il modo in cui vengono chiamate dagli sviluppatori di API.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario creare un servizio Gestione API e disporre di un'API esistente che sia possibile modificare al posto dell'API Conference (API Conferenza).

## <a name="about-versions"></a>Informazioni sulle versioni
Può risultare poco pratico che tutti i chiamanti dell'API usino esattamente la stessa versione. Talvolta si desidera pubblicare funzionalità dell'API nuove o diverse destinate ad alcuni utenti, mentre altri preferiscono continuare a usare l'API in uso e funzionante. Quando i chiamanti sono pronti per passare a una versione successiva dell'API, preferiscono farlo adottando un approccio facile da comprendere.  A tal fine, in Gestione API di Azure sono disponibili le **versioni**.

## <a name="walkthrough"></a>Procedura dettagliata
In questa procedura dettagliata viene aggiunta una nuova versione a un'API esistente, scegliendo uno schema di controllo delle versioni e un identificatore di versione.

## <a name="add-a-new-version"></a>Aggiungere una nuova versione
![Menu di scelta rapida delle API - Aggiungere versione](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. Aprire la pagina delle **API** nel servizio di Gestione API nel portale di Azure.
2. Selezionare **Conference API** (API Conferenza) nell'elenco di API, quindi selezionare il menu di scelta rapida (**... **) accanto.
3. Selezionare **+ Aggiungi versione**.

    > [!TIP]
    > È possibile abilitare le versioni anche quando si crea una nuova API. A tal fine, nella schermata **Aggiungi API** selezionare **Assegnare un numero di versione a questa API?**.

## <a name="choose-a-versioning-scheme"></a>Scegliere uno schema di controllo delle versioni
In Gestione API di Azure è possibile scegliere come consentire ai chiamanti di specificare quale versione dell'API usare. A tal fine, scegliere uno **schema di controllo delle versioni**. Può trattarsi di uno schema **stringa di query, percorso o intestazione**. Nell'esempio viene usato il percorso.
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
    > Se si aggiunge una versione a un'API senza versione, si crea sempre una versione **originale**, che risponde dall'URL predefinito. Ciò garantisce che i chiamanti esistenti non subiscano interruzioni a causa della procedura di aggiunta della versione. Se si crea una nuova API con le versioni abilitate sin dall'inizio, non viene creato alcun originale.

6. È ora possibile modificare e configurare **v1** come API completamente distinta dall'**originale**. Le modifiche apportate a una versione non influiscono sulle altre.

## <a name="add-the-version-to-a-product"></a>Aggiungere la versione a un prodotto
Affinché i chiamanti visualizzino la nuova versione, è necessario aggiungerla a un **prodotto** (i prodotti non vengono ereditati dalle versioni padre).

1. Selezionare **Prodotti** nella pagina di gestione del servizio.
2. Selezionare **Illimitato**.
3. Selezionare **API**.
4. Selezionare **Aggiungi**.
5. Selezionare **Conference API, Version v1** (API Conferenza, Versione v1).
6. Tornare alla pagina di gestione del servizio e selezionare **API**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Passare al portale per sviluppatori per visualizzare la versione
1. Selezionare **Portale per sviluppatori** dal menu principale.
2. Selezionare **API** e notare che **Conference API** (API Conferenza) visualizza sia la versione **Original** (Originale), sia la versione **v1**.
3. Selezionare **v1**.
4. Si noti l'**URL della richiesta** della prima operazione nell'elenco. Mostra che il percorso dell'URL dell'API include **v1**.
![Versione visualizzata sul portale per sviluppatori](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)


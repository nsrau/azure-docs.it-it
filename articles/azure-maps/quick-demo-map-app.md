---
title: 'Guida introduttiva: Ricerca interattiva nelle mappe con Mappe di Azure'
description: Informazioni su come creare un'applicazione Web demo per la ricerca interattiva di mappe tramite l'SDK Web Mappe di Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: bb6c5ee48e22e0c913e7fc6150d3986af805a08f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004613"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Avvio rapido: Creare una mappa per la ricerca interattiva con Mappe di Azure

Questo articolo mostra come usare Mappe di Azure per creare una mappa che consenta agli utenti un'esperienza di ricerca interattiva. L'articolo descrive le operazioni di base seguenti:

* Creare un proprio account di Mappe di Azure.
* Ottenere la chiave primaria da usare nell'applicazione Web demo.
* Scaricare e aprire l'applicazione demo per le mappe.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Accedere al [portale di Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

Creare un account di Mappe di Azure eseguendo questa procedura:

1. Nell'angolo superiore sinistro del [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa**.
2. Nella casella *Cerca nel marketplace* digitare **Mappe**.
3. Dai *risultati* scegliere **Mappe**. Fare clic sul pulsante **Crea** visualizzato sotto la mappa.
4. Nella pagina **Crea account di Mappe** immettere i valori seguenti:
    * La *sottoscrizione* da usare per l'account.
    * Il nome del *gruppo di risorse* per l'account. Per il gruppo di risorse è possibile selezionare l'opzione *Crea nuovo* o *Usa esistente*.
    * Il *nome* del nuovo account.
    * *Piano tariffario* dell'account.
    * Leggere la *Licenza* e l'*Informativa sulla Privacy* e selezionare la casella di controllo per accettare le condizioni.
    * Fare clic sul pulsante **Create** (Crea).

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Creare un account di Mappe nel portale":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Ottenere la chiave primaria per l'account

Dopo che è stato creato l'account di Mappe, recuperare la chiave primaria che consente di eseguire query nelle API di Mappe.

1. Aprire l'account di Mappe nel portale.
2. Nella sezione delle impostazioni selezionare **Autenticazione**.
3. Copiare il valore di **Chiave primaria** negli Appunti. Salvarlo in locale per usarlo in seguito in questa esercitazione.

>[!NOTE]
> Se si usa la chiave di sottoscrizione invece della chiave primaria, non verrà eseguito il rendering corretto della mappa. Inoltre, per motivi di sicurezza, si consiglia di eseguire la rotazione tra le chiavi primarie e secondarie. Per eseguire la rotazione delle chiavi, aggiornare l'app per usare la chiave secondaria, distribuire, quindi premere il pulsante di ciclo/aggiornamento accanto alla chiave primaria per generare una nuova chiave primaria. La chiave primaria precedente verrà disabilitata. Per altre informazioni sulla rotazione delle chiavi, vedere [Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi](https://docs.microsoft.com/azure/key-vault/secrets/key-rotation-log-monitoring)

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Ottenere la chiave primaria di Mappe di Azure nel portale di Azure":::

## <a name="download-the-demo-application"></a>Scaricare l'applicazione demo

1. Passare a [interactiveSearch. html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Copiare il contenuto del file.
2. Salvare il contenuto del file in locale come **AzureMapDemo.html**. Aprirlo in un editor di testo.
3. Cercare la stringa `<Your Azure Maps Key>`. Sostituirla con il valore di **Chiave primaria** ottenuto nella sezione precedente.

## <a name="open-the-demo-application"></a>Aprire l'applicazione demo

1. Aprire il file **AzureMapDemo.html** nel browser che si preferisce.
2. Osservare la mappa visualizzata della città di Los Angeles. Fare zoom avanti e indietro per osservare come la mappa esegue automaticamente il rendering con più o meno informazioni a seconda del livello di zoom.
3. Modificare il centro predefinito della mappa. Nel file **AzureMapDemo.html** cercare la variabile denominata **center**. Sostituire il valore della coppia longitudine, latitudine di questa variabile con i nuovi valori **[-74,0060, 40,7128]** . Salvare il file e aggiornare il browser.
4. Provare l'esperienza di ricerca interattiva. Nella casella di ricerca nell'angolo in alto a sinistra dell'applicazione Web demo cercare **restaurants**.
5. Spostare il puntatore del mouse sull'elenco di indirizzi e posizioni visualizzato sotto la casella di ricerca. Osservare che l'indicatore corrispondente sulla mappa visualizza le informazioni sulla posizione corrispondente. Per la privacy delle aziende private, sono visualizzati nomi e indirizzi fittizi.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Applicazione Web per la ricerca interattiva nelle mappe":::


## <a name="clean-up-resources"></a>Pulire le risorse

>[!WARNING]
>Le esercitazioni elencate nella sezione [Passaggi successivi](#next-steps) offrono indicazioni dettagliate su come usare e configurare Mappe di Azure con il proprio account. Non eliminare le risorse create in questa guida introduttiva se si prevede di continuare con le esercitazioni.

Se non si intende continuare con le esercitazioni, pulire le risorse eseguendo questa procedura:

1. Chiudere il browser che esegue l'applicazione Web **AzureMapDemo.html**.
2. Accedere alla pagina del portale di Azure. Selezionare **Tutte le risorse** dalla pagina principale del portale. In alternativa, fare clic sull'icona di menu nell'angolo superiore sinistro. Selezionare **Tutte le risorse**.
3. Selezionare il proprio account di Mappe di Azure. Nella parte superiore della pagina fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva vengono creati l'account di Mappe di Azure e un'applicazione demo. Per altre informazioni su Mappe di Azure, esaminare queste esercitazioni:

> [!div class="nextstepaction"]
> [Eseguire ricerche vicino a punti di interesse con Mappe di Azure](tutorial-search-location.md)

Per altri esempi di codice e un'esperienza interattiva di codifica, vedere queste guide:

> [!div class="nextstepaction"]
> [Trovare un indirizzo usando il servizio di ricerca di Mappe di Azure](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Usare il controllo mappa di Mappe di Azure](how-to-use-map-control.md)

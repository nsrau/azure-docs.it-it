---
title: 'Guida introduttiva: Ricerca interattiva nelle mappe con Mappe di Azure | Mappe di Microsoft Azure'
description: Informazioni su come creare un'applicazione Web demo per la ricerca interattiva di mappe tramite l'SDK Web Mappe di Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 1/14/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 13dc5f6c7175e1ed568199abcbaa4c5d9a20fa7f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80334429"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Guida introduttiva: Creare una mappa per la ricerca interattiva con Mappe di Azure

Questo articolo illustra le funzionalità di Mappe di Azure che consentono di creare una mappa che offre agli utenti un'esperienza di ricerca interattiva. L'articolo descrive le operazioni di base seguenti:
* Creare un proprio account di Mappe di Azure.
* Ottenere la chiave dell'account da usare nell'applicazione Web demo.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Creare un account con Mppe di Azure

Creare un nuovo account di Mappe con i passaggi seguenti:

1. Nell'angolo superiore sinistro del [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa**.
2. Nella casella *Cerca nel Marketplace* digitare **Mappe**.
3. Fra i *risultati* selezionare **Mappe**. Fare clic sul pulsante **Crea** visualizzato sotto la mappa.
4. Nella pagina **Crea account di Mappe** immettere i valori seguenti:
    * La *sottoscrizione* da usare per l'account.
    * Il nome del *gruppo di risorse* per l'account. Per il gruppo di risorse è possibile selezionare l'opzione *Crea nuovo* o *Usa esistente*.
    * Il *nome* del nuovo account.
    * *Piano tariffario* dell'account.
    * Leggere la *Licenza* e l'*Informativa sulla Privacy* e selezionare la casella di controllo per accettare le condizioni.
    * Fare clic sul pulsante **Create** (Crea).

![Creare account di Mappe nel portale](./media/quick-demo-map-app/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Ottenere la chiave primaria per l'account

Dopo che è stato creato l'account di Mappe, recuperare la chiave che consente di eseguire query nelle API di Mappe. Nella chiamata dei servizi di Mappe di Azure è consigliabile usare la chiave primaria dell'account come chiave di sottoscrizione.

1. Aprire l'account di Mappe nel portale.
2. Nella sezione delle impostazioni selezionare **Autenticazione**.
3. Copiare il valore di **Chiave primaria** negli Appunti. Salvarlo in locale per usarlo in seguito in questa esercitazione.

![Ottenere la chiave primaria di Mappe di Azure nel portale di Azure](./media/quick-demo-map-app/get-key.png)

## <a name="download-the-application"></a>Scaricare l'applicazione

1. Passare al file [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) e fare clic su di esso per visualizzare il contenuto nell'interfaccia utente di GitHub. Fare clic con il pulsante destro del mouse sul pulsante **Raw** e copiare il contenuto del file oppure scegliere "Salva con nome" per scaricare il file.
2. Salvare il contenuto del file in locale come **AzureMapDemo.html**. Aprirlo in un editor di testo.
3. Cercare la stringa `<Your Azure Maps Key>`. Sostituirla con il valore di **Chiave primaria** ottenuto nella sezione precedente.

## <a name="open-the-application"></a>Aprire l'applicazione

1. Aprire il file **AzureMapDemo.html** nel browser che si preferisce.
2. Osservare la mappa visualizzata della città di Los Angeles. Fare zoom avanti e indietro per osservare come la mappa esegue automaticamente il rendering con più o meno informazioni a seconda del livello di zoom. 
3. Modificare il centro predefinito della mappa. Nel file **AzureMapDemo.html** cercare la variabile denominata **center**. Sostituire il valore della coppia longitudine, latitudine di questa variabile con i nuovi valori **[-74,0060, 40,7128]** . Salvare il file e aggiornare il browser.
4. Provare l'esperienza di ricerca interattiva. Nella casella di ricerca nell'angolo in alto a sinistra dell'applicazione Web demo cercare **restaurants**.
5. Spostare il puntatore del mouse sull'elenco di indirizzi e posizioni visualizzato sotto la casella di ricerca. Osservare che l'indicatore corrispondente sulla mappa visualizza le informazioni sulla posizione corrispondente. Per la privacy delle aziende private, sono visualizzati nomi e indirizzi fittizi.

    ![Applicazione Web di ricerca interattiva di mappe](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Le esercitazioni analizzano in dettaglio come usare e configurare Mappe di Azure con l'account. Non eliminare le risorse create in questa guida introduttiva se si prevede di continuare con le esercitazioni. Se non si intende continuare, eseguire questi passaggi per pulire le risorse:

1. Chiudere il browser che esegue l'applicazione Web **AzureMapDemo.html**.
2. Dal menu a sinistra nel portale di Azure selezionare **Tutte le risorse**. Quindi, selezionare il proprio account di Mappe di Azure. Nella parte superiore del pannello **Tutte le risorse** selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato l'account di Mappe di Azure ed è stata creata un'app demo. Per informazioni su Mappe di Azure, consultare le esercitazioni seguenti:

> [!div class="nextstepaction"]
> [Eseguire ricerche vicino a punti di interesse con Mappe di Azure](tutorial-search-location.md)

Per altri esempi di codice e un'esperienza interattiva di codifica, vedere queste guide:

> [!div class="nextstepaction"]
> [Trovare un indirizzo usando il servizio di ricerca di Mappe di Azure](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Usare il controllo mappa di Mappe di Azure](how-to-use-map-control.md)

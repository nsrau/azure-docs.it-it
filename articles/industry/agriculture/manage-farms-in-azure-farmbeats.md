---
title: Gestire le farm
description: Descrive come gestire le farm
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271720"
---
# <a name="manage-farms"></a>Gestire aziende agricole

È possibile gestire le farm in Azure FarmBeats.You can manage your farms in Azure FarmBeats. In questo articolo vengono fornite informazioni su come creare farm, installare dispositivi, sensori e droni che consentono di gestire le farm.

## <a name="create-farms"></a>Creare farm

Eseguire la procedura descritta di seguito:

1. Accedere all'acceleratore di farm, viene visualizzata la pagina **Farm.**
    Nella pagina **Farm** viene visualizzato l'elenco delle farm nel caso in cui siano già state create nella sottoscrizione.

    Di seguito è riportata l'immagine di esempio:Here is the sample image:

    ![Progetto FarmBeats](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Selezionare **Crea farm** e specificare **Nome**, **Ritaglia** e **Indirizzo**.
3. Nel campo **Definisci limite farm**,nel campo obbligatorio selezionare **Contrassegna sulla mappa** o Incolla codice **GeoJSON**.

Di seguito sono riportati i due modi per definire un limite di farm:

1. **Mark on Map**: Utilizzare lo strumento di controllo mappa per disegnare e contrassegnare il contorno della farm. Per contrassegnare i ![confini,](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) Project Farm Beats e contrassegnare i limiti esatti.

    ![Progetto FarmBeats](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Incolla codice GeoJson**: Il GeoJSON è un formato per la codifica di strutture di dati geografici, utilizzando JavaScript Object Notation (JSON). Questa opzione consente di visualizzare una casella di testo in cui è possibile immettere una stringa GeoJSON per contrassegnare i limiti della farm. È anche possibile creare codice GeoJSON da GeoJSON.io.
Utilizzare le descrizioni comandi per compilare le informazioni.

    ![Progetto FarmBeats](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Selezionare **Invia** per creare una farm. Una nuova farm viene creata e visualizzata nella pagina **Farm.**

## <a name="view-farm"></a>Visualizza farm

Nella pagina elenco Farm viene visualizzato un elenco delle farm create. Selezionare una farm per visualizzare l'elenco di:

 - **Conteggio dispositivi:** visualizza il numero e lo stato dei dispositivi distribuiti nella farm.
 - **Mappa:** mappa della farm con i dispositivi distribuiti nella farm.
 - **Telemetria:** visualizza i dati di telemetria dai sensori distribuiti nella farm.
 - Mappe di **precisione più recenti:** visualizza l'ultima mappa degli indici satellitari (EVI, NDWI), la mappa di calore dell'umidità del suolo e la mappa di posizionamento del sensore.

## <a name="edit-farm"></a>Modifica farm

Nella pagina **Farm viene** visualizzato un elenco delle farm create.

1.  Selezionare una farm per visualizzare e modificare la farm.
2.  Selezionare **Modifica farm** per modificare le informazioni sulla farm. Nella finestra **Dettagli farm** è possibile modificare i campi **Nome**, **Ritaglia**, **Indirizzo**e definire **Contorno farm.**

    ![Progetto FarmBeats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Selezionare **Invia** per salvare i dettagli modificati.

## <a name="delete-farm"></a>Elimina farm

Nella pagina **Farm viene** visualizzato un elenco di farm create. Utilizzare la procedura seguente per eliminare una farm:

1.  Selezionare una farm dall'elenco per eliminare i dettagli della farm.
2.  Selezionare **Elimina farm** per eliminare la farm.

    ![Progetto FarmBeats](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Quando si elimina una farm, i dispositivi e le mappe associati alla farm non vengono eliminati. Le informazioni sulla farm associate al dispositivo e alle mappe non saranno rilevanti. È possibile continuare a visualizzare dispositivi, telemetria e mappe dal servizio FarmBeats.You can continue to view devices, telemetry and maps from the FarmBeats service.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato la farm, imparare a ottenere il flusso dei [dati dei sensori](get-sensor-data-from-sensor-partner.md) nella farm.

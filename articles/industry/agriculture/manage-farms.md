---
title: Gestione farm
description: Viene descritto come gestire le farm
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c83ac8a42a91c9db06726e917ed142e20995b987
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797972"
---
# <a name="manage-farms"></a>Gestione farm

È possibile gestire le farm in Azure FarmBeats. Questo articolo fornisce le informazioni su come creare farm, installare dispositivi, sensori e droni che consentono di gestire le farm.

## <a name="create-farms"></a>Creazione di farm

Seguire questa procedura:

1. Accedere all'acceleratore farm. verrà visualizzata la pagina **Farm** .
    Nella pagina **Farm** viene visualizzato l'elenco delle farm nel caso in cui siano già state create nella sottoscrizione.

    Di seguito è illustrata l'immagine di esempio:

    ![Beat Farm progetto](./media/create-farms/create-farm-main-page-1.png)


2. Selezionare **Crea Farm** e specificare **nome**, **raccolti** e **Indirizzo**.
3. Nel **Definisci limite Farm**, (campo obbligatorio) selezionare **contrassegno sulla mappa** o **Incolla codice GeoJSON**.

Ecco i due modi per definire un limite della farm:

1. **Contrassegno sulla mappa**: usare lo strumento di controllo della mappa per tracciare e contrassegnare il limite della farm. Per contrassegnare i limiti, ![farm del progetto batte](./media/create-farms/pencil-icon-1.png) e contrassegnare i limiti esatti.

    ![Beat Farm progetto](./media/create-farms/create-farm-mark-on-map-1.png)

2. **Incollare il codice GeoJSON**: il formato GeoJSON è un formato per la codifica di strutture di dati geografici, usando JavaScript Object Notation (JSON). Questa opzione consente di visualizzare una casella di testo in cui è possibile immettere una stringa GeoJSON per contrassegnare i limiti della farm. È anche possibile creare codice GeoJSON da GeoJSON.io.
Utilizzare le descrizioni comandi per compilare le informazioni.

    ![Beat Farm progetto](./media/create-farms/create-new-farm-1.png)

4.  Selezionare **Submit (Invia** ) per creare una farm. Una nuova farm verrà creata e visualizzata nella pagina **Farm** .

## <a name="view-farm"></a>Visualizza Farm

Nella pagina elenco farm viene visualizzato un elenco di farm create. Selezionare una farm per visualizzare l'elenco di:

 - **Conteggio dispositivi** : Visualizza il numero e lo stato dei dispositivi distribuiti nella farm.
 - **Map** : mappa della farm con i dispositivi distribuiti nella farm.
 - **Telemetria** : Visualizza i dati di telemetria dai sensori distribuiti nella farm.
 - **Mappe di precisione più recenti** : Visualizza la mappa degli indici satellite più recente (Evi, NDWI), il mappa termica di umidità del suolo e la mappa di posizionamento dei sensori.

## <a name="edit-farm"></a>Modifica Farm

Nella pagina **Farm** viene visualizzato un elenco di farm create.

1.  Selezionare una farm per visualizzare e modificare la farm.
2.  Selezionare **modifica Farm** per modificare le informazioni della farm. Nella finestra **dei dettagli della farm** è possibile modificare i campi relativi a **nome**, **colture**, **Indirizzo**e **limite della farm** .

    ![Beat Farm progetto](./media/create-farms/edit-farm-1.png)

3. Selezionare **Submit (Invia** ) per salvare i dettagli modificati.

## <a name="delete-farm"></a>Elimina Farm

Nella pagina **Farm** viene visualizzato un elenco di farm create. Per eliminare una farm, attenersi alla procedura seguente:

1.  Selezionare una farm dall'elenco per eliminare i dettagli della farm.
2.  Selezionare **Elimina Farm** per eliminare la farm.

    ![Beat Farm progetto](./media/create-farms/delete-farm-1.png)

    > [!NOTE]
    > Quando si elimina una farm, i dispositivi e le mappe associate alla farm non vengono eliminati. Le informazioni sulla farm associate al dispositivo e alle mappe non saranno rilevanti. È possibile continuare a visualizzare dispositivi, telemetria e mappe dal servizio FarmBeats.


## <a name="next-steps"></a>Passaggi successivi

Ora che è stata creata la farm, è possibile ottenere informazioni su come [ottenere i dati dei sensori](get-sensor-data-from-sensor-partner.md) nella farm.

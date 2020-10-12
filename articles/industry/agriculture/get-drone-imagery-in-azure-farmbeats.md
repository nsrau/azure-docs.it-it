---
title: Ottenere immagini da drone
description: Questo articolo descrive come ottenere immagini drone dai partner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 04f0c3c63d00ea49bf43f00f256266599a73d6c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88508805"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Ottenere immagini drone da partner drone

Questo articolo descrive come è possibile importare i dati di orthomosaic dai partner di immagini drone ad Azure FarmBeats datahub. Un orthomosaic è un'illustrazione o un'immagine aerea con correzione geometrica e cucita dai dati raccolti da un drone.

Attualmente sono supportati i seguenti partner di immagini.

  ![Partner di immagini drone FarmBeats](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

L'integrazione di dati di immagini Drone con Azure FarmBeats consente di ottenere i dati di orthomosaic dai voli drone eseguiti sulla farm nel datahub. Quando i dati sono disponibili, è possibile visualizzarli nell'acceleratore FarmBeats. I dati possono essere usati per la creazione di modelli di data fusion e intelligenza artificiale e di machine learning.

## <a name="before-you-begin"></a>Prima di iniziare

  - Assicurarsi di aver installato Azure FarmBeats. Per informazioni su come installare FarmBeats, vedere [Install Azure FarmBeats](install-azure-farmbeats.md).
  - Assicurarsi di disporre della farm per cui si desidera definire le immagini dei droni nel sistema FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Abilitare l'integrazione di immagini Drone con FarmBeats

Fornire le informazioni seguenti al provider di dispositivi per abilitare l'integrazione con FarmBeats:
 - Endpoint API
 - ID tenant
 - ID client
 - Segreto client

Attenersi a questa procedura.

1. Scaricare questo [script](https://aka.ms/farmbeatspartnerscript)ed estrarlo nell'unità locale. Due file sono inclusi nel file zip.
2. Accedere al [portale di Azure](https://portal.azure.com/) e aprire Azure Cloud Shell. Questa opzione è disponibile sulla barra degli strumenti nell'angolo superiore destro del portale.

    ![Apri Azure Cloud Shell sulla barra superiore destra del portale](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Verificare che l'ambiente sia impostato su **PowerShell**.

    ![Impostazione di PowerShell](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Caricare i due file scaricati al passaggio 1 nell'istanza di Cloud Shell.

    ![Caricare file](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Passare alla directory in cui sono stati caricati i file. Per impostazione predefinita, vengono caricati nella home directory sotto il nome utente.
6. Eseguire lo script riportato di seguito:

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. Seguire le istruzioni visualizzate per acquisire i valori di endpoint API, ID tenant, ID client, segreto client e stringa di connessione EventHub.

    Dopo aver immesso le credenziali necessarie nel sistema software drone del partner, è possibile importare tutte le farm dal sistema FarmBeats. È quindi possibile usare i dettagli della farm per eseguire la pianificazione del percorso del volo e la raccolta di immagini drone.

    Una volta che le immagini non elaborate vengono elaborate dal software dei provider di droni, il sistema software drone carica il orthomosaic Stitched e altre immagini elaborate in datahub.

## <a name="view-drone-imagery"></a>Visualizza immagini drone

Dopo l'invio dei dati al datahub FarmBeats, è possibile eseguire una query sull'archivio scene usando le API datahub di FarmBeats.

In alternativa, è possibile visualizzare l'immagine drone più recente nella pagina dei **Dettagli della farm** . Per visualizzare l'immagine, attenersi alla procedura.

1. Selezionare la farm in cui è stata caricata l'immagine. Verrà visualizzata la pagina Dettagli **Farm** .
2. Scorrere verso il basso fino alla sezione **mappe di precisione** più recenti.
3. Visualizzare l'immagine nella sezione relativa alle **Immagini drone** .

    ![Sezione delle immagini drone](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Scaricare immagini drone

Quando si seleziona la sezione relativa alle immagini drone, viene visualizzata una finestra popup che mostra un'immagine ad alta risoluzione della orthomosaic drone.

![Orthomosaic ad alta risoluzione](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Visualizza tutte le mappe drone

I file e le immagini caricati dal provider drone vengono visualizzati nella sezione **Maps** . Selezionare la sezione **Maps** , filtrare in base alla **Farm**e selezionare i file appropriati da visualizzare e scaricare.

  ![Sezione Maps](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare le [API](rest-api-in-azure-farmbeats.md) datahub di FarmBeats per ottenere le immagini dei droni.

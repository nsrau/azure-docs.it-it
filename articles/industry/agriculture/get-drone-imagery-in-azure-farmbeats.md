---
title: Ottenere immagini da drone
description: Questo articolo descrive come ottenere immagini drone dai partner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132042"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Ottieni immagini di droni dai partner di droni

Questo articolo descrive come importare dati ortomosaici dai partner di immagini dei droni in Azure FarmBeats Datahub.This article describes how you can bring in orthomosaic data from your drone imagery partners to Azure FarmBeats Datahub. Un ortomosaico è un'illustrazione aerea o un'immagine che viene geometricamente corretta e cucita dai dati raccolti da un drone.

Attualmente sono supportati i seguenti partner di immagini.

  ![Partner di immagini drone FarmBeats](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

L'integrazione dei dati delle immagini dei droni con Azure FarmBeats ti aiuta a ottenere dati ortomosaici dai voli drone che conduci nella tua fattoria nell'hub dati. Una volta che i dati sono disponibili, è possibile visualizzarli in FarmBeats Accelerator. I dati possono essere utilizzati per la fusione dei dati e l'intelligenza artificiale e la creazione di modelli di apprendimento automatico.

## <a name="before-you-begin"></a>Prima di iniziare

  - Assicurarsi di aver installato Azure FarmBeats. Per informazioni su come installare FarmBeats, vedere [Installare Azure FarmBeats.](install-azure-farmbeats.md)
  - Assicurati di avere la fattoria per la quale vuoi che le immagini dei droni siano definite nel tuo sistema FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Abilita l'integrazione delle immagini dei droni con FarmBeats

Fornire le informazioni seguenti al provider di dispositivi per consentire l'integrazione con FarmBeats:
 - Endpoint API
 - ID tenant
 - ID client
 - Segreto client

Seguire questa procedura.

1. Scaricare questo [script](https://aka.ms/farmbeatspartnerscript)ed estrarlo nell'unità locale. Due file sono all'interno del file zip.
2. Accedere al [portale di Azure](https://portal.azure.com/) e aprire Azure Cloud Shell. Questa opzione è disponibile sulla barra degli strumenti nell'angolo superiore destro del portale.

    ![Aprire Azure Cloud Shell nella barra in alto a destra del portale](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Assicurarsi che l'ambiente sia impostato su **PowerShell**.

    ![Impostazione di PowerShell](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Caricare i due file scaricati dal passaggio 1 nell'istanza di Cloud Shell.

    ![Caricare file](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Passare alla directory in cui sono stati caricati i file. Per impostazione predefinita, vengono caricati nella home directory con il nome utente.
6. Eseguire lo script riportato di seguito:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Seguire le istruzioni visualizzate per acquisire i valori di Endpoint API, ID tenant, ID client, Segreto client e Stringa di connessione EventHub.

    Dopo aver immesso le credenziali necessarie nel sistema software drone del partner, è possibile importare tutte le farm dal sistema FarmBeats. Quindi è possibile utilizzare i dettagli della fattoria per fare la pianificazione del percorso di volo e la raccolta di immagini drone.

    Dopo che le immagini grezze sono state elaborate dal software dei fornitori di droni, il sistema software del drone carica l'ortomosaico cucito e altre immagini trasformate nel datahub.

## <a name="view-drone-imagery"></a>Visualizza le immagini dei droni

Dopo l'invio dei dati all'hub dati FarmBeats, è possibile eseguire una query sull'archivio scene utilizzando le API FarmBeats Datahub.

In alternativa, è possibile visualizzare l'immagine del drone più recente nella pagina **Dettagli farm.** Per visualizzare l'immagine, attenersi alla procedura.

1. Selezionare la farm in cui sono state caricate le immagini. Viene visualizzata la pagina **Dettagli farm.**
2. Scorri verso il basso fino alla sezione Mappe di **precisione** più recente.
3. Visualizzare l'immagine nella sezione **Immagini drone.**

    ![Sezione Immagini drone](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Scarica le immagini dei droni

Quando si seleziona la sezione Immagini drone, si apre un pop-up per mostrare un'immagine ad alta risoluzione dell'ortomosaico del drone.

![Ortomosaico ad alta risoluzione](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Visualizza tutte le mappe dei droni

I file e le immagini caricati dal fornitore del drone vengono visualizzati nella sezione **Mappe.** Selezionare la sezione **Mappe,** filtrare in base **alla farm**e selezionare i file appropriati da visualizzare e scaricare.

  ![Sezione Mappe](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Passaggi successivi

Scopri come usare [le API](rest-api-in-azure-farmbeats.md) FarmBeats Datahub per ottenere le immagini dei droni.

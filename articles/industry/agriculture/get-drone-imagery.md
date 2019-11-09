---
title: Ottenere immagini drone
description: Viene descritto come ottenere le immagini dei droni dai partner
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6ee4391369744f966ce273697e5ba9e7f0590edd
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890986"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Ottenere immagini drone da partner drone

Questo articolo descrive come è possibile importare i dati di orthomosaic dai partner di immagini drone in Azure FarmBeats Data Hub. Un orthomosaic è un'illustrazione/immagine aerea con correzione geometrica e cucita dai dati raccolti dal drone.

Attualmente sono supportati i seguenti partner di immagini.

  ![Beat Farm progetto](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

L'integrazione di dati di immagini Drone con Azure FarmBeats consente di ottenere i dati di orthomosaic dai voli drone eseguiti sulla farm nell'hub dati. Dopo che i dati sono disponibili, è possibile visualizzarli nell'acceleratore FarmBeats e possono essere usati per la fusione dei dati e la creazione di modelli AI/ML.

## <a name="before-you-begin"></a>Prima di iniziare

  - Assicurarsi di aver distribuito Azure FarmBeats. Per la distribuzione, vedere [distribuire FarmBeats](prepare-for-deployment.md).
  - Assicurarsi di avere la farm (per cui si vuole usare le immagini drone) definita nel sistema FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Abilitare l'integrazione di immagini Drone con FarmBeats   

Per abilitare l'integrazione con FarmBeats, è necessario fornire le informazioni seguenti al provider di dispositivi:  
 - Endpoint API  
 - ID tenant  
 - ID client  
 - Client Secret  

Seguire questa procedura:

1. Scaricare questo [script](https://aka.ms/farmbeatspartnerscript) ed estrarlo nell'unità locale. All'interno di questo file ZIP sono presenti due file.  
2. Accedere a [portale di Azure](https://portal.azure.com/) e aprire cloud Shell (questa opzione è disponibile nella barra superiore destra del portale).   

    ![Beat Farm progetto](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Verificare che l'ambiente sia impostato su **PowerShell**

    ![Beat Farm progetto](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Caricare i due file scaricati (dal passaggio 1 precedente) nell'Cloud Shell.  

    ![Beat Farm progetto](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Passare alla directory in cui sono stati caricati i file. Per impostazione predefinita, viene caricato nella Home Directory > nome utente.  
6. Eseguire lo script seguente:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Seguire le istruzioni visualizzate per acquisire i valori di endpoint API, ID tenant, ID client, segreto client e stringa di connessione EventHub.

    Una volta immesse le credenziali necessarie nel sistema software drone del partner, sarà possibile importare tutte le farm dal sistema FarmBeats e usare i dettagli della farm per eseguire la pianificazione del percorso del volo e la raccolta di immagini drone.

    Dopo che le immagini non elaborate vengono elaborate dal software dei provider di droni, il sistema software drone carica i orthomosaic Uniti e altre immagini elaborate nell'hub dati.

## <a name="view-drone-imagery"></a>Visualizza immagini drone

Dopo che i dati sono stati inviati all'hub dati FarmBeats, dovrebbe essere possibile eseguire query nell'archivio scene usando le API dell'hub dati di FarmBeats.

In alternativa, è possibile visualizzare l'immagine drone più recente nella pagina dei dettagli della **Farm** . Per visualizzare, attenersi alla procedura seguente:  

1. Selezionare la farm in cui è stata caricata l'immagine. Viene visualizzata la pagina dei dettagli della **Farm** .
2. Scorrere verso il basso fino alla sezione **mappe di precisione** più recenti.
3. Dovrebbe essere possibile visualizzare l'immagine nella sezione relativa alle **Immagini drone** .

    ![Beat Farm progetto](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Scaricare immagini drone

Quando si seleziona la sezione relativa alle immagini drone, viene visualizzata una finestra popup che mostra un'immagine ad alta risoluzione della orthomosaic drone.

![Beat Farm progetto](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Visualizza tutte le mappe drone

I file e le immagini caricati dal provider drone vengono visualizzati nella sezione Maps. Selezionare la sezione **Maps** , filtrare in base alla **Farm** e scegliere i file appropriati da visualizzare e scaricare:

  ![Beat Farm progetto](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare le [API](references-for-farmbeats.md#rest-api) dell'hub dati FarmBeats per ottenere le immagini dei droni.

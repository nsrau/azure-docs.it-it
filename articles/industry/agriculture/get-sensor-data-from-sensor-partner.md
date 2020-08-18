---
title: Ottenere i dati dei sensori dai partner
description: Questo articolo descrive come ottenere i dati dei sensori dai partner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 08099ede82f8ebd9c1cd6e585a16800ee1626f65
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508788"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Ottenere i dati dei sensori dai partner dei sensori

Azure FarmBeats consente di portare i dati in streaming dai dispositivi e dai sensori Internet in datahub. Attualmente sono supportati i seguenti partner di dispositivi per i sensori.

  ![Partner FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

L'integrazione dei dati del dispositivo con Azure FarmBeats consente di ottenere dati di base dai sensori di Internet delle cose distribuiti nella farm nell'hub dati. I dati, una volta disponibili, possono essere visualizzati tramite l'acceleratore FarmBeats. I dati possono essere usati per la compilazione di modelli di data fusion e Machine Learning/intelligenza artificiale (ML/AI) usando FarmBeats.

Per avviare il flusso di dati dei sensori, verificare quanto segue:

-  FarmBeats è stato installato in Azure Marketplace.
-  Si è deciso di scegliere i sensori e i dispositivi da installare nella farm.
-  Se si prevede di usare i sensori di umidità del suolo, usare la mappa di posizionamento del sensore di umidità del suolo FarmBeats per ottenere una raccomandazione sul numero di sensori e sul percorso in cui devono essere posizionati esattamente. Per altre informazioni, vedere [generate Maps](generate-maps-in-azure-farmbeats.md).
- È possibile acquistare e distribuire i dispositivi o i sensori dal partner del dispositivo nella farm. Assicurarsi che sia possibile accedere ai dati dei sensori tramite la soluzione per i partner del dispositivo.

## <a name="enable-device-integration-with-farmbeats"></a>Abilitare l'integrazione dei dispositivi con FarmBeats

Dopo aver avviato il flusso dei dati del sensore, è possibile iniziare il processo di recupero dei dati nel sistema FarmBeats. Fornire le informazioni seguenti al provider di dispositivi per abilitare l'integrazione con FarmBeats:

 - Endpoint API
 - ID tenant
 - ID client
 - Segreto client
 - Stringa di connessione all’Hub eventi

Per generare le informazioni precedenti, attenersi alla procedura seguente:

> [!NOTE]
> È necessario completare questi passaggi in Azure per accedere alla sottoscrizione di Azure in cui viene distribuito FarmBeats.

1. Accedere a https://portal.azure.com/.

2. **Se si utilizza FarmBeats versione 1.2.7 o successiva, ignorare i passaggi a, b e c e andare al passaggio 3.** È possibile controllare la versione di FarmBeats selezionando l'icona **delle impostazioni** nell'angolo superiore destro dell'interfaccia utente di FarmBeatsri.

      a.  Passa a **Azure Active Directory**  >  **registrazioni** per l'app

      b. Selezionare la **registrazione dell'app** creata come parte della distribuzione di FarmBeats. Avrà lo stesso nome di FarmBeats datahub.

      c. Selezionare **esporre un'API** > selezionare **Aggiungi un'applicazione client** e immettere **04b07795-8ddb-461A-BBEE-02f9e1bf7b46** e selezionare **autorizzazione ambito**. In questo modo si concederà l'accesso all'interfaccia della riga di comando di Azure (Cloud Shell) per eseguire i passaggi seguenti:

3. Aprire Cloud Shell. Questa opzione è disponibile sulla barra degli strumenti nell'angolo superiore destro della portale di Azure.

    ![Barra degli strumenti portale di Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Verificare che l'ambiente sia impostato su **PowerShell**. Per impostazione predefinita, è impostato su bash.

    ![Impostazione della barra degli strumenti di PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Passare alla Home Directory.

    ```azurepowershell-interactive
    cd
    ```

6. Eseguire il comando seguente. In questo modo si connette un account autenticato da usare per le richieste di Azure AD

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. Eseguire il comando seguente. Verrà scaricato uno script nella Home Directory.

    ```azurepowershell-interactive

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. Eseguire lo script seguente. Lo script richiede l'ID tenant, che può essere ottenuto da **Azure Active Directory**  >  pagina**Panoramica** .

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

9. Seguire le istruzioni visualizzate per acquisire i valori per l' **endpoint API**, l' **ID tenant**, l' **ID client**, il **segreto client**e la **stringa di connessione EventHub**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrare i dati del dispositivo usando le credenziali generate

A questo punto sono disponibili le informazioni seguenti generate dalla sezione precedente.
 - Endpoint API
 - Stringa di connessione all’Hub eventi
 - ID client
 - Segreto client
 - ID tenant

Per il collegamento di FarmBeats è necessario fornire al partner del dispositivo. Passare al portale per i partner per dispositivi per eseguire la stessa operazione. Ad esempio, nel caso in cui si utilizzino dispositivi di Davis Instruments, Teralytic o Pessl Instruments (Metos.at) passano alle pagine corrispondenti, come indicato di seguito:

1. [Davis Instruments](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralytic](https://app.teralytic.com/)

3. [Strumenti Pessl](https://ng.fieldclimate.com/user-api-services)

Il provider del dispositivo conferma una corretta integrazione. Al momento della conferma, è possibile visualizzare tutti i dispositivi e i sensori in Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Visualizzare dispositivi e sensori

Usare la sezione seguente per visualizzare i dispositivi e i sensori per la farm.

### <a name="view-devices"></a>Visualizzare i dispositivi

Attualmente, FarmBeats supporta i dispositivi seguenti:

- **Node**: dispositivo a cui sono collegati uno o più sensori.
- **Gateway**: un dispositivo a cui sono collegati uno o più nodi.

Attenersi ai passaggi descritti di seguito.

1. Nella home page selezionare **dispositivi** dal menu.
  Nella pagina **dispositivi** vengono visualizzati il tipo di dispositivo, il modello, lo stato, la farm in cui è inserito e la data dell'ultimo aggiornamento per i metadati. Per impostazione predefinita, la colonna della farm è impostata su *null*. È possibile scegliere di assegnare un dispositivo a una farm. Per altre informazioni, vedere [assegnare i dispositivi](#assign-devices).
2. Selezionare il dispositivo per visualizzare le proprietà del dispositivo, la telemetria e i dispositivi figlio connessi al dispositivo.

    ![Pagina Dispositivi](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Visualizza sensori

Attenersi ai passaggi descritti di seguito.

1. Nella home page selezionare **sensori** dal menu.
  La pagina **sensori** Visualizza i dettagli relativi al tipo di sensore, alla farm a cui è connessa, al dispositivo padre, al nome della porta, al tipo di porta e all'ultimo stato aggiornato.
2. Selezionare il sensore per visualizzare le proprietà dei sensori, gli avvisi attivi e i dati di telemetria dal sensore.

    ![Pagina sensori](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Assegnare i dispositivi

Una volta che i dati del sensore sono stati propagati, è possibile assegnarli alla farm in cui sono stati distribuiti i sensori.

1. Nella home page selezionare **Farm** dal menu. Verrà visualizzata la pagina dell'elenco **Farm** .
2. Selezionare la farm a cui si vuole assegnare il dispositivo e selezionare **Aggiungi dispositivi**.
3. Viene visualizzata la finestra **Aggiungi dispositivi** . Selezionare il dispositivo che si desidera assegnare alla farm.

    ![Finestra Aggiungi dispositivi](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selezionare **Aggiungi dispositivi**. In alternativa, passare al menu **dispositivi** , selezionare i dispositivi che si desidera assegnare a una farm e selezionare **Associa dispositivi**.
5. Nella finestra **Associa dispositivi** selezionare la farm nell'elenco a discesa e selezionare **applica a tutti** per associare la farm a tutti i dispositivi selezionati.

    ![Finestra associa dispositivi](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Per associare ogni dispositivo a una farm diversa, selezionare la freccia a discesa nella colonna **assegna a farm** e selezionare una farm per ogni riga del dispositivo.

7. Selezionare **assegna** per completare l'assegnazione del dispositivo.

### <a name="visualize-sensor-data"></a>Visualizzare i dati dei sensori

Attenersi ai passaggi descritti di seguito.

1. Nella home page selezionare **Farm** dal menu per visualizzare la pagina **Farm** .
2. Selezionare la **Farm** per cui si desidera visualizzare i dati del sensore.
3. Nel dashboard della **Farm** è possibile visualizzare i dati di telemetria. È possibile visualizzare la telemetria in tempo reale o usare un **intervallo personalizzato** per visualizzare un intervallo di date specifico.

    ![Dashboard della farm](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Eliminare un sensore

Attenersi ai passaggi descritti di seguito.

1. Nella home page selezionare **sensori** dal menu per visualizzare la pagina **sensori** .
2. Selezionare il dispositivo che si desidera eliminare e selezionare **Elimina** nella finestra di conferma.

    ![Pulsante Elimina](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Un messaggio di conferma indica che il sensore è stato eliminato correttamente.

## <a name="delete-devices"></a>Eliminare dispositivi

Attenersi ai passaggi descritti di seguito.

1. Nella home page selezionare **dispositivi** dal menu per visualizzare la pagina **dispositivi** .
2. Selezionare il dispositivo che si desidera eliminare e selezionare **Elimina** nella finestra di conferma.

    ![Pulsante Elimina](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare i dati del sensore nell'istanza di Azure FarmBeats. A questo punto, viene illustrato come [generare mappe](generate-maps-in-azure-farmbeats.md#generate-maps) per le farm.

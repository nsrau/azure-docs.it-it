---
title: Ottenere i dati dei sensori dai partner
description: In questo articolo viene descritto come ottenere i dati dei sensori dai partner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 113ab07af8ada16c0779da510c5f5b1f1f5a290b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398225"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Ottenere i dati dei sensori dai partner di sensori

Azure FarmBeats ti aiuta a portare i dati in streaming dai tuoi dispositivi e sensori IoT in Datahub. Attualmente sono supportati i seguenti partner di dispositivi sensore.

  ![Partner di FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

L'integrazione dei dati dei dispositivi con Azure FarmBeats consente di ottenere dati di terra dai sensori IoT distribuiti nella farm all'hub dati. I dati, una volta disponibili, possono essere visualizzati tramite l'acceleratore FarmBeats. I dati possono essere utilizzati per la fusione dei dati e la creazione di modelli di machine learning/intelligenza artificiale (ML/AI) utilizzando FarmBeats.

Per avviare lo streaming dei dati del sensore, verificare quanto segue:

-  FarmBeats è stato installato in Azure Marketplace.
-  Si è deciso i sensori e i dispositivi che si desidera installare nella farm.
-  Se si prevede di utilizzare i sensori di umidità del suolo, utilizzare la mappa FarmBeats Soil Moisture Sensor Placement per ottenere una raccomandazione sul numero di sensori e dove esattamente si dovrebbe posizionarli. Per ulteriori informazioni, consultate [Generare mappe.](generate-maps-in-azure-farmbeats.md)
- È possibile acquistare e distribuire dispositivi o sensori dal partner dispositivi nella farm. Assicurati di poter accedere ai dati del sensore tramite la soluzione dei tuoi partner di dispositivi.

## <a name="enable-device-integration-with-farmbeats"></a>Abilitare l'integrazione dei dispositivi con FarmBeatsEnable device integration with FarmBeats

Dopo aver avviato lo streaming dei dati dei sensori, è possibile iniziare il processo di recupero dei dati nel sistema FarmBeats. Fornire le informazioni seguenti al provider di dispositivi per consentire l'integrazione con FarmBeats:

 - Endpoint API
 - ID tenant
 - ID client
 - Segreto client
 - Stringa di connessione EventHub

Attenersi alla seguente procedura per generare le informazioni di cui sopra:

> [!NOTE]
> Questi passaggi devono essere completati in Azure per accedere alla sottoscrizione di Azure in cui viene distribuito FarmBeats.These steps are required to be completed on Azure to access the Azure subscription where FarmBeats is deployed.

1. Accedere a https://portal.azure.com/.

2. **Se si utilizza FarmBeats versione 1.2.7 o successiva, ignorare i passaggi a, b e c e andare al passaggio 3.** Puoi controllare la versione di FarmBeats selezionando l'icona **Impostazioni** nell'angolo in alto a destra dell'interfaccia utente di FarmBeats.

      a.  Passare a Registrazioni delle app **di Azure Active DirectoryGo** > to Azure Active Directory**App Registrations**

      b. Selezionare la **registrazione dell'app** creata come parte della distribuzione di FarmBeats. Avrà lo stesso nome del tuo datahub FarmBeats.

      c. Selezionare **Esporre un'API** > selezionare **Aggiungi un'applicazione client** e immettere **04b07795-8ddb-461a-bbee-02f9e1bf7b46** e **selezionare Authorize Scope**. In questo modo verrà consentito l'accesso all'interfaccia della riga di comando di Azure (Cloud Shell) per eseguire i passaggi seguenti:This will give access to the Azure CLI (Cloud Shell) to perform the below steps:

3. Aprire Cloud Shell. Questa opzione è disponibile sulla barra degli strumenti nell'angolo superiore destro del portale di Azure.This option is available on the toolbar in the upper-right corner of the Azure portal.

    ![Barra degli strumenti del portale di AzureAzure portal toolbar](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Verificare che l'ambiente sia impostato su **PowerShell**. Per impostazione predefinita, è impostato su Bash.By default, it's set to Bash.

    ![Impostazione della barra degli strumenti di PowerShellPowerShell toolbar setting](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Vai alla tua home directory.

    ```azurepowershell-interactive 
    cd  
    ```

6. Eseguire il comando seguente. Questo scaricherà uno script nella tua home directory.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. Eseguire lo script seguente. Lo script richiede l'ID tenant, che può essere ottenuto dalla pagina Panoramica di **Azure Active Directory.The** > script asks for the Tenant ID, which can be obtained from Azure Active Directory**Overview page.**

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. Seguire le istruzioni visualizzate per acquisire i valori per **Endpoint API**, **ID tenant**, ID **client**, **Segreto client**e Stringa di **connessione EventHub**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrare i dati del dispositivo usando le credenziali generateIntegrate device data by using the generated credentials

A questo punto sono disponibili le seguenti informazioni generate dalla sezione precedente.
 - Endpoint API
 - Stringa di connessione EventHub
 - ID client
 - Segreto client
 - ID tenant

Dovrai fornirlo al tuo partner di dispositivi per il collegamento di FarmBeats. Passare al portale dei partner per dispositivi per eseguire la stessa operazione. Ad esempio, nel caso in cui si utilizzino dispositivi di Davis Instruments, Teralytic o Pessl Instruments (Metos.at) andare alle pagine corrispondenti come indicato di seguito:

1. [Davis Strumenti](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralittico](https://app.teralytic.com/)

3. [Strumenti Pessl](https://ng.fieldclimate.com/user-api-services)

Il provider di dispositivi conferma la corretta integrazione. Dopo la conferma, è possibile visualizzare tutti i dispositivi e sensori in Azure FarmBeats.Upon confirmation, you can view all the devices and sensors on Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Visualizzare dispositivi e sensori

Utilizzare la sezione seguente per visualizzare i dispositivi e i sensori per la farm.

### <a name="view-devices"></a>Visualizzare i dispositivi

Attualmente, FarmBeats supporta i seguenti dispositivi:

- **Nodo**: Dispositivo a cui sono collegati uno o più sensori.
- **Gateway**: Dispositivo a cui sono collegati uno o più nodi.

A tale scopo, seguire questa procedura:

1. Nella home page, selezionare **Dispositivi** dal menu.
  Nella pagina **Dispositivi** vengono visualizzati il tipo di dispositivo, il modello, lo stato, la farm in cui è inserito e la data dell'ultimo aggiornamento per i metadati. Per impostazione predefinita, la colonna della farm è impostata su *NULL*. È possibile scegliere di assegnare un dispositivo a una farm. Per ulteriori informazioni, consultate [Assegnare dispositivi.](#assign-devices)
2. Selezionare il dispositivo per visualizzare le proprietà del dispositivo, i dati di telemetria e i dispositivi figlio connessi al dispositivo.

    ![Pagina Dispositivi](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Visualizza sensori

A tale scopo, seguire questa procedura:

1. Nella home page, selezionare **Sensori** dal menu.
  Nella pagina Sensori vengono **visualizzati** i dettagli relativi al tipo di sensore, alla farm a cui è connesso, al nome della porta padre, al nome della porta, al tipo di porta e all'ultimo stato aggiornato.
2. Selezionare il sensore per visualizzare le proprietà del sensore, gli avvisi attivi e i dati di telemetria dal sensore.

    ![Pagina Sensori](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Assegnare dispositivi  

Dopo il flusso dei dati del sensore, è possibile assegnarli alla farm in cui sono stati distribuiti i sensori.

1. Nella home page selezionare **Farms** dal menu. Viene visualizzata la pagina elenco **Farms.**
2. Selezionare la farm a cui si desidera assegnare il dispositivo e scegliere **Aggiungi dispositivi**.
3. Viene visualizzata la finestra **Aggiungi dispositivi.** Selezionare il dispositivo che si desidera assegnare alla farm.

    ![Finestra Aggiungi dispositivi](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selezionare **Aggiungi dispositivi**. In alternativa, passare al menu **Dispositivi,** selezionare i dispositivi che si desidera assegnare a una farm e selezionare **Associa dispositivi**.
5. Nella finestra **Associa dispositivi** selezionare la farm dall'elenco a discesa e selezionare Applica **a tutti** per associare la farm a tutti i dispositivi selezionati.

    ![Finestra Associa dispositivi](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Per associare ogni dispositivo a una farm diversa, selezionare la freccia a discesa nella colonna **Assegna a farm** e selezionare una farm per ogni riga del dispositivo.

7. Selezionare **Assegna** per completare l'assegnazione del dispositivo.

### <a name="visualize-sensor-data"></a>Visualizzare i dati dei sensori

A tale scopo, seguire questa procedura:

1. Nella home page selezionare **Farms** dal menu per visualizzare la pagina **Farm.**
2. Selezionare la **farm** per la quale si desidera visualizzare i dati del sensore.
3. Nel dashboard **della farm** è possibile visualizzare i dati di telemetria. È possibile visualizzare i dati di telemetria in tempo reale o usare **L'intervallo** di date personalizzato per visualizzare un intervallo di date specifico.

    ![Dashboard della farm](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Eliminare un sensore

A tale scopo, seguire questa procedura:

1. Nella home page, selezionare **Sensori** dal menu per visualizzare la pagina **Sensori.**
2. Selezionare il dispositivo che si desidera eliminare e selezionare **Elimina** nella finestra di conferma.

    ![Pulsante Elimina](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Un messaggio di conferma indica che il sensore è stato eliminato correttamente.

## <a name="delete-devices"></a>Eliminare dispositivi

A tale scopo, seguire questa procedura:

1. Nella home page, selezionare **Dispositivi** dal menu per visualizzare la pagina **Dispositivi.**
2. Selezionare il dispositivo che si desidera eliminare e selezionare **Elimina** nella finestra di conferma.

    ![Pulsante Elimina](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto si dispone del flusso di dati del sensore nell'istanza di Azure FarmBeats.You now have sensor data flowing into your Azure FarmBeats instance. A questo punto, scopri come [generare mappe](generate-maps-in-azure-farmbeats.md#generate-maps) per le tue farm.

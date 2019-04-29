---
title: Visualizzare i dati della soluzione di monitoraggio remoto usando Power BI - Azure | Microsoft Docs
description: In questa esercitazione si useranno Power BI Desktop e Cosmos DB per integrare i dati generati da una soluzione di monitoraggio remoto in una visualizzazione personalizzata. In questo modo gli utenti possono compilare dashboard personalizzati e condividerli con gli utenti esterni alla soluzione.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 3398c6d318e0e3c51d3f6cfe8af651a6e3f55c9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448136"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualizzare i dati della soluzione di monitoraggio remoto usando Power BI - Azure

Questa esercitazione illustrerà in modo dettagliato come collegare i dati della soluzione di monitoraggio remoto da Cosmos DB a Power BI. Grazie a questo collegamento è possibile creare dashboard personalizzati e aggiungerli al dashboard della soluzione di monitoraggio remoto. Questo flusso di lavoro consente di creare grafi più specifici, in aggiunta a quelli predefiniti. È quindi possibile usare questa esercitazione per integrare altri flussi di dati o per creare dashboard personalizzati che è possibile usare all'esterno della soluzione di monitoraggio remoto. La creazione di dashboard in Power BI consente anche l'interazione reciproca dei pannelli quando si selezionano elementi specifici. Ad esempio, con un filtro che visualizza solo le informazioni sui veicoli simulati, ogni elemento del dashboard interagisce per mostrare solo le informazioni sui veicoli simulati. Se si vuole usare uno strumento diverso da Power BI, è possibile estendere questi passaggi per usare uno strumento di visualizzazione a scelta e creare un hook a Cosmos DB o a un database personalizzato, se ne è stato configurato uno. 

## <a name="prerequisites"></a>Prerequisiti

- È necessario che una soluzione di monitoraggio remoto sia attualmente in esecuzione
- È necessario avere l'accesso al [portale di Azure](https://portal.azure.com) e alla sottoscrizione in cui sono in esecuzione la soluzione e l'hub IoT
- È necessario che sia installata una versione qualsiasi di [Power BI Desktop](https://powerbi.microsoft.com).


## <a name="information-needed-from-azure-portal"></a>Informazioni necessarie dal portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) ed effettuare l'accesso se necessario

2. Fare clic sui gruppi di risorse nel pannello di sinistra.

    ![Navigazione pannello a sinistra](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Passare al gruppo di risorse di cui è in esecuzione la soluzione IoT e fare clic per passare alla pagina di panoramica del gruppo di risorse. 

4. Nella pagina di panoramica fare clic sull'elemento, che ha il tipo "Account Azure Cosmos DB"; si verrà quindi reindirizzati alla pagina di panoramica del flusso Cosmos DB per tale soluzione IoT.

    ![Gruppo di risorse](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Nel pannello a sinistra fare clic sulla sezione "Chiavi" e prendere nota dei valori seguenti da usare in Power BI:

   - URI
   - Chiave primaria

     ![chiavi](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Impostazione del flusso in Power BI
  
1. Aprire l'app Power BI Desktop e fare clic su "Scarica dati" nell'angolo superiore sinistro. 

    ![Scarica i dati](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Quando viene richiesto di immettere i dati, scegliere di cercare "Azure Cosmos DB" e selezionare questo connettore. In sostanza questo connettore estrae i dati direttamente dal database Cosmos DB della soluzione IoT di Azure
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Immettere le informazioni registrate in precedenza:

    * URI
    * Chiave primaria

4. Selezionare tutte le tabelle da importare in Power BI. Questa azione inizia il caricamento dei dati. Più lungo è il tempo di esecuzione della soluzione, più lungo può essere il tempo di caricamento dei dati (fino a poche ore). 

    ![Importa tabelle](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Al termine del caricamento dei dati fare clic su "Modifica query" nella riga superiore di Power BI ed espandere tutte le tabelle facendo clic sulle frecce sulla barra gialla per ogni tabella. In questo modo la visualizzazione si espande in modo da includere tutte le colonne. Si noterà che il tipo di dati per aspetti come umidità, tempo velocità e così via, non è corretto.

    ![Nuova colonna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Ad esempio, i dati ricevuti in Power BI sono stati modificati nell'ora UNIX quando sono stati inseriti tramite il connettore. Per regolare questa conversione, in futuro è possibile creare una nuova colonna e usare questa equazione per convertire i valori nel formato data/ora: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Tabella aggiornata](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received è solo una delle colonne con la formattazione UNIX e può essere sostituita con altre che richiedono la conversione. 
  
    Altri punti dati che sono stati convertiti nel tipo stringa possono essere modificati in valori Double o Int, se appropriato, usando la stessa procedura illustrata in precedenza.

## <a name="creating-a-dashboard"></a>Creazione di un dashboard

Dopo avere connesso il flusso si è pronti a creare dashboard personalizzati. Di seguito è riportato un dashboard di esempio in cui i dati di telemetria generati da dispositivi simulati vengono usati e valutati con diversi pivot, ad esempio: 

* Percorso dei dispositivi su una mappa (destra)
* Dispositivi con relativo stato e gravità (in alto a sinistra)
* Dispositivi con le regole attivate ed eventuali avvisi disattivati corrispondenti (in basso a sinistra)

![Visualizzazione in Power BI](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Pubblicazione del dashboard e aggiornamento dei dati

Dopo avere creato i dashboard, è consigliabile [pubblicare i dashboard di Power BI](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) per condividerli con altri utenti.

È opportuno anche [aggiornare i dati](https://docs.microsoft.com/power-bi/refresh-data) nel dashboard pubblicato per disporre del set di dati più recente.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato appreso come visualizzare i dati della soluzione di monitoraggio remoto tramite Power BI

Per altre informazioni sulla personalizzazione della soluzione di monitoraggio remoto, vedere:

* [Personalizzare l'acceleratore di soluzioni di monitoraggio remoto](iot-accelerators-remote-monitoring-customize.md)
* [Guida di riferimento per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guida per la risoluzione dei problemi per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)


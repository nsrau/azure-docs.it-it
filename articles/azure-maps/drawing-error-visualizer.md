---
title: Usare il visualizzatore errori di disegno di Mappe di Azure
description: Questo articolo illustra come visualizzare avvisi ed errori restituiti dall'API di conversione di Creator.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3f9451a5ffd13c67232107d8db1e2da4a3891ec
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524744"
---
# <a name="using-the-azure-maps-drawing-error-visualizer"></a>Uso del visualizzatore errori di disegno di Mappe di Azure

Il visualizzatore errori di disegno è un'applicazione Web autonoma che consente di visualizzare [avvisi ed errori del pacchetto di disegno](drawing-conversion-error-codes.md) rilevati durante il processo di conversione. L'applicazione Web del visualizzatore errori è costituita da una pagina statica che è possibile usare senza connettersi a Internet.  È possibile usare il visualizzatore errori per correggere errori e avvisi in base ai [requisiti del pacchetto di disegno](drawing-requirements.md). L'[API di conversione Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) restituisce una risposta con un collegamento al visualizzatore errori solo quando viene rilevato un errore.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter scaricare il visualizzatore errori di disegno, è necessario:

1. [Creare un account di Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione.
3. [Creare una risorsa Creator](how-to-manage-creator.md)

Questa esercitazione usa l'applicazione [Postman](https://www.postman.com/), ma è possibile scegliere un ambiente di sviluppo API diverso.

## <a name="download"></a>Download

1. Caricare il pacchetto di disegno nel servizio Creator di Mappe di Azure per ottenere un valore di `udid` per il pacchetto caricato. Per istruzioni su come caricare un pacchetto, vedere [Caricare un pacchetto di disegno](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Ora che il pacchetto di disegno è stato caricato, si userà il valore di `udid` del pacchetto caricato per convertirlo in dati della pianta. Per istruzioni su come convertire un pacchetto, vedere [Convertire un pacchetto di disegno](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Se il processo di conversione riesce, non si riceverà un collegamento allo strumento del visualizzatore errori.

3. Nella scheda **Headers** (Intestazioni) della risposta nell'applicazione Postman cercare la proprietà `diagnosticPackageLocation`, restituita dall'API di conversione. La risposta dovrebbe essere simile al codice JSON seguente:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Per scaricare il visualizzatore errori del pacchetto di disegno, effettuare una richiesta `HTTP-GET` nell'URL di `diagnosticPackageLocation`.

## <a name="setup"></a>Configurazione

All'interno del pacchetto compresso scaricato dal collegamento di `diagnosticPackageLocation` sono presenti due file.

* _VisualizationTool.zip_: contiene il codice sorgente, i file multimediali e la pagina Web relativi al visualizzatore di errori di disegno.
* _ConversionWarningsAndErrors.json_: contiene un elenco formattato di avvisi, errori e dettagli aggiuntivi usati dal visualizzatore di errori di disegno.

Decomprimere la cartella _VisualizationTool.zip_. Contiene gli elementi seguenti:

* Cartella _assets_: immagini e file multimediali
* Cartella _static_: codice sorgente
* File _index.html_: applicazione Web

Aprire il file _index.html_ con uno dei browser seguenti, prestando attenzione al numero di versione. È possibile usare una versione diversa, purché garantisca un comportamento compatibile con la versione indicata.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Uso dello strumento del visualizzatore errori di disegno

Dopo aver avviato lo strumento del visualizzatore errori di disegno, verrà visualizzata la pagina per il caricamento. La pagina di caricamento contiene una casella per il trascinamento della selezione. Tale casella funge anche da pulsante che consente di avviare una finestra di dialogo Esplora file.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="App del visualizzatore errori di disegno - Pagina iniziale":::

Il file _ConversionWarningsAndErrors.json_ è stato inserito nella radice della directory scaricata. Per caricare il file _ConversionWarningsAndErrors.json_, è possibile trascinarlo nella casella di trascinamento della selezione oppure fare clic sulla casella, individuare il file nella finestra di dialogo Esplora file e quindi caricarlo.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="App del visualizzatore errori di disegno - Caricamento dei dati con trascinamento della selezione":::

Dopo aver caricato il file _ConversionWarningsAndErrors.json_, verrà visualizzato un elenco di errori e avvisi del pacchetto di disegno. Per ogni errore o avviso viene specificato il layer, il livello e un messaggio dettagliato. Per visualizzare informazioni dettagliate su un errore o un avviso, fare clic sul collegamento **Dettagli** . Al di sotto dell'elenco verrà visualizzata una sezione intratta. È ora possibile passare ai singoli errori per visualizzare altre informazioni su come risolverli.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="App del visualizzatore errori di disegno - Errori e avvisi":::

## <a name="next-steps"></a>Passaggi successivi

Quando il [pacchetto di disegno soddisfa i requisiti](drawing-requirements.md), è possibile usare il [servizio del set di dati di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/conversion) per convertire il pacchetto di disegno in un set di dati. È quindi possibile usare il modulo Web Piante di interni per sviluppare l'applicazione. Per altre informazioni, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Codici errore della conversione di disegni](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Creator per piante di interni](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Usare il modulo Piante di interni](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementare stili dinamici per le piante di interni](indoor-map-dynamic-styling.md)
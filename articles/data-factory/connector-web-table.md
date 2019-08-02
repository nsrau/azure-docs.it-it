---
title: Copiare dati da Tabella Web usando Azure Data Factory | Documentazione Microsoft
description: Informazioni sul connettore Tabella Web del servizio Azure Data Factory, che consente di copiare dati da una tabella Web in archivi dati supportati da Data Factory come sink.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 09de2919b22ebb088b23c1ab59f60d182657a2f1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720417"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Copiare dati da Tabella Web usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-web-table-connector.md)
> * [Versione corrente](connector-web-table.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database Tabella Web. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

La differenza tra questo connettore Tabella Web, il [connettore REST](connector-rest.md) e il [connettore HTTP](connector-http.md) è la seguente:

- Il **connettore Tabella Web** estrae il contenuto della tabella da una pagina Web HTML.
- Il **connettore REST** supporta in modo specifico la copia dei dati dalle API RESTful.
- Il **connettore HTTP** è un connettore generico per recuperare i dati da qualsiasi endpoint HTTP, ad esempio per scaricare file. 

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database Tabella Web in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Tabella Web supporta **l'estrazione del contenuto di una tabella da una pagina HTML**.

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore di tabelle Web, è necessario impostare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Tabella Web.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Tabella Web sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Web** |Yes |
| url | URL dell'origine Web |Sì |
| authenticationType | Il valore consentito è: **Anonymous**. |Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Sì |

**Esempio:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Tabella Web.

Per copiare dati da Tabella Web, impostare la proprietà type del set di dati su **WebTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **WebTable** | Sì |
| path |URL relativo della risorsa che contiene la tabella. |No. Quando non è specificato alcun percorso, viene usato solo l'URL specificato nella definizione del servizio collegato. |
| index |Indice della tabella nella risorsa. Per i passaggi per ottenere l'indice di una tabella in una pagina HTML, vedere la sezione [Ottenere l'indice di una tabella in una pagina HTML](#get-index-of-a-table-in-an-html-page) . |Yes |

**Esempio:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Tabella Web.

### <a name="web-table-as-source"></a>Tabella Web come origine

Per copiare dati da Tabella Web, impostare il tipo di origine nell'attività di copia su **WebSource**. Non sono supportate altre proprietà.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Ottenere l'indice di una tabella in una pagina HTML

Per ottenere l'indice di una tabella che è necessario configurare nelle [proprietà del set di dati](#dataset-properties), è possibile usare, ad esempio, Excel 2016 come strumento, come indicato di seguito:

1. Avviare **Excel 2016** e passare alla scheda **Dati**.
2. Fare clic su **Nuova query** sulla barra degli strumenti, scegliere **Da altre origini** e fare clic su **Da Web**.

    ![Menu di Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. Nella finestra di dialogo **Da Web** immettere l'**URL** che si intende usare nel codice JSON del servizio collegato (ad esempio, https://en.wikipedia.org/wiki/) insieme al percorso specificato per il set di dati (ad esempio, AFI%27s_100_Years...100_Movies) e fare clic su **OK**.

    ![Finestra di dialogo Da Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL usato in questo esempio: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se viene visualizzata la finestra di dialogo **Accedi a contenuto Web**, selezionare l'**URL** corretto, l'**autenticazione** e fare clic su **Connetti**.

   ![Finestra di dialogo Accedi a contenuto Web](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Fare clic su un elemento della **tabella** nella visualizzazione ad albero per visualizzare il contenuto dalla tabella e quindi fare clic su **Modifica** nella parte inferiore.  

   ![Finestra di dialogo Strumento di spostamento](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Nella finestra **Editor di query** fare clic sul pulsante **Editor avanzato** sulla barra degli strumenti.

    ![Pulsante Editor avanzato](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. Nella finestra di dialogo Editor avanzato il numero accanto a "Source" è l'indice.

    ![Editor avanzato - Indice](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Se si usa Excel 2013, per ottenere l'indice usare [Microsoft Power Query per Excel](https://www.microsoft.com/download/details.aspx?id=39379) . Per informazioni dettagliate, vedere l'articolo [Connettersi a una pagina Web (Power Query)](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) . I passaggi sono simili se si usa [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

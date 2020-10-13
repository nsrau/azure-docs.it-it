---
title: Creare un registro dello schema di hub eventi di Azure
description: Questo articolo illustra come creare un registro dello schema in uno spazio dei nomi di hub eventi di Azure.
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 90556e0843cda94dc79330321d027f8d28eb7d20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652194"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Creare un registro dello schema di hub eventi di Azure (anteprima)
Questo articolo illustra come creare un gruppo di schemi con gli schemi in un registro dello schema ospitato da Hub eventi di Azure. Per una panoramica della funzionalità registro dello schema di hub eventi di Azure, vedere [registro dello schema di Azure in hub eventi](schema-registry-overview.md).

> [!NOTE]
> - La funzionalità **Registro schemi** è attualmente disponibile in **anteprima** e non è consigliata per i carichi di lavoro di produzione.
> - La funzionalità è disponibile solo nei livelli **Standard** e **Dedicato**, non nel livello **Basic**.

## <a name="prerequisites"></a>Prerequisiti
[Creare uno spazio dei nomi di hub eventi](event-hubs-create.md#create-an-event-hubs-namespace). È anche possibile usare uno spazio dei nomi esistente. 

## <a name="create-a-schema-group"></a>Creazione di un gruppo di schemi
1. Passare alla pagina **dello spazio dei nomi di hub eventi** . 
1. Selezionare **Registro schemi** nel menu a sinistra. Per creare un gruppo di schemi, selezionare **+ gruppo di schemi** sulla barra degli strumenti. 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Pagina Registro schema":::
1. Nella pagina **Crea gruppo di schemi** eseguire le operazioni seguenti:
    1. Immettere un **nome** per il gruppo di schemi.
    1. Per **tipo di serializzazione**selezionare un formato di serializzazione applicabile a tutti gli schemi nel gruppo di schemi. Attualmente, **avro** è l'unico tipo supportato, quindi selezionare **avro**. 
    1. Consente di selezionare una **modalità di compatibilità** per tutti gli schemi del gruppo. Per **avro**, sono supportate le modalità di compatibilità con le versioni precedenti e precedenti. 
    1. Selezionare quindi **Crea** per creare il gruppo di schemi. 
1. Consente di selezionare il nome del **gruppo di schemi** nell'elenco dei gruppi di schemi.

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="Pagina Registro schema":::    
1. Viene visualizzata la pagina del **gruppo di schemi** per il gruppo.

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="Pagina Registro schema":::
    

## <a name="add-a-schema-to-the-schema-group"></a>Aggiungere uno schema al gruppo di schemi
In questa sezione si aggiunge uno schema al gruppo di schemi utilizzando il portale di Azure. 

1. Nella pagina **gruppo di schemi** selezionare **+ schema** sulla barra degli strumenti. 
1. Nella pagina **Crea schema** eseguire i passaggi seguenti:
    1. Immettere un **nome** per lo schema.
    1. Immettere lo **schema** seguente nella casella di testo. È anche possibile selezionare file con lo schema.
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. Selezionare **Crea**. 
1. Consente di selezionare lo **schema** dall'elenco degli schemi. 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="Pagina Registro schema":::
1. Viene visualizzata la pagina **Panoramica schema** per lo schema. 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="Pagina Registro schema":::    
1. Se sono presenti più versioni di uno schema, queste verranno visualizzate nell'elenco a discesa **versioni** . Selezionare una versione per passare allo schema della versione. 

## <a name="create-a-new-version-of-schema"></a>Creare una nuova versione dello schema

1. Aggiornare lo schema nella casella di testo e selezionare **convalida**. Nell'esempio seguente `id` è stato aggiunto un nuovo campo allo schema. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="Pagina Registro schema":::    
    
1. Verificare lo stato di convalida e le modifiche e selezionare **Salva**. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="Pagina Registro schema":::     
1. Si noterà che `2` è selezionato per la **versione** nella pagina **Panoramica schema** . 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="Pagina Registro schema":::    
1. Selezionare `1` per visualizzare la versione 1 dello schema. 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="Pagina Registro schema":::    


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul Registro di sistema dello schema, vedere [registro dello schema di Azure in hub eventi](schema-registry-overview.md).


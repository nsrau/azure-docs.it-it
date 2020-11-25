---
title: includere file
description: includere file
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 37cdb6b466417add8dae69464304ce2f32247c8d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559429"
---
È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un’API di Azure Cosmos DB per un database e un contenitore MongoDB. 

1. Selezionare **Esplora dati** > **Nuovo contenitore**. 
    
    A destra viene visualizzata l'area **Aggiungi contenitore**. Per vederla potrebbe essere necessario scorrere verso destra.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Esplora dati nel portale di Azure, riquadro Aggiungi contenitore":::

2. Nella pagina **Aggiungi contenitore** immettere le impostazioni per il nuovo contenitore.

    |Impostazione|Valore consigliato|Descrizione
    |---|---|---|
    |**ID database**|db|Immettere *db* come nome del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/, \\, #, ?` o spazi finali. Selezionare l'opzione **Provision database throughput** (Provisioning delle unità di elaborazione del database) che consente di condividere le unità di elaborazione del database con tra tutti i contenitori al suo interno. Questa opzione permette anche di risparmiare sui costi. |
    |**Velocità effettiva**|400|Lasciare la velocità effettiva a 400 unità di richiesta al secondo (UR/s). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento. È anche possibile scegliere la [modalità di scalabilità automatica](../articles/cosmos-db/provision-throughput-autoscale.md), che fornirà un intervallo di UR/s che aumenterà e diminuirà in modo dinamico in base alle esigenze.| 
    |**ID raccolta**|coll|Immettere `coll` come nome per il nuovo contenitore. Gli ID contenitore prevedono gli stessi requisiti relativi ai caratteri dei nomi di database.|
    |**Capacità di archiviazione**|Fisso (10 GB)|Immettere *Fisso (10 GB)* per questa applicazione. Se si seleziona *Illimitati*, sarà necessario creare una `Shard Key` che verrà richiesta da tutti gli elementi inseriti.|
    |**Chiave di partizione**| /_id| L'esempio descritto in questo articolo non usa una chiave di partizione, quindi l’impostazione su */_id* comporterà l’uso del campo ID generato automaticamente come chiave di partizione. Per altre informazioni sul partizionamento, vedere [Partizionamento in Azure Cosmos DB](../articles/cosmos-db/partitioning-overview.md)|
        
    Selezionare **OK**. In Esplora dati vengono visualizzati il nuovo database e il nuovo contenitore.
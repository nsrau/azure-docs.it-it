---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 45fa6a332697cf298b2446212701025007682357
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754244"
---
È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database e un contenitore. 

1. Selezionare **Esplora dati** > **Nuovo contenitore**. 
    
    A destra viene visualizzata l'area **Aggiungi contenitore**. Per vederla potrebbe essere necessario scorrere verso destra.

    ![Esplora dati nel portale di Azure, riquadro Aggiungi contenitore](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. Nella pagina **Aggiungi contenitore** immettere le impostazioni per il nuovo contenitore.

    |Impostazione|Valore consigliato|DESCRIZIONE
    |---|---|---|
    |**ID database**|Attività|Immettere *ToDoList* come nome del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/, \\, #, ?` o spazi finali. Selezionare l'opzione **Provision database throughput** (Provisioning delle unità di elaborazione del database) che consente di condividere le unità di elaborazione del database con tra tutti i contenitori al suo interno. Questa opzione permette anche di risparmiare sui costi. |
    |**Velocità effettiva**|400|Lasciare la velocità effettiva a 400 unità di richiesta al secondo (UR/s). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento.| 
    |**ID contenitore**|Items|Immettere *Items* come nome per il nuovo contenitore. Gli ID contenitore prevedono gli stessi requisiti relativi ai caratteri dei nomi di database.|
    |**Chiave di partizione**| /category| L'esempio descritto in questo articolo usa */category* come chiave di partizione.|
    
    Oltre alle impostazioni precedenti, è possibile aggiungere **Chiavi univoche** per il contenitore. In questo esempio il campo viene lasciato vuoto. Le chiavi univoche consentono agli sviluppatori di aggiungere un livello di integrità dei dati nel database. Se si definiscono criteri di chiave univoca durante la creazione di un contenitore, si ha la sicurezza che uno o più valori siano univoci per ogni chiave di partizione. Per altre informazioni, vedere l'articolo [Chiavi univoche in Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Selezionare **OK**. In Esplora dati vengono visualizzati il nuovo database e il nuovo contenitore.
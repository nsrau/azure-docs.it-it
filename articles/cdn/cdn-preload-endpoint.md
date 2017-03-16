---
title: Precaricare asset in un endpoint della rete CDN di Azure | Documentazione Microsoft
description: Informazioni su come precaricare il contenuto memorizzato nella cache in un endpoint della rete CDN di Azure.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 242c3a6bf656da9b029a780e8b80667405b7b92f
ms.lasthandoff: 03/04/2017


---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Precaricamento di risorse in un endpoint della rete CDN di Azure
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Per impostazione predefinita, gli asset vengono prima di tutto memorizzati nella cache quando vengono richiesti. Questo significa che la prima richiesta da ogni area potrebbe richiedere più tempo, poiché i server perimetrali non avranno il contenuto memorizzato nella cache e la richiesta dovrà essere inoltrata al server di origine. Il precaricamento del contenuto consente di evitare questa latenza della prima richiesta.

Oltre a fornire una migliore esperienza utente, il precaricamento di asset memorizzati nella cache può inoltre ridurre il traffico di rete sul server di origine.

> [!NOTE]
> Il precaricamento degli asset è utile per eventi di grandi dimensioni o contenuto che diventa disponibile contemporaneamente per un numero elevato di utenti, come ad esempio la nuova versione di un filmato o un aggiornamento software.
> 
> 

Questa esercitazione illustra in modo dettagliato il precaricamento di contenuto memorizzato nella cache in tutti i nodi perimetrali della rete CDN di Azure.

## <a name="walkthrough"></a>Procedura dettagliata
1. Nel [portale di Azure](https://portal.azure.com)passare al profilo di rete CDN contenente l'endpoint che si vuole precaricare.  Viene visualizzato il pannello del profilo.
2. Fare clic sull'endpoint nell'elenco.  Viene visualizzato il pannello dell'endpoint.
3. Nel pannello dell'endpoint della rete CDN fare clic sul pulsante Carica.
   
    ![Pannello dell'endpoint della rete CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Viene visualizzato il pannello Carica.
   
    ![Pannello di caricamento della rete CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Immettere il percorso completo di ogni asset da caricare, ad esempio `/pictures/kitten.png`, nella casella di testo **Percorso** .
   
   > [!TIP]
   > Dopo l'immissione di testo verranno visualizzate altre caselle di testo **Percorso** che consentono di compilare un elenco di più asset.  È possibile eliminare gli asset dall'elenco facendo clic sul pulsante con i puntini di sospensione (...).
   > 
   > I percorsi devono essere URL relativi che soddisfino l'[espressione regolare](https://msdn.microsoft.com/library/az24scfc.aspx) seguente:  
   > >Caricare un singolo percorso di file `@"^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$"`;  
   > >Caricare un singolo file con stringa di query `@"^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$";`  
   > 
   > Ogni asset deve avere il proprio percorso.  Non esiste alcuna funzionalità con caratteri jolly per il pre-caricamento degli asset.
   > 
   > 
   
    ![Pulsante Carica](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Fare clic sul pulsante **Carica** .
   
    ![Pulsante Carica](./media/cdn-preload-endpoint/cdn-load-button.png)

> [!NOTE]
> Le richieste di caricamento sono limitate a un massimo di 10 al minuto per ogni profilo di rete CDN.
> 
> 

## <a name="see-also"></a>Vedere anche
* [Ripulire un endpoint della rete CDN di Azure](cdn-purge-endpoint.md)
* [Riferimento API REST della rete CDN di Azure - Ripulire o precaricare un endpoint](https://msdn.microsoft.com/library/mt634451.aspx)



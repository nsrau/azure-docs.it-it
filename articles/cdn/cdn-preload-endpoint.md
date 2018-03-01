---
title: Precaricare asset in un endpoint della rete CDN di Azure | Documentazione Microsoft
description: Informazioni su come precaricare il contenuto memorizzato nella cache in un endpoint della rete CDN di Azure.
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: mazha
ms.openlocfilehash: e00205ddcaab277029d7185d0158a64818d0d49b
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Precaricamento di risorse in un endpoint della rete CDN di Azure
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Per impostazione predefinita, gli asset vengono memorizzati nella cache solo se vengono richiesti. I server perimetrali, quindi, non hanno ancora il contenuto memorizzato nella cache e devono inoltrare la richiesta al server di origine. La prima richiesta da ogni area, pertanto, può richiedere più tempo rispetto alle richieste successive. Per evitare questa latenza della prima richiesta, precaricare gli asset. Oltre a fornire una migliore esperienza utente, il precaricamento di asset memorizzati nella cache può ridurre il traffico di rete sul server di origine.

> [!NOTE]
> Il precaricamento degli asset è utile per eventi di grandi dimensioni o contenuto che diventa disponibile contemporaneamente per un numero elevato di utenti, come ad esempio la nuova versione di un filmato o un aggiornamento software.
> 
> 

Questa esercitazione illustra in modo dettagliato il precaricamento di contenuto memorizzato nella cache in tutti i nodi perimetrali della rete CDN di Azure.

## <a name="to-pre-load-assets"></a>Per precaricare gli asset
1. Nel [portale di Azure](https://portal.azure.com) passare al profilo di rete CDN contenente l'endpoint che si vuole precaricare. Viene visualizzato il riquadro del profilo.
    
2. Fare clic sull'endpoint nell'elenco. Viene visualizzato il riquadro dell'endpoint.
3. Nel riquadro dell'endpoint di rete CDN selezionare **Carica**.
   
    ![Nome endpoint di rete CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Viene visualizzato il riquadro **Carica**.
   
    ![Riquadro di caricamento della rete CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. In **Percorso contenuto** immettere il percorso completo di ogni asset da caricare, ad esempio `/pictures/kitten.png`.
   
   > [!TIP]
   > Nel momento in cui si inizia a immettere il testo vengono visualizzate altre caselle di testo **Percorso contenuto**, che consentono di compilare un elenco di più asset. Per eliminare gli asset dall'elenco, selezionare il pulsante con i puntini di sospensione (...) e quindi scegliere **Elimina**.
   > 
   > Ogni percorso di contenuto deve essere un URL relativo che soddisfa le [espressioni regolari](https://msdn.microsoft.com/library/az24scfc.aspx) seguenti:  
   > - Caricare un singolo percorso di file `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Caricare un singolo file con stringa di query: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Poiché ogni asset deve avere il proprio percorso, non esiste alcuna funzionalità con caratteri jolly per il pre-caricamento degli asset.
   > 
   > 
   
    ![Pulsante Carica](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Dopo avere immesso tutti i percorsi di contenuto, selezionare **Carica**.
   

> [!NOTE]
> È previsto un limite di 10 richieste di carico al minuto per il profilo di rete CDN e possono essere elaborati contemporaneamente fino a 50 percorsi simultanei. Ogni percorso ha un limite di lunghezza di 1024 caratteri.
> 
> 

## <a name="see-also"></a>Vedere anche 
* [Ripulire un endpoint della rete CDN di Azure](cdn-purge-endpoint.md)
* [Azure CDN REST API reference: Pre-load content on an endpoint](https://docs.microsoft.com/en-us/rest/api/cdn/endpoints/loadcontent) (Riferimento API REST della rete CDN di Azure: Precacaricare contenuto su un endpoint)
* [Azure CDN REST API reference: Pre-load content on an endpoint](https://docs.microsoft.com/en-us/rest/api/cdn/endpoints/purgecontent) (Riferimento API REST della rete CDN di Azure: Ripulire contenuto da un endpoint)


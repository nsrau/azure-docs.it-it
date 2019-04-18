---
title: Precaricare asset in un endpoint della rete CDN di Azure | Documentazione Microsoft
description: Informazioni su come precaricare il contenuto memorizzato nella cache in un endpoint della rete CDN di Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: 0d34985c8d83e8adad43aeec36ead939d8b22132
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918366"
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
   > Ogni percorso di contenuto deve essere un URL relativo che soddisfa le [espressioni regolari](/dotnet/standard/base-types/regular-expression-language-quick-reference) seguenti:  
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
* [Azure CDN REST API reference: Precaricare il contenuto in un endpoint](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure CDN REST API reference: Ripulire il contenuto da un endpoint](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)


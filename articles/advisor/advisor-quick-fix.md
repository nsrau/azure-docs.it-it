---
title: Correzione rapida della correzione per consigli di Advisor
description: Eseguire la correzione in blocco usando la correzione rapida in AdvisorPerform bulk remediation using Quick Fix in Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502490"
---
# <a name="quick-fix-remediation-for-advisor"></a>Correzione rapida per Advisor
**Quick Fix** consente un modo più rapido e semplice di correzione per la raccomandazione su più risorse. Fornisce funzionalità per le correzioni bulk per le risorse e consente di ottimizzare le sottoscrizioni più rapidamente con la correzione su larga scala per le risorse.
La funzionalità è disponibile solo per determinati consigli, tramite il portale di Azure.The feature is available for certain recommendations only, via Azure portal.


## <a name="steps-to-use-quick-fix"></a>Passaggi per l'utilizzo di 'Correzione rapida'

1. Nell'elenco dei suggerimenti con l'etichetta **Correzione rapida** fare clic sul suggerimento.

   ![Correzione rapida Advisor](./media/quick-fix-1.png)
   
   *I prezzi nell'immagine sono solo a scopo esemplificativo*

2. Nella pagina Dettagli raccomandazione verrà visualizzato un elenco di risorse per le quali si dispone di questo consiglio. Selezionare tutte le risorse che si desidera correggere per il suggerimento.

   ![Correzione rapida Advisor](./media/quick-fix-2.png)
   
   *I prezzi nell'immagine sono solo a scopo esemplificativo*

3. Dopo aver selezionato le risorse, fare clic sul pulsante **Correzione rapida** per correggere in blocco.

   > [!NOTE]
   > Alcune delle risorse elencate potrebbero essere disabilitate, perché non si dispone delle autorizzazioni appropriate per modificarle.
   
   > [!NOTE]
   > Se ci sono altre implicazioni, oltre ai vantaggi menzionati in Advisor, sarete comunicati nell'esperienza per aiutarvi a prendere decisioni di correzione informate.
   
4. Riceverai una notifica per il completamento della correzione. Verrà visualizzato un errore se sono presenti risorse che non sono corretti e risorse nella modalità selezionata nella visualizzazione elenco risorse.  


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione a Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Consigli di Advisor sulle prestazioni](advisor-performance-recommendations.md)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Consigli per l'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)

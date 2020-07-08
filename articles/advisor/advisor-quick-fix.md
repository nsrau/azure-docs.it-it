---
title: Correzione rapida per le raccomandazioni di Advisor
description: Eseguire il monitoraggio e l'aggiornamento in blocco utilizzando la correzione rapida in Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79502490"
---
# <a name="quick-fix-remediation-for-advisor"></a>Correzione rapida per Advisor
La **correzione rapida** consente un modo più semplice e rapido per la correzione per consigli su più risorse. Offre funzionalità di monitoraggio e aggiornamento bulk per le risorse e consente di ottimizzare le sottoscrizioni più velocemente con la correzione su larga scala per le risorse.
La funzionalità è disponibile solo per alcune indicazioni, tramite portale di Azure.


## <a name="steps-to-use-quick-fix"></a>Passaggi per usare la correzione rapida

1. Dall'elenco di raccomandazioni con l'etichetta **correzione rapida** , fare clic sull'indicazione.

   ![Correzione rapida Advisor](./media/quick-fix-1.png)
   
   *I prezzi nell'immagine sono solo a scopo esemplificativo*

2. Nella pagina Dettagli raccomandazione verrà visualizzato un elenco di risorse per cui si dispone di questa raccomandazione. Selezionare tutte le risorse che si vuole correggere per la raccomandazione.

   ![Correzione rapida Advisor](./media/quick-fix-2.png)
   
   *I prezzi nell'immagine sono solo a scopo esemplificativo*

3. Dopo aver selezionato le risorse, fare clic sul pulsante **correzione rapida** per eseguire il monitoraggio e l'aggiornamento in blocco.

   > [!NOTE]
   > Alcune delle risorse elencate potrebbero essere disabilitate, perché non si dispone delle autorizzazioni appropriate per modificarle.
   
   > [!NOTE]
   > Se sono presenti altre implicazioni, oltre ai vantaggi indicati in Advisor, l'utente verrà comunicato nell'esperienza per consentire di prendere decisioni informate sulla correzione.
   
4. Si riceverà una notifica per il completamento della correzione. Verrà visualizzato un errore se sono presenti risorse che non vengono risolte e risorse nella modalità selezionata nella visualizzazione elenco risorse.  


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Consigli di Advisor sulle prestazioni](advisor-performance-recommendations.md)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
* [API REST Advisor](https://docs.microsoft.com/rest/api/advisor/)

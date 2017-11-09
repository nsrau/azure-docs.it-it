---
title: "Monitorare l'integrità delle risorse della rete CDN di Azure | Documentazione Microsoft"
description: "Informazioni su come monitorare l'integrità delle risorse della rete CDN di Azure con Integrità risorse di Azure."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 37fe208f5087f318e665e76825127854b4a11c98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorare l'integrità delle risorse della rete CDN di Azure
  
L'integrità delle risorse della rete CDN di Azure costituisce un sottoinsieme di [Integrità risorse di Azure](../resource-health/resource-health-overview.md).  È possibile usare Integrità risorse di Azure per monitorare l'integrità delle risorse della rete CDN e ricevere indicazioni pratiche per risolvere i problemi.

>[!IMPORTANT] 
>L'integrità delle risorse della rete CDN di Azure attualmente riguarda solo l'integrità delle funzionalità API e di distribuzione della rete CDN globale.  Non vengono verificati i singoli endpoint della rete CDN.
>
>I segnali che indicano l'integrità delle risorse della rete CDN di Azure possono presentare ritardi fino a 15 minuti.

## <a name="how-to-find-azure-cdn-resource-health"></a>Come trovare l'integrità delle risorse della rete CDN di Azure

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo CDN.

2. Fare clic sul pulsante **Settings** .

    ![Pulsante Impostazioni](./media/cdn-resource-health/cdn-profile-settings.png)

3. In *Supporto e risoluzione dei problemi* fare clic su **Integrità risorsa**.

    ![Integrità delle risorse della rete CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Le risorse della rete CDN sono elencate anche nel riquadro *Integrità risorsa* del pannello *Guida e supporto*.  È possibile accedere rapidamente a *Guida e supporto* facendo clic sul punto interrogativo (**?**) cerchiato nell'angolo superiore destro del portale.
>
> ![Guida e supporto](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Messaggi specifici della rete CDN di Azure

Di seguito sono riportati gli stati correlati all'integrità delle risorse della rete CDN di Azure.

|Messaggio | Azione consigliata |
|---|---|
|Uno o più endpoint di rete CDN potrebbero essere stati arrestati, rimossi o configurati in modo non corretto | Uno o più endpoint di rete CDN potrebbero essere stati arrestati, rimossi o configurati in modo non corretto.|
|Il servizio di gestione della rete CDN non è attualmente disponibile | Visualizzare di nuovo questa pagina per eventuali aggiornamenti dello stato. Se il problema persiste anche dopo l'ora di risoluzione prevista, contattare il supporto tecnico.|
|Gli endpoint di rete CDN potrebbero essere interessati dai problemi in corso con alcuni provider di servizi di rete CDN | Visualizzare di nuovo questa pagina per eventuali aggiornamenti dello stato. Usare lo strumento per la risoluzione dei problemi per informazioni su come testare l'origine e l'endpoint di rete CDN. Se il problema persiste anche dopo l'ora di risoluzione prevista, contattare il supporto tecnico. |
|Sono stati riscontrati ritardi di propagazione delle modifiche di configurazione agli endpoint di rete CDN | Visualizzare di nuovo questa pagina per eventuali aggiornamenti dello stato. Se le modifiche di configurazione non vengono propagate completamente entro il tempo previsto, contattare il supporto tecnico.|
|Sono stati riscontrati problemi durante il caricamento del portale supplementare | Visualizzare di nuovo questa pagina per eventuali aggiornamenti dello stato. Se il problema persiste anche dopo l'ora di risoluzione prevista, contattare il supporto tecnico.|
Sono stati riscontrati problemi con alcuni provider di servizi di rete CDN | Visualizzare di nuovo questa pagina per eventuali aggiornamenti dello stato. Se il problema persiste anche dopo l'ora di risoluzione prevista, contattare il supporto tecnico. |

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica su Integrità risorse di Azure](../resource-health/resource-health-overview.md)
- [Risolvere i problemi di compressione della rete CDN](./cdn-troubleshoot-compression.md)
- [Risolvere i problemi relativi a errori 404](./cdn-troubleshoot-endpoint.md)
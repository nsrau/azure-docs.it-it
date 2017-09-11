---
title: Gestire i log di flusso del gruppo di sicurezza di rete con Network Watcher di Azure | Microsoft Docs
description: Questa pagina illustra come gestire i log di flusso del gruppo di sicurezza di rete in Network Watcher di Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 41cb5ffab9bd3a3bed75ffdb6a7383ca1690f810
ms.contentlocale: it-it
ms.lasthandoff: 06/01/2017

---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Gestire i log di flusso del gruppo di sicurezza di rete nel portale di Azure

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

I log di flusso del gruppo di sicurezza di rete sono una funzionalità di Network Watcher che consente di visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete. Questi log di flusso sono scritti in formato JSON e contengono informazioni importanti, tra cui: 

- Flussi in ingresso e in uscita in base a ciascuna regola.
- La scheda di interfaccia di rete che si applica al flusso.
- Informazioni a 5 tuple sul flusso, IP di origine/destinazione, porta di origine/destinazione, protocollo.
- Indica se il traffico è stato consentito o negato.

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un'istanza di Azure Network Watcher](network-watcher-create.md). Lo scenario presuppone anche che l'utente possieda un gruppo di risorse con una macchina virtuale valida.

## <a name="register-insights-provider"></a>Registrare il provider Insights

Per il corretto funzionamento della registrazione dei flussi, è necessario registrare il provider **Microsoft.Insights**. Registrare il provider seguendo la procedura seguente: 

1. Passare a **Sottoscrizioni** e selezionare la sottoscrizione per la quale si vuole abilitare i log dei flussi. 
2. Nel pannello **Sottoscrizione** selezionare **Provider di risorse**. 
3. Osservare l'elenco di provider e verificare che il provider **microsoft.insights** sia registrato. In caso contrario selezionare **Registra**.

![Visualizzare i provider][providers]

## <a name="enable-flow-logs"></a>Abilitare i log di flusso

Questi passaggi descrivono il processo di abilitazione dei log di flusso in un gruppo di sicurezza di rete.

### <a name="step-1"></a>Passaggio 1

Passare a un'istanza di Network Watcher e selezionare **Log del flusso del NSG**.

![Panoramica dei log di flusso][1]

### <a name="step-2"></a>Passaggio 2

Selezionare un gruppo di sicurezza di rete dall'elenco.

![Panoramica dei log di flusso][2]

### <a name="step-3"></a>Passaggio 3 

Nel pannello **Impostazioni dei log dei flussi** impostare lo stato su **On** (Attivo) e configurare un account di archiviazione.  Al termine, fare clic su **OK**. Selezionare quindi **Salva**.

![Panoramica dei log di flusso][3]

## <a name="download-flow-logs"></a>Scaricare i log di flusso

I log di flusso vengono salvati in un account di archiviazione. Scaricare i log di flusso per visualizzarli.

### <a name="step-1"></a>Passaggio 1

Per scaricare i log di flusso, fare clic su **È possibile scaricare i log dei flussi dagli account di archiviazione configurati**. Con questo passaggio si accede a una visualizzazione dell'account di archiviazione in cui è possibile scegliere i log per il download.

![Impostazioni dei log di flusso][4]

### <a name="step-2"></a>Passaggio 2

Passare all'account di archiviazione corretto. Selezionare quindi **Contenitori** > **insights-log-networksecuritygroupflowevent**.

![Impostazioni dei log di flusso][5]

### <a name="step-3"></a>Passaggio 3

Passare al percorso del log di flusso, selezionarlo e quindi selezionare **Scarica**.

![Impostazioni dei log di flusso][6]

Per informazioni sulla struttura del log, leggere [Panoramica sul log di flusso del gruppo di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [visualizzare i log di flusso NSG con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png


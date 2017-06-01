---
title: Gestire i log di flusso del gruppo di sicurezza di rete con Network Watcher di Azure | Microsoft Docs
description: Questa pagina illustra come gestire i log di flusso del gruppo di sicurezza di rete in Network Watcher di Azure
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: bbea08798a601989d06774475cb25ee67e99add6
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Gestire i log di flusso del gruppo di sicurezza di rete nel portale di Azure

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

I log di flusso del gruppo di sicurezza di rete sono una funzionalità di Network Watcher che consente di visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete. Sono scritti in formato JSON e mostrano i flussi in ingresso e in uscita in base a regole, scheda di rete a cui si applica il flusso, informazioni su 5 tuple relative al flusso (IP di origine/destinazione, porta di origine/destinazione, protocollo), e se il traffico è consentito o meno.

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher. Lo scenario presuppone inoltre che esista e possa essere usato un gruppo di risorse con una macchina virtuale valida.

## <a name="register-insights-provider"></a>Registrare il provider Insights

Per il corretto funzionamento della registrazione dei flussi, è necessario registrare il provider **Microsoft.Insights**. Per registrare il provider, passare a **Sottoscrizioni** e selezionare la sottoscrizione per la quale si vuole abilitare i log dei flussi. Nel pannello **Sottoscrizione** selezionare **Provider di risorse**. Spostarsi nell'elenco di provider e verificare che il provider **microsoft.insights** sia registrato. Se non lo è, fare clic su **Registra**.

![visualizzare i provider][providers]

## <a name="enable-flow-logs"></a>Abilitare i log di flusso

Questi passaggi descrivono l'abilitazione dei log di flusso in un gruppo di sicurezza di rete.

### <a name="step-1"></a>Passaggio 1

Passare a un'istanza di Network Watcher e selezionare **Log dei flussi**

![Panoramica dei log di flusso][1]

### <a name="step-2"></a>Passaggio 2

Selezionare un gruppo di sicurezza di rete dall'elenco facendovi clic sopra.

![Panoramica dei log di flusso][2]

### <a name="step-3"></a>Passaggio 3 

Nel pannello **Impostazioni dei log dei flussi** impostare lo stato su **On** e configurare un account di archiviazione.  Al termine, fare clic su **OK** e **Salva**.

![Panoramica dei log di flusso][3]

## <a name="download-flow-logs"></a>Scaricare i log di flusso

I log di flusso vengono salvati in un account di archiviazione. Per visualizzare i log di flusso è necessario scaricarli.

### <a name="step-1"></a>Passaggio 1

Per scaricare i log di flusso, fare clic su **È possibile scaricare i log dei flussi dagli account di archiviazione configurati**.  Viene mostrata la visualizzazione dell'account di archiviazione in cui è possibile passare al log e scaricarlo.

![Impostazioni dei log dei flussi][4]

### <a name="step-2"></a>Passaggio 2

Passare all'account di archiviazione corretto e quindi scegliere **Contenitori** > **insights-log-networksecuritygroupflowevent**

![Impostazioni dei log dei flussi][5]

### <a name="step-3"></a>Passaggio 3

Eseguire il drill down nel percorso del log di flusso, selezionare il log di flusso e fare clic su **Download**.

![Impostazioni dei log dei flussi][6]

Per informazioni sulla struttura del log, leggere la [panoramica sul log di flusso del gruppo di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [visualizzare i log di flusso con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png

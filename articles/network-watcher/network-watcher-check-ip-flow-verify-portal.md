---
title: Verificare il traffico con la verifica del flusso IP di Network Watcher di Azure - Portale di Azure | Documentazione Microsoft
description: "Questo articolo descrive come verificare se il traffico da o verso una macchina virtuale è consentito o negato"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 7db29c186cf6e6f3b40a680ab76f1d2763f806ba
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017

---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Controllare se il traffico da o verso una macchina virtuale è consentito o negato con la verifica del flusso IP, una funzionalità di Network Watcher di Azure

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST di Azure](network-watcher-check-ip-flow-verify-rest.md)


La verifica del flusso IP è una funzionalità di Network Watcher che consente di verificare se il traffico da o verso una macchina virtuale è consentito o negato. La convalida può essere eseguita per il traffico in ingresso o in uscita. Questo scenario è utile per stabilire se una macchina virtuale può comunicare con una risorsa esterna o un'altra risorsa. La funzionalità può essere usata per verificare se le regole del gruppo di sicurezza di rete sono configurate correttamente e per risolvere i problemi dei flussi bloccati da tali regole. La verifica del flusso IP consente inoltre di verificare che il traffico che si vuole bloccare sia correttamente bloccato dal gruppo di sicurezza di rete.

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher o aprire un'istanza esistente di Network Watcher. Lo scenario presuppone inoltre che esista e possa essere usato un gruppo di risorse con una macchina virtuale valida.

## <a name="scenario"></a>Scenario

Questo scenario usa la verifica del flusso IP per verificare se una macchina virtuale può comunicare con un altro computer sulla porta 443. Se il traffico viene negato, restituisce la regola di sicurezza che nega il traffico. Per altre informazioni sulla verifica del flusso IP, leggere la [panoramica sulla verifica del flusso IP](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>Eseguire la verifica del flusso IP

Passare a Network Watcher e fare clic su **Verifica del flusso IP**. Selezionare la macchina virtuale e l'interfaccia di rete da cui si desidera verificare il traffico. Immettere le eventuali informazioni di filtro aggiuntive e fare clic su **Controlla**.

Dopo avere fatto clic **Controlla**, viene controllato il flusso in base ai criteri forniti. Il risultato è **Accesso consentito** o **Accesso negato**. Se l'accesso è negato, vengono forniti il gruppo di sicurezza di rete (NSG) e la regola di sicurezza che bloccano il traffico. Se il comportamento atteso era la negazione del traffico, la regola ha funzionato.

> [!NOTE]
> La verifica del flusso IP richiede che la risorsa VM sia allocata.

Come si può notare dalla figura seguente, il traffico HTTPS in uscita era consentito.

![Panoramica della verifica del flusso IP][1]

Come illustrato nella figura seguente, il traffico diventa in ingresso e la porta di ingresso a la 123. Il traffico ora è negato, viene fornito il messaggio "Accesso negato" insieme al gruppo di sicurezza di rete e alla regola di sicurezza che negano il traffico.

![Risultati del flusso di IP][2]

## <a name="next-steps"></a>Passaggi successivi

Se il traffico risulta bloccato e non dovrebbe esserlo, vedere [Gestire i gruppi di sicurezza di rete](../virtual-network/virtual-network-manage-nsg-arm-portal.md) per individuare il gruppo di sicurezza di rete e le relative regole di sicurezza definite.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png















---
title: Panoramica del bilanciamento del carico Internet | Documentazione Microsoft
description: "Panoramica del bilanciamento del carico Internet e delle relative funzionalità. Modalità di funzionamento del bilanciamento del carico per Azure con macchine virtuali e servizi cloud."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 529b37aa-a45c-41d1-8877-fee8cc1fa375
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5b9ffeadf6b1ffc4eaf4f49b85ba752c27da0e46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="internet-facing-load-balancer-overview"></a>Panoramica del bilanciamento del carico Internet

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Il bilanciamento del carico di Azure esegue il mapping dell'indirizzo IP pubblico e del numero di porta del traffico in ingresso all'indirizzo IP privato e al numero di porta della macchina virtuale e viceversa per il traffico di risposta proveniente dalla macchina virtuale. Le regole di bilanciamento del carico consentono di distribuire tipi di traffico specifici tra più macchine virtuali o servizi. È ad esempio possibile dividere il carico del traffico delle richieste Web tra più server Web o ruoli Web.

Per un servizio cloud contenente istanze di ruoli Web o ruoli di lavoro, è possibile definire un endpoint pubblico nel file di definizione del servizio (con estensione csdef).

Il file *servicedefinition.csdef* contiene la configurazione dell'endpoint e, se sono presenti più istanze per la distribuzione di un ruolo Web o di lavoro, il bilanciamento del carico verrà configurato di conseguenza. La modalità di aggiunta di istanze alla distribuzione cloud comporta la modifica del numero di istanze nel file di configurazione del servizio (con estensione csfg).

## <a name="example-of-an-internet-facing-load-balancer"></a>Esempio di servizio di bilanciamento del carico con connessione Internet

La figura seguente mostra un endpoint con carico bilanciato per il traffico Web condiviso tra tre macchine virtuali per la porta TCP 80, pubblica e privata. Queste tre macchine virtuali appartengono a un set con carico bilanciato.

![esempio di bilanciamento del carico pubblico](./media/load-balancer-internet-overview/IC727496.png)

Figura 1. Endpoint con bilanciamento del carico per il traffico Web

Quando i client Internet inviano richieste di pagine Web all'indirizzo IP pubblico del servizio cloud sulla porta TCP 80, Azure Load Balancer distribuisce le richieste tra le tre macchine virtuali del set con carico bilanciato. Per altre informazioni sull'algoritmo di bilanciamento di carico, vedere la [pagina di panoramica del bilanciamento di carico](load-balancer-overview.md#load-balancer-features).

Per impostazione predefinita, Azure Load Balancer distribuisce il traffico di rete in modo uniforme tra più istanze di macchine virtuali. È inoltre possibile configurare l'affinità di sessione. Per altre informazioni, vedere l'articolo [Modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni sul [bilanciamento del carico interno](load-balancer-internal-overview.md) per capire quale sia il bilanciamento del carico più adatto per la propria distribuzione cloud.

È anche possibile [iniziare a creare un bilanciamento del carico con connessione Internet](load-balancer-get-started-internet-arm-ps.md) e configurare il tipo di [modalità di distribuzione](load-balancer-distribution-mode.md) per il comportamento specifico del traffico di rete per il bilanciamento del carico.

Se l'applicazione deve mantenere attive le connessioni per i server dietro il servizio di bilanciamento del carico, è possibile ottenere altre informazioni sulle [impostazioni di timeout delle connessioni TCP inattive per un bilanciamento del carico](load-balancer-tcp-idle-timeout.md). Ciò consente di ottenere informazioni sul comportamento delle connessioni inattive quando si usa il servizio di bilanciamento del carico di Azure.

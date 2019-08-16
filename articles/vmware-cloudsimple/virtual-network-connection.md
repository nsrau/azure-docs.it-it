---
title: Connettere la rete virtuale di Azure a CloudSimple usando ExpressRoute
description: Viene descritto come ottenere informazioni di peering per una connessione tra la rete virtuale di Azure e l'ambiente CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ba2d2fd9c943fe55e82956d022f6ba9840a550f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536599"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Connettere la rete virtuale di Azure a CloudSimple usando ExpressRoute

È possibile estendere la rete cloud privata alla rete virtuale di Azure e alle risorse di Azure. Una connessione ExpressRoute consente di accedere alle risorse in esecuzione nella sottoscrizione di Azure dal cloud privato.

## <a name="request-authorization-key"></a>Richiedi chiave di autorizzazione

È necessaria una chiave di autorizzazione per la connessione ExpressRoute tra il cloud privato e la rete virtuale di Azure. Per ottenere una chiave, archiviare un ticket con <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supporto</a>.  Usare le informazioni seguenti nella richiesta:

* Tipo di problema: **Tecnico**
* Sottoscrizione: Selezionare la sottoscrizione in cui è distribuito il servizio CloudSimple * *
* Servizio: **Soluzione VMware per CloudSimple**
* Tipo di problema: **Richiesta di servizio**
* Sottotipo di problema: **Chiave di autorizzazione per la connessione di Azure VNET**
* Soggetto: **Richiesta di chiave di autorizzazione per la connessione di Azure VNET**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>Ottenere informazioni sul peering per la rete virtuale di Azure per la connessione a CloudSimple

Per configurare la connessione, è necessario stabilire un collegamento tra la rete virtuale di Azure e l'ambiente CloudSimple.  Come parte della procedura, è necessario fornire l'URI del circuito peer e la chiave di autorizzazione. Ottenere l'URI e la chiave di autorizzazione dal [portale di CloudSimple](access-cloudsimple-portal.md).  Selezionare **rete** dal menu laterale, quindi selezionare connessione di **rete di Azure**. In alternativa, selezionare **account** nel menu laterale, quindi selezionare **connessione di rete di Azure**.

Si notino le icone di copia per l'URI del circuito peer e la chiave di autorizzazione per ciascuna delle aree. Per ogni cloud privato che si vuole connettere:

* Fare clic su **copia** per copiare l'URI. Incollarlo in un file in cui può essere disponibile per l'aggiunta all'portale di Azure.  
* Fare clic su **copia** per copiare la chiave di autorizzazione e incollarla nel file.

![Pagina connessione rete virtuale](media/network-virt-conn-page.png)

Per informazioni dettagliate sulla configurazione della rete virtuale di Azure per il collegamento CloudSimple, vedere [connettere l'ambiente cloud privato CloudSimple alla rete virtuale di Azure usando ExpressRoute](azure-expressroute-connection.md).

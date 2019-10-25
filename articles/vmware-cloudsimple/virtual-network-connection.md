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
ms.openlocfilehash: 2e2195c61acbf39b40a7659335afff78ac03cb4b
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881439"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Connettere la rete virtuale di Azure a CloudSimple usando ExpressRoute

È possibile estendere la rete cloud privata alla rete virtuale di Azure e alle risorse di Azure. Una connessione ExpressRoute consente di accedere alle risorse in esecuzione nella sottoscrizione di Azure dal cloud privato.

## <a name="request-authorization-key"></a>Richiedi chiave di autorizzazione

È necessaria una chiave di autorizzazione per la connessione ExpressRoute tra il cloud privato e la rete virtuale di Azure. Per ottenere una chiave, archiviare un ticket con <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supporto</a>.  Usare le informazioni seguenti nella richiesta:

* Tipo di problema: **tecnico**
* Sottoscrizione: **selezionare la sottoscrizione in cui è distribuito il servizio CloudSimple**
* Servizio: **soluzione VMware di CloudSimple**
* Tipo di problema: **richiesta di servizio**
* Sottotipo di problema: **chiave di autorizzazione per la connessione di Azure VNET**
* Subject: **richiesta di chiave di autorizzazione per la connessione di Azure VNET**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Ottenere informazioni sul peering dal portale di CloudSimple

Per configurare la connessione, è necessario stabilire una connessione tra la rete virtuale di Azure e l'ambiente CloudSimple.  Come parte della procedura, è necessario fornire l'URI del circuito peer e la chiave di autorizzazione. Ottenere l'URI e la chiave di autorizzazione dal [portale di CloudSimple](access-cloudsimple-portal.md).  Selezionare **rete** dal menu laterale, quindi selezionare connessione di **rete di Azure**. In alternativa, selezionare **account** nel menu laterale, quindi selezionare **connessione di rete di Azure**.

Copiare l'URI del circuito peer e la chiave di autorizzazione per ognuna delle aree usando l'icona di *copia* . Per ogni area di CloudSimple che si vuole connettere:

1. Fare clic su **copia** per copiare l'URI. Incollarlo in un file in cui può essere disponibile per l'aggiunta all'portale di Azure.  
2. Fare clic su **copia** per copiare la chiave di autorizzazione e incollarla nel file.

Copiare la chiave di autorizzazione e l'URI del circuito peer nello stato **disponibile** .  Stato **utilizzato** indica che la chiave è già stata utilizzata per creare una connessione di rete virtuale.

![Pagina connessione rete virtuale](media/virtual-network-connection.png)

Per informazioni dettagliate sulla configurazione della rete virtuale di Azure per il collegamento CloudSimple, vedere [connettere l'ambiente cloud privato CloudSimple alla rete virtuale di Azure usando ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Passaggi successivi

* [Connessione di rete virtuale di Azure al cloud privato](azure-expressroute-connection.md)
* [Connettersi alla rete locale usando Azure ExpressRoute](on-premises-connection.md)

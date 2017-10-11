---
title: Gestire i profili di Gestione Traffico di Azure | Documentazione Microsoft
description: Questo articolo illustra come creare, disabilitare, abilitare ed eliminare un profilo di Gestione traffico di Azure.
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: kumud
ms.openlocfilehash: a5164282264124835692bc72a4ab61891aa7af9d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="manage-an-azure-traffic-manager-profile"></a>Gestire un profilo di Gestione traffico di Azure

I profili di Gestione traffico di Azure usano metodi di routing del traffico per controllare la distribuzione del traffico verso i servizi cloud o gli endpoint di siti Web. Questo articolo illustra come creare e gestire tali profili.

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

È possibile creare un profilo di Gestione traffico usando il portale di Azure. Dopo aver creato il profilo è possibile configurare endpoint, il monitoraggio e altre impostazioni nel portale di Azure. Gestione traffico supporta fino a 200 endpoint per profilo. Tuttavia, la maggior parte degli scenari di utilizzo richiede solo un numero ridotto di endpoint.

### <a name="to-create-a-traffic-manager-profile"></a>Per creare un profilo di Gestione traffico

1. Da un browser accedere al [portale di Azure](http://portal.azure.com). Se non si ha già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free/). 
2. Nel menu **Hub** fare clic su **Nuovo** > **Reti** > **Vedi tutto**, fare clic su **Profilo di Gestione traffico** per aprire il pannello **Crea profilo di Gestione traffico** e quindi fare clic su **Crea**.
3. Nel pannello **Crea profilo di Gestione traffico** procedere come segue:
    1. In **Nome** specificare un nome per il profilo. Questo nome deve essere univoco all'interno della zona trafficmanager.net e determina il nome DNS <name>, trafficmanager.net, che viene usato per accedere al profilo di Gestione traffico.
    2. In **Metodo di routing** selezionare il metodo di routing **Priorità**.
    3. In **Sottoscrizione** selezionare la sottoscrizione in cui si vuole creare il profilo.
    4. In **Gruppo di risorse** creare un nuovo gruppo di risorse in cui aggiungere il profilo.
    5. In **Località del gruppo di risorse** selezionare la località del gruppo di risorse. Questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sul profilo di Gestione traffico che sarà distribuito a livello globale.
    6. Fare clic su **Crea**.
    7. Una volta completata la distribuzione globale del profilo di Gestione traffico, il profilo sarà elencato nel rispettivo gruppo di risorse come una delle risorse.

## <a name="disable-enable-or-delete-a-profile"></a>Disabilitare, abilitare o eliminare un profilo

È possibile disabilitare un profilo di Gestione traffico esistente in modo che non indirizzi le richieste dell'utente agli endpoint configurati. Quando si disabilita un profilo di Gestione traffico, il profilo stesso e le informazioni in esso contenute rimangono invariati e possono essere modificati nell'interfaccia di Gestione traffico.  I riferimenti vengono ripristinati quando si riabilita il profilo. Un profilo di Gestione traffico creato nel portale di Azure viene abilitato automaticamente. Se si decide che un profilo non sarà più necessario, è possibile eliminarlo.

### <a name="to-disable-a-profile"></a>Per disabilitare un profilo

1. Se si usa un nome di dominio personalizzato, modificare il record CNAME per il server DNS Internet in modo che non punti più al profilo di Gestione traffico.
2. Il traffico non sarà più indirizzato agli endpoint attraverso le impostazioni del profilo di Gestione traffico.
3. Da un browser accedere al [portale di Azure](http://portal.azure.com).
2. Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** che si vuole modificare e quindi fare clic su tale profilo nei risultati visualizzati.
3. Nel pannello **Profilo di Gestione traffico** fare clic su **Panoramica**. Nel pannello Panoramica fare clic su **Disabilita** e quindi confermare la disabilitazione del profilo di Gestione traffico.

### <a name="to-enable-a-profile"></a>Per abilitare un profilo

1. Da un browser accedere al [portale di Azure](http://portal.azure.com).
2. Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** che si vuole modificare e quindi fare clic su tale profilo nei risultati visualizzati.
3. Nel pannello **Profilo di Gestione traffico** fare clic su **Panoramica** e nel pannello Panoramica fare clic su **Abilita**.
5. Se si usa un nome di dominio personalizzato, creare un record di risorse CNAME sul server DNS Internet in modo da puntare al nome di dominio del profilo di Gestione traffico.
6. Il traffico verrà nuovamente indirizzato verso gli endpoint.

### <a name="to-delete-a-profile"></a>Per eliminare un profilo

1. Assicurarsi che il record di risorse DNS sul server DNS Internet non usi più un record di risorse CNAME che punta al nome di dominio del profilo di Gestione traffico.
2. Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** che si vuole modificare e quindi fare clic su tale profilo nei risultati visualizzati.
3. Nel pannello **Profilo di Gestione traffico** fare clic su **Panoramica**. Nel pannello Panoramica fare clic su **Elimina** e quindi confermare l'eliminazione del profilo di Gestione traffico.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere un endpoint](traffic-manager-endpoints.md)
* [Configurare il metodo di routing Priorità](traffic-manager-configure-priority-routing-method.md)
* [Configurare il metodo di routing Geografico](traffic-manager-configure-geographic-routing-method.md) 
* [Configurare il metodo di routing Ponderato](traffic-manager-configure-weighted-routing-method.md)
* [Configurare il metodo di routing Prestazioni](traffic-manager-configure-performance-routing-method.md)

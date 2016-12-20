---
title: Disabilitare o abilitare un endpoint di Gestione traffico | Documentazione Microsoft
description: "Questo articolo aiuterà a disabilitare o abilitare gli endpoint del profilo di Gestione traffico."
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 9b2264ce-be06-43b2-a00b-5c724e5d71fd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a3c8d9f5995ac51b128e46691962b5f6a220ab7d


---
<!-- repub for nofollow -->

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>Disabilitare o abilitare un endpoint di Gestione traffico
È anche possibile disabilitare singoli endpoint che appartengono a un profilo di Gestione traffico. Gli endpoint includono servizi cloud e siti Web. Quando si disabilita un endpoint questo continua a far parte del profilo, sebbene quest'ultimo si comporti come se non fosse incluso. Questa azione è particolarmente utile per rimuovere temporaneamente un endpoint che si trova in modalità di manutenzione o è in corso di ridistribuzione. Quando l'endpoint è di nuovo attivo e in esecuzione, è possibile abilitarlo.

> [!NOTE]
> **La disabilitazione di un endpoint non ha nulla a che vedere con il relativo stato di distribuzione in Azure. Un endpoint integro rimane attivo e in grado di ricevere traffico anche se è disabilitato in Gestione traffico. La disabilitazione di un endpoint in un profilo non influisce sul relativo stato in un altro profilo.**
> 
> 

## <a name="to-disable-an-endpoint"></a>Per disabilitare un endpoint
1. Nel riquadro di Gestione traffico nel portale di Azure classico trovare il profilo di Gestione traffico che contiene le impostazioni degli endpoint da modificare e quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
2. Per visualizzare gli endpoint inclusi nella configurazione, fare clic su **Endpoints** nella parte superiore della pagina.
3. Fare clic sull'endpoint da disabilitare, quindi fare clic su **Disabilita** nella parte inferiore della pagina.
4. Il traffico indirizzato all'endpoint verrà arrestato in base alla durata (TTL) del DNS configurato per il nome di dominio di Gestione traffico. È possibile modificare la durata (TTL) dalla pagina di configurazione del profilo di Gestione traffico.

## <a name="to-enable-an-endpoint"></a>Per abilitare un endpoint
1. Nel riquadro di Gestione traffico nel portale di Azure classico trovare il profilo di Gestione traffico che contiene le impostazioni degli endpoint da modificare e quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
2. Per visualizzare gli endpoint inclusi nella configurazione, fare clic su **Endpoints** nella parte superiore della pagina.
3. Fare clic sull'endpoint da abilitare, quindi fare clic su **Abilita** nella parte inferiore della pagina.
4. Il traffico indirizzato al servizio ospitato riprenderà di nuovo secondo il profilo.

## <a name="next-steps"></a>Passaggi successivi
[Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)

[Considerazioni sulle prestazioni di gestione traffico](traffic-manager-performance-considerations.md)




<!--HONumber=Nov16_HO3-->



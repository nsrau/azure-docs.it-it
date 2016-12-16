---
title: Disabilitare, abilitare o eliminare un profilo di Gestione traffico | Microsoft Docs
description: "Questo articolo illustrerà l&quot;uso dei profili di Gestione traffico."
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: f19143e2-2f4f-4ead-bb00-89b95ac92cb4
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3ea5860767cd21d263c0f0126362d3f6d92d58c6


---
<!-- repub for nofollow -->

# <a name="disable-enable-or-delete-a-profile"></a>Disabilitare, abilitare o eliminare un profilo
È possibile disabilitare un profilo di Gestione traffico esistente in modo che non indirizzi le richieste dell'utente agli endpoint configurati. Quando si disabilita un profilo di Gestione traffico, il profilo stesso e le informazioni in esso contenute rimarranno invariati e potranno essere modificati nell'interfaccia di Gestione traffico. Quando si desidera abilitare nuovamente il profilo, è possibile effettuare l'operazione nel portale di Azure e i riferimenti verranno ripresi. Un profilo di Gestione traffico creato nel portale di Azure viene abilitato automaticamente.

## <a name="to-disable-a-profile"></a>Per disabilitare un profilo
1. Modificare il record di risorse DNS sul server DNS Internet in modo che usi il tipo di record e il puntatore appropriati, immettendo un altro nome o l'indirizzo IP di un percorso specifico su Internet. In altre parole, modificare il record di risorse DNS sul server DNS Internet in modo che non usi più un record di risorse CNAME che punta al nome di dominio del profilo di Gestione traffico.
2. L'indirizzamento del traffico agli endpoint verrà arrestato tramite le impostazioni del profilo di Gestione traffico.
3. Selezionare il profilo da disabilitare. Per selezionare il profilo, nella pagina di Gestione traffico evidenziare il profilo facendo clic sulla colonna accanto al nome del profilo. Non fare clic sul nome del profilo o sulla freccia accanto al nome, altrimenti verrà visualizzata la pagina delle impostazioni del profilo.
4. Dopo aver selezionato il profilo, fare clic su Disabilita nella parte inferiore della pagina.

## <a name="to-enable-a-profile"></a>Per abilitare un profilo
1. Selezionare il profilo da abilitare. Per selezionare il profilo, nella pagina di Gestione traffico evidenziare il profilo facendo clic sulla colonna accanto al nome del profilo. Non fare clic sul nome del profilo o sulla freccia accanto al nome, altrimenti verrà visualizzata la pagina delle impostazioni del profilo.
2. Dopo aver selezionato il profilo, fare clic su Abilita nella parte inferiore della pagina.
3. Modificare il record di risorse DNS sul server DNS Internet in modo che usi il tipo di record CNAME, che esegue il mapping del nome di dominio aziendale al nome di dominio del profilo di Gestione traffico. Per altre informazioni, vedere [Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md).
4. Il traffico verrà nuovamente indirizzato verso gli endpoint.

## <a name="delete-a-profile"></a>Eliminare un profilo
1. Assicurarsi che il record di risorse DNS sul server DNS Internet non usi più un record di risorse CNAME che punta al nome di dominio del profilo di Gestione traffico.
2. Selezionare il profilo da eliminare. Per selezionare il profilo, nella pagina di Gestione traffico evidenziare il profilo
3. facendo clic sulla colonna accanto al profilo. Non fare clic sul nome del profilo o sulla freccia accanto al nome, altrimenti verrà visualizzata la pagina delle impostazioni del profilo.
4. Dopo aver selezionato il profilo, fare clic su Elimina nella parte inferiore della pagina.

## <a name="next-steps"></a>Passaggi successivi
[Gestione traffico: disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md)

[Configurare metodo di routing failover](traffic-manager-configure-failover-routing-method.md)

[Configurare il metodo di routing Round Robin](traffic-manager-configure-round-robin-routing-method.md)

[Configurare un metodo di routing del traffico delle prestazioni](traffic-manager-configure-performance-routing-method.md)

[Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)




<!--HONumber=Nov16_HO3-->



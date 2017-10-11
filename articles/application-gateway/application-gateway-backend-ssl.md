---
title: Abilitazione di SSL end-to-end nel gateway applicazione di Azure | Microsoft Docs
description: Questa pagina offre un'introduzione al supporto di SSL end-to-end del gateway applicazione.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: amsriva
ms.openlocfilehash: 689ee54dc1db2ea371b08270718278fd98c65bb5
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Panoramica di SSL end-to-end con il gateway applicazione

Il gateway applicazione supporta la terminazione SSL nel gateway, dopo la quale il traffico scorre generalmente non crittografato verso i server back-end. Questa funzionalità consente ai server Web di non gestire il costoso carico di crittografia e decrittografia. Tuttavia, per alcuni clienti le comunicazioni non crittografate verso i server back-end non rappresentano un'opzione accettabile. Le comunicazioni non crittografate potrebbero essere causate da requisiti di sicurezza o conformità oppure da un'applicazione che può accettare solo una connessione protetta. Per tali applicazioni, il gateway applicazione supporta ora la crittografia SSL end-to-end.

## <a name="overview"></a>Panoramica

La crittografia SSL end-to-end consente di trasmettere in modo sicuro dati sensibili crittografati al back-end, usufruendo comunque dei vantaggi delle funzionalità di bilanciamento del carico di livello 7 offerte dal gateway applicazione. Alcune di queste funzionalità sono l'affinità di sessione basata su cookie, il routing basato su URL, il supporto per il routing basato su siti o la possibilità di inserire intestazioni X-Forwarded-*.

Se configurato con la modalità di comunicazione SSL end-to-end, il gateway applicazione termina le sessioni SSL nel gateway ed esegue la decrittografia del traffico utente. Applica quindi le regole configurate per selezionare un'istanza del pool di back-end adeguata su cui instradare il traffico. Il gateway applicazione avvia a questo punto una nuova connessione SSL al server back-end e crittografa nuovamente i dati usando il certificato di chiave pubblica del server back-end prima di trasmettere la richiesta al back-end. Per abilitare SSL end-to-end si imposta la configurazione del protocollo in BackendHTTPSetting su HTTPS. Questa impostazione viene quindi applicata a un pool back-end. Per una comunicazione protetta, ogni server back-end nel pool di back-end con SSL end-to-end abilitato deve essere configurato con un certificato.

![Scenario di SSL end-to-end][1]

In questo esempio, le richieste che usano TLS1.2 vengono instradate ai server back-end in Pool1 con SSL end-to-end.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL end-to-end e aggiunta dei certificati all'elenco dei consentiti

Il gateway applicazione comunica solo con istanze back-end note, il cui certificato è incluso nell'elenco dei consentiti del gateway applicazione. Per abilitare l'aggiunta dei certificati all'elenco dei consentiti, è necessario caricare nel gateway applicazione la chiave pubblica dei certificati dei server back-end (non il certificato radice). Sono quindi consentite solo le connessioni a back-end noti e inclusi nell'elenco. Gli altri back-end generano un errore del gateway. I certificati autofirmati vengono usati a scopo di test e non sono consigliati per i carichi di lavoro. Prima dell'uso, anche questi certificati devono essere aggiunti all'elenco dei consentiti nel gateway applicazione, come descritto nei passaggi precedenti.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso i concetti di SSL end-to-end, passare all'articolo che spiega come [abilitare SSL end-to-end nel gateway applicazione](application-gateway-end-to-end-ssl-powershell.md) per creare un gateway applicazione usando SSL end-to-end.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png

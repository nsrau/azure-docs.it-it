---
title: Abilitazione di criteri SSL ed SSL end-to-end nel gateway applicazione | Documentazione Microsoft
description: Questa pagina offre un&quot;introduzione al supporto di SSL end-to-end del gateway applicazione.
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
ms.workload: infrastructure-services
ms.date: 11/10/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9642ea16bff4b0cd4bf3e88d7530765d4c7bfbc5


---
# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Abilitazione di criteri SSL ed SSL end-to-end nel gateway applicazione
## <a name="overview"></a>Panoramica
Il gateway applicazione supporta la terminazione SSL nel gateway, dopo la quale il traffico scorre generalmente non crittografato verso i server back-end. In questo modo, i server Web non devono gestire il costoso carico di crittografia/decrittografia. Tuttavia, per alcuni clienti le comunicazioni non crittografate verso i server back-end non rappresentano un'opzione accettabile. Questo potrebbe dipendere da requisiti di sicurezza/conformità o da un'applicazione in grado di accettare solo connessioni protette. Per queste applicazioni, il gateway applicazione supporta ora la crittografia SSL end-to-end.

La crittografia SSL end-to-end consente di trasmettere in modo sicuro dati sensibili crittografati al back-end, usufruendo comunque dei vantaggi delle funzionalità di bilanciamento del carico di livello 7 offerte dal gateway applicazione, come l'affinità basata su cookie, il routing basato su URL, il supporto per il routing basato su siti o la possibilità di inserire intestazioni in formato X-Forwarded-*.

Se configurato con la modalità di comunicazione SSL end-to-end, il gateway applicazione termina le sessioni SSL dell'utente nel gateway ed esegue la decrittografia del traffico utente. Applica quindi le regole configurate per selezionare un'istanza del pool di back-end adeguata su cui instradare il traffico. Il gateway applicazione avvia a questo punto una nuova connessione SSL al server back-end e crittografa nuovamente i dati usando il certificato di chiave pubblica del server back-end prima di trasmettere la richiesta al back-end. L'SSL end-to-end viene abilitato impostando la configurazione del protocollo in BackendHTTPSetting su Https, che viene quindi applicata a un pool di back-end. Per una comunicazione protetta, ogni server back-end nel pool di back-end con SSL end-to-end abilitato deve essere configurato con un certificato.

![Scenario di SSL end-to-end][1]

In questo esempio, le richieste che usano TLS1.2 vengono instradate ai server back-end in Pool1 con SSL end-to-end.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL end-to-end e aggiunta dei certificati all'elenco dei consentiti
Il gateway applicazione comunica solo con istanze back-end note, il cui certificato è incluso nell'elenco dei consentiti del gateway applicazione. Per abilitare l'aggiunta dei certificati all'elenco dei consentiti, è necessario caricare nel gateway applicazione la chiave pubblica dei certificati dei server back-end (non il certificato radice). Sono quindi consentite solo le connessioni a back-end noti e inclusi nell'elenco. Gli altri back-end generano un errore del gateway. I certificati autofirmati vengono usati a scopo di test e non sono consigliati per i carichi di lavoro. Prima dell'uso, anche questi certificati devono essere aggiunti all'elenco dei consentiti nel gateway applicazione, come descritto nei passaggi precedenti.

## <a name="application-gateway-ssl-policy"></a>Criteri SSL del gateway applicazione
Il gateway applicazione supporta criteri di negoziazione SSL configurabili dell'utente, che consentono ai clienti di avere maggiore controllo sulle connessioni SSL nel gateway applicazione.

1. Per impostazione predefinita, SSL 2.0 e 3.0 sono disabilitati per tutti i gateway applicazione. Non sono configurabili in nessun modo.
2. La definizione dei criteri SSL offre all'utente la possibilità di disabilitare i 3 protocolli seguenti: TLSv1\_0, TLSv1\_1 e TLSv1\_2.
3. Se non è definito alcun criterio SSL, sono abilitati tutti e tre (TLSv1\_0, TLSv1\_1 e TLSv1_2).

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso i concetti di SSL end-to end e i criteri SSL, vedere l'articolo su come [abilitare SSL end-to-end nel gateway applicazione](application-gateway-end-to-end-ssl-powershell.md) per creare un gateway applicazione che consenta di inviare traffico ai back-end in formato crittografato.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png



<!--HONumber=Nov16_HO2-->



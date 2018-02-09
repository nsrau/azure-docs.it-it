---
title: Panoramica dei back-end multi-tenant con il gateway applicazione di Azure | Microsoft Docs
description: Questa pagina offre una panoramica del supporto del gateway applicazione per i back-end multi-tenant.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: d093af064bca46aa1f454b61b1099f47f61ccd33
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Supporto del gateway applicazione per i back-end multi-tenant

Il gateway applicazione di Azure supporta set di scalabilità di macchine virtuali, interfacce di rete, indirizzi IP pubblici/privati o il nome di dominio completo (FQDN) come parte dei propri pool back-end. Per impostazione predefinita, il gateway applicazione non modifica l'intestazione host HTTP in ingresso dal client e invia l'intestazione invariata al back-end. Molti servizi, come [App Web di Azure](../app-service/app-service-web-overview.md), sono di natura multi-tenant e si basano su un'estensione SNI o un'intestazione host specifica per la risoluzione nell'endpoint corretto. Il gateway applicazione consente ora agli utenti di sovrascrivere l'intestazione host HTTP in ingresso in base alle impostazioni HTTP del back-end. Questa funzionalità garantisce il supporto di App Web di Azure e Gestione API come back-end multi-tenant ed è disponibile sia per lo SKU standard che per lo SKU WAF. Il supporto di back-end multi-tenant può essere usato anche con scenari di terminazione SSL e SSL end-to-end.

![Scenario con app Web](./media/application-gateway-web-app-overview/scenario.png)

La possibilità di specificare una sostituzione dell'host è definita nelle impostazioni HTTP ed è applicabile a qualsiasi pool back-end durante la creazione di regole. I back-end multi-tenant supportano i due modi seguenti per sostituire l'intestazione host e l'estensione SNI.

1. È possibile impostare il nome host su un valore fisso nelle impostazioni HTTP. Questa funzionalità garantisce la sostituzione dell'intestazione host con questo valore per tutto il traffico verso il pool back-end in cui vengono applicate le impostazioni HTTP. Quando si usa SSL end-to-end, il nome host sostituito viene usato nell'estensione SNI. Questa funzionalità supporta gli scenari in cui la farm di un pool back-end prevede un'intestazione host diversa dall'intestazione host in ingresso del cliente.

2. È possibile derivare il nome host dall'indirizzo IP o dal nome di dominio completo dei membri del pool back-end. Le impostazioni HTTP offrono anche un'opzione per selezionare il nome host dal nome di dominio completo di un membro del pool back-end, se nella configurazione è stata inclusa la possibilità di derivare il nome host da un singolo membro del pool back-end. Quando si usa SSL end-to-end, il nome host è derivato dal nome di dominio completo e viene usato nell'estensione SNI. Questa funzionalità supporta gli scenari in cui un pool back-end può avere due o più servizi PaaS multi-tenant, come App Web di Azure, e l'intestazione host della richiesta per ogni membro contiene il nome host derivato dal rispettivo nome di dominio completo.

> [!NOTE]
> In entrambi i casi precedenti, le impostazioni influiscono solo sul comportamento del traffico attivo e non sul comportamento dei probe di integrità. I probe personalizzati supportano già la possibilità di specificare un'intestazione host nella configurazione del probe. I probe personalizzati offrono ora la possibilità di derivare il comportamento dell'intestazione host dalle impostazioni HTTP attualmente configurate. Questa configurazione può essere specificata usando il parametro `PickHostNameFromback endAddress` nella configurazione del probe. Per il funzionamento della funzionalità end-to-end, sia il probe che le impostazioni HTTP devono essere modificati per riflettere la configurazione corretta.

Con questa funzionalità, i clienti specificano le opzioni nelle impostazioni HTTP e nei probe personalizzati in base alla configurazione appropriata. L'impostazione viene quindi collegata a un listener e un pool back-end usando una regola.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un gateway applicazione con un'app Web come membro del pool back-end, vedere [Configurare app Web del servizio app con il gateway applicazione](application-gateway-web-app-powershell.md)

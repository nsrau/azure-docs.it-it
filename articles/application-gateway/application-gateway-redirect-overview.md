---
title: Panoramica del reindirizzamento per il gateway applicazione di Azure | Microsoft Docs
description: "Informazioni sulla funzionalità di reindirizzamento nel gateway applicazione di Azure"
services: application-gateway
documentationcenter: na
author: amsriva
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ea9ae8373ff67bf9557b06bbc8a4b0d82a03e2d0
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="application-gateway-redirect-overview"></a>Panoramica del reindirizzamento nel gateway applicazione

Uno scenario comune per molte applicazioni Web è il supporto del reindirizzamento automatico da HTTP a HTTPS per assicurare che tutte le comunicazioni tra l'applicazione e gli utenti avvengano tramite un percorso crittografato. In passato, i clienti hanno usato tecniche come la creazione di un pool back-end dedicato il cui unico scopo è quello di reindirizzare le richieste ricevute su HTTP a HTTPS.  Il gateway applicazione ora consente di reindirizzare il traffico sul gateway applicazione. In questo modo semplifica la configurazione dell'applicazione, ottimizza l'utilizzo delle risorse e supporta i nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso. Il supporto del reindirizzamento nel gateway applicazione non è limitato al solo reindirizzamento HTTP -> HTTPS. Questo è un meccanismo di reindirizzamento generico, che consente il reindirizzamento del traffico ricevuto in un listener a un altro listener nel gateway applicazione. Supporta anche il reindirizzamento a un sito esterno. Il supporto del reindirizzamento nel gateway applicazione offre le funzionalità seguenti:

1. Reindirizzamento globale da un listener a un altro listener nel gateway, che consente il reindirizzamento da HTTP a HTTPS in un sito.
2. Reindirizzamento basato sul percorso. Questo tipo di reindirizzamento consente il reindirizzamento da HTTP a HTTPS solo in un'area specifica del sito, ad esempio l'area del carrello acquisti indicata da /cart/*.
3. Reindirizzamento a un sito esterno.

![Reindirizzamento](./media/application-gateway-redirect-overview/redirect.png)

Con questa modifica, i clienti dovranno creare un nuovo oggetto di configurazione del reindirizzamento, che specifica il listener di destinazione o il sito esterno scelto per il reindirizzamento. L'elemento di configurazione supporta anche opzioni per abilitare l'aggiunta del percorso URI e della stringa di query all'URL reindirizzato. I clienti possono anche scegliere se il reindirizzamento è temporaneo (codice di stato HTTP 302) o permanente (codice di stato HTTP 301). Questa configurazione del reindirizzamento, dopo essere stata creata, viene collegata al listener di origine tramite una nuova regola. Quando si usa una regola di base, la configurazione del reindirizzamento viene associata a un listener di origine e il reindirizzamento è globale. Quando viene usata una regola basata sul percorso, la configurazione del reindirizzamento viene definita nel mapping del percorso URL e quindi si applica solo all'area del percorso specifica di un sito.

### <a name="next-steps"></a>Passaggi successivi

[Configure URL redirection on an application gateway (Configurare il reindirizzamento dell'URL in un gateway applicazione)](application-gateway-configure-redirect-powershell.md)


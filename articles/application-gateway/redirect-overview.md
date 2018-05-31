---
title: Panoramica del reindirizzamento per il gateway applicazione di Azure
description: Informazioni sulla funzionalità di reindirizzamento nel gateway applicazione di Azure
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 65c631ca9beb5eab5d8fe2b7e71daa0cf3b768fa
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204378"
---
# <a name="application-gateway-redirect-overview"></a>Panoramica del reindirizzamento nel gateway applicazione

Uno scenario comune per molte applicazioni Web è il supporto del reindirizzamento automatico da HTTP a HTTPS per assicurare che tutte le comunicazioni tra l'applicazione e gli utenti avvengano tramite un percorso crittografato. In passato, i clienti hanno usato tecniche come la creazione di un pool back-end dedicato il cui unico scopo è quello di reindirizzare le richieste ricevute su HTTP a HTTPS.

Il gateway applicazione ora consente di reindirizzare il traffico sul gateway. In questo modo semplifica la configurazione dell'applicazione, ottimizza l'utilizzo delle risorse e supporta i nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso. Il supporto del reindirizzamento nel gateway applicazione non è limitato al solo reindirizzamento HTTP -> HTTPS. Dispone di un meccanismo di reindirizzamento generico, che consente il reindirizzamento del traffico ricevuto in un listener a un altro listener nel gateway applicazione. È supportato anche il reindirizzamento a un sito esterno.

Il supporto del reindirizzamento nel gateway applicazione offre le funzionalità seguenti:

-  **Reindirizzamento globale**

   Reindirizzamento da un listener a un altro listener nel gateway. che consente il reindirizzamento da HTTP a HTTPS in un sito.
- **Reindirizzamento basato sul percorso**

   Questo tipo di reindirizzamento consente il reindirizzamento da HTTP a HTTPS solo in un'area specifica del sito, ad esempio l'area del carrello acquisti indicata da /cart/*.
- **Reindirizzamento a un sito esterno**

![Reindirizzamento](./media/redirect-overview/redirect.png)

Con questa modifica, i clienti devono creare un nuovo oggetto di configurazione del reindirizzamento, che specifica il listener di destinazione o il sito esterno scelto per il reindirizzamento. L'elemento di configurazione supporta anche opzioni per abilitare l'aggiunta del percorso URI e della stringa di query all'URL reindirizzato. È anche possibile scegliere se il reindirizzamento è temporaneo (codice di stato HTTP 302) o permanente (codice di stato HTTP 301). Questa configurazione del reindirizzamento, dopo essere stata creata, viene collegata al listener di origine tramite una nuova regola. Quando si usa una regola di base, la configurazione del reindirizzamento viene associata a un listener di origine e il reindirizzamento è globale. Quando viene usata una regola basata sul percorso, la configurazione del reindirizzamento è definita sulla mappa del percorso URL. Pertanto, si applica solo all'area del percorso specifico di un sito.

### <a name="next-steps"></a>Passaggi successivi

[Configure URL redirection on an application gateway (Configurare il reindirizzamento dell'URL in un gateway applicazione)](tutorial-url-redirect-powershell.md)

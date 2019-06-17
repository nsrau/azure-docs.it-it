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
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60715802"
---
# <a name="application-gateway-redirect-overview"></a>Panoramica del reindirizzamento nel gateway applicazione

È possibile usare il gateway applicazione per reindirizzare il traffico.  Ha un meccanismo di reindirizzamento generico che consente di reindirizzare il traffico ricevuto in un listener a un altro listener o a un sito esterno. In questo modo semplifica la configurazione dell'applicazione, ottimizza l'utilizzo delle risorse e supporta i nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso.

Uno scenario comune di reindirizzamento per molte applicazioni web deve supportare automatico da HTTP per il reindirizzamento HTTPS per assicurarsi che tutte le comunicazioni tra applicazione e gli utenti si verificano tramite un percorso crittografato. In passato, i clienti hanno usato tecniche come la creazione di un pool back-end dedicato il cui unico scopo è quello di reindirizzare le richieste ricevute su HTTP a HTTPS. Con supporto del reindirizzamento nel Gateway applicazione, è possibile farlo semplicemente tramite l'aggiunta di una nuova configurazione di reindirizzamento a una regola di routing e specificando un altro listener con il protocollo HTTPS come i listener di destinazione.

Sono supportati i tipi di reindirizzamento seguenti:

- 301 che indica come reindirizzamento permanente
- 302 - Trovato
- 303 zobrazit jiné
- Reindirizzamento temporaneo 307

Il supporto del reindirizzamento nel gateway applicazione offre le funzionalità seguenti:

-  **Reindirizzamento globale**

   Reindirizzamento da un listener a un altro listener nel gateway. che consente il reindirizzamento da HTTP a HTTPS in un sito.
- **Reindirizzamento basato sul percorso**

   Questo tipo di reindirizzamento consente il reindirizzamento da HTTP a HTTPS solo in un'area specifica del sito, ad esempio l'area del carrello acquisti indicata da /cart/*.
- **Reindirizzamento a un sito esterno**

![Reindirizzamento](./media/redirect-overview/redirect.png)

Con questa modifica, i clienti devono creare un nuovo oggetto di configurazione del reindirizzamento, che specifica il listener di destinazione o il sito esterno scelto per il reindirizzamento. L'elemento di configurazione supporta anche opzioni per abilitare l'aggiunta del percorso URI e della stringa di query all'URL reindirizzato. È anche possibile scegliere il tipo di reindirizzamento. Questa configurazione del reindirizzamento, dopo essere stata creata, viene collegata al listener di origine tramite una nuova regola. Quando si usa una regola di base, la configurazione del reindirizzamento viene associata a un listener di origine e il reindirizzamento è globale. Quando viene usata una regola basata sul percorso, la configurazione del reindirizzamento è definita sulla mappa del percorso URL. Pertanto, si applica solo all'area del percorso specifico di un sito.

### <a name="next-steps"></a>Passaggi successivi

[Configure URL redirection on an application gateway (Configurare il reindirizzamento dell'URL in un gateway applicazione)](tutorial-url-redirect-powershell.md)

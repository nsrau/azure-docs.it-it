---
title: Restrizioni IP del Servizio app di Azure | Microsoft Docs
description: Come usare le restrizioni IP con il Servizio app di Azure
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/23/2017
ms.author: byvinyal
ms.openlocfilehash: 22e05af889b4e792dcc6f6fc438e8a58674b9f0e
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="azure-app-service-static-ip-restrictions"></a>Restrizioni IP statico del Servizio app di Azure #

Le restrizioni IP consentono di definire un elenco di indirizzi IP che possono accedere all'app. Questo elenco può includere singoli indirizzi IP o un intervallo di indirizzi IP definito da una subnet mask.

Quando un client genera una richiesta all'app, l'indirizzo IP viene controllato rispetto all'elenco di indirizzi IP consentiti. Se l'indirizzo IP non è presente nell'elenco, l'app risponde con un codice di stato [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

Le restrizioni IP sono definite nel file web.config utilizzato dall'app in fase di esecuzione. In determinate circostanze, qualche modulo potrebbe essere eseguito prima della logica delle restrizioni IP nella pipeline HTTP. In questo caso, la richiesta ha esito negativo con un diverso codice di errore HTTP.

Le restrizioni IP vengono valutate nelle stesse istanze del piano di servizio App assegnate all'app.

Per aggiungere una regola di restrizione IP all'app, usare il menu per aprire **Rete**>**Restrizioni IP** e fare clic su **Configura restrizioni IP**

![Restrizioni IP] (media/app-service-ip-restrictions/ip-restrictions.png)

Da qui è possibile esaminare l'elenco delle regole di restrizioni IP definite per l'app.

![elencare le restrizioni IP](media/app-service-ip-restrictions/browse-ip-restrictions.png)

È possibile fare clic su **[+] Aggiungi** per aggiungere una nuova regola di restrizioni IP.

![aggiungere restrizioni IP](media/app-service-ip-restrictions/add-ip-restrictions.png)

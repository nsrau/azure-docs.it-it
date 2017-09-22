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
ms.date: 09/12/2017
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 83cdc42d412f646ddf1ecd1b65bf9aa46983b26b
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="azure-app-service-static-ip-restrictions"></a>Restrizioni IP statico del Servizio app di Azure #

Le restrizioni IP consentono di definire un elenco di indirizzi IP che non possono accedere alle app. L'elenco di blocchi può includere singoli indirizzi IP o un intervallo di indirizzi IP definito da una subnet mask.

Quando viene generata sa un client una richiesta per l'app, l'indirizzo IP viene controllato a fronte dell'elenco degli IP bloccati. Se viene trovata una corrispondenza, l'applicazione risponde con un codice di stato [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

Le restrizioni IP vengono valutate nelle stesse istanze del piano di servizio App assegnate all'app.

Per aggiungere una regola di restrizione IP all'app, usare il menu per aprire **Rete**>**Restrizioni IP** e fare clic su **Configura restrizioni IP**

![Restrizioni IP] (media/app-service-ip-restrictions/ip-restrictions.png)

Da qui è possibile esaminare l'elenco delle regole di restrizioni IP definite per l'app.

![elencare le restrizioni IP](media/app-service-ip-restrictions/browse-ip-restrictions.png)

È possibile fare clic su **[+] Aggiungi** per aggiungere una nuova regola di restrizioni IP.

![aggiungere restrizioni IP](media/app-service-ip-restrictions/add-ip-restrictions.png)


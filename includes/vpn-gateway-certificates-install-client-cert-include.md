---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 0068bd151c3d7d243b05c326ec73a201f4131296
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679986"
---
Se si vuole creare una connessione da punto a sito da un computer client diverso da quello usato per generare i certificati client, è necessario installare un certificato client. Quando si installa un certificato client, è necessaria la password che è stata creata durante l'esportazione del certificato client.

1. Individuare e copiare il file *.pfx* nel computer client. Nel computer client, fare doppio clic sul file *.pfx* per installarlo. Lasciare l'opzione **Percorso archivio** impostata su **Utente corrente** e quindi fare clic su **Avanti**.
2. Nella pagina **File da importare** non apportare alcuna modifica. Fare clic su **Avanti**.
3. Nella pagina **Protezione della chiave privata** immettere la password per il certificato o verificare che l'entità di sicurezza sia corretta e quindi fare clic su **Avanti**.
4. Nella pagina **Archivio certificati** lasciare la posizione come predefinita e quindi fare clic su **Avanti**.
5. Fare clic su **Finish**. In **Avviso di sicurezza** per l'installazione del certificato fare clic su **Sì**. È possibile fare clic su "Sì" perché il certificato è stato generato. Il certificato è stato importato correttamente.
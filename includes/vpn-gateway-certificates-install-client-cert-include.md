---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f860a0cebefaebe16c40abf008bd24e333eb80d5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
Se si vuole creare una connessione da punto a sito da un computer client diverso da quello usato per generare i certificati client, è necessario installare un certificato client. Quando si installa un certificato client, è necessaria la password che è stata creata durante l'esportazione del certificato client.

1. Individuare e copiare il file *.pfx* nel computer client. Nel computer client, fare doppio clic sul file *.pfx* per installarlo. Lasciare l'opzione **Percorso archivio** impostata su **Utente corrente** e quindi fare clic su **Avanti**.
2. Nella pagina **File da importare** non apportare alcuna modifica. Fare clic su **Avanti**.
3. Nella pagina **Protezione della chiave privata** immettere la password per il certificato o verificare che l'entità di sicurezza sia corretta e quindi fare clic su **Avanti**.
4. Nella pagina **Archivio certificati** lasciare la posizione come predefinita e quindi fare clic su **Avanti**.
5. Fare clic su **Fine**. In **Avviso di sicurezza** per l'installazione del certificato fare clic su **Sì**. È possibile fare clic su "Sì" perché il certificato è stato generato. Il certificato è stato importato correttamente.
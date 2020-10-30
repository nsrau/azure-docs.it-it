---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061625"
---
Se si vuole creare una connessione da punto a sito da un computer client diverso da quello usato per generare i certificati client, è necessario installare un certificato client. Quando si installa un certificato client, è necessaria la password che è stata creata durante l'esportazione del certificato client.

1. Individuare e copiare il file *.pfx* nel computer client. Nel computer client, fare doppio clic sul file *.pfx* per installarlo. Lasciare il **percorso dell'archivio** come **utente corrente** e quindi fare clic su **Avanti** .
1. Nella pagina **File da importare** non apportare alcuna modifica. Selezionare **Avanti** .
1. Nella pagina **protezione della chiave privata** immettere la password per il certificato o verificare che l'entità di sicurezza sia corretta, quindi fare clic su **Avanti** .
1. Nella pagina **archivio certificati** lasciare il percorso predefinito e quindi fare clic su **Avanti** .
1. Selezionare **Fine** . Nell' **avviso di sicurezza** per l'installazione del certificato selezionare **Sì** . È possibile selezionare "Sì" per questo avviso di sicurezza perché il certificato è stato generato.
1. Il certificato è stato importato correttamente.

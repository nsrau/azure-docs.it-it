---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875561"
---
Le impostazioni DNS non sono una parte obbligatoria di questa configurazione, ma il DNS è necessario se si vuole la risoluzione dei nomi tra le macchine virtuali. Se si specifica un valore, non verrà creato un nuovo server DNS. L'indirizzo IP del server DNS specificato deve essere un server DNS in grado di risolvere i nomi per le risorse a cui ci si connette.

Dopo aver creato la rete virtuale, è possibile aggiungere l'indirizzo IP di un server DNS per gestire la risoluzione dei nomi. Aprire le impostazioni per la rete virtuale, selezionare server DNS e aggiungere l'indirizzo IP del server DNS che si vuole usare per la risoluzione dei nomi.

1. Individuare la rete virtuale nel portale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale selezionare **server DNS**.
3. Aggiungere un server DNS.
4. Per salvare le impostazioni, fare clic su **Salva** nella parte superiore della pagina.
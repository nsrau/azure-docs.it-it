---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/11/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320103"
---
In caso di problemi di connessione, effettuare i controlli seguenti:

- Se è stato esportato un certificato client con la **procedura guidata di esportazione dei certificati**, assicurarsi che si tratti di un file con estensione pfx e che sia selezionata l'opzione **Include all certificates in the certification path if possible** (Se possibile, includi tutti i certificati nel percorso di certificazione). Usando questo valore per l'esportazione, vengono esportate anche le informazioni del certificato radice. Dopo l'installazione del certificato nel computer client, viene installato anche il certificato radice nel file con estensione pfx. Per verificare che il certificato radice sia installato, aprire **Gestire i certificati utente** e selezionare **Autorità di certificazione radice attendibili\Certificati**. Per il funzionamento dell'autenticazione, verificare che il certificato radice sia presente nell'elenco.

- Se è stato usato un certificato emesso tramite una soluzione CA globale (enterprise) e non è possibile eseguire l'autenticazione, verificare l'ordine di autenticazione del certificato client. Controllare l'ordine dell'elenco di autenticazione facendo doppio clic sul certificato client, selezionando **Dettagli**, quindi **Utilizzo chiavi avanzato**. Assicurarsi che l'elenco mostri *Autenticazione client* come primo elemento. In caso contrario, emettere un certificato client in base al modello di utente che presenta *Autenticazione client* come primo elemento nell'elenco.

- Per altre informazioni sulla risoluzione dei problemi delle connessioni P2S, vedere [Risoluzione dei problemi di connessione da punto a sito](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

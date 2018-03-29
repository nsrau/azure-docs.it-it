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
ms.openlocfilehash: 9fa18b14b82376a25bb434acd770d340b1ef9262
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
In caso di problemi di connessione, effettuare i controlli seguenti:

- Se è stato esportato un certificato client, assicurarsi che si tratti di un file PFX con il valore predefinito "Se possibile, includi tutti i certificati nel percorso certificazione". Usando questo valore per l'esportazione, vengono esportate anche le informazioni del certificato radice. Quando il certificato viene installato nel computer client, anche il certificato radice contenuto nel file PFX viene installato nel computer client. Nel computer client devono essere installate le informazioni del certificato radice. Per verificare, aprire **Gestire i certificati utente** e passare ad **Autorità di certificazione radice attendibili\Certificati**. Verificare che il certificato radice sia incluso nell'elenco. Per il corretto funzionamento dell'autenticazione è necessario che il certificato radice sia presente.

- Se si usa un certificato che è stato rilasciato tramite una soluzione CA globale e si riscontrano problemi durante l'autenticazione, controllare l'ordine di autenticazione del certificato client. È possibile controllare l'ordine dell'elenco di autenticazione facendo doppio clic sul certificato client e andando in **Dettagli > Utilizzo chiavi avanzato**. Assicurarsi che l'elenco mostri "Autenticazione client" come primo elemento. In caso contrario, è necessario emettere un certificato client in base al modello di utente con l'autenticazione client come primo elemento nell'elenco.

- Per altre informazioni sulla risoluzione dei problemi delle connessioni P2S, vedere [Risoluzione dei problemi di connessione da punto a sito](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
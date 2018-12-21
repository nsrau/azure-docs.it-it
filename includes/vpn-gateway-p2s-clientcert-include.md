---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 7ae3886db6391836cd8d281e44c95c5253cc8dd5
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323803"
---
Ogni computer client che viene connesso a una rete virtuale con la connessione Da punto a sito deve avere un certificato client installato. Il certificato viene generato dal certificato radice e viene installato in ogni computer client. Se non si installa un certificato client valido, l'autenticazione avrà esito negativo quando il client prova a connettersi alla rete virtuale.

È possibile generare un certificato univoco per ogni client oppure usare lo stesso certificato per più client. Generare certificati client univoci offre il vantaggio di poter revocare un singolo certificato. In caso contrario, se più client utilizzano lo stesso certificato client per l’autenticazione e questo viene revocato, è necessario generare e installare nuovi certificati per tutti i client che usano tale certificato.

È possibile generare i certificati client usando i metodi seguenti:

- **Certificato aziendale:**

  - Se si usa una soluzione aziendale per la creazione di certificati, generare un certificato client con il valore di nome comune nel formato *name@yourdomain.com*. Usare questo formato anziché il formato *nome dominio\nome utente*.
  - Verificare che il certificato client sia basato sul modello di certificato utente con *Autenticazione client* riportato come primo elemento nell'elenco d'uso. Controllare il certificato facendo doppio clic sul certificato client e aprendo **Utilizzo chiavi avanzato** nella scheda **Dettagli**.

- **Certificato radice autofirmato:** Seguire la procedura descritta in uno dei seguenti articoli sui certificati di connessione da punto a sito, in modo che i certificati client creati siano compatibili con le connessioni da punto a sito. I passaggi negli articoli indicati generano un certificato compatibile: 

  * [Istruzioni per Windows 10 PowerShell ](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): queste istruzioni richiedono Windows 10 e PowerShell per generare i certificati. I certificati generati possono essere installati in qualsiasi client con connessione da punto a sito supportato.
  * [Istruzioni per MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): se non si ha accesso a un computer con Windows 10, è possibile usare MakeCert per generare i certificati. Sebbene MakeCert sia deprecato, è comunque possibile usarlo per generare i certificati. È possibile installare i certificati generati in qualsiasi client con connessione da punto a sito supportato.
  * [Istruzioni per Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Se si genera un certificato client da un certificato radice autofirmato, viene eseguita l'installazione automatica nel computer usato per generarlo. Se si vuole installare un certificato client in un altro computer client, esportarlo come file con estensione .pfx, insieme all'intera catena di certificati. In questo modo, viene creato un file .pfx contenente le informazioni del certificato radice necessarie per l’autenticazione del client. Per scoprire i passaggi necessari per esportare un certificato, vedere [Generare ed esportare i certificati per le connessioni da punto a sito usando PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).
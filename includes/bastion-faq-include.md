---
title: File di inclusione
description: File di inclusione
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d31e30991056cc891e63347a2c88e7fc4caeab28
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875442"
---
### <a name="regions"></a>Quali aree sono disponibili?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>È necessario un indirizzo IP pubblico nella macchina virtuale?

NON è necessario un indirizzo IP pubblico nella macchina virtuale di Azure a cui ci si connette con il servizio Azure Bastion. Il servizio Bastion aprirà la sessione/connessione RDP/SSH con la macchina virtuale tramite l'indirizzo IP privato della macchina virtuale, all'interno della rete virtuale.

### <a name="rdpssh"></a>È necessario un client RDP o SSH?

Non è necessario un client RDP o SSH per accedere alla macchina virtuale di Azure tramite RDP/SSH nel portale di Azure. Usare il [portale di Azure](https://portal.azure.com) per accedere tramite RDP/SSH alla macchina virtuale direttamente nel browser.

### <a name="agent"></a>È necessario un agente in esecuzione nella macchina virtuale di Azure?

Non è necessario installare un agente o altro software nel browser o nella macchina virtuale di Azure. Il servizio Bastion è senza agente e non richiede alcun software aggiuntivo per la connettività RDP/SSH.

### <a name="browsers"></a>Quali browser sono supportati?

Usare il browser Microsoft Edge o Google Chrome in Windows. Per Apple Mac, usare il browser Google Chrome. Anche Microsoft Edge Chromium è supportato sia in Windows sia in Mac.

### <a name="roles"></a>Sono necessari ruoli specifici per accedere alla macchina virtuale?

Per stabilire una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

### <a name="pricingpage"></a>Quali sono i prezzi?

Per altre informazioni vedere la [pagina dei prezzi](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Perché viene visualizzato il messaggio di errore "Sessione utente scaduta" prima dell'avvio della sessione di Bastion?

Una sessione deve essere avviata solo dal portale di Azure. Accedere al portale di Azure e avviare di nuovo la sessione. Questo errore è previsto se si passa all'URL direttamente da un'altra sessione o scheda del browser. Consente di assicurarsi che la sessione sia più sicura e che sia accessibile solo tramite il portale di Azure.

### <a name="keyboard"></a>Quali layout di tastiera sono supportati durante la sessione remota di Bastion?

Azure Bastion supporta attualmente il layout di tastiera QWERTY en-us nella macchina virtuale.  Il supporto di altre impostazioni locali per il layout di tastiera è in fase di elaborazione.

### <a name="udr"></a>Il routing definito dall'utente è supportato in una subnet Azure Bastion?

No. Il routing definito dall'utente non è supportato in una subnet Azure Bastion.
Per gli scenari che includono Azure Bastion e Firewall di Azure/appliance di rete virtuale nella stessa rete virtuale, non è necessario forzare il traffico da una subnet Azure Bastion a Firewall di Azure, perché la comunicazione tra Azure Bastion e le VM è privata. Per altre informazioni, vedere [Accesso alle VM dietro Firewall di Azure con Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="filetransfer"></a>Il trasferimento di file è supportato con la sessione RDP di Azure Bastion?

Ci stiamo impegnando ad aggiungere nuove funzionalità. Al momento, il trasferimento di file non è supportato, ma fa parte della nostra roadmap. È possibile condividere commenti e suggerimenti sulle nuove funzionalità nella pagina di [feedback su Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).

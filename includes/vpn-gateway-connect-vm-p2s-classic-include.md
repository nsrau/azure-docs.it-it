---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6d0737a7300b2a6025f776c1ed65a05cacf2141a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157680"
---
Creare una connessione Desktop remoto per connettersi a una macchina virtuale distribuita nella rete virtuale. Il modo migliore per verificare di potersi connettere alla macchina virtuale consiste nel connettersi con il suo indirizzo IP privato invece che con il nome del computer. Ciò consente di verificare se è possibile connettersi, non se la risoluzione dei nomi è configurata correttamente. 

1. Individuare l'indirizzo IP privato per la VM. Per trovare l'indirizzo IP privato di una macchina virtuale, visualizzare le proprietà di tale macchina virtuale nel portale di Azure oppure usare PowerShell.
2. Verificare di essere connessi alla rete virtuale con la connessione VPN da punto a sito. 
3. Per aprire Connessione Desktop remoto, immettere *RDP* o *Connessione Desktop remoto* nella casella di ricerca nella barra delle applicazioni, quindi selezionare **Connessione Desktop remoto**. È anche possibile aprire con il comando **mstsc** in PowerShell. 
3. In **Connessione Desktop remoto** immettere l'indirizzo IP privato della macchina virtuale. Se necessario, selezionare **Mostra opzioni** per modificare altre impostazioni e quindi connettersi.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Per risolvere i problemi di una connessione RDP a una VM

Se si verificano problemi di connessione a una macchina virtuale tramite la connessione VPN, è possibile controllare alcuni elementi. 

- Verificare che la connessione VPN sia attiva.
- Verificare che venga effettuata la connessione all'indirizzo IP privato per la macchina virtuale.
- Immettere **ipconfig** per controllare l'indirizzo IPv4 assegnato alla scheda Ethernet nel computer da cui viene effettuata la connessione. Quando l'indirizzo IP è compreso nell'intervallo di indirizzi della rete virtuale a cui ci si connette o nell'intervallo di indirizzi del pool di indirizzi del client VPN, si verifica una sovrapposizione dello spazio di indirizzi. Con questo tipo di sovrapposizione, il traffico di rete non raggiunge Azure e rimane nella rete locale.
- Se è possibile connettersi alla macchina virtuale usando l'indirizzo IP privato, ma non il nome del computer, verificare di avere configurato correttamente il valore per DNS. Per altre informazioni sul funzionamento della risoluzione dei nomi per le macchine virtuali, vedere [Risoluzione dei nomi per le macchine virtuali](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Verificare che il pacchetto di configurazione del client VPN sia stato generato dopo aver specificato gli indirizzi IP del server DNS per la rete virtuale. Se gli indirizzi IP del server DNS vengono aggiornati, generare e installare un nuovo pacchetto di configurazione del client VPN.

Per altre informazioni sulla risoluzione dei problemi, vedere l'articolo su come [risolvere i problemi delle connessioni Desktop remoto a una VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).
---
title: Informazioni sulla connettività di rete per desktop virtuali Windows
titleSuffix: Azure
description: Informazioni sulla connettività di rete per desktop virtuali Windows
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 4c0017a36d84973a4d99c49a5ea33faeb189b35f
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639342"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Informazioni sulla connettività di rete per desktop virtuali Windows

Desktop virtuale di Windows offre la possibilità di ospitare sessioni client negli host di sessione in esecuzione in Azure. Microsoft gestisce parti dei servizi per conto del cliente e fornisce endpoint sicuri per la connessione di client e host di sessione. Il diagramma seguente fornisce una panoramica di alto livello delle connessioni di rete usate da desktop virtuale Windows

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Diagramma delle connessioni di rete di desktop virtuali Windows" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Connettività della sessione

Desktop virtuale di Windows usa Remote Desktop Protocol (RDP) per fornire funzionalità di visualizzazione e input Remote sulle connessioni di rete. Il protocollo RDP è stato rilasciato inizialmente con Windows NT 4,0 Terminal Server Edition ed è stato continuamente in evoluzione con tutte le versioni di Microsoft Windows e Windows Server. Dall'inizio, RDP è stato sviluppato per essere indipendente dallo stack di trasporto sottostante e attualmente supporta più tipi di trasporto.

## <a name="reverse-connect-transport"></a>Trasporto di connessione inversa

Desktop virtuale Windows utilizza il trasporto di connessione inversa per stabilire la sessione remota e per il traffico RDP. Diversamente dalle distribuzioni Servizi Desktop remoto locali, il trasporto con connessione inversa non usa un listener TCP per ricevere le connessioni RDP in ingresso. USA invece la connettività in uscita all'infrastruttura di desktop virtuale Windows tramite la connessione HTTPS.

## <a name="session-host-communication-channel"></a>Canale di comunicazione host sessione

All'avvio dell'host sessione desktop virtuale di Windows, il servizio caricatore agente Desktop remoto stabilisce il canale di comunicazione persistente di Windows Virtual Desktop Broker. Questo canale di comunicazione è sovrapposto a una connessione TLS (Secure Transport Layer Security) e funge da bus per lo scambio di messaggi di servizio tra host sessione e infrastruttura desktop virtuale di Windows.

## <a name="client-connection-sequence"></a>Sequenza di connessione client

Sequenza di connessione client descritta di seguito:

1. L'utilizzo di un utente client desktop virtuale Windows supportato sottoscrive l'area di lavoro desktop virtuale di Windows
2. Azure Active Directory autentica l'utente e restituisce il token utilizzato per enumerare le risorse disponibili per un utente
3. Il client passa il token al servizio di sottoscrizione feed desktop virtuale di Windows
4. Il servizio di sottoscrizione feed desktop virtuale di Windows convalida il token
5. Il servizio di sottoscrizione feed desktop virtuale di Windows passa di nuovo l'elenco dei desktop e dei RemoteApp disponibili al client sotto forma di configurazione della connessione con firma digitale
6. Il client archivia la configurazione della connessione per ogni risorsa disponibile in un set di file con estensione RDP
7. Quando un utente seleziona la risorsa per la connessione, il client usa il file con estensione RDP associato e stabilisce la connessione TLS 1,2 protetta all'istanza del gateway desktop virtuale Windows più vicina e passa le informazioni di connessione
8. Il gateway desktop virtuale di Windows convalida la richiesta e chiede al broker di desktop virtuali Windows di orchestrare la connessione
9. Windows Virtual Desktop Broker identifica l'host sessione e utilizza il canale di comunicazione persistente stabilito in precedenza per inizializzare la connessione.
10. Desktop remoto stack avvia la connessione TLS 1,2 alla stessa istanza del gateway di desktop virtuale Windows usata dal client
11. Dopo che sia il client che l'host della sessione si sono connessi al gateway, il gateway avvia l'inoltro dei dati non elaborati tra entrambi gli endpoint, che stabilisce il trasporto di base per la connessione inversa per il protocollo RDP
12. Dopo aver impostato il trasporto di base, il client avvia l'handshake RDP

## <a name="connection-security"></a>Sicurezza delle connessioni

TLS 1,2 viene usato per tutte le connessioni avviate dai client e dagli host di sessione ai componenti dell'infrastruttura desktop virtuale di Windows.
Per il trasporto con connessione inversa, sia il client che l'host sessione si connettono al gateway desktop virtuale di Windows. Dopo aver stabilito la connessione TCP, il client o l'host sessione convalida il certificato del gateway di desktop virtuale di Windows.
Dopo aver stabilito il trasporto di base, RDP stabilisce una connessione TLS annidata tra il client e l'host sessione usando i certificati dell'host sessione. Per impostazione predefinita, il certificato usato per la crittografia RDP viene generato automaticamente dal sistema operativo durante la distribuzione. Se lo si desidera, i clienti possono distribuire certificati gestiti centralmente emessi dall'autorità di certificazione globale (Enterprise). Per ulteriori informazioni sulla configurazione dei certificati, vedere la [documentazione di Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui requisiti di larghezza di banda per desktop virtuali Windows, vedere [informazioni sui requisiti della larghezza di banda Remote Desktop Protocol (RDP) per desktop virtuale di Windows](rdp-bandwidth.md).
* Per iniziare a usare la qualità del servizio (QoS) per desktop virtuale di Windows, vedere [implementare la qualità del servizio (QoS) per desktop virtuale di Windows](rdp-quality-of-service-qos.md).

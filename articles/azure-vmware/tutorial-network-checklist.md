---
title: 'Esercitazione: Elenco di controllo per la rete'
description: Prerequisiti e dettagli relativi ai requisiti per la connettività e le porte di rete
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 42bd579a455c7efe3b8f6e4d4154e687726adb1d
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739526"
---
# <a name="networking-checklist-for-azure-vmware-solution-avs"></a>Elenco di controllo per la rete per la soluzione Azure VMware (AVS)

La soluzione Azure VMware offre un ambiente di cloud privato VMware, accessibile per utenti e applicazioni da risorse o ambienti locali e basati su Azure. La connettività viene fornita tramite servizi di rete, ad esempio le connessioni ExpressRoute e VPN di Azure, e richiede alcuni intervalli di indirizzi di rete e porte del firewall specifici per l'abilitazione dei servizi. Questo articolo fornisce le informazioni necessarie per configurare correttamente la rete per l'uso con la soluzione AVS.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
> * Requisiti di rete e connettività
> * DHCP in AVS

## <a name="network-connectivity"></a>Connettività di rete

Il cloud privato di AVS è connesso alla rete virtuale di Azure tramite una connessione ExpressRoute di Azure. Questa connessione ad ampia larghezza di banda e a bassa latenza consente di accedere ai servizi in esecuzione nella sottoscrizione di Azure dall'ambiente cloud privato.

I cloud privati di AVS richiedono almeno un blocco di indirizzi di rete `/22` CIDR per le subnet, come illustrato di seguito. Questa rete integra le reti locali disponibili. Per la connessione ad ambienti locali e reti virtuali, questo blocco di indirizzi di rete non deve essere sovrapposto.

Esempio di blocco di indirizzi di rete `/22` CIDR: `10.10.0.0/22`

Le subnet:

| Utilizzo di rete             | Subnet | Esempio        |
| ------------------------- | ------ | -------------- |
| Gestione del cloud privato            | `/24`    | `10.10.0.0/24`   |
| Rete vMotion       | `/24`    | `10.10.1.0/24`   |
| Carichi di lavoro della VM | `/24`   | `10.10.2.0/24`   |
| Peering ExpressRoute | `/24`    | `10.10.3.8/30`   |

### <a name="network-ports-required-to-communicate-with-the-service"></a>Porte di rete necessarie per comunicare con il servizio

Source (Sorgente)|Destination|Protocollo |Porta |Descrizione  |Comment
---|-----|:-----:|:-----:|-----|-----
Server DNS del cloud privato  |Server DNS locale  |UDP |53|Client DNS: inoltra le richieste di PC vCenter per qualsiasi query DNS locale (vedere la sezione seguente su DNS) |
Server DNS locale  |Server DNS del cloud privato  |UDP |53|Client DNS: inoltra le richieste di servizi locali a server DNS del cloud privato (vedere la sezione seguente su DNS)
Rete locale  |Server vCenter del cloud privato  |TCP (HTTP)  |80|Il server vCenter richiede la porta 80 per connessioni HTTP dirette. La porta 80 reindirizza le richieste alla porta HTTPS 443. Questo reindirizzamento è utile se si usa `http://server` invece di `https://server`.  <br><br>WS-Management (richiede anche che la porta 443 sia aperta) <br><br>Se si usa un database Microsoft SQL personalizzato e non il database di SQL Server 2008 in bundle nel server vCenter, la porta 80 viene usata da SQL Reporting Services. Quando si installa il server vCenter, il programma di installazione richiede di cambiare la porta HTTP. Per una corretta installazione, cambiare la porta HTTP del server vCenter specificando un valore personalizzato. Anche Microsoft Internet Information Services (IIS) usa la porta 80. Vedere l'articolo sul conflitto tra il server vCenter e IIS per la porta 80.
Rete di gestione del cloud privato |Active Directory locale  |TCP  |389|Questa porta deve essere aperta nell'istanza locale e in tutte le istanze remote del server vCenter. Questa porta corrisponde al numero di porta LDAP per i servizi directory per il gruppo di server vCenter. Il sistema server vCenter deve essere associato alla porta 389, anche se questa istanza del server vCenter non viene aggiunta al gruppo Linked Mode. Se un altro servizio è in esecuzione su questa porta, può essere preferibile rimuoverlo o sostituirne la porta con un'altra. È possibile eseguire il servizio LDAP su qualsiasi porta da 1025 a 65535.  Se questa istanza funge da Active Directory di Microsoft Windows, sostituire il numero di porta 389 con una porta disponibile compresa tra 1025 e 65535. Questa porta è facoltativa per la configurazione di AD locale come origine delle identità nell'istanza di vCenter per cloud privato
Rete locale  |Server vCenter del cloud privato  |TCP (HTTPS)  |443|Questa porta consente di accedere a vCenter dalla rete locale. La porta predefinita usata dal sistema server vCenter per restare in ascolto delle connessioni provenienti dal client vSphere. Per consentire al sistema server vCenter di ricevere dati dal client vSphere, aprire la porta 443 nel firewall. Il sistema server vCenter usa la porta 443 anche per monitorare il trasferimento di dati inviati da client SDK. Questa porta viene usata anche per i servizi seguenti: WS-Management (richiede anche che la porta 80 sia aperta). Accesso del client vSphere a vSphere Update Manager. Connessioni di client di gestione di rete di terze parti al server vCenter. Accesso di client di gestione di rete di terze parti agli host. 
Web browser  | Hybrid Cloud Manager  | TCP (HTTPS) | 9443 | Interfaccia di gestione di appliance virtuali per la configurazione di sistema di Hybrid Cloud Manager.
Rete di amministrazione  | Hybrid Cloud Manager |ssh |22| Accesso SSH come amministratore a Hybrid Cloud Manager.
HCM |   Gateway cloud|TCP (HTTPS) |8123| Invia le istruzioni del servizio di replica basato su host al gateway cloud ibrido.
HCM |   Gateway cloud  |    HTTP TCP (HTTPS) |    9443  |  Invia le istruzioni di gestione al gateway cloud ibrido locale tramite l'API REST.
Gateway cloud|      L2C |    TCP (HTTP)  |   443 |   Invia le istruzioni di gestione dal gateway cloud a L2C quando L2C usa lo stesso percorso del gateway cloud ibrido.
Gateway cloud |     Host ESXi |     TCP |    80.902  |   Gestione e distribuzione di OVF
Gateway cloud (locale)|     Gateway cloud (remoto)|     UDP  |   4500 | Obbligatorio per IPSEC<br>   IKEv2 (Internet Key Exchange) per incapsulare i carichi di lavoro per il tunnel bidirezionale. È supportato anche lo standard NAT-T (Network Address Translation-Traversal).
Gateway cloud (locale)  |   Gateway cloud (remoto)  |   UDP  |   500   | Obbligatorio per IPSEC<br> ISAKMP (Internet Key Exchange) per il tunnel bidirezionale.
Rete vCenter locale|      Rete di gestione del cloud privato|      TCP  |    8000    |  vMotion di macchine virtuali da vCenter locale al cloud privato vCenter   |     

## <a name="dhcp"></a>DHCP

Le applicazioni e i carichi di lavoro in esecuzione in un ambiente cloud privato richiedono la risoluzione dei nomi e i servizi DHCP per la ricerca e l'assegnazione di indirizzi IP. Per fornire questi servizi, è necessaria un'infrastruttura DHCP e DNS corretta. È possibile configurare una macchina virtuale per fornire questi servizi nell'ambiente cloud privato.  
È consigliabile usare il servizio DHCP incorporato in NSX o usare un server DHCP locale nel cloud privato invece di instradare la trasmissione del traffico DHCP tramite la rete WAN all'ambiente locale.

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * Requisiti di rete e connettività
> * DHCP in AVS

## <a name="next-steps"></a>Passaggi successivi

Una volta completata la configurazione corretta della rete, continuare con l'esercitazione successiva per creare il cloud privato di AVS.

> [!div class="nextstepaction"]
> [Esercitazione: Creare un cloud privato di AVS](tutorial-create-private-cloud.md)

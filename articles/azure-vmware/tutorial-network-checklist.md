---
title: 'Esercitazione: Elenco di controllo di pianificazione della rete'
description: Informazioni sui prerequisiti della rete e dettagli relativi ai requisiti per la connettività e le porte di rete per la soluzione Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 5ee70b8a297e1b8418049ff229b3c1869819145b
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948205"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Elenco di controllo di pianificazione della rete per la soluzione Azure VMware 

La soluzione Azure VMware offre un ambiente di cloud privato VMware, accessibile per utenti e applicazioni da risorse o ambienti locali e basati su Azure. La connettività viene fornita tramite servizi di rete, ad esempio le connessioni VPN e Azure ExpressRoute, e richiede alcuni intervalli di indirizzi di rete e porte del firewall specifici per l'abilitazione dei servizi. Questo articolo fornisce le informazioni necessarie per configurare correttamente la rete per l'uso con la soluzione Azure VMware.

In questa esercitazione si vedrà:

> [!div class="checklist"]
> * Considerazioni sulla rete virtuale e sul circuito ExpressRoute
> * Requisiti di routing e subnet
> * Porte di rete necessarie per comunicare con i servizi
> * Considerazioni su DHCP e DNS nella soluzione Azure VMware

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>Considerazioni sulla rete virtuale e sul circuito ExpressRoute
Quando si crea una connessione di rete virtuale nella sottoscrizione, il circuito ExpressRoute viene stabilito tramite peering e usa una chiave di autorizzazione e un ID peering che si richiede nel portale di Azure. Il peering è una connessione privata, uno-a-uno tra il cloud privato e la rete virtuale.

> [!NOTE] 
> Il circuito ExpressRoute non fa parte di una distribuzione di cloud privato. Il circuito ExpressRoute locale esula dall'ambito di questo documento. Se è necessaria la connettività locale al cloud privato, è possibile usare uno dei circuiti ExpressRoute esistenti o acquistarne uno nel portale di Azure.

Quando si distribuisce un cloud privato, si ricevono gli indirizzi IP per vCenter e NSX-T Manager. Per accedere a tali interfacce di gestione, è necessario creare risorse aggiuntive nella rete virtuale della sottoscrizione. Nelle esercitazioni sono disponibili le procedure per creare le risorse e stabilire il [peering privato ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md).

La rete logica del cloud privato include NSX-T con il provisioning effettuato. Vengono forniti un gateway di livello 0 e uno di livello 1 con il provisioning effettuato. È possibile creare un segmento e collegarlo al gateway di livello 1 esistente o a uno nuovo definito. I componenti della rete logica NSX-T forniscono connettività da destra a sinistra tra carichi di lavoro e connettività dall'alto in basso a Internet e ai servizi di Azure.

## <a name="routing-and-subnet-considerations"></a>Considerazioni su routing e subnet
Il cloud privato di AVS è connesso alla rete virtuale di Azure tramite una connessione ExpressRoute di Azure. Questa connessione ad ampia larghezza di banda e a bassa latenza consente di accedere ai servizi in esecuzione nella sottoscrizione di Azure dall'ambiente cloud privato. Il routing è basato su BGP (Border Gateway Protocol), ne viene eseguito automaticamente il provisioning ed è abilitato per impostazione predefinita per ogni distribuzione di cloud privato. 

I cloud privati di AVS richiedono almeno un blocco di indirizzi di rete `/22` CIDR per le subnet, come illustrato di seguito. Questa rete integra le reti locali disponibili. Il blocco di indirizzi non deve sovrapporsi ai blocchi di indirizzi usati in altre reti virtuali presenti nella sottoscrizione e nelle reti locali. All'interno di questo blocco di indirizzi, le reti di gestione, provisioning e vMotion vengono automaticamente sottoposte a provisioning.

>[!NOTE]
>Gli intervalli consentiti per il blocco di indirizzi sono inclusi negli spazi indirizzi privati RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), ad eccezione di 172.17.0.0/16.

Esempio di blocco di indirizzi di rete `/22` CIDR: `10.10.0.0/22`

Le subnet:

| Utilizzo di rete             | Subnet | Esempio          |
| ------------------------- | ------ | ---------------- |
| Gestione del cloud privato  | `/26`  | `10.10.0.0/26`   |
| Rete vMotion           | `/25`  | `10.10.1.128/25` |
| Carichi di lavoro della VM              | `/24`  | `10.10.2.0/24`   |
| Peering ExpressRoute      | `/29`  | `10.10.3.8/29`   |


## <a name="required-network-ports"></a>Porte di rete necessarie

| Source (Sorgente) | Destination | Protocollo | Porta | Descrizione  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| Server DNS del cloud privato | Server DNS locale | UDP | 53 | Client DNS: inoltra le richieste di PC vCenter per qualsiasi query DNS locale (vedere la sezione seguente su DNS) |  
| Server DNS locale   | Server DNS del cloud privato | UDP | 53 | Client DNS: inoltra le richieste di servizi locali a server DNS del cloud privato (vedere la sezione seguente su DNS) |  
| Rete locale  | Server vCenter del cloud privato  | TCP (HTTP)  | 80 | Il server vCenter richiede la porta 80 per connessioni HTTP dirette. La porta 80 reindirizza le richieste alla porta HTTPS 443. Questo reindirizzamento è utile se si usa `http://server` invece di `https://server`.  <br><br>WS-Management (richiede anche che la porta 443 sia aperta) <br><br>Se si usa un database Microsoft SQL personalizzato e non il database di SQL Server 2008 in bundle nel server vCenter, la porta 80 viene usata da SQL Reporting Services. Quando si installa il server vCenter, il programma di installazione richiede di cambiare la porta HTTP. Per una corretta installazione, cambiare la porta HTTP del server vCenter specificando un valore personalizzato. Anche Microsoft Internet Information Services (IIS) usa la porta 80. Vedere l'articolo sul conflitto tra il server vCenter e IIS per la porta 80. |  
| Rete di gestione del cloud privato | Active Directory locale  | TCP  | 389 | Questa porta deve essere aperta nell'istanza locale e in tutte le istanze remote del server vCenter. Questa porta corrisponde al numero di porta LDAP per i servizi directory per il gruppo di server vCenter. Il sistema server vCenter deve essere associato alla porta 389, anche se questa istanza del server vCenter non viene aggiunta al gruppo Linked Mode. Se un altro servizio è in esecuzione su questa porta, può essere preferibile rimuoverlo o sostituirne la porta con un'altra. È possibile eseguire il servizio LDAP su qualsiasi porta da 1025 a 65535.  Se questa istanza funge da Active Directory di Microsoft Windows, sostituire il numero di porta 389 con una porta disponibile compresa tra 1025 e 65535. Questa porta è facoltativa, per la configurazione di AD locale come origine di identità nell'istanza di vCenter del cloud privato. |  
| Rete locale  | Server vCenter del cloud privato  | TCP (HTTPS)  | 443 | Questa porta consente di accedere a vCenter dalla rete locale. La porta predefinita usata dal sistema server vCenter per restare in ascolto delle connessioni provenienti dal client vSphere. Per consentire al sistema server vCenter di ricevere dati dal client vSphere, aprire la porta 443 nel firewall. Il sistema server vCenter usa la porta 443 anche per monitorare il trasferimento di dati inviati da client SDK. Questa porta viene usata anche per i servizi seguenti: WS-Management (richiede anche che la porta 80 sia aperta). Accesso del client vSphere a vSphere Update Manager. Connessioni di client di gestione di rete di terze parti al server vCenter. Accesso di client di gestione di rete di terze parti agli host. |  
| Web browser  | Hybrid Cloud Manager  | TCP (HTTPS) | 9443 | Interfaccia di gestione di appliance virtuali per la configurazione di sistema di Hybrid Cloud Manager. |
| Rete di amministrazione  | Hybrid Cloud Manager | SSH | 22 | Accesso SSH come amministratore a Hybrid Cloud Manager. |
| HCM | Gateway cloud | TCP (HTTPS) | 8123 | Invia le istruzioni del servizio di replica basato su host al gateway cloud ibrido. |
| HCM | Gateway cloud | HTTP TCP (HTTPS) | 9443 | Invia le istruzioni di gestione al gateway cloud ibrido locale tramite l'API REST. |
| Gateway cloud | L2C | TCP (HTTPS) | 443 | Invia le istruzioni di gestione dal gateway cloud a L2C quando L2C usa lo stesso percorso del gateway cloud ibrido. |
| Gateway cloud | Host ESXi | TCP | 80.902 | Gestione e distribuzione di OVF. |
| Gateway cloud (locale)| Gateway cloud (remoto) | UDP | 4500 | Obbligatorio per IPSEC<br>   IKEv2 (Internet Key Exchange) per incapsulare i carichi di lavoro per il tunnel bidirezionale. È supportato anche lo standard NAT-T (Network Address Translation-Traversal). |
| Gateway cloud (locale) | Gateway cloud (remoto)  | UDP | 500 | Obbligatorio per IPSEC<br> ISAKMP (Internet Key Exchange) per il tunnel bidirezionale. |
| Rete vCenter locale | Rete di gestione del cloud privato | TCP | 8000 |  vMotion di macchine virtuali da vCenter locale al cloud privato vCenter   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>Considerazioni su DHCP e sulla risoluzione DNS
Le applicazioni e i carichi di lavoro in esecuzione in un ambiente cloud privato richiedono la risoluzione dei nomi e i servizi DHCP per la ricerca e le assegnazioni di indirizzi IP. Per fornire questi servizi, è necessaria un'infrastruttura DHCP e DNS corretta. È possibile configurare una macchina virtuale per fornire questi servizi nell'ambiente cloud privato.  

Usare il servizio DHCP predefinito in NSX o usare un server DHCP locale nel cloud privato invece di instradare la trasmissione del traffico DHCP tramite la rete WAN all'ambiente locale.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato le considerazioni e i requisiti per la distribuzione di un cloud privato della soluzione Azure VMware. 


Una volta completata la configurazione corretta della rete, continuare con l'esercitazione successiva per creare il cloud privato della soluzione Azure VMware.

> [!div class="nextstepaction"]
> [Creare un cloud privato della soluzione Azure VMware](tutorial-create-private-cloud.md)

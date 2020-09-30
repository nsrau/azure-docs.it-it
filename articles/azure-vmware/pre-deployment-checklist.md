---
title: Elenco di controllo pre-distribuzione della soluzione VMware di Azure
description: Utilizzare questo elenco di controllo come parte del processo di pianificazione pre-distribuzione.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580021"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Elenco di controllo pre-distribuzione della soluzione VMware di Azure
Questo elenco di controllo pre-distribuzione verrà usato durante la [fase di pianificazione](production-ready-deployment-steps.md).

## <a name="success-criteria"></a>Criteri di superamento
Definire i test da eseguire e il risultato previsto.

| Informazioni fondamentali necessarie | Risposta |
| ----------- | ------------- |
| Distribuire SDDC | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Creare una rete virtuale | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Crea jump box | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Creare un gateway di rete virtuale | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Connetti ExpressRoute Copertura globale | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Accedere a gestione NSX e vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Informazioni primarie necessarie | Risposta |
| --------| --------------|
| Crea server DHCP | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Creare segmenti di rete | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Ridimensiona (Aggiungi o Elimina nodi) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Distribuire HCX VMware | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Creazione di macchine virtuali (VM) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| Abilita Internet | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Migrazione di VM da locale a cloud privato | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Informazioni aggiuntive necessarie | Risposta |
| --------| --------------|
| Operazioni snapshot VM | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Distribuire il servizio di bilanciamento del carico NSX-T | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Integrazione di Azure<br><ul><li>Raccolta contenuto condivisa</li><li>Integrazione della sicurezza</li><li>Carica ISO</li><li>Compila da ISO</li><li>Backup di Azure</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Segmentazione micro | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Routing | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Informazioni sulla soluzione VMware di Azure

#### <a name="azure-subscription"></a>Sottoscrizione di Azure
Fornire il nome della sottoscrizione e l'ID sottoscrizione per la soluzione VMware di Azure. La sottoscrizione può essere una sottoscrizione nuova o esistente. Non usare una sottoscrizione di sviluppo/test.

| Informazioni necessarie  | Risposta |
| ------------| ------------- |
| Sottoscrizione e ID | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Sottoscrizione con contratto Enterprise | ☐ Sì &nbsp; &nbsp; ☐ No  |
| Nome del gruppo di risorse | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Percorso | Stati Uniti orientali ☒ |
| Amministratore di Azure<br><br>Specificare il nome e il contatto dell'amministratore<br>assegnato per abilitare il servizio dal Marketplace.<br>Collaboratore è il ruolo minimo necessario per <br>[registrare il provider di risorse della soluzione VMware di Azure](tutorial-create-private-cloud.md#register-the-resource-provider). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>Informazioni VMware locali

| Informazioni necessarie  | Risposta |
| ------------| --------------|
| Versione di vSphere | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| versione di vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| amministratore vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Estensione L2<br><br>Se si estendono le reti L2 con VMware HCX,<br>fornire la rete locale che si estenderà. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vSwitch<br><br>Consente di specificare il tipo di vSwitch utilizzato in tutto l'ambiente. | ☐ Standard &nbsp; &nbsp; distribuito ☐<br><br>Se si usa standard, VMware HCX non è disponibile. |
| DNS e DHCP<br><br>È necessaria un'infrastruttura DNS e DHCP corretta. <br>È consigliabile usare il servizio DHCP incorporato in <br>NSX o usare un server DHCP locale nel cloud privato <br>invece di instradare il traffico DHCP broadcast attraverso <br>WAN di nuovo in locale. Per altre informazioni, <br>vedere [come creare e gestire DHCP in una soluzione VMware di Azure](manage-dhcp.md). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>Rete-infrastruttura della soluzione VMware di Azure 
I dati necessari consentono di soddisfare le esigenze di rete della soluzione VMware di Azure per i test di rete iniziali e di base. 

| Informazioni necessarie | Risposta |
| ----------- | ------------- |
|CIDR della soluzione VMware di Azure<br><br>Obbligatorio per gli host vSphere, rete VSAN e la gestione <br>reti nella soluzione VMware di Azure. Per ulteriori informazioni <br>per informazioni, vedere [considerazioni sul routing e sulla subnet](tutorial-network-checklist.md#routing-and-subnet-considerations). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| CIDR del carico di lavoro della soluzione VMware di Azure (facoltativo)<br><br>Assegnare una rete da usare in Azure VMware<br>Soluzione per il test iniziale. Macchine virtuali<br>verrà distribuito per convalidare la connettività di rete <br>da/verso la soluzione VMware di Azure. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>Rete: connettere la soluzione VMware di Azure alla rete virtuale di Azure
I dati necessari per dopo che è stato eseguito il cluster della soluzione VMware di Azure possono essere connessi ad Azure tramite un circuito ExpressRoute, che fa parte del servizio della soluzione VMware di Azure.

| Informazioni necessarie | Risposta |
| ------------------ | ------------- |
| Jump Box-Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Creare una rete virtuale | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Creare una subnet del gateway | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Creare un gateway di rete virtuale<br><br>Per altre informazioni, vedere [creare il gateway di rete virtuale](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>Rete: connettere la soluzione VMware di Azure a un Data Center locale

| Informazioni necessarie | Risposta |
| ------------------ | ------------- |
| CIDR peering ExpressRoute<br><br>`/29`Blocco di indirizzi CIDR. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Chiave di autorizzazione ExpressRoute e ID risorsa<br><br>Fornire una chiave di autorizzazione e un ID risorsa, <br>generato dal ExpressRoute corrente <br>circuito che si connette al Data Center locale.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Direzione della route predefinita<br><br>Le macchine virtuali nella soluzione VMware di Azure <br>accedere a Internet tramite la soluzione VMware di Azure fornita <br>Internet o tornare al circuito ExpressRoute per <br>in locale per la route predefinita? | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Porte di rete necessarie per comunicare con il servizio<br><br>Per ulteriori informazioni, vedere [porte di rete obbligatorie](tutorial-network-checklist.md#required-network-ports). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>Rete-VMware HCX

| Informazioni necessarie | Risposta |
| ------------------ | ------------- |
| Porte di rete<br><br>Se è presente un firewall, verificare che le porte di rete necessarie <br>vengono aperti tra la soluzione locale e la soluzione VMware di Azure. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>Per informazioni sulla configurazione di DNS, <br>vedere [rete-infrastruttura della soluzione VMware di Azure](#networking---azure-vmware-solution-infrastructure). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| CIDRs HCX VMware<br><br>Sono necessari due `/29` blocchi CIDR, usati per <br>i componenti dell'infrastruttura VMware HCX in locale.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


---
title: Soluzioni VMware di Azure (AVS)-cloud privato AVS sicuro
description: Viene descritto come proteggere il cloud privato di Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020081"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>Come proteggere l'ambiente cloud privato AVS

Definire il controllo degli accessi in base al ruolo (RBAC) per AVS Service, AVS Portal e AVS private cloud da Azure. Gli utenti, i gruppi e i ruoli per l'accesso a vCenter di AVS private cloud vengono specificati con VMware SSO. 

## <a name="rbac-for-avs-service"></a>RBAC per il servizio AVS

La creazione del servizio AVS richiede il ruolo di **proprietario** o **collaboratore** nella sottoscrizione di Azure. Per impostazione predefinita, tutti i proprietari e i collaboratori possono creare un servizio AVS e accedere ad AVS Portal per creare e gestire i cloud privati AVS. È possibile creare un solo servizio AVS per area. Per limitare l'accesso a amministratori specifici, attenersi alla procedura riportata di seguito.

1. Creare un servizio AVS in un nuovo **gruppo di risorse** portale di Azure
2. Specificare RBAC per il gruppo di risorse.
3. Acquistare nodi e usare lo stesso gruppo di risorse del servizio AVS

Solo gli utenti con privilegi di **proprietario** o **collaboratore** sul gruppo di risorse visualizzeranno il servizio AVS e lanceranno il portale AVS.

Per ulteriori informazioni su RBAC, vedere informazioni sul [controllo degli accessi in base al ruolo (RBAC) per le risorse di Azure](../role-based-access-control/overview.md).

## <a name="rbac-for-avs-private-cloud-vcenter"></a>RBAC per AVS private cloud vCenter

Quando viene creato un cloud privato AVS, viene creato un `CloudOwner@AVS.local` utente predefinito nel dominio vCenter SSO. L'utente CloudOwner dispone dei privilegi per la gestione di vCenter. Per consentire l'accesso a utenti diversi, vengono aggiunte ulteriori origini di identità a vCenter SSO. I ruoli e i gruppi predefiniti sono configurati in vCenter che possono essere usati per aggiungere altri utenti.

### <a name="add-new-users-to-vcenter"></a>Aggiungere nuovi utenti a vCenter

1. [Escalation dei privilegi](escalate-private-cloud-privileges.md) per **CloudOwner@AVS.local** utente nel cloud privato AVS.
2. Accedere a vCenter con **CloudOwner@AVS.local**
3. [Aggiungere gli utenti di vCenter Single Sign-on](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Aggiungere utenti a [vCenter Single Sign-on gruppi](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Per altre informazioni sui ruoli e sui gruppi predefiniti, vedere l'articolo relativo al [modello di autorizzazione del cloud privato AVS di VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Aggiungi nuove origini identità

È possibile aggiungere altri provider di identità per il dominio vCenter SSO del cloud privato AVS. I provider di identità forniscono i gruppi di autenticazione e vCenter SSO forniscono l'autorizzazione per gli utenti.

* [Usare Active Directory come provider di identità](set-vcenter-identity.md) in AVS private cloud vCenter.
* [Usare Azure ad come provider di identità](azure-ad.md) in AVS private cloud vCenter

1. [Escalation dei privilegi](escalate-private-cloud-privileges.md) per **CloudOwner@AVS.local** utente nel cloud privato AVS.
2. Accedere a vCenter con **CloudOwner@AVS.local**
3. Aggiungere utenti dal provider di identità ai [gruppi di Single Sign-on vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>Rete sicura nell'ambiente di cloud privato AVS

La sicurezza di rete dell'ambiente di cloud privato AVS viene controllata proteggendo l'accesso alla rete e controllando il traffico di rete tra le risorse.

### <a name="access-to-avs-private-cloud-resources"></a>Accesso alle risorse del cloud privato AVS

AVS private cloud vCenter e l'accesso alle risorse è attraverso una connessione di rete sicura:

* **[Connessione ExpressRoute](on-premises-connection.md)** . ExpressRoute offre una connessione protetta, a larghezza di banda elevata e a bassa latenza dall'ambiente locale. L'uso della connessione consente ai servizi, alle reti e agli utenti locali di accedere a AVS private cloud vCenter.
* **[Gateway VPN da sito a sito](vpn-gateway.md)** . La VPN da sito a sito consente di accedere alle risorse del cloud privato AVS dall'ambiente locale tramite un tunnel sicuro. Si specificano le reti locali che possono inviare e ricevere il traffico di rete nel cloud privato AVS.
* **[Gateway VPN da punto a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Usare la connessione VPN da punto a sito per l'accesso remoto rapido a AVS private cloud vCenter.

### <a name="control-network-traffic-in-avs-private-cloud"></a>Controllare il traffico di rete nel cloud privato AVS

Le tabelle e le regole del firewall controllano il traffico di rete nel cloud privato AVS. La tabella firewall consente di controllare il traffico di rete tra una rete di origine o un indirizzo IP e una rete di destinazione o un indirizzo IP in base alla combinazione di regole definite nella tabella.

1. Creare una [tabella del firewall](firewall.md#add-a-new-firewall-table).
2. [Aggiungere regole](firewall.md#create-a-firewall-rule) alla tabella del firewall.
3. [Alleghi una tabella firewall a una VLAN/subnet] (firewall. MD # alleghi-vlanssubnet.

---
title: Soluzione VMware di Azure di CloudSimple-cloud privato sicuro
description: Viene descritto come proteggere la soluzione VMware di Azure dal cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 39f451e94f2a825e69425f71aceda5f34de7eeb5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642643"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Come proteggere l'ambiente cloud privato

Definire il controllo degli accessi in base al ruolo (RBAC) per il servizio CloudSimple, il portale di CloudSimple e il cloud privato da Azure.  Gli utenti, i gruppi e i ruoli per l'accesso a vCenter del cloud privato vengono specificati tramite l'accesso SSO VMware.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC per il servizio CloudSimple

La creazione del servizio CloudSimple richiede il ruolo di **proprietario** o **collaboratore** nella sottoscrizione di Azure.  Per impostazione predefinita, tutti i proprietari e i collaboratori possono creare un servizio CloudSimple e accedere al portale CloudSimple per la creazione e la gestione di cloud privati.  È possibile creare un solo servizio CloudSimple per area.  Per limitare l'accesso a amministratori specifici, attenersi alla procedura riportata di seguito.

1. Creare un servizio CloudSimple in un nuovo **gruppo di risorse** portale di Azure
2. Specificare RBAC per il gruppo di risorse.
3. Acquistare nodi e usare lo stesso gruppo di risorse del servizio CloudSimple

Solo gli utenti con privilegi di **proprietario** o **collaboratore** sul gruppo di risorse vedranno il servizio CloudSimple e lanceranno il portale di CloudSimple.

Per ulteriori informazioni su RBAC, vedere informazioni sul [controllo degli accessi in base al ruolo (RBAC) per le risorse di Azure](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC per cloud privato vCenter

Quando viene creato `CloudOwner@cloudsimple.local` un cloud privato, viene creato un utente predefinito nel dominio vCenter SSO.  L'utente CloudOwner dispone dei privilegi per la gestione di vCenter.   Per consentire l'accesso a utenti diversi, vengono aggiunte ulteriori origini di identità a vCenter SSO.  I ruoli e i gruppi predefiniti sono configurati in vCenter che possono essere usati per aggiungere altri utenti.

### <a name="add-new-users-to-vcenter"></a>Aggiungere nuovi utenti a vCenter

1. [Escalation](escalate-private-cloud-privileges.md) dei *CloudOwner@cloudsimple.local* privilegi per l'utente nel cloud privato.
2. Accedere a vCenter con*CloudOwner@cloudsimple.local*
3. [Aggiungere gli utenti di vCenter Single Sign-on](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Aggiungere utenti a [vCenter Single Sign-on gruppi](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Per altre informazioni sui ruoli e sui gruppi predefiniti, vedere l'articolo relativo al [modello di autorizzazione del cloud privato CloudSimple di VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Aggiungi nuove origini identità

È possibile aggiungere altri provider di identità per il dominio vCenter SSO del cloud privato.  I provider di identità forniscono i gruppi di autenticazione e vCenter SSO forniscono l'autorizzazione per gli utenti.

* [Usare Active Directory come provider di identità](set-vcenter-identity.md) nel cloud privato vCenter.
* [Usare Azure ad come provider di identità](azure-ad.md) nel cloud privato vCenter

1. [Escalation](escalate-private-cloud-privileges.md) dei *CloudOwner@cloudsimple.local* privilegi per l'utente nel cloud privato.
2. Accedere a vCenter con*CloudOwner@cloudsimple.local*
3. Aggiungere utenti dal provider di identità ai [gruppi di Single Sign-on vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Sicurezza della rete nell'ambiente cloud privato

La sicurezza di rete dell'ambiente cloud privato viene controllata proteggendo l'accesso alla rete e controllando il traffico di rete tra le risorse.

### <a name="access-to-private-cloud-resources"></a>Accesso alle risorse del cloud privato

L'accesso al cloud privato e alle risorse è attraverso una connessione di rete sicura:

* **[Connessione ExpressRoute](on-premises-connection.md)**. ExpressRoute offre una connessione protetta, a larghezza di banda elevata e a bassa latenza dall'ambiente locale.  L'uso della connessione consente ai servizi locali, alle reti e agli utenti di accedere al cloud privato vCenter.
* **[Gateway VPN da sito a sito](vpn-gateway.md)**. La VPN da sito a sito consente di accedere alle risorse del cloud privato dall'ambiente locale tramite un tunnel sicuro.  Si specificano le reti locali che possono inviare e ricevere il traffico di rete nel cloud privato.
* **[Gateway VPN da punto a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Usare la connessione VPN da punto a sito per l'accesso remoto rapido al cloud privato vCenter.

### <a name="control-network-traffic-in-private-cloud"></a>Controllare il traffico di rete nel cloud privato

Le tabelle e le regole del firewall controllano il traffico di rete nel cloud privato.  La tabella firewall consente di controllare il traffico di rete tra una rete di origine o un indirizzo IP e una rete di destinazione o un indirizzo IP in base alla combinazione di regole definite nella tabella.

1. Creare una [tabella del firewall](firewall.md#add-a-new-firewall-table).
2. [Aggiungere regole](firewall.md#create-a-firewall-rule) alla tabella del firewall.
3. [Alleghi una tabella firewall a una VLAN/subnet] (firewall. MD # alleghi-vlanssubnet.

---
title: Soluzioni Azure VMware di CloudSimple - Secure Private Cloud
description: Descrive come proteggere le soluzioni VMware di Azure da CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565979"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Come proteggere l'ambiente Cloud privato

Definire il controllo degli accessi in base al ruolo per CloudSimple Service, il portale CloudSimple e il cloud privato da Azure.Define role-based access control (RBAC) for CloudSimple Service, CloudSimple portal, and Private Cloud from Azure.  Gli utenti, i gruppi e i ruoli per l'accesso a vCenter of Private Cloud vengono specificati tramite VMware SSO.  

## <a name="rbac-for-cloudsimple-service"></a>Controllo degli accessi in base al ruolo per il servizio CloudSimple

La creazione del servizio CloudSimple richiede il ruolo Proprietario o **Collaboratore** nella sottoscrizione di Azure.Creation of CloudSimple service requires **Owner** or Contributor role on the Azure subscription.  Per impostazione predefinita, tutti i proprietari e i collaboratori possono creare un servizio CloudSimple e accedere al portale CloudSimple per la creazione e la gestione di cloud privati.  È possibile creare un solo servizio CloudSimple per ogni area.  Per limitare l'accesso a amministratori specifici, attenersi alla procedura seguente.

1. Creare un servizio CloudSimple in un nuovo gruppo di risorse nel portale di AzureCreate a CloudSimple Service in a new **resource group** on Azure portal
2. Specificare il controllo degli accessi in base al ruolo per il gruppo di risorse.
3. Acquistare nodi e usare lo stesso gruppo di risorse del servizio CloudSimplePurchase nodes and use the same resource group as CloudSimple service

Solo gli utenti che dispongono dei privilegi **proprietario** o **collaboratore** per il gruppo di risorse vedranno il servizio CloudSimple e lanceranno il portale CloudSimple.

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere Che cos'è il controllo degli accessi in base al ruolo per le risorse di Azure.For more information about RBAC, see [What is role-based access control (RBAC) for Azure resources.](../role-based-access-control/overview.md)

## <a name="rbac-for-private-cloud-vcenter"></a>Controllo degli accessi in base al ruolo per il cloud privato vCenter

Un utente `CloudOwner@cloudsimple.local` predefinito viene creato nel dominio SSO vCenter quando viene creato un cloud privato.  L'utente CloudOwner dispone dei privilegi per la gestione di vCenter. Ulteriori origini di identità vengono aggiunte all'SSO di vCenter per consentire l'accesso a utenti diversi.  I ruoli e i gruppi predefiniti vengono impostati nel vCenter che può essere utilizzato per aggiungere altri utenti.

### <a name="add-new-users-to-vcenter"></a>Aggiungere nuovi utenti a vCenter

1. [Inoltrare i privilegi](escalate-private-cloud-privileges.md) per **cloudOwner\@cloudsimple.local** user sul cloud privato.
2. Accedere a vCenter utilizzando **CloudOwner\@cloudsimple.local**
3. [Aggiungere gli utenti Single Sign-On di vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Aggiungere utenti ai [gruppi Single Sign-On vCenter.](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)

Per altre informazioni sui ruoli e i gruppi predefiniti, vedere l'articolo [Modello di autorizzazione CloudSimple Private Cloud di VMware vCenter.For](learn-private-cloud-permissions.md) more information about pre-defined roles and groups, see CloudSimple Private Cloud permission model of VMware vCenter article.

### <a name="add-new-identity-sources"></a>Aggiungere nuove origini di identità

È possibile aggiungere ulteriori provider di identità per il dominio SSO vCenter del cloud privato.  I provider di identità forniscono l'autenticazione e i gruppi SSO vCenter forniscono l'autorizzazione per gli utenti.

* [Utilizzare Active Directory come provider](set-vcenter-identity.md) di identità in Private Cloud vCenter.
* [Usare Azure AD come provider](azure-ad.md) di identità in Private Cloud vCenterUse Azure AD as an identity provider on Private Cloud vCenter

1. [Inoltrare i privilegi](escalate-private-cloud-privileges.md) per **cloudOwner\@cloudsimple.local** user sul cloud privato.
2. Accedere a vCenter utilizzando **CloudOwner\@cloudsimple.local**
3. Aggiungere utenti dal provider di identità ai [gruppi Single Sign-On vCenter.](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)

## <a name="secure-network-on-your-private-cloud-environment"></a>Rete sicura nell'ambiente Private Cloud

La sicurezza di rete dell'ambiente Private Cloud è controllata proteggendo l'accesso alla rete e controllando il traffico di rete tra le risorse.

### <a name="access-to-private-cloud-resources"></a>Accesso alle risorse del cloud privato

Private Cloud vCenter e l'accesso alle risorse si trova tramite una connessione di rete sicura:

* **[Connessione ExpressRoute](on-premises-connection.md)**. ExpressRoute fornisce una connessione sicura, a larghezza di banda elevata e a bassa latenza dall'ambiente locale.  L'utilizzo della connessione consente ai servizi, alle reti e agli utenti locali di accedere al vCenter del cloud privato.
* **[Gateway VPN](vpn-gateway.md)** da sito a sito . La VPN da sito a sito consente di accedere alle risorse del cloud privato in locale tramite un tunnel sicuro.  Specificare quali reti locali possono inviare e ricevere traffico di rete verso il cloud privato.
* **[Gateway VPN da punto a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Utilizzare la connessione VPN da punto a sito per un rapido accesso remoto al centro commerciale Private Cloud.

### <a name="control-network-traffic-in-private-cloud"></a>Controllare il traffico di rete nel cloud privato

Le tabelle e le regole del firewall controllano il traffico di rete nel cloud privato.  La tabella firewall consente di controllare il traffico di rete tra una rete di origine o un indirizzo IP e una rete o un indirizzo IP di destinazione in base alla combinazione di regole definite nella tabella.

1. Creare una [tabella firewall](firewall.md#add-a-new-firewall-table).
2. [Aggiungere regole](firewall.md#create-a-firewall-rule) alla tabella del firewall.
3. [Collegare una tabella firewall a una rete Virtuale/subnet](firewall.md#attach-vlans-subnet).

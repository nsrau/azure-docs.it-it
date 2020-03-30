---
title: Soluzione Azure VMware - Inoltro DNS dal cloud privato all'ambiente locale
description: Descrive come abilitare il server CloudSimple Private Cloud per inoltrare la ricerca delle risorse locali
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961127"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Abilitare i server CloudSimple Private Cloud DNS per inoltrare la ricerca DNS delle risorse locali ai server DNS

I server DNS cloud privati possono inoltrare la ricerca DNS per qualsiasi risorsa locale ai server DNS.  L'abilitazione della ricerca consente ai componenti vSphere del cloud privato di cercare tutti i servizi in esecuzione nell'ambiente locale e di comunicare con essi utilizzando nomi di dominio completi (FQDN).

## <a name="scenarios"></a>Scenari 

La ricerca DNS di inoltro per il server DNS locale consente di utilizzare il cloud privato per gli scenari seguenti:

* Usare il cloud privato come configurazione di ripristino di emergenza per la soluzione VMware localeUse Private Cloud as a disaster recovery setup for your on-premises VMware solution
* Usare Active Directory locale come origine di identità per il cloud privato vSphereUse on-premises Active Directory as an identity source for your Private Cloud vSphere
* Usare HCX per la migrazione di macchine virtuali da locale a cloud privatoUse HCX for migrating virtual machines from on-premises to Private Cloud

## <a name="before-you-begin"></a>Prima di iniziare

Per il funzionamento dell'inoltro DNS, è necessario che sia presente una connessione di rete dalla rete cloud privata alla rete locale.  È possibile configurare la connessione di rete utilizzando:

* [Connettersi da locale a CloudSimple usando ExpressRouteConnect from on-premises to CloudSimple using ExpressRoute](on-premises-connection.md)
* [Configurare un gateway VPN da sito a sito](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

Le porte del firewall devono essere aperte su questa connessione affinché l'inoltro DNS funzioni.  Le porte utilizzate sono la porta TCP 53 o la porta UDP 53.

> [!NOTE]
> Se si utilizza una VPN da sito a sito, è necessario aggiungere la subnet del server DNS locale come parte dei prefissi locali.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Richiedere l'inoltro DNS dal cloud privato all'ambiente locale

Per abilitare l'inoltro DNS dal cloud privato all'ambiente locale, inviare una richiesta di [supporto,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fornendo le informazioni seguenti.

* Tipo di problema: **Tecnico**
* Sottoscrizione: **sottoscrizione in cui viene distribuito il servizio CloudSimpleSubscription: Subscription where CloudSimple service is deployed**
* Servizio: **VMware Solution di CloudSimple**
* Tipo di problema: **Advisory o Come faccio a...**
* Sottotipo di problema: **Hai bisogno di aiuto con NW**
* Specificare il nome di dominio del dominio locale nel riquadro dei dettagli.
* Fornire l'elenco dei server DNS locali a cui verrà inoltrata la ricerca dal cloud privato nel riquadro dei dettagli.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sulla configurazione del firewall localeLearn more about on-premises firewall configuration](on-premises-firewall-configuration.md)
* [Configurazione del server DNS locale](on-premises-dns-setup.md)

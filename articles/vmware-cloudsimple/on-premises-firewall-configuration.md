---
title: Accedi alla soluzione Azure VMware di CloudSimple da locale
titleSuffix: Azure VMware Solution by CloudSimple
description: Accesso alla soluzione Azure VMware da CloudSimple dalla rete locale tramite un firewall
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df4c51953c6f50e30ba61b993cdb35856fcb8e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082962"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Accesso all'ambiente e alle applicazioni CloudSimple Private Cloud da locale

È possibile configurare una connessione dalla rete locale a CloudSimple usando Azure ExpressRoute o la VPN da sito a sito.  Accedi al tuo CloudSimple Private Cloud vCenter e a tutti i carichi di lavoro eseguiti sul cloud privato utilizzando la connessione.  È possibile controllare quali porte vengono aperte sulla connessione utilizzando un firewall nella rete locale.  In questo articolo vengono illustrati alcuni dei requisiti tipici delle porte delle applicazioni.  Per tutte le altre applicazioni, fare riferimento alla documentazione dell'applicazione per i requisiti delle porte.

## <a name="ports-required-for-accessing-vcenter"></a>Porte necessarie per accedere a vCenter

Per accedere al vCenter del cloud privato e al gestore NSX-T, le porte definite nella tabella seguente devono essere aperte nel firewall locale.  

| Porta       | Source (Sorgente)                           | Destination                      | Scopo                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | server DNS locali          | Server DNS cloud privato        | Obbligatorio per l'inoltro della ricerca DNS di *az.cloudsimple.io* ai server DNS Private Cloud dalla rete locale.       |
| 53 (UDP)   | Server DNS cloud privato        | server DNS locali          | Necessario per inoltrare il DNS per la ricerca dei nomi di dominio locali da Private Cloud vCenter ai server DNS locali. |
| 80 (TCP)   | Rete locale              | Rete di gestione del cloud privato | Obbligatorio per reindirizzare l'URL di vCenter da *http* a *https*.                                                           |
| 443 (TCP)  | Rete locale              | Rete di gestione del cloud privato | Necessario per l'accesso a vCenter e NSX-T manager dalla rete locale.                                             |
| 8000 (TCP) | Rete locale              | Rete di gestione del cloud privato | Obbligatorio per vMotion di macchine virtuali da locale a cloud privato.                                            |
| 8000 (TCP) | Rete di gestione del cloud privato | Rete locale              | Obbligatorio per vMotion di macchine virtuali dal cloud privato all'ambiente locale.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Porte necessarie per l'utilizzo di Active Directory locale come origine di identità

Per configurare Active Directory locale come origine di identità in Private Cloud vCenter, è necessario aprire le porte definite nella tabella.  Per i passaggi di configurazione, vedere Usare Azure AD come provider di [identità per vCenter nel cloud CloudSimple Private Cloud.See Use Azure AD as an identity provider for vCenter on CloudSimple Private Cloud](https://docs.azure.cloudsimple.com/azure-ad/) for configuration steps.

| Porta         | Source (Sorgente)                           | Destination                                         | Scopo                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Server DNS cloud privato        | server DNS locali                             | Necessario per l'inoltro DNS di ricerca dei nomi di dominio active directory locali da Private Cloud vCenter ai server DNS locali.          |
| 389 (TCP/UDP) | Rete di gestione del cloud privato | Controller di dominio Active Directory locali     | Necessario per la comunicazione LDAP dal server private cloud vCenter ai controller di dominio Active Directory per l'autenticazione utente.                |
| 636 (TCP)     | Rete di gestione del cloud privato | Controller di dominio Active Directory locali     | Necessario per la comunicazione LDAP sicura (LDAPS) dal server vCenter private cloud ai controller di dominio Active Directory per l'autenticazione utente. |
| 3268 (TCP)    | Rete di gestione del cloud privato | Server di catalogo globale active directory locali | Necessario per la comunicazione LDAP in una distribuzione di controller multidominio.                                                                        |
| 3269 (TCP)    | Rete di gestione del cloud privato | Server di catalogo globale active directory locali | Necessario per la comunicazione LDAPS in una distribuzione di controller multidominio.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Porte comuni necessarie per l'accesso alle macchine virtuali del carico di lavoroCommon ports required for accessing workload virtual machines

Le macchine virtuali di accesso al carico di lavoro in esecuzione nel cloud privato richiedono l'apertura delle porte nel firewall locale.  La tabella seguente mostra alcune delle porte comuni necessarie e il loro scopo.  Per i requisiti delle porte specifiche dell'applicazione, fare riferimento alla documentazione dell'applicazione.

| Porta         | Source (Sorgente)                         | Destination                          | Scopo                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Rete locale            | Rete di carico di lavoro del cloud privato       | Accesso sicuro alla shell alle macchine virtuali Linux in esecuzione su Cloud privato.              |
| 3389 (TCP)    | Rete locale            | Rete di carico di lavoro del cloud privato       | Macchine virtuali da desktop remoto a Windows in esecuzione su Private Cloud.Remote desktop to windows virtual machines running on Private Cloud.                 |
| 80 (TCP)      | Rete locale            | Rete di carico di lavoro del cloud privato       | Accedere a qualsiasi server Web distribuito in macchine virtuali in esecuzione nel cloud privato.        |
| 443 (TCP)     | Rete locale            | Rete di carico di lavoro del cloud privato       | Accedi a tutti i server Web protetti distribuiti nelle macchine virtuali in esecuzione nel cloud privato. |
| 389 (TCP/UDP) | Rete di carico di lavoro del cloud privato | Rete di Active Directory locale | Aggiungere le macchine virtuali del carico di lavoro di Windows al dominio di Active Directory locale.       |
| 53 (UDP)      | Rete di carico di lavoro del cloud privato | Rete locale                  | Accesso al servizio DNS per le macchine virtuali del carico di lavoro nei server DNS locali.         |

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire VLAN e subnet](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Connettersi alla rete locale usando Azure ExpressRouteConnect to on-premises network using Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurare la VPN da sito a sito dall'ambiente locale](https://docs.azure.cloudsimple.com/vpn-gateway/)

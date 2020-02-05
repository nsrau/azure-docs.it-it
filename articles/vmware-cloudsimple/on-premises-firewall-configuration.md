---
title: Accesso ad Azure VMware Solutions (AVS) da locale
description: Accesso alle soluzioni VMware di Azure (AVS) dalla rete locale tramite un firewall
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 09b25dbdc8fc13c40ccd89b2cfd78611cedaac9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024467"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>Accesso all'ambiente e alle applicazioni del cloud privato AVS da locale

È possibile configurare una connessione dalla rete locale a AVS usando Azure ExpressRoute o VPN da sito a sito. Accedere a AVS private cloud vCenter e a tutti i carichi di lavoro eseguiti sul cloud privato AVS usando la connessione. È possibile controllare quali porte vengono aperte sulla connessione usando un firewall nella rete locale. Questo articolo illustra alcuni dei requisiti tipici delle porte delle applicazioni. Per tutte le altre applicazioni, fare riferimento alla documentazione dell'applicazione per i requisiti delle porte.

## <a name="ports-required-for-accessing-vcenter"></a>Porte necessarie per l'accesso a vCenter

Per accedere a AVS private cloud vCenter e NSX-T Manager, è necessario aprire le porte definite nella tabella seguente nel firewall locale. 

| Porta       | Origine                           | Destinazione                      | Finalità                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Server DNS locali          | Server DNS del cloud privato AVS        | Obbligatorio per l'invio della ricerca DNS di *AZ. AVS.io* a AVS server DNS del cloud privato dalla rete locale.     |
| 53 (UDP)   | Server DNS del cloud privato AVS        | Server DNS locali          | Obbligatorio per l'invio della ricerca DNS dei nomi di dominio locali da AVS private cloud vCenter ai server DNS locali. |
| 80 (TCP)   | Rete locale              | Rete di gestione del cloud privato AVS | Obbligatorio per il reindirizzamento dell'URL vCenter da *http* a *https*.                                                         |
| 443 (TCP)  | Rete locale              | Rete di gestione del cloud privato AVS | Obbligatorio per accedere alla gestione vCenter e NSX-T dalla rete locale.                                           |
| 8000 (TCP) | Rete locale              | Rete di gestione del cloud privato AVS | Obbligatorio per vMotion di macchine virtuali dall'ambiente locale al cloud privato AVS.                                          |
| 8000 (TCP) | Rete di gestione del cloud privato AVS | Rete locale              | Obbligatorio per vMotion di macchine virtuali da AVS private cloud a locale.                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Porte necessarie per l'uso di Active Directory locale come origine di identità

Per configurare Active Directory locale come origine di identità in AVS private cloud vCenter, è necessario aprire le porte definite nella tabella. Per la procedura di configurazione [, vedere usare Azure ad come provider di identità per vCenter in AVS AVS private cloud](https://docs.azure.cloudsimple.com/azure-ad/) .

| Porta         | Origine                           | Destinazione                                         | Finalità                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Server DNS del cloud privato AVS        | Server DNS locali                             | Obbligatorio per l'invio della ricerca DNS dei nomi di dominio di Active Directory locali da AVS private cloud vCenter ai server DNS locali.        |
| 389 (TCP/UDP) | Rete di gestione del cloud privato AVS | Controller di dominio di Active Directory locali     | Obbligatorio per la comunicazione LDAP da AVS private cloud vCenter Server a controller di dominio Active Directory per l'autenticazione utente.              |
| 636 (TCP)     | Rete di gestione del cloud privato AVS | Controller di dominio di Active Directory locali     | Obbligatorio per la comunicazione LDAP sicura (LDAPs) da AVS private cloud vCenter Server a controller di dominio Active Directory per l'autenticazione utente. |
| 3268 (TCP)    | Rete di gestione del cloud privato AVS | Server di catalogo globale di Active Directory locale | Obbligatorio per la comunicazione LDAP in una distribuzione di controller multidominio.                                                                      |
| 3269 (TCP)    | Rete di gestione del cloud privato AVS | Server di catalogo globale di Active Directory locale | Obbligatorio per la comunicazione LDAPs in una distribuzione di controller multidominio.                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Porte comuni necessarie per accedere alle macchine virtuali del carico di lavoro

Per accedere alle macchine virtuali del carico di lavoro in esecuzione in AVS private cloud è necessario che le porte siano aperte nel firewall locale. La tabella seguente mostra alcune delle porte comuni necessarie e il relativo scopo. Per tutti i requisiti di porta specifici dell'applicazione, fare riferimento alla documentazione dell'applicazione.

| Porta         | Origine                         | Destinazione                          | Finalità                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Rete locale            | Rete del carico di lavoro del cloud privato AVS       | Proteggere l'accesso alla Shell alle macchine virtuali Linux in esecuzione su AVS private cloud.            |
| 3389 (TCP)    | Rete locale            | Rete del carico di lavoro del cloud privato AVS       | Desktop remoto per le macchine virtuali Windows in esecuzione in AVS private cloud.               |
| 80 (TCP)      | Rete locale            | Rete del carico di lavoro del cloud privato AVS       | Accesso a tutti i server Web distribuiti in macchine virtuali in esecuzione in AVS private cloud.      |
| 443 (TCP)     | Rete locale            | Rete del carico di lavoro del cloud privato AVS       | Accesso a tutti i server Web protetti distribuiti in macchine virtuali in esecuzione in AVS private cloud. |
| 389 (TCP/UDP) | Rete del carico di lavoro del cloud privato AVS | Rete di Active Directory locale | Aggiungere le macchine virtuali del carico di lavoro Windows al dominio di Active Directory locale.     |
| 53 (UDP)      | Rete del carico di lavoro del cloud privato AVS | Rete locale                  | Accesso al servizio DNS per le macchine virtuali del carico di lavoro nei server DNS locali.       |

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire VLAN e subnet](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Connettersi alla rete locale usando Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurare una VPN da sito a sito da locale](https://docs.azure.cloudsimple.com/vpn-gateway/)

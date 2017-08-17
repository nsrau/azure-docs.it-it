---
title: Panoramica del servizio DNS di Azure | Documenti Microsoft
description: Panoramica del servizio di hosting DNS in Microsoft Azure. Ospitare il dominio in Microsoft Azure.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3705457e4c90f8869496f7f5177531bd128d1057
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="azure-dns-overview"></a>Panoramica di DNS di Azure

Il nome DNS (Domain Name System) è responsabile della conversione (o risoluzione) del nome di un sito Web o del servizio nel relativo indirizzo IP. DNS di Azure è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.

![Panoramica del servizio DNS](./media/dns-overview/scenario.png)

## <a name="features"></a>Funzionalità

* **Affidabilità e prestazioni**: i domini DNS nel servizio DNS di Azure sono ospitati nella rete globale di Azure dei server dei nomi DNS. Usiamo le reti Anycast, in modo che ogni query DNS riceva una risposta dal server DNS disponibile più vicino. Ciò consente di accelerare le prestazioni e ottenere la disponibilità elevata per il dominio.

* **Integrazione perfetta**: il servizio DNS di Azure può essere usato per gestire i record DNS per i servizi di Azure, nonché per garantire il servizio DNS alle risorse esterne. DNS di Azure è integrato nel portale di Azure e usa le stesse credenziali, i metodi di fatturazione e il contratto di supporto di altri servizi di Azure.

* **Sicurezza**: il servizio DNS di Azure si basa su Azure Resource Manager. Usufruisce quindi delle funzionalità di Resource Manager, come il controllo degli accessi in base al ruolo, i log di controllo e il blocco delle risorse. I domini e i record possono essere gestiti tramite il portale di Azure, i cmdlet di Azure PowerShell e l'interfaccia della riga di comando di Azure. Le applicazioni che richiedono la gestione automatica di DNS possono essere integrate con il servizio tramite l'API REST e gli SDK.

DNS di Azure attualmente non supporta l'acquisto dei nomi di dominio. Se si vogliono acquistare domini, è necessario ricorrere a un registrar di terze parti, che addebita in genere una tariffa annuale contenuta. I domini possono quindi essere ospitati in DNS di Azure per la gestione dei record DNS. Per informazioni dettagliate, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md) .

## <a name="pricing"></a>Prezzi

La fatturazione di DNS di Azure è basata sul numero di zone DNS ospitate in Azure e sul numero di query DNS. Per altre informazioni sui prezzi visitare [Prezzi di DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="faq"></a>domande frequenti

Per domande frequenti sul servizio DNS di Azure, vedere [Domande frequenti su DNS di Azure](dns-faq.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui record e le zone DNS visitare la pagina [Panoramica delle zone e dei record DNS](dns-zones-records.md).

Informazioni su come [creare una zona DNS](./dns-getstarted-create-dnszone-portal.md) in DNS di Azure.

Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.



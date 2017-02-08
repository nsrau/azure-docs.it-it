---
title: Prerequisiti per l&quot;adozione di ExpressRoute | Microsoft Docs
description: Questa pagina fornisce un elenco di requisiti da soddisfare prima di potere ordinare un circuito Azure ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: f872d25e-acfd-405d-9d1b-dcb9f323a2ff
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b7e47a469127d2a06aa1be07f46ef04a2c23d351


---
# <a name="expressroute-prerequisites--checklist"></a>Prerequisiti di ExpressRoute ed elenco di controllo
Per connettersi ai servizi cloud Microsoft usando ExpressRoute, sarà necessario verificare che siano stati soddisfatti i requisiti elencati nelle sezioni seguenti.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Account Azure
* Un account Microsoft Azure valido e attivo. Questo è necessario per configurare il circuito ExpressRoute. I circuiti ExpressRoute sono risorse all'interno di sottoscrizioni di Azure. Una sottoscrizione di Azure è un requisito, anche se la connettività è limitata a servizi cloud non Microsoft Azure, ad esempio servizi di Office 365 e CRM Online.
* Una sottoscrizione di Office 365 attiva (se si usano servizi di Office 365). Per altre informazioni, vedere la sezione [Requisiti specifici di Office 365](#office-365-specific-requirements) in questo articolo.

## <a name="connectivity-provider"></a>Provider di connettività
* È possibile collaborare con un [partner per la connettività ExpressRoute](expressroute-locations.md#partners) per connettersi a Microsoft Cloud. È possibile configurare una connessione tra la rete locale e Microsoft in [tre modi](expressroute-introduction.md#howtoconnect)diversi. 
* Se il provider non è un partner per la connettività ExpressRoute, è comunque possibile connettersi a Microsoft Cloud tramite un [provider Cloud Exchange](expressroute-locations.md#nonpartners).

## <a name="network-requirements"></a>Requisiti di rete
* **Connettività ridondante**: non è stato definito alcun requisito di ridondanza per la connettività fisica con il provider. Microsoft richiede la configurazione di sessioni BGP ridondanti tra i router di Microsoft e i router di peering, anche in presenza di [una sola connessione fisica a un'infrastruttura Cloud Exchange](expressroute-faqs.md#onep2plink). 
* **Routing**: a seconda di come ci si connette a Microsoft Cloud, è necessario configurare e gestire le sessioni BGP per i [domini di routing](expressroute-circuit-peerings.md)o chiedere al provider di farlo. Alcuni provider di connettività Ethernet o provider Cloud Exchange possono offrire la gestione BGP come servizio a valore aggiunto.
* **NAT**: Microsoft accetta solo indirizzi IP pubblici tramite peer Microsoft. Se si usano indirizzi IP privati nella rete locale, è necessario convertirli in indirizzi IP pubblici (o chiedere al provider di farlo) [tramite il processo NAT](expressroute-nat.md).
* **QoS**: Skype for Business include diversi servizi, ad esempio voce, video o testo, che richiedono una gestione QoS differenziata. È necessario seguire i [requisiti QoS](expressroute-qos.md).
* **Sicurezza di rete**: quando ci si connette a Microsoft Cloud tramite ExpressRoute, è consigliabile tenere conto della [sicurezza di rete](../best-practices-network-security.md) .

## <a name="office-365"></a>Office 365
Se si prevede di abilitare Office 365 in ExpressRoute, esaminare i documenti seguenti per altre informazioni sui requisiti di Office 365.

* [Panoramica di ExpressRoute per Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Routing con ExpressRoute per Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Pianificazione della rete e ottimizzazione delle prestazioni per Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Calcolatori e strumenti per la larghezza di banda di rete](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integrazione di Office 365 con ambienti locali](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## <a name="crm-online"></a>CRM Online
Se si prevede di abilitare CRM Online in ExpressRoute, esaminare i documenti seguenti per altre informazioni su CRM Online.

* [CRM Online URLs](https://support.microsoft.com/kb/2655102) (URL di CRM Online) e [IP address ranges](https://support.microsoft.com/kb/2728473) (Intervalli di indirizzi IP)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
* Individuare un partner per la connettività ad ExpressRoute. Vedere [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md).
* Fare riferimento ai requisiti per [routing](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurare la connessione ExpressRoute.
  * [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configurare il routing](expressroute-howto-routing-classic.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)




<!--HONumber=Dec16_HO2-->



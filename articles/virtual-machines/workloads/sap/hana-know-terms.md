---
title: Conoscere i termini di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Conoscere i termini di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2564e0d34383f7c1daff5b02a871778fb90546cc
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868999"
---
# <a name="know-the-terms"></a>Conoscere i termini

Nella guida all'architettura e alla distribuzione vengono comunemente usati alcuni termini. Tenere presenti i termini seguenti e i relativi significati.

- **IaaS**: Infrastruttura distribuita come servizio.
- **PaaS**: Piattaforma distribuita come servizio.
- **SaaS**: Software come servizio.
- **Componente SAP**: Una singola applicazione SAP, ad esempio ERP Central Component (ECC), Business Warehouse (BW), Solution Manager o Enterprise Portal (EP). I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
- **Ambiente SAP**: Uno o più componenti SAP raggruppati in modo logico per eseguire una funzione aziendale, ad esempio sviluppo, controllo di qualità, formazione, ripristino di emergenza o produzione.
- **Panorama**applicativo SAP: Si riferisce all'intero asset SAP nel panorama IT. Il panorama applicativo SAP include tutti gli ambienti di produzione e non produzione.
- **Sistema SAP**: Combinazione del livello DBMS e del livello dell'applicazione, ad esempio, un sistema di sviluppo SAP ERP, un sistema di test SAP BW e un sistema di produzione SAP CRM. Le distribuzioni di Azure non supportano la divisione di questi due livelli tra l'istanza locale e Azure. Un sistema SAP deve essere distribuito o in locale o in Azure. I diversi sistemi di un panorama applicativo SAP possono essere distribuiti in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale. Nel caso di SAP HANA in Azure (istanze Large), è previsto che il livello applicazione SAP dei sistemi SAP sia ospitato in VM e l'istanza di SAP HANA correlata sia ospitata in un'unità nel modulo di SAP HANA in Azure (istanze Large).
- **Timbro per istanze large**: Uno stack di infrastruttura hardware SAP HANA certificato TDI e dedicato per l'esecuzione di SAP HANA istanze in Azure.
- **SAP HANA in Azure (istanze large):** Nome ufficiale dell'offerta in Azure per l'esecuzione di istanze di HANA in in SAP HANA hardware certificato con TDI distribuito in moduli per istanze large in diverse aree di Azure. Il termine *istanze Large di HANA* correlato è la forma abbreviata di *SAP HANA in Azure (istanze Large)* prevalentemente usata in questa guida tecnica alla distribuzione.
- **Cross-premise**: Descrive uno scenario in cui le VM vengono distribuite in una sottoscrizione di Azure con connettività da sito a sito, multisito o Azure ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure, questi tipi di distribuzioni vengono definiti anche scenari cross-premise. La connessione consente di estendere i domini locali, l'istanza locale di Azure Active Directory/OpenLDAP e il DNS locale in Azure. Il panorama applicativo locale viene esteso agli asset di Azure delle sottoscrizioni di Azure. Con questa estensione, le VM possono far parte del dominio locale. 

   Gli utenti del dominio locale possono accedere ai server ed eseguire servizi in queste VM, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra le VM distribuite in locale e quelle distribuite in Azure sono possibili. Questo è lo scenario tipico in cui viene distribuita la maggior parte degli asset SAP. Per altre informazioni, vedere [gateway VPN di Azure](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [creare una rete virtuale con una connessione da sito a sito usando il portale di Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant**: Un cliente distribuito in un timbro di istanze large di HANA viene isolato in un *tenant.* Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e calcolo assegnate ai diversi tenant non possono vedersi o comunicare tra loro a livello del modulo per istanze Large di HANA. Un cliente può scegliere di avere distribuzioni in tenant diversi. Neppure in questo caso i tenant possono comunicare tra di essi a livello di stamp di istanze Large di HANA.
- **Categoria SKU**: Per le istanze large di HANA sono disponibili le due categorie di SKU seguenti:
    - **Classe di tipo I**: S72, S72m, S96, S144, S144m, S192, S192m e S192xm
    - **Classe di tipo II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm e S960m
- **Timbro**: Definisce la dimensione di distribuzione interna Microsoft delle istanze large di HANA. Prima che le unità di istanze large di HANA possano essere distribuite, un timbro di istanze large di HANA costituito da rack di calcolo, rete e archiviazione deve essere distribuito in una posizione del Data Center. Una distribuzione di questo tipo è detta timbro di istanze large di HANA o dalla revisione 4 (vedere di seguito) in cui si usa l'alternativa del termine di una **riga di istanze large**
- **Revisione**: Esistono due diverse revisioni di timbri per gli indicatori di istanze large di HANA. Questi sono diversi in architettura e prossimità degli host di macchine virtuali di Azure
    - "Revisione 3" (Rev 3): è la progettazione originale che è stata distribuita dalla metà dell'anno 2016
    - "Revisione 4" (Rev 4): è una nuova progettazione che può fornire una prossimità più vicina agli host di macchine virtuali di Azure e con una latenza di rete inferiore tra le macchine virtuali di Azure e le unità di istanze large di HANA 

In merito alla distribuzione di un carico di lavoro SAP nel cloud è disponibile un'ampia gamma di risorse aggiuntive. Se si pianifica una distribuzione di SAP HANA in Azure è necessario essere utenti esperti e conoscere i principi dell'infrastruttura IaaS di Azure e la distribuzione dei carichi di lavoro SAP in tale infrastruttura. Prima di continuare, vedere [Uso di soluzioni SAP nelle macchine virtuali di Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per altre informazioni. 

**Passaggi successivi**
- Vedere [Certificazione HLI](hana-certification.md)
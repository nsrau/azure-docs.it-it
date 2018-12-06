---
title: Conoscere i termini di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Conoscere i termini di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 902c4c94cc7366af342816e9b4776af603711c8b
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275440"
---
# <a name="know-the-terms"></a>Conoscere i termini

Nella guida all'architettura e alla distribuzione vengono comunemente usati alcuni termini. Tenere presenti i termini seguenti e i relativi significati.

- **IaaS**: (Infrastructure as a Service) infrastruttura distribuita come servizio.
- **PaaS**: (Platform as a Service) piattaforma distribuita come servizio.
- **SaaS**: (Software as a Service) software come servizio.
- **Componente SAP**: singola applicazione SAP, ad esempio ECC (ERP Central Component), BW (Business Warehouse), Solution Manager o EP (Enterprise Portal). I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
- **Ambiente SAP**: raggruppamento logico di uno o più componenti SAP per l'esecuzione di una funzione aziendale, ad esempio sviluppo, controllo della qualità, formazione, ripristino di emergenza o produzione.
- **Panorama applicativo SAP**: fa riferimento a tutti gli asset SAP nel panorama applicativo IT del cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non produzione.
- **Sistema SAP**: combinazione del livello DBMS e del livello applicazione, ad esempio di un sistema di sviluppo SAP ERP, un sistema di test SAP BW e un sistema di produzione SAP CRM. Le distribuzioni di Azure non supportano la divisione di questi due livelli tra l'istanza locale e Azure. Un sistema SAP deve essere distribuito o in locale o in Azure. I diversi sistemi di un panorama applicativo SAP possono essere distribuiti in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale. Nel caso di SAP HANA in Azure (istanze Large), è previsto che il livello applicazione SAP dei sistemi SAP sia ospitato in VM e l'istanza di SAP HANA correlata sia ospitata in un'unità nel modulo di SAP HANA in Azure (istanze Large).
- **Modulo per istanze Large**: stack dell'infrastruttura hardware con certificazione SAP HANA TDI, dedicato all'esecuzione di istanze di SAP HANA in Azure.
- **SAP HANA in Azure (istanze Large)**: nome ufficiale dell'offerta in Azure per l'esecuzione di istanze di HANA in hardware con certificazione SAP HANA TDI distribuito in moduli per istanze Large in diverse aree di Azure. Il termine *istanze Large di HANA* correlato è la forma abbreviata di *SAP HANA in Azure (istanze Large)* prevalentemente usata in questa guida tecnica alla distribuzione.
- **Cross-premise**: indica uno scenario in cui le VM vengono distribuite a una sottoscrizione di Azure con connettività da sito a sito, multisito o Azure ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure, questi tipi di distribuzioni vengono definiti anche scenari cross-premise. La connessione consente di estendere i domini locali, l'istanza locale di Azure Active Directory/OpenLDAP e il DNS locale in Azure. Il panorama applicativo locale viene esteso agli asset di Azure delle sottoscrizioni di Azure. Con questa estensione, le VM possono far parte del dominio locale. 

   Gli utenti del dominio locale possono accedere ai server ed eseguire servizi in queste VM, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra le VM distribuite in locale e quelle distribuite in Azure sono possibili. Questo è lo scenario tipico in cui viene distribuita la maggior parte degli asset SAP. Per altre informazioni, vedere [Pianificazione e progettazione per il gateway VPN](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Creare una connessione da sito a sito nel portale di Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant**: un cliente distribuito nel modulo per istanze Large di HANA viene isolato in un *tenant*. Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e calcolo assegnate ai diversi tenant non possono vedersi o comunicare tra loro a livello del modulo per istanze Large di HANA. Un cliente può scegliere di avere distribuzioni in tenant diversi. Neppure in questo caso i tenant possono comunicare tra di essi a livello di stamp di istanze Large di HANA.
- **Categoria SKU**: per le istanze Large di HANA sono disponibili le due categorie di SKU seguenti:
    - **Classe di tipo I**: S72, S72m, S96, S144, S144m, S192, S192m e S192xm
    - **Classe di tipo II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm e S960m


In merito alla distribuzione di un carico di lavoro SAP nel cloud è disponibile un'ampia gamma di risorse aggiuntive. Se si pianifica una distribuzione di SAP HANA in Azure è necessario essere utenti esperti e conoscere i principi dell'infrastruttura IaaS di Azure e la distribuzione dei carichi di lavoro SAP in tale infrastruttura. Prima di continuare, vedere [Uso di soluzioni SAP nelle macchine virtuali di Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per altre informazioni. 

**Passaggi successivi**
- Vedere [Certificazione HLI](hana-certification.md)
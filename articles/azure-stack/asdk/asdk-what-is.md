---
title: Introduzione a Azure Stack Development Kit (ASDK) | Documenti Microsoft
description: "Vengono descritte le funzionalità di ASDK e casi d'uso comuni per la valutazione di Microsoft Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 5a268a29c7a767084049bf56270aa8bc9d2ccc3f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="what-is-the-azure-stack-development-kit"></a>Che cos'è il Kit di sviluppo dello Stack di Azure?
[Microsoft Azure Stack integrati sistemi](.\.\azure-stack-poc.md) intervallo dimensioni da 12 a 4 nodi e congiuntamente supportati da un partner di hardware e Microsoft. Usare sistemi Azure Stack integrato per abilitare i nuovi scenari per i carichi di lavoro di produzione. Se si ha un operatore di Stack di Azure che gestisce l'infrastruttura di sistemi integrati e offre servizi, vedere il nostro [documentazione operatore](https://docs.microsoft.com/azure/azure-stack).

Il Kit di sviluppo dello Stack di Azure (ASDK) è una distribuzione a nodo singolo dello Stack di Azure che è possibile scaricare e usare **gratuitamente**. Tutti i componenti ASDK installati nelle macchine virtuali in esecuzione in un singolo server computer host che deve soddisfare o superare i [requisiti hardware minimi](asdk-deploy-considerations.md#hardware). Il ASDK è progettato per fornire un ambiente in cui è possibile valutare dello Stack di Azure e sviluppare le applicazioni moderne tramite le API e strumenti coerenti con Azure in un *non di produzione* ambiente. 

> [!IMPORTANT]
> Il ASDK non può essere utilizzato o supportato in un ambiente di produzione.

Poiché tutti i componenti ASDK vengono distribuiti in un computer host di kit sviluppo singolo, sono disponibili risorse fisiche limitate. Con le distribuzioni ASDK, le macchine virtuali di Azure Stack infrastruttura sia il tenant di macchine virtuali coesistono nello stesso computer server. Questa configurazione non è destinata la valutazione di scalabilità o prestazioni.

Il ASDK è progettato per offrire un'esperienza di cloud ibrido coerenti con Azure per:
- **Gli amministratori** (gli operatori di Azure Stack). Il ASDK è un'ottima risorsa per valutare e avere informazioni sui servizi disponibili Stack di Azure.
- **Gli sviluppatori**. Il ASDK utilizzabile per sviluppare applicazioni moderne in locale (ambienti di sviluppo/test) o ibrida. Ciò offre ripetibilità di esperienza di sviluppo prima o insieme, le distribuzioni di produzione Azure Stack. 

Guardare questo breve video per altre informazioni sul ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Differenze di Stack Azure ASDK e a più nodi
Distribuzioni a nodo singolo ASDK diversi da distribuzioni di più nodi Azure Stack alcune importanti differenze che è necessario essere consapevoli di.

|DESCRIZIONE|ASDK|A più nodi Azure Stack|
|-----|-----|-----|
|**Ridimensionare**|Tutti i componenti vengono installati in un computer server a nodo singolo.|Possono variare nella dimensione, da 12 a 4 nodi.|
|**Resilienza**|Configurazione a nodo singolo non fornisce la disponibilità elevata|[Ad alta disponibilità](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) funzionalità sono supportate.|
|**Rete**|Il ASDK utilizza una macchina virtuale denominata AzS BGPNAT01 per indirizzare tutto il traffico di rete ASDK. Non sono previsti requisiti aggiuntivi commutatore.|La macchina virtuale AzS BGPNAT01 non esiste nelle distribuzioni a più nodi. Più complessi [rete infrastruttura di routing](.\.\azure-stack-network.md#network-infrastructure) necessario inclusi Top-Of-Rack (TOR), Baseboard Management Controller (BMC) e commutatori bordo (Data Center di rete).|
|**Processo di patch e aggiornamento**|Per spostare in una nuova versione di ASDK, è necessario ridistribuire il ASDK nel computer host kit di sviluppo.|[Aggiornamenti e patch](.\.\azure-stack-updates.md) processo utilizzato per aggiornare la versione installata di Stack di Azure.|
|**Supporto**|Forum di MSDN Azure Stack. Il supporto tecnico Microsoft (CSS) supporta *non* disponibili per ambienti non di produzione.|[Forum di MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) e supporto completo CSS.|
| | |

## <a name="learn-about-available-services"></a>Informazioni sui servizi disponibili
Un operatore di Stack di Azure, è necessario sapere quali servizi è possibile rendere disponibili agli utenti. Stack di Azure supporta un sottoinsieme di servizi di Azure e l'elenco di servizi supportati continuerà a evolve nel tempo.

### <a name="foundational-services"></a>Servizi fondamentali
Per impostazione predefinita, Azure Stack include i seguenti "fondamentali servizi" quando si distribuisce il ASDK:
- Calcolo
- Archiviazione
- Rete
- Insieme di credenziali di chiave

Con questi servizi fondamentali, è possibile offrire Infrastructure-as-a-Service (IaaS) per gli utenti con la configurazione minima.

### <a name="additional-services"></a>Servizi aggiuntivi
Attualmente, sono supportati i servizi Platform-as-a-Service (PaaS) aggiuntivi seguenti:
- Servizio app
- Funzioni di Azure
- Database MySQL e SQL Server

> [!NOTE]
> Questi servizi richiedono un'ulteriore configurazione prima di renderli disponibili agli utenti e non sono disponibili per impostazione predefinita quando si installa il ASDK.

## <a name="service-roadmap"></a>Guida di orientamento per servizio
Stack Azure continuerà aggiungere il supporto per i servizi di Azure aggiuntivi. Per informazioni sulle novità successivo con lo Stack di Azure, vedere la [Guida di orientamento per Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Passaggi successivi
Per iniziare la valutazione dello Stack di Azure, è necessario preparare l'host di kit sviluppo computer server e quindi [installare la ASDK](asdk-deploy.md). Successivamente, è possibile accedere ai portali amministratore e utente per iniziare a usare Azure Stack.
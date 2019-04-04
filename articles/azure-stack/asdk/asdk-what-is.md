---
title: Introduzione a Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Descrive le novità di ASDK e casi d'uso comuni per la valutazione di Microsoft Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 54eb2ff43a5f36999294b8d0c580bc425ab65b28
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629079"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Che cos'è Azure Stack Development Kit?
[I sistemi integrati di Microsoft Azure Stack](../azure-stack-poc.md) dimensioni variano da 4 a 16 nodi e congiuntamente supportati da un partner di hardware e Microsoft. Usare i sistemi integrati Azure Stack per rendere possibili nuovi scenari per i carichi di lavoro di produzione. Se si ha un operatore di Azure Stack che gestisce l'infrastruttura di sistemi integrati e offre servizi, vedere la [documentazione dell'operatore](https://docs.microsoft.com/azure/azure-stack).

Azure Stack Development Kit (ASDK) è una distribuzione a nodo singolo di Azure Stack che è possibile scaricare e usare **gratuitamente**. Tutti i componenti ASDK sono installati in macchine virtuali in esecuzione in un singolo server computer host deve soddisfare o superare il [requisiti hardware minimi](asdk-deploy-considerations.md#hardware). Il ASDK è progettato per fornire un ambiente in cui è possibile valutare Azure Stack e sviluppare applicazioni moderne usando le API e strumenti coerenti con Azure in un *non di produzione* ambiente. 

> [!IMPORTANT]
> Il ASDK non può essere usato o supportato in un ambiente di produzione.

Poiché tutti i componenti ASDK vengono distribuiti a un computer host kit di sviluppo singolo, sono disponibili risorse fisiche limitate. Con le distribuzioni ASDK, le macchine virtuali dell'infrastruttura di Azure Stack sia il tenant di macchine virtuali di coesistono sullo stesso computer server. Questa configurazione non è per la valutazione di scalabilità o prestazioni.

Il ASDK è progettato per offrire un'esperienza di cloud ibrido coerenti con Azure per:
- **Gli amministratori** (operatori di Azure Stack). Il ASDK è un'ottima risorsa per valutare e ottenere informazioni sui servizi di Azure Stack disponibili.
- **Gli sviluppatori**. Il ASDK utilizzabile per lo sviluppo di applicazioni moderne in locale (ambienti di sviluppo/test) o ibride. Questo offre la ripetibilità dell'esperienza di sviluppo precedenti a, o insieme, le distribuzioni di produzione di Azure Stack. 

Questo breve video per altre informazioni sul ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Differenze di Azure Stack ASDK e a nodi multipli
Distribuzioni a nodo singolo ASDK differiscono dalle distribuzioni di Azure Stack con più nodi alcuni aspetti importanti che è necessario essere consapevoli di.

|DESCRIZIONE|ASDK|A più nodi di Azure Stack|
|-----|-----|-----|
|**Ridimensionare**|Tutti i componenti vengono installati in un computer server a nodo singolo.|Può variare nella dimensione, da 4 a 16 nodi.|
|**Resilienza**|Configurazione di nodo singolo non fornisce la disponibilità elevata|[Disponibilità elevata](../azure-stack-overview.md#providing-high-availability) funzionalità sono supportate.|
|**Rete**|L'host ASDK instrada tutto il traffico di rete ASDK. Non sono previsti requisiti aggiuntivi switch.|Più complessa [infrastruttura di routing di rete](../azure-stack-network.md#network-infrastructure) nelle distribuzioni a più nodi è necessario come Top-Of-Rack (TOR), Baseboard Management Controller (BMC) e opzioni del bordo (rete datacenter).|
|**Processo di patch e aggiornamento**|Per spostare in una nuova versione del ASDK, è necessario ridistribuire il ASDK nel computer host kit di sviluppo.|[Applicare patch e aggiornamenti](../azure-stack-updates.md) processo usato per aggiornare la versione installata di Azure Stack.|
|**Supporto**|Forum di MSDN Azure Stack. Il supporto tecnico Microsoft e supporto (CSS) è *non* disponibile per gli ambienti non di produzione.|[Forum di MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) e supporto CSS completo.|
| | |

## <a name="learn-about-available-services"></a>Informazioni su servizi disponibili
Un operatore di Stack di Azure, è necessario sapere quali servizi è possibile rendere disponibili agli utenti. Azure Stack supporta un subset dei servizi di Azure e l'elenco di servizi supportati continuerà a evolversi nel tempo.

### <a name="foundational-services"></a>Servizi fondamentali
Per impostazione predefinita, Azure Stack include i servizi seguenti: "base" quando si distribuisce il ASDK:
- Calcolo
- Archiviazione
- Rete
- Key Vault

Con questi servizi fondamentali, è possibile offrire agli utenti con la configurazione minima Infrastructure-as-a-Service (IaaS).

### <a name="additional-services"></a>Servizi aggiuntivi
Attualmente sono supportati i servizi Platform-as-a-Service (PaaS) aggiuntivi seguenti:
- Servizio app
- Funzioni di Azure
- Database SQL e MySQL

> [!NOTE]
> Questi servizi richiedono un'ulteriore configurazione prima di renderli disponibili agli utenti e non sono disponibili per impostazione predefinita quando si installa il ASDK.

## <a name="service-roadmap"></a>Guida di orientamento per servizio
Azure Stack continuerà aggiungere il supporto per i servizi Azure aggiuntivi. Per altre informazioni sulle novità in arrivo con Azure Stack, vedere la [roadmap per Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Passaggi successivi
Per iniziare la valutazione di Azure Stack, è necessario innanzitutto [scaricare l'ultima ASDK](asdk-download.md) e preparare il computer host ASDK. Dopo aver preparato l'host del kit di sviluppo, è possibile installare il ASDK e accedere ai portali di amministratore e utente per iniziare a usare Azure Stack.
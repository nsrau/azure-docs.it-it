---
title: Confrontare Azure globale e Azure Stack | Microsoft Docs
description: Informazioni su come Microsoft fornisce la famiglia di Azure Stack dei servizi in un ecosistema di Azure e Azure
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 5e871d467fec476f1dac77fb879d180ea2322c80
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650089"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>Differenze tra Azure, Azure Stack e Azure Stack uomo globale

Microsoft fornisce la famiglia di Azure Stack dei servizi in un ecosistema di Azure e Azure. Usare la stesso modello applicativo, i portali self-service e API con Azure Resource Manager per offrire funzionalità basate sul cloud, se l'azienda Usa Azure globale o a risorse locali.

Questo articolo descrive le funzionalità globali di Azure, Azure Stack e Azure Stack uomo e fornisce indicazioni di uno scenario comune che consentono di adottare la scelta migliore per la distribuzione di servizi Microsoft basati su cloud per l'organizzazione.

![Panoramica di ecosistema di Azure](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Azure globale

Microsoft Azure è un set in continua espansione di servizi cloud che aiuta le organizzazioni ad affrontare le sfide professionali. Ti permette di creare, gestire e distribuire applicazioni su una rete globale di dimensioni elevate usando i tuoi strumenti e framework preferiti.

Azure globale offre più di 100 servizi disponibili in 54 aree in tutto il mondo. Per l'elenco più aggiornato dei servizi globali di Azure, vedere la [ *prodotti disponibili in base all'area*](https://azure.microsoft.com/regions/services). I servizi disponibili in Azure sono disponibili tramite l'anteprima o elencate per categoria, anche se sono disponibili a livello generale.

Per altre informazioni sui servizi globali di Azure, vedere [iniziare a usare Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack"></a>Azure Stack

Azure Stack è un'estensione di Azure che offre la flessibilità e l'innovazione del cloud computing al tuo ambiente locale. Distribuito in locale, Azure Stack è utilizzabile per fornire servizi coerenti con Azure che sia connessa a internet (e Azure) o in ambienti disconnessi senza connettività internet. Stack di Azure Usa le stesse tecnologie sottostanti come Azure globale, che include i componenti principali di Infrastructure-as-a-Service (IaaS), Software-as-a-Service (SaaS), e facoltativi funzionalità Platform-as-a-Service (PaaS), tra cui:

- Macchine virtuali di Azure per Windows e Linux
- Funzioni e App Web di azure
- Azure Key Vault
- Azure Resource Manager
- Azure Marketplace
- Contenitori
- L'Hub IoT e hub eventi
- Strumenti di amministrazione (piani, offerte, RBAC, ecc.)

Le funzionalità PaaS di Azure Stack sono facoltative, perché Azure Stack non è gestito da Microsoft, viene eseguita da parte dei clienti. Ciò significa che è possibile offrire qualsiasi servizio PaaS da utenti finali se si è pronti per astrarre l'infrastruttura sottostante e processi lontano dall'utente finale. Tuttavia, Azure Stack include diversi provider servizio PaaS facoltativo tra servizio App, database SQL e i database MySQL. Questi sono recapitati come provider di risorse, in modo che siano pronti multi-tenant, aggiornato nel corso del tempo con standard che degli aggiornamenti di Azure Stack, visibili nel portale di Azure Stack e perfettamente integrata con Azure Stack.

Oltre a provider di risorse descritti in precedenza, esistono servizi PaaS aggiuntivi disponibili e testata come [soluzioni basate sul modello di Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) che vengono eseguiti in IaaS, ma è un operatore come Azure Stack offrirle come Servizi PaaS per gli utenti tra cui:

- Service Fabric
- Servizio contenitore di Kubernetes
- L'Hub IoT e Hub eventi
- Etherium Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Casi d'uso di esempio per Azure Stack:

- Modellazione finanziaria
- Dati clinici e attestazioni
- Analitica di dispositivi IoT
- Ottimizzazione assortment delle vendite al dettaglio
- Ottimizzazione della supply chain
- IoT industriale
- Manutenzione predittiva
- Città intelligente
- Coinvolgimento dei cittadini

Altre informazioni su Azure Stack in [cos'è Azure Stack](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI 

Soluzioni di Azure Stack uomo consentono di eseguire macchine virtuali in locale e connettersi con facilità ad Azure con una soluzione di infrastruttura (HCL) iperconvergente. Crea ed esegui applicazioni cloud usando servizi di Azure coerenti in locale per rispettare requisiti normativi o tecnici. Oltre a in esecuzione le applicazioni virtualizzate in locale, Azure Stack uomo consente di sostituire e consolidare l'infrastruttura del server degli oggetti eliminati e connettersi ad Azure per i servizi cloud utilizzando il Windows Admin Center.

Azure Stack uomo fornisce convalidate soluzioni uomo con tecnologia Hyper-V e spazi di archiviazione è diretta con Windows Server 2019 definita dal software Datacenter (SDDC). Il Windows Admin Center viene usato per la gestione e accesso ai servizi di Azure integrato, ad esempio:

- Backup di Azure
- Azure Site Recovery
- Aggiornamento e monitoraggio di azure

Per un elenco aggiornato di Azure dei servizi che è possibile connettere Azure Stack uomo per visualizzare [ci si connette a Windows Server a servizi di Azure hybrid](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hci"></a>Casi d'uso di esempio per Azure Stack uomo
- Sistemi di ufficio remoto o un ramo
- Consolidamento dei Data Center
- Infrastruttura desktop virtuale
- Infrastruttura di business-critical
- Archiviazione a costi ridotti
- Ripristino di emergenza e disponibilità elevato nel cloud
- App aziendali, ad esempio SQL Server

Visitare il [sito Web di Azure Stack uomo](https://azure.microsoft.com/overview/azure-stack/hci/) visualizzare 70 uomo dello Stack di Azure attualmente disponibili soluzioni dei partner Microsoft.

## <a name="next-steps"></a>Passaggi successivi

[Nozioni fondamentali sull'amministrazione di Azure Stack](azure-stack-manage-basics.md)

[Guida introduttiva: usare il portale di amministrazione di Azure Stack](azure-stack-manage-portals.md)

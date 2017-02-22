---
title: Introduzione a Cloud Foundry in Microsoft Azure | Documentazione Microsoft
description: Eseguire OSS o Pivotal Cloud Foundry in Microsoft Azure
services: virtual-machines-linux
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 89117c59ae8948eeb32b51a0ecd4fdf0a3a2edd7
ms.openlocfilehash: d026d6d901374a91864edf9f6ee38d5450e7f179

---

# <a name="cloud-foundry-on-azure"></a>Cloud Foundry in Azure

Cloud Foundry è una piattaforma distribuita come servizio (PaaS) open source per la compilazione, la distribuzione e l'uso di applicazioni a 12 fattori sviluppate in una vasta gamma di linguaggi e framework. Questo documento descrive le opzioni disponibili per eseguire Cloud Foundry in Azure e come iniziare.

## <a name="cloud-foundry-offerings"></a>Offerte Cloud Foundry

Esistono due soluzioni Cloud Foundry eseguibili in Azure: Open-Source Cloud Foundry (OSS CF) e Pivotal Cloud Foundry (PCF). OSS CF è una versione [open source](https://github.com/cloudfoundry) di Cloud Foundry gestita da Cloud Foundry Foundation. Pivotal Cloud Foundry è una distribuzione aziendale di Cloud Foundry fornita da Pivotal Software Inc. Verranno ora esaminate alcune differenze tra le due offerte.

### <a name="open-source-cloud-foundry"></a>Open-Source Cloud Foundry

È possibile distribuire OSS Cloud Foundry in Azure distribuendo prima un server Director BOSH e quindi Cloud Foundry, seguendo le [istruzioni disponibili in GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Per altre informazioni sull'uso di OSS CF, vedere la [documentazione](https://docs.cloudfoundry.org/) fornita da Cloud Foundry Foundation.

Microsoft offre il miglior supporto possibile per OSS CF tramite i canali di community seguenti:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>Canale bosh-azure-cpi in [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [Lista di distribuzione cf-bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemi relativi a [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) e [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues) in GitHub

>[!NOTE]
> Il livello di supporto per le risorse di Azure, come le macchine virtuali in cui viene eseguito Cloud Foundry, dipende dal contratto di supporto di Azure. Il supporto tramite community si applica solo ai componenti specifici di Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry include la stessa piattaforma di base della distribuzione OSS, nonché un set di strumenti di gestione proprietari e supporto aziendale. Per eseguire PCF in Azure, è necessario acquisire una licenza da Pivotal. L'offerta PCF in Azure Marketplace include una licenza di valutazione di 90 giorni.

Gli strumenti includono [Pivotal Operations Manager](http://docs.pivotal.io/pivotalcf/customizing/), un'applicazione Web che semplifica la distribuzione e la gestione di una base Cloud Foundry, e [Pivotal Apps Manager](https://docs.pivotal.io/pivotalcf/console/), un'applicazione Web per la gestione di utenti e applicazioni.

Oltre ai canali di supporto elencati sopra per OSS CF, una licenza di PCF offre la possibilità di richiedere supporto a Pivotal. Microsoft e Pivotal hanno anche abilitato flussi di lavoro di supporto che consentono di rivolgersi per assistenza a una delle due parti usufruendo dell'instradamento appropriato della richiesta a seconda della causa del problema.

## <a name="azure-service-broker"></a>Service Broker di Azure

Cloud Foundry incoraggia la metodologia delle ["app a dodici fattori"](https://12factor.net/), che promuove una netta separazione tra i processi delle applicazioni senza stato e i servizi sottostanti con stato. I [Service Broker](https://docs.cloudfoundry.org/services/api.html) offrono un modo coerente per effettuare il provisioning e il binding dei servizi sottostanti alle applicazioni. Il [Service Broker di Azure](https://github.com/Azure/meta-azure-service-broker) fornisce tramite questo canale alcuni dei principali servizi di Azure, tra cui Archiviazione di Azure e Azure SQL.

Se si usa Pivotal Cloud Foundry, il Service Broker è anche [disponibile come riquadro](https://docs.pivotal.io/azure-sb/installing.html) in Pivotal Network.

## <a name="related-resources"></a>Risorse correlate

### <a name="visual-studio-team-services-plugin"></a>Plug-in di Visual Studio Team Services

Cloud Foundry è particolarmente adatto per Agile Software Development e l'uso dell'integrazione continua e del recapito continuo. Se si usa Visual Studio Team Services (VSTS) per gestire i progetti e si vuole configurare una pipeline di integrazione continua/recapito continuo per Cloud Foundry, è possibile usare l'[estensione di VSTS per la compilazione per Cloud Foundry](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Il plug-in semplifica la configurazione e l'automazione delle distribuzioni in Cloud Foundry, indipendentemente dal fatto che vengano eseguite in Azure o in un altro ambiente.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire Pivotal Cloud Foundry da Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)



<!--HONumber=Jan17_HO3-->



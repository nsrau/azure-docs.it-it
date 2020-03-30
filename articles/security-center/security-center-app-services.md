---
title: Proteggere le API e le app Web del servizio app di AzureProtect your Azure App Web apps and APIs
description: Questo articolo consente di iniziare a proteggere le api e le api Web del servizio app di Azure nel Centro sicurezza di Azure.This article helps you to get started protecting your Azure App Service web apps and APIs in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616475"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteggere le API e le app Web del servizio app di AzureProtect your Azure App Web apps and APIs

Il servizio app di Azure è una piattaforma completamente gestita per la creazione e l'hosting di api e app Web senza dover gestire l'infrastruttura. Fornisce informazioni gestionali, di monitoraggio e operative per soddisfare i requisiti di conformità, sicurezza e conformità di livello enterprise. Per altre informazioni, vedere [Servizio app di Azure.For](https://azure.microsoft.com/services/app-service/)more information, see Azure App Service .

Per abilitare la protezione avanzata dalle minacce per il piano di servizio app di Azure, è necessario:To enable advanced threat protection for your Azure App Service plan, you must:

* Sottoscrivere il piano tariffario Standard del Centro sicurezza di AzureSubscribe to Azure Security Center's Standard pricing tier
* Abilitare il piano di servizio app come illustrato di seguito. Il Centro sicurezza è integrato in modo nativo con il servizio app, eliminando la necessità di distribuzione e onboarding: l'integrazione è trasparente.
* Disporre di un piano di servizio app associato a computer dedicati. I piani supportati sono: Basic, Standard, Premium, Isolated o Linux.Supported plans are: Basic, Standard, Premium, Isolated, or Linux. Il Centro sicurezza non supporta i piani Gratuito, Condiviso o Consumo. Per altre informazioni, vedere [Piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).

Con il piano di servizio app abilitato, il Centro sicurezza valuta le risorse coperte dal piano di servizio app e genera consigli sulla sicurezza in base ai risultati. Il Centro sicurezza protegge l'istanza della macchina virtuale in cui è in esecuzione il servizio app e l'interfaccia di gestione. Monitora anche le richieste e le risposte inviate da e verso le applicazioni in esecuzione nel servizio app.

Il Centro sicurezza sfrutta la scala del cloud e la visibilità di Azure come provider di cloud per monitorare gli attacchi comuni alle app Web. Il Centro sicurezza è in grado di individuare gli attacchi alle applicazioni e identificare gli attacchi emergenti, anche quando gli utenti malintenzionati sono in fase di ricognizione, analizzando per identificare le vulnerabilità tra più applicazioni ospitate da Azure.Security Center can discover attacks to your applications and identify emerging attacks - even while attackers are in the reconnaissance phase, scanning to identify vulnerabilities across multiple Azure-hosted applications. Come servizio nativo di Azure, il Centro sicurezza si trova anche in una posizione unica per offrire analisi di sicurezza basate su host che coprono i nodi di calcolo sottostanti per questo PaaS, consentendo al Centro sicurezza di rilevare gli attacchi contro le applicazioni Web che sono già state sfruttate. Per altre informazioni, vedere Protezione dalle minacce per il servizio app di Azure.For more details, see [Threat protection for Azure App Service](threat-protection.md#app-services).


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Abilitazione del monitoraggio e della protezione del servizio app

1. Nel portale di Azure scegliere Centro sicurezza.
2. Vai a **Impostazioni di & dei prezzi** e scegli un abbonamento.
3. In **Piano tariffario**, nella riga **Servizio app** attivare/disattivare il piano su **Abilitato**.

    [![Abilitazione dei servizi app nella sottoscrizione di livello StandardEnabling app services in your Standard tier subscription](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Il numero di istanze elencate per quantità **di risorse** rappresenta il numero totale di istanze di calcolo, in tutti i piani del servizio app in questa sottoscrizione, in esecuzione nel momento in cui è stato aperto il pannello del piano tariffario.
>
> Il servizio app di Azure offre un'ampia gamma di piani. Il piano di servizio app definisce il set di risorse di calcolo per l'esecuzione di un'app Web. Sono equivalenti alle server farm nell'hosting Web convenzionale. È possibile configurare una o più app per eseguirle nelle stesse risorse di calcolo (o nello stesso piano di servizio app).
>
>Per convalidare il conteggio, passare a "Piani del servizio app" nel portale di Azure, in cui è possibile visualizzare il numero di istanze di calcolo usate da ogni piano. 






Per disabilitare il monitoraggio e gli elementi consigliati per il servizio app, ripetere questo processo e impostare il piano di **servizio app** su **Disabilitato**.



## <a name="see-also"></a>Vedere anche
Questo articolo descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza di Azure, vedere gli articoli seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md): informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Servizi app:](security-center-virtual-machine-protection.md#app-services)visualizzare un elenco degli ambienti del servizio app con riepiloghi di integrità.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* Blog sulla sicurezza di [Azure:](https://blogs.msdn.com/b/azuresecurity/)trova post di blog sulla sicurezza e la conformità di Azure.Azure Security Blog : Find blog posts about Azure security and compliance.

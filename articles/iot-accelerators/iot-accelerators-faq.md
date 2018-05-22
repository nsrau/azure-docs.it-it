---
title: Domande frequenti sugli acceleratori di soluzioni Azure IoT | Microsoft Docs
description: Domande frequenti sugli acceleratori di soluzioni IoT
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 6c8c87dc4f7bb91ce227bbfbaf1cabf0c44d2e0b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Domande frequenti sugli acceleratori di soluzioni IoT

Vedere anche le[Domande frequenti specifiche per Connected Factory](iot-accelerators-faq-cf.md) e le [Domande frequenti specifiche per il monitoraggio remoto](iot-accelerators-faq-rm-v2.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Dove è possibile visualizzare il codice sorgente degli acceleratori di soluzioni?

Il codice sorgente è memorizzato nei repository di GitHub seguenti:

* [Acceleratore di soluzioni di monitoraggio remoto (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Acceleratore di soluzioni di monitoraggio remoto (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Acceleratore di soluzioni di manutenzione predittiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Acceleratore di soluzioni di connected factory](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Quali SDK è possibile usare per sviluppare i client di dispositivi per gli acceleratori di soluzioni?

È possibile trovare collegamenti agli SDK dei dispositivi IoT per i vari linguaggi (C, .NET, Java, Node.js, Python) nel repository GitHub [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks).

Se si usa il dispositivo DevKit, è possibile trovare risorse ed esempi nel repository GitHub [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk).

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>La nuova architettura dei microservizi è disponibile per tutte e tre gli acceleratori di soluzioni?

Attualmente solo la soluzione di monitoraggio remoto usa l'architettura dei microservizi, in quanto è associata allo scenario più esteso.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quali vantaggi offre la nuova architettura basata su microservizi open source nel nuovo aggiornamento?

Negli ultimi due anni l'architettura cloud si è evoluta notevolmente. I microservizi sono emersi come ottimo modello per ottenere scalabilità e flessibilità senza compromettere la velocità di sviluppo. Questo modello di architettura viene usato in diversi servizi Microsoft internamente, con ottimi risultati in termini di affidabilità e scalabilità. Microsoft sta mettendo in pratica tutte le conoscenze acquisite perché i clienti possano trarne vantaggio.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Il nuovo acceleratore di soluzioni è disponibile nelle stesse aree geografiche di quella esistente?

Sì, la nuova soluzione di monitoraggio remoto è disponibile nelle stesse aree geografiche.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Che differenza c'è tra eliminare un gruppo di risorse nel portale di Azure e fare clic per eliminare un acceleratore di soluzioni in azureiotsuite.com?

* Se si elimina l'acceleratore di soluzioni in [azureiotsuite.com](https://www.azureiotsuite.com/), si eliminano anche tutte le risorse di cui è stato effettuato il provisioning al momento dell'acceleratore di soluzioni. Se sono state aggiunte altre risorse al gruppo, anche queste ultime vengono eliminate.
* Se si elimina il gruppo di risorse nel [portale di Azure](https://portal.azure.com), si eliminano solo le risorse presenti in tale gruppo. È anche necessario eliminare l'applicazione Azure Active Directory associata all'acceleratore di soluzioni.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>È possibile continuare a sfruttare gli investimenti negli acceleratori di soluzioni Azure IoT?

Sì. Qualsiasi soluzione esistente oggi continua a essere inclusa nella sottoscrizione di Azure e il codice sorgente è sempre disponibile in GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Di quante istanze dell'hub IoT è possibile eseguire il provisioning in una sottoscrizione?

Per impostazione predefinita, è possibile eseguire il provisioning di [10 hub IoT per ogni sottoscrizione](../azure-subscription-service-limits.md#iot-hub-limits). È possibile creare un [ticket di supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare questo limite. Di conseguenza, poiché ogni acceleratore di soluzioni effettua il provisioning di un nuovo hub IoT, è possibile effettuare il provisioning solo di un massimo di 10 acceleratori di soluzioni in una determinata sottoscrizione.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Di quante istanze di Azure Cosmos DB è possibile effettuare il provisioning in una sottoscrizione?

Cinquanta. Anche se è possibile creare un [ticket di supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare questo limite, per impostazione predefinita è possibile effettuare il provisioning solo di 50 istanze di Cosmos DB per ogni sottoscrizione.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Di quante API di Bing Maps gratuite è possibile eseguire il provisioning in una sottoscrizione?

Due. È possibile creare solo due Transazioni sito Web interno - Livello 1 per Bing Maps per i piani aziendali in una sottoscrizione di Azure. Per impostazione predefinita, il provisioning della soluzione per il monitoraggio remoto viene effettuato con il piano Transazioni sito Web interno - Livello 1. Di conseguenza, è possibile eseguire il provisioning di un massimo di due soluzioni per il monitoraggio remoto in una sottoscrizione senza modifiche.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>È possibile creare un acceleratore di soluzioni se è disponibile Microsoft Azure per DreamSpark?

> [!NOTE]
> Microsoft Azure per DreamSpark è ora noto come Microsoft Imagine per studenti.

Attualmente non è possibile creare un acceleratore di soluzioni con un account [Microsoft Azure per DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/), ma in un paio di minuti è possibile creare un [account di valutazione gratuito per Azure](https://azure.microsoft.com/free/), che consente di creare un acceleratore di soluzioni.

### <a name="can-i-create-a-solution-accelerator-if-i-have-cloud-solution-provider-csp-subscription"></a>È possibile creare un acceleratore di soluzioni se si dispone di una sottoscrizione di Cloud Solution Provider?

Non è possibile attualmente creare acceleratore di soluzioni con una sottoscrizione di Cloud Solution Provider, ma in un paio di minuti è possibile creare un [account di valutazione gratuito per Azure](https://azure.microsoft.com/free/), che consente di creare un acceleratore di soluzioni.

### <a name="how-do-i-delete-an-aad-tenant"></a>Come si elimina un tenant AAD?

Vedere il post del blog di Eric Golpe relativo alla [procedura dettagliata di eliminazione di un tenant di Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Passaggi successivi

È anche possibile esplorare alcune altre funzionalità degli acceleratori di soluzioni IoT:

* [Esplorare le funzionalità dell'acceleratore di soluzioni di monitoraggio remoto](iot-accelerators-remote-monitoring-explore.md)
* [Panoramica dell'acceleratore di soluzioni di manutenzione predittiva](../iot-suite/iot-suite-predictive-overview.md)
* [Panoramica dell'acceleratore di soluzioni di connected factory](iot-accelerators-connected-factory-overview.md)
* [Sicurezza IoT sin dall'inizio](../iot-suite/securing-iot-ground-up.md)

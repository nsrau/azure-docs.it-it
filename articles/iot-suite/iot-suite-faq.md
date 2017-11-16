---
title: Domande frequenti su Azure IoT Suite | Microsoft Docs
description: Domande frequenti su IoT Suite
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: aa8a053e6b3b6fcf6e026f2fd65942a943e12a08
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Domande frequenti su IoT Suite

Vedere anche le [domande frequenti](iot-suite-faq-cf.md) sulla soluzione di fabbrica connessa.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Dove è possibile visualizzare il codice sorgente per la soluzione preconfigurata?

Il codice sorgente è memorizzato nei repository di GitHub seguenti:

* [Remote monitoring preconfigured solution (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) (Soluzione preconfigurata di monitoraggio remoto - .NET)
* [Remote monitoring preconfigured solution (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) (Soluzione preconfigurata di monitoraggio remoto - (Java))
* [Predictive maintenance preconfigured solution](https://github.com/Azure/azure-iot-predictive-maintenance) (Soluzione preconfigurata di manutenzione predittiva)
* [Connected factory preconfigured solution](https://github.com/Azure/azure-iot-connected-factory) (Soluzione preconfigurata di connected factory)

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto costa effettuare il provisioning della nuova soluzione di monitoraggio?

La nuova soluzione preconfigurata offre due opzioni di distribuzione:

* Un'opzione *di base* progettata per sviluppatori che necessitano di costi di distribuzione inferiori o clienti che vogliono compilare una demo o un modello di verifica.
* Un'opzione *standard* progettata per le organizzazioni che vogliono distribuire un'infrastruttura pronta per l'ambiente di produzione.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Come si mantengono ridotti i costi durante lo sviluppo della soluzione?

Oltre a offrire due distribuzioni diverse, la nuova soluzione di monitoraggio remoto include un'impostazione per abilitare o disabilitare on demand tutti i dispositivi simulati. La disabilitazione dei dispositivi di simulazione riduce i dati inseriti nella soluzione e, di conseguenza, il costo complessivo.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>La nuova architettura dei microservizi è disponibile per tutte e tre le soluzioni preconfigurate?

Attualmente solo la soluzione di monitoraggio remoto usa l'architettura dei microservizi, in quanto è associata allo scenario più esteso.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quali vantaggi offre la nuova architettura basata su microservizi open source nel nuovo aggiornamento?

Negli ultimi due anni l'architettura cloud si è evoluta notevolmente. I microservizi sono emersi come ottimo modello per ottenere scalabilità e flessibilità senza compromettere la velocità di sviluppo. Questo modello di architettura viene usato in diversi servizi Microsoft internamente, con ottimi risultati in termini di affidabilità e scalabilità. Microsoft sta mettendo in pratica tutte le conoscenze acquisite perché i clienti possano trarne vantaggio.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>La nuova soluzione preconfigurata è disponibile nelle stesse aree geografiche di quella esistente?

Sì, la nuova soluzione di monitoraggio remoto è disponibile nelle stesse aree geografiche.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Qual è la differenza tra le opzioni di distribuzione di base e standard? Come si sceglie quella più appropriata?

Ogni opzione di distribuzione soddisfa esigenze diverse. La distribuzione di base è progettata per avviare e sviluppare modelli di verifica e piccoli progetti pilota. Offre un'architettura semplificata con le risorse minime necessarie e costi inferiori. La distribuzione standard è progettata per compilare e personalizzare una soluzione pronta per l'ambiente di produzione e offre una distribuzione con gli elementi necessari per realizzare tale soluzione. Per scopi di affidabilità e scalabilità, i microservizi dell'applicazione vengono creati come contenitori Docker e distribuiti usando un agente di orchestrazione (per impostazione predefinita, Kubernetes). L'agente di orchestrazione è responsabile della distribuzione, del ridimensionamento e della gestione dell'applicazione. È consigliabile scegliere un'opzione in base alle attuali esigenze. È possibile usarne una, l'altra o una combinazione di entrambe, a seconda della fase del progetto.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>È possibile continuare a sfruttare gli investimenti esistenti in Azure IoT Suite?

Sì. Qualsiasi soluzione esistente oggi continua a essere inclusa nella sottoscrizione di Azure e il codice sorgente è sempre disponibile in GitHub.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Che differenza c'è tra eliminare un gruppo di risorse nel portale di Azure e fare clic per eliminare una soluzione preconfigurata in azureiotsuite.com?

* Se si elimina la soluzione preconfigurata in [azureiotsuite.com](https://www.azureiotsuite.com/), si eliminano anche tutte le risorse di cui è stato effettuato il provisioning al momento della creazione della soluzione. Se sono state aggiunte altre risorse al gruppo, anche queste ultime vengono eliminate.
* Se si elimina il gruppo di risorse nel [portale di Azure](https://portal.azure.com), si eliminano solo le risorse presenti in tale gruppo. È anche necessario eliminare l'applicazione Azure Active Directory associata alla soluzione preconfigurata.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Di quante istanze dell'hub IoT è possibile eseguire il provisioning in una sottoscrizione?

Per impostazione predefinita, è possibile eseguire il provisioning di [10 hub IoT per ogni sottoscrizione](../azure-subscription-service-limits.md#iot-hub-limits). È possibile creare un [ticket di supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare questo limite. Di conseguenza, poiché ogni soluzione preconfigurata effettua il provisioning di un nuovo hub IoT, è possibile effettuare il provisioning solo di un massimo di 10 soluzioni preconfigurate in una determinata sottoscrizione.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Di quante istanze di Azure Cosmos DB è possibile effettuare il provisioning in una sottoscrizione?

Cinquanta. Anche se è possibile creare un [ticket di supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare questo limite, per impostazione predefinita è possibile effettuare il provisioning solo di 50 istanze di Cosmos DB per ogni sottoscrizione.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Di quante API di Bing Maps gratuite è possibile eseguire il provisioning in una sottoscrizione?

Due. È possibile creare solo due Transazioni sito Web interno - Livello 1 per Bing Maps per i piani aziendali in una sottoscrizione di Azure. Per impostazione predefinita, il provisioning della soluzione per il monitoraggio remoto viene effettuato con il piano Transazioni sito Web interno - Livello 1. Di conseguenza, è possibile eseguire il provisioning di un massimo di due soluzioni per il monitoraggio remoto in una sottoscrizione senza modifiche.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>È possibile creare una soluzione preconfigurata se è disponibile Microsoft Azure per DreamSpark?

Attualmente non è possibile creare una soluzione preconfigurata con un account [Microsoft Azure per DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99), ma in un paio di minuti è possibile creare un [account di valutazione gratuito per Azure](https://azure.microsoft.com/free/), che consente di creare una soluzione preconfigurata.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>È possibile creare una soluzione preconfigurata se si dispone di una sottoscrizione di Cloud Solution Provider?

È possibile attualmente creare una soluzione preconfigurata con una sottoscrizione di Cloud Solution Provider, ma in un paio di minuti è possibile creare un [account di valutazione gratuito per Azure](https://azure.microsoft.com/free/), che consente di creare una soluzione preconfigurata.

### <a name="how-do-i-delete-an-aad-tenant"></a>Come si elimina un tenant AAD?

Vedere il post del blog di Eric Golpe relativo alla [procedura dettagliata di eliminazione di un tenant di Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Passaggi successivi

È anche possibile esplorare alcune altre funzionalità delle soluzioni preconfigurate di IoT Suite:

* [Panoramica della soluzione preconfigurata di manutenzione predittiva](iot-suite-predictive-overview.md)
* [Panoramica della soluzione preconfigurata di connected factory](iot-suite-connected-factory-overview.md)
* [Sicurezza IoT sin dall'inizio](securing-iot-ground-up.md)
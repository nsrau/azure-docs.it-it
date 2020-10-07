---
title: Che cosa sono i servizi dati con abilitazione di Azure Arc
description: Introduzione ai servizi dati con abilitazione di Azure Arc
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: 47fef490c5ece577823a14e3fa4c415f0f613ccb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944565"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Che cosa sono i servizi dati con abilitazione di Azure Arc (anteprima)?

Azure Arc consente di eseguire i servizi dati di Azure in locale, nel perimetro e in ambienti cloud pubblici con Kubernetes e l'infrastruttura desiderata.

I servizi dati con abilitazione di Azure Arc seguenti sono attualmente disponibili in anteprima:

- Istanza gestita di SQL
- PostgreSQL Hyperscale

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Sempre aggiornati

I servizi dati con abilitazione di Azure Arc, ad esempio l'istanza gestita di SQL e PostgreSQL Hyperscale con abilitazione di Azure Arc, ricevono gli aggiornamenti a intervalli regolari, incluse le patch di manutenzione e le nuove funzionalità, in modo analogo all'esperienza in Azure. Gli aggiornamenti dal Registro Container di Microsoft vengono forniti all'utente, che imposta la cadenza di distribuzione in base ai propri criteri. In questo modo, i database locali possono rimanere aggiornati lasciando al tempo stesso il controllo all'utente. Poiché i servizi dati con abilitazione di Azure Arc costituiscono un servizio di sottoscrizione, non sarà più necessario affrontare situazioni di fine del supporto per i database.

## <a name="elastic-scale"></a>Scalabilità elastica

L'elasticità in locale di tipo cloud consente di ridimensionare dinamicamente i database in modo analogo a quanto avviene in Azure, in base alla capacità disponibile dell'infrastruttura. Questa funzionalità è in grado di soddisfare scenari di burst con esigenze volatili, inclusi scenari che richiedono l'inserimento di dati e l'esecuzione di query su di essi in tempo reale, su qualsiasi scala, con tempi di risposta di frazioni di secondo. È inoltre possibile aumentare le istanze di database usando l'opzione di distribuzione con iperscalabilità unica di Database di Azure per PostgreSQL Hyperscale. Questa funzionalità offre ai carichi di lavoro dei dati un ulteriore incremento dell'ottimizzazione della capacità, usando operazioni di lettura e scrittura univoche con scalabilità *orizzontale*.

## <a name="self-service-provisioning"></a>Provisioning self-service

Azure Arc offre anche altri vantaggi per il cloud, ad esempio la distribuzione rapida e l'automazione su larga scala. Grazie all'orchestrazione basata su Kubernetes, è possibile distribuire un database in pochi secondi usando la GUI o gli strumenti dell'interfaccia della riga di comando.

## <a name="unified-management"></a>Gestione unificata

Usando strumenti familiari come il portale di Azure, Azure Data Studio e l'interfaccia della riga di comando di Azure Data, è ora possibile ottenere una visualizzazione unificata di tutti gli asset di dati distribuiti con Azure Arc. È possibile non solo visualizzare e gestire un'ampia gamma di database relazionali nell'ambiente e in Azure, ma anche ottenere log e dati di telemetria dalle API Kubernetes per analizzare la capacità e l'integrità dell'infrastruttura sottostante. Oltre a disporre dell'analisi dei log e del monitoraggio delle prestazioni localizzati, è ora possibile usufruire di Monitoraggio di Azure per ottenere informazioni operative complete sull'intera struttura.

## <a name="disconnected-scenario-support"></a>Supporto di scenari disconnessi

Molti dei servizi, ad esempio il provisioning self-service, i backup o il ripristino automatico e il monitoraggio possono essere eseguiti localmente nell'infrastruttura con o senza connessione diretta ad Azure. La connessione diretta ad Azure offre opzioni aggiuntive per l'integrazione con altri servizi di Azure, ad esempio Monitoraggio di Azure, e la possibilità di usare le API del portale di Azure e di Azure Resource Manager da qualsiasi parte del mondo per gestire i propri servizi dati con abilitazione di Azure Arc.

## <a name="next-steps"></a>Passaggi successivi

> **Si desidera fare semplicemente una prova?**  
> È possibile iniziare rapidamente a usare [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) nel servizio Azure Kubernetes, in AWS Elastic Kubernetes Service (EKS), in Google Cloud Kubernetes Engine (GKE) o in una macchina virtuale di Azure.

[Installare gli strumenti client](install-client-tools.md)

[Creare il controller dati di Azure Arc](create-data-controller.md) (è prima necessario installare gli strumenti client)

[Creare un'istanza gestita di SQL di Azure in Azure Arc](create-sql-managed-instance.md) (è prima necessario creare un controller dati di Azure Arc)

[Creare un gruppo di server di Database di Azure per PostgreSQL Hyperscale in Azure Arc](create-postgresql-hyperscale-server-group.md) (è prima necessario creare un controller dati di Azure Arc)

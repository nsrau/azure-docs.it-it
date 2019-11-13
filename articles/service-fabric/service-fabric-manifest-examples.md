---
title: Esempi di manifesto dell'applicazione Service Fabric di Azure
description: Informazioni su come configurare le informazioni del manifesto dell'applicazione e del servizio per un'applicazione di Service Fabric.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 969f15ecb45164e3b3f26c22f756a022e55782ed
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013184"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Esempi di manifesti dell'applicazione e del servizio di Service Fabric
Questa sezione contiene esempi di manifesti dell'applicazione e del servizio. Questi esempi non sono concepiti per illustrare scenari importanti, ma per mostrare le diverse impostazioni disponibili e come usarle. 

Di seguito è riportato un indice delle funzionalità illustrate e dei manifesti di esempio in cui sono incluse.

|Funzionalità|Manifesto|
|---|---|
|[Governance delle risorse](service-fabric-resource-governance.md)|[Manifesto dell'applicazione Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [Manifesto dell'applicazione contenitore](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Eseguire un servizio come account amministratore locale](service-fabric-application-runas-security.md)|[Manifesto dell'applicazione Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Applicare un criterio predefinito a tutti i pacchetti di codice del servizio](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Manifesto dell'applicazione Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Creare entità per utenti e gruppi](service-fabric-application-runas-security.md)|[Manifesto dell'applicazione Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|condividere un pacchetto di dati tra istanze del servizio|[Manifesto dell'applicazione Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Eseguire l'override degli endpoint del servizio](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Manifesto dell'applicazione Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Eseguire uno script all'avvio del servizio](service-fabric-run-script-at-service-startup.md)|[Manifesto del servizio VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definire un endpoint HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifesto del servizio VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Dichiarare un pacchetto di configurazione](service-fabric-application-and-service-manifests.md)|[Manifesto del servizio VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Dichiarare un pacchetto di dati](service-fabric-application-and-service-manifests.md)|[Manifesto del servizio VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Eseguire l'override delle variabili di ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto dell'applicazione contenitore](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Configurare il mapping da porta a host del contenitore](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifesto dell'applicazione contenitore](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Configurare l'autenticazione del registro contenitori](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifesto dell'applicazione contenitore](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Impostare la modalità di isolamento](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifesto dell'applicazione contenitore](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Specificare immagini del contenitore specifiche per la build del sistema operativo](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifesto dell'applicazione contenitore](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Impostare le variabili di ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto del servizio FrontEndService del contenitore](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Manifesto del servizio BackEndService del contenitore](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Configurare un endpoint](service-fabric-get-started-containers.md#configure-communication)|[Manifesto del servizio FrontEndService del contenitore](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Manifesto del servizio BackEndService del contenitore](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [Manifesto del servizio VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|passare comandi al contenitore|[Manifesto del servizio FrontEndService del contenitore](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importare un certificato in un contenitore](service-fabric-securing-containers.md)|[Manifesto del servizio FrontEndService del contenitore](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Configurare i driver di volume](service-fabric-containers-volume-logging-drivers.md)|[Manifesto del servizio BackEndService del contenitore](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|


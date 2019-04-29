---
title: Scalabilità di Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni sul ridimensionamento dei servizi in Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 1688cac35ea9de43bac529a4994bd4ea55eb0ab7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811098"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Applicazioni Service Fabric Mesh per il ridimensionamento

Uno dei principali vantaggi della distribuzione di applicazioni in Service Fabric Mesh è la possibilità di ridurre o aumentare facilmente il numero di istanze dei servizi. Questo consente di gestire diverse quantità di carico nei servizi o migliorare la disponibilità. È possibile ridurre o aumentare manualmente le istanze dei servizi o impostare criteri di ridimensionamento automatico.

## <a name="manual-scaling-instances"></a>Ridimensionamento manuale delle istanze

Nel modello di distribuzione per la risorsa dell'applicazione, ogni servizio ha una proprietà *replicaCount* che può essere usata per impostare il numero di volte in cui si vuole distribuire il servizio. Un'applicazione può essere costituita da più servizi, ognuno dei quali con un valore *replicaCount* univoco, che vengono distribuiti e gestiti insieme. Per ridimensionare il numero di repliche del servizio, modificare il valore *replicaCount* per ogni servizio ridimensionare nel modello o di distribuzione o nel file dei parametri. Aggiornare quindi l'applicazione.

Per esempi di ridimensionamento manualmente delle istanze dei servizi, vedere [Ridurre o aumentare manualmente il numero di istanze dei servizi](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Ridimensionamento automatico delle istanze del servizio
Il ridimensionamento automatico è una funzionalità aggiuntiva di Service Fabric che consente di ridimensionare dinamicamente il numero di istanze del servizio (scalabilità orizzontale). Offre una notevole elasticità e consente di effettuare il provisioning o la rimozione delle istanze del servizio in base all'utilizzo della CPU o della memoria.  Il ridimensionamento automatico consente di eseguire il numero corretto di istanze del servizio per il carico di lavoro e di ottimizzare i costi.

Per ogni servizio i criteri di ridimensionamento vengono definiti automaticamente nel file di risorse del servizio. Ogni criterio di scalabilità automatica è costituito da due parti:

- Un trigger di ridimensionamento, che indica quando verrà eseguito il ridimensionamento del servizio. Esistono tre fattori che determinano il momento in cui il servizio viene ridimensionato. *Soglia di carico inferiore*: valore che determina quando le istanze del servizio si riducono. Se il carico medio di tutte le istanze delle partizioni è inferiore a questo valore, il servizio verrà ridotto. *Soglia di carico superiore*: valore che determina quando le istanze del servizio aumentano. Se il carico medio di tutte le istanze delle partizioni è superiore a questo valore, il servizio verrà aumentato. *Intervallo di ridimensionamento*: determina ogni quanti secondi verrà eseguito un controllo del trigger. Una volta controllato il trigger, se è necessario eseguire un ridimensionamento verrà applicato il meccanismo. Se il ridimensionamento non è necessario, non verrà eseguita alcuna azione. In entrambi i casi il trigger non verrà controllato nuovamente prima dello scadere dell'intervallo di ridimensionamento.

- Un meccanismo di ridimensionamento, che descrive come verrà eseguito il ridimensionamento dopo l'attivazione. *Incremento di scalabilità*: determina il numero di istanze che verranno aggiunte o rimosse quando viene attivato il meccanismo. *Numero massimo di istanze*: definisce il limite superiore per il ridimensionamento. Se il numero di istanze raggiunge questo limite, non sarà possibile aumentare le istanze del servizio, indipendentemente dal carico. *Numero minimo di istanze*: definisce il limite inferiore per il ridimensionamento. Se il numero di istanze della partizione raggiunge questo limite, non sarà possibile ridurre il servizio, indipendentemente dal carico.

Per le procedure di impostazione dei criteri di scalabilità automatica per il servizio, leggere le informazioni sul [ridimensionamento automatico dei servizi](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul modello dell'applicazione, consultare [risorse di Service Fabric](service-fabric-mesh-service-fabric-resources.md)

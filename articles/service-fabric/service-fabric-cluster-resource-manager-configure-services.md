---
title: Specificare metriche e impostazioni di posizionamento nei microservizi di Azure | Documentazione Microsoft
description: Descrive un servizio di Service Fabric specificando metriche, vincoli di posizionamento e altri criteri di posizionamento.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c1df3b77f3fa621a60d6ab73f6dc2c24abbc3366


---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configurazione delle impostazioni di Cluster Resource Manager per i servizi Service Fabric
Cluster Resource Manager di Service Fabric consente un controllo con granularità fine sulle regole che disciplinano ogni singolo servizio denominato. Ogni istanza del servizio denominato può specificare regole per l'allocazione nel cluster. Ogni servizio denominato può anche definire il set di metriche di cui generare un report, incluso il relativo livello di importanza per tale servizio. La configurazione dei servizi prevede tre diverse attività:

1. Configurazione dei vincoli di posizionamento
2. Configurazione delle metriche
3. Configurazione dei criteri di posizionamento avanzati e di altre regole (meno comune)

## <a name="placement-constraints"></a>Vincoli di posizionamento
I vincoli di posizionamento vengono usati per controllare su quali nodi del cluster un servizio può effettivamente essere eseguito. Un'istanza del servizio denominato o tutti i servizi di un determinato tipo sono in genere vincolati all'esecuzione in un tipo di nodo specifico. Ciò premesso, i vincoli di posizionamento sono estensibili: è possibile definire set di proprietà sulla base di un tipo di nodo e quindi selezionare quelli con vincoli quando viene creato il servizio. I vincoli di posizionamento sono inoltre aggiornabili in modo dinamico per l'intera durata del servizio, consentendo di rispondere alle modifiche del cluster. Le proprietà di un determinato nodo possono essere aggiornate anche in modo dinamico nel cluster. Altre informazioni sui vincoli di posizionamento e sulla loro configurazione sono disponibili in [questo articolo](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)

## <a name="metrics"></a>Metrica
Le metriche sono il set di risorse necessarie per una specifica istanza del servizio denominato. La configurazione delle metriche di un servizio include informazioni relative alla quantità della risorsa che ogni replica con stato o istanza senza stato usa per impostazione predefinita. Le metriche includono anche un peso che ne indica l'importanza del bilanciamento per il servizio, nel caso di compromessi necessari.

## <a name="other-placement-rules"></a>Altre regole di posizionamento
Esistono altri tipi di regole di posizionamento utili nei cluster distribuiti geograficamente o in altri scenari meno comuni. Altre regole di posizionamento sono configurate tramite correlazioni o criteri.

## <a name="next-steps"></a>Passaggi successivi
* Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)
* L'affinità è un modo di configurare i servizi. Non è un'operazione frequente, ma se necessario sono disponibili altre informazioni [qui](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* Sono disponibili molte regole di posizionamento diverse da configurare nel servizio per gestire scenari aggiuntivi. È possibile trovare informazioni sui vari criteri di posizionamento [qui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
* Partire dall'inizio e vedere l' [introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
* Cluster Resource Manager offre diverse opzioni per descrivere il cluster. Per altre informazioni su queste opzioni, vedere [Descrizione di un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)



<!--HONumber=Jan17_HO4-->



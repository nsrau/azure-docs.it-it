<properties
   pageTitle="Configurazione dei servizi con Cluster Resource Manager di Service Fabric"
   description="Describing a Service Fabric Service by specifying metrics, placement constraints, and other placement policies."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>


# Configurazione dei servizi
Cluster Resource Manager di Service Fabric consente un controllo con granularità molto fine sulle regole che disciplinano ogni singolo servizio denominato. Ogni istanza del servizio può specificare regole specifiche per la modalità di allocazione nel cluster e può definire il set di metriche di cui desidera generare un report, tra cui il relativo livello di importanza per tale servizio. La configurazione dei servizi prevede in genere tre diverse attività:

1. Configurazione dei vincoli di posizionamento
2. Configurazione delle metriche
3. Configurazione delle regole di posizionamento avanzate (meno comune)

Di seguito un'analisi dettagliata:

## Vincoli di posizionamento
I vincoli di posizionamento vengono usati per controllare su quali nodi del cluster un servizio può effettivamente essere eseguito. In genere si noteranno un'istanza del servizio denominato particolare o tutti i servizi di un tipo vincolato all'esecuzione su un particolare tipo di nodo, ma i vincoli di posizionamento sono estendibili, dal momento che è possibile definire qualsiasi set di proprietà su una base a nodo e quindi selezionarle con vincoli quando viene creato il servizio. I vincoli di posizionamento sono inoltre aggiornabili in modo dinamico per l'intera durata del servizio, consentendo di rispondere alle modifiche del cluster.

## Metrica
Le metriche rappresentano l'elenco delle risorse su cui questo servizio deve essere bilanciato, incluse le informazioni relative alla quantità di risorsa che ogni replica o istanza del servizio utilizza per impostazione predefinita. Le metriche includono anche un peso che ne indica l'importanza per il servizio, nel caso di compromessi necessari.

## Altre regole di posizionamento
Esistono altri tipi di regole di posizionamento che sono principalmente utili nei cluster distribuiti geograficamente o in altri scenari meno comuni. Queste sono configurate tramite correlazioni o criteri. Sebbene non vengano utilizzate in molti scenari, verranno descritte per motivi di completezza.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Informazioni sulle metriche](service-fabric-cluster-resource-manager-metrics.md)
- [Informazioni sull'affinità del servizio](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- [Informazioni sui criteri di posizionamento del servizio](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- [Introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)

<!---HONumber=AcomDC_0309_2016-->
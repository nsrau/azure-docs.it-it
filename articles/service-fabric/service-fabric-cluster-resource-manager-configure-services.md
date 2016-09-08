<properties
   pageTitle="Configurazione dei servizi con Cluster Resource Manager di Service Fabric | Microsoft Azure"
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
   ms.date="08/19/2016"
   ms.author="masnider"/>


# Configurazione delle impostazioni di Cluster Resource Manager per i servizi Service Fabric
Cluster Resource Manager di Service Fabric consente un controllo con granularità molto fine sulle regole che disciplinano ogni singolo servizio denominato. Ogni istanza del servizio con nome può specificare regole per la modalità di allocazione nel cluster e può definire il set di metriche di cui desidera generare un report, tra cui il relativo livello di importanza per tale servizio. La configurazione dei servizi prevede in genere tre diverse attività:

1. Configurazione dei vincoli di posizionamento
2. Configurazione delle metriche
3. Configurazione dei criteri di posizionamento avanzati (meno comune)

Di seguito un'analisi dettagliata:

## Vincoli di posizionamento
I vincoli di posizionamento vengono usati per controllare su quali nodi del cluster un servizio può effettivamente essere eseguito. In genere si noterà un'istanza del servizio con nome specifico o tutti i servizi di un determinato tipo vincolati all'esecuzione su un particolare tipo di nodo. Ciò premesso, i vincoli di posizionamento sono estensibili: è possibile definire set di proprietà sulla base di un tipo di nodo e quindi selezionare quelli con vincoli quando viene creato il servizio. I vincoli di posizionamento sono inoltre aggiornabili in modo dinamico per l'intera durata del servizio, consentendo di rispondere alle modifiche del cluster. Le proprietà di un determinato nodo possono essere aggiornate anche in modo dinamico nel cluster. Altre informazioni sui vincoli di posizionamento e sulla loro configurazione sono disponibili in [questo articolo](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)

## Metrica
Le metriche rappresentano l'elenco delle risorse su cui questo servizio deve essere bilanciato, incluse le informazioni relative alla quantità di risorsa che ogni replica o istanza del servizio usa per impostazione predefinita. Le metriche includono anche un peso che ne indica l'importanza del bilanciamento per il servizio, nel caso di compromessi necessari.

## Altre regole di posizionamento
Esistono altri tipi di regole di posizionamento che sono principalmente utili nei cluster distribuiti geograficamente o in altri scenari meno comuni. Queste sono configurate tramite correlazioni o criteri. Sebbene non vengano utilizzate in molti scenari, verranno descritte per motivi di completezza.

## Passaggi successivi
- Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md).
- L'affinità è un modo di configurare i servizi. Non è un'operazione frequente, ma se necessario sono disponibili altre informazioni [qui](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Sono disponibili molte regole di posizionamento diverse da configurare nel servizio per gestire scenari aggiuntivi. È possibile trovare informazioni sui vari criteri di posizionamento [qui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Partire dall'inizio e vedere l'[introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager dispone di molte opzioni per la descrizione del cluster. Per altre informazioni a riguardo vedere l'articolo [Descrizione di un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

<!---HONumber=AcomDC_0824_2016-->
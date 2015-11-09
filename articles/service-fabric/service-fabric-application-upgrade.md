<properties
   pageTitle="Aggiornamento di un'applicazione di Service Fabric"
   description="Questo articolo fornisce un'introduzione all'aggiornamento di un'applicazione di Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>


# Aggiornamento di un'applicazione di Service Fabric


Un'applicazione di Service Fabric è una raccolta di servizi. Durante un aggiornamento, Service Fabric confronta il nuovo [manifesto dell'applicazione](service-fabric-application-model.md#describe-an-application) con la versione precedente e determina quali servizi dell'applicazione richiedono aggiornamenti. A tale scopo, vengono confrontati i numeri di versione dei manifesti dei servizi con la versione precedente. Se un servizio non è cambiato, non viene aggiornato.

## Panoramica degli aggiornamenti in sequenza
In un aggiornamento in sequenza di un'applicazione l'operazione viene eseguita in fasi. A ogni fase l'aggiornamento viene applicato a un subset di nodi del cluster denominato dominio di aggiornamento. In questo modo l'applicazione continua a essere disponibile durante l'aggiornamento. È possibile che durante l'aggiornamento il cluster contenga una combinazione di versioni precedenti e nuove. Per questo motivo è necessario che le due versioni siano compatibili con le versioni precedenti e successive. In caso contrario, l'amministratore di applicazioni deve organizzare un aggiornamento in più fasi per mantenere la disponibilità. A tale scopo, viene eseguito un aggiornamento con una versione intermedia dell'applicazione compatibile con la versione precedente prima di effettuare l'aggiornamento alla versione finale.

I domini di aggiornamento vengono specificati nel manifesto del cluster quando si configura il cluster. Non è sottinteso che i domini di aggiornamento ricevano gli aggiornamenti in un ordine specifico. Un dominio di aggiornamento è un'unità logica di distribuzione per un'applicazione. I domini di aggiornamento consentono ai servizi di garantire disponibilità elevata durante un aggiornamento.

Gli aggiornamenti non in sequenza sono possibili qualora l'aggiornamento venga applicato a tutti i nodi del cluster, ad esempio nel caso in cui l'applicazione disponga di un solo dominio di aggiornamento. Questa non è una procedura consigliata, poiché il servizio resterà inattivo e non disponibile durante l'aggiornamento. Azure inoltre non offre alcuna garanzia per i casi in cui un cluster è configurato con un solo dominio di aggiornamento.

## Controlli di integrità durante gli aggiornamenti
Per un aggiornamento devono essere impostati criteri di integrità oppure possono essere usati valori predefiniti. Un aggiornamento viene considerato riuscito quando tutti i domini di aggiornamento vengono aggiornati entro i valori di timeout specificati e risultano integri. Un dominio di aggiornamento integro significa che supera tutti i controlli di integrità specificati nei criteri di integrità, che possono ad esempio richiedere che tutti i servizi in un'istanza dell'applicazione siano <em>integri</em> secondo quanto definito per l'integrità da Service Fabric.

I criteri e i controlli di integrità durante l'aggiornamento da parte di Service Fabric sono indipendenti dai servizi e dalle applicazioni, ovvero non vengono eseguiti test specifici per i servizi. Si supponga ad esempio che un servizio preveda un requisito di velocità effettiva minima. Service Fabric tuttavia non dispone delle informazioni per questo tipo di test e pertanto non controllerà la velocità effettiva, secondo quanto definito per l'applicazione. Per i controlli che vengono eseguiti, fare riferimento agli [articoli sull'integrità](service-fabric-health-introduction.md). I controlli effettuati durante l'aggiornamento includono la verifica che il pacchetto applicazione sia stato copiato correttamente, che l'istanza si sia avviata e così via.

L'integrità dell'applicazione è un'aggregazione delle entità figlio dell'applicazione. In breve, Service Fabric valuta l'integrità dell'applicazione tramite l'integrità segnalata per l'applicazione e l'integrità dei relativi servizi. L'integrità dei servizi dell'applicazione viene ulteriormente valutata aggregando l'integrità degli elementi figlio, ad esempio la replica dei servizi. Dopo che sono stati soddisfatti i criteri di integrità dell'applicazione, l'aggiornamento può continuare. Se invece i criteri non vengono soddisfatti, l'aggiornamento dell'applicazione ha esito negativo.

## Modalità di aggiornamento

La modalità comune e consigliata per l'aggiornamento è la modalità monitorata (Monitored), che esegue l'aggiornamento di un dominio di aggiornamento e, se vengono superati tutti i controlli di integrità (in base ai criteri specificati), passa automaticamente al dominio di aggiornamento successivo e così via. In caso di esito negativo e/o di timeout dei controlli di integrità, viene eseguito il rollback dell'aggiornamento del dominio di aggiornamento oppure la modalità cambia in UnmonitoredManual, qualora questa sia l'opzione selezionata al momento dell'aggiornamento.

UnmonitoredManual richiede l'intervento manuale dopo ogni aggiornamento in un dominio di aggiornamento per passare al dominio di aggiornamento successivo. Questa modalità non prevede controlli di integrità di Service Fabric e dipende dall'intervento dell'operatore per eseguire i controlli di integrità o di stato prima di avviare l'aggiornamento nel dominio di aggiornamento successivo.

## Diagramma di flusso di aggiornamento di un'applicazione

Il diagramma di flusso seguente illustra il processo di aggiornamento di un'applicazione di Service Fabric. In particolare, il flusso descrive in che modo i timeout, inclusi *HealthCheckStableDuration*, *HealthCheckRetryTimeout* e *UpgradeHealthCheckInterval*, consentono di controllare quando l'aggiornamento in un dominio di aggiornamento viene considerato riuscito o non riuscito.

![Processo di aggiornamento per un'applicazione di Service Fabric][image]


## Passaggi successivi

[Esercitazione sull'aggiornamento](service-fabric-application-upgrade-tutorial.md)

[Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md)

[Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md)

[Argomenti avanzati](service-fabric-application-upgrade-advanced.md)

[Risoluzione dei problemi relativi all'aggiornamento di un'applicazione](service-fabric-application-upgrade-troubleshooting.md)



[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
 

<!---HONumber=Nov15_HO1-->
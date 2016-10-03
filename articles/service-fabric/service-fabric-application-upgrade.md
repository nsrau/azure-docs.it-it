<properties
   pageTitle="Aggiornamento di un'applicazione di infrastruttura di servizi | Microsoft Azure"
   description="Questo articolo fornisce un'introduzione all'aggiornamento di un'applicazione di Service Fabric, inclusa la scelta delle modalità di aggiornamento e dei controlli di integrità eseguiti."
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
   ms.date="09/14/2016"
   ms.author="subramar"/>  


# Aggiornamento di un'applicazione di infrastruttura di servizi

Un'applicazione di Azure Service Fabric è una raccolta di servizi. Durante un aggiornamento, Service Fabric confronta il nuovo [manifesto dell'applicazione](service-fabric-application-model.md#describe-an-application) con la versione precedente e determina quali servizi dell'applicazione richiedono aggiornamenti. Service Fabric confronta i numeri di versione nel manifesto del servizio con quelli della versione precedente. Se un servizio non è cambiato, non viene aggiornato.

## Panoramica degli aggiornamenti in sequenza

In un aggiornamento in sequenza di un'applicazione l'operazione viene eseguita in fasi. In ogni fase l'aggiornamento viene applicato a un subset di nodi del cluster denominato dominio di aggiornamento. In questo modo l'applicazione continua a essere disponibile durante l'aggiornamento. È possibile che durante l'aggiornamento il cluster contenga una combinazione di versioni precedenti e nuove.

Per questo motivo è necessario che le due versioni siano compatibili con le versioni precedenti e successive. In caso contrario, l'amministratore di applicazioni deve organizzare un aggiornamento in più fasi per mantenere la disponibilità. In un aggiornamento a più fasi come primo passaggio si esegue l'aggiornamento a una versione intermedia dell'applicazione che sia compatibile con la versione precedente. Come secondo passaggio si esegue l'aggiornamento alla versione finale, non più compatibile con la versione di pre-aggiornamento ma compatibile con la versione intermedia.

I domini di aggiornamento vengono specificati nel manifesto del cluster quando si configura il cluster. I domini di aggiornamento non ricevono gli aggiornamenti in un ordine particolare. Un dominio di aggiornamento è un'unità logica di distribuzione per un'applicazione. I domini di aggiornamento consentono ai servizi di garantire disponibilità elevata durante un aggiornamento.

Gli aggiornamenti non in sequenza sono possibili qualora l'aggiornamento venga applicato a tutti i nodi del cluster, ad esempio nel caso in cui l'applicazione disponga di un solo dominio di aggiornamento. Questo approccio non è consigliato perché durante l'aggiornamento il servizio rimane inattivo e non disponibile. Azure, inoltre, non offre alcuna garanzia per i casi in cui un cluster è configurato con un solo dominio di aggiornamento.

## Controlli di integrità durante gli aggiornamenti

Per un aggiornamento devono essere impostati criteri di integrità oppure possono essere usati valori predefiniti. Un aggiornamento viene considerato riuscito quando tutti i domini di aggiornamento vengono aggiornati entro i valori di timeout specificati e risultano integri. Un dominio di aggiornamento integro significa che ha superato tutti i controlli di integrità specificati nei criteri di integrità. Un criterio di integrità, ad esempio, può richiedere che tutti i servizi all'interno di un'istanza dell'applicazione siano *integri*, secondo quanto definito per l'integrità da Service Fabric.

I criteri e i controlli di integrità durante l'aggiornamento da parte dell’infrastruttura di servizi sono indipendenti dai servizi e dalle applicazioni. In altre parole, non vengono eseguiti test specifici per i servizi. Ad esempio il servizio potrebbe avere un requisito di velocità effettiva, ma Service Fabric non dispone delle informazioni per verificare la velocità effettiva. Per informazioni sui controlli che vengono eseguiti, vedere gli [articoli sull'integrità](service-fabric-health-introduction.md). I controlli eseguiti durante l'aggiornamento includono test di verifica che il pacchetto dell'applicazione sia stato copiato correttamente, che l'istanza sia stata avviata e così via.

L'integrità dell'applicazione è un'aggregazione delle entità figlio dell'applicazione. In breve, Service Fabric valuta l'integrità dell'applicazione tramite l'integrità segnalata per l'applicazione. In questo modo, può valutare anche l'integrità di tutti i servizi relativi all'applicazione. In alternativa, Service Fabric può valutare l'integrità dei servizi aggregando l'integrità degli elementi figlio, ad esempio la replica dei servizi. Se il criterio di integrità dell'applicazione è soddisfatto, l'aggiornamento può continuare. In caso contrario, l'aggiornamento dell'applicazione ha esito negativo.

## Modalità di aggiornamento

La modalità consigliata per l'aggiornamento dell'applicazione è la modalità monitorata, il metodo più comune. che esegue l'aggiornamento di un dominio di aggiornamento e, se vengono superati tutti i controlli di integrità (in base ai criteri specificati), passa automaticamente al dominio di aggiornamento successivo. In caso di esito negativo e/o di timeout dei controlli di integrità, viene eseguito il rollback dell'aggiornamento del dominio di aggiornamento oppure la modalità cambia in UnmonitoredManual. È possibile configurare l'aggiornamento in modo che venga scelta una di queste due modalità per gli aggiornamenti non riusciti.

La modalità UnmonitoredManual richiede l'intervento manuale dopo ogni aggiornamento eseguito in un dominio di aggiornamento per passare al dominio di aggiornamento successivo. Non viene eseguito alcun controllo di integrità su Service Fabric. L'amministratore esegue i controlli di integrità o di stato previsti prima di avviare l'aggiornamento nel dominio di aggiornamento successivo.

## Diagramma di flusso di aggiornamento di un'applicazione

Il diagramma di flusso che segue questo paragrafo aiuta a capire il processo di aggiornamento di un'applicazione di Service Fabric. In particolare, il flusso descrive in che modo i valori di timeout, ad esempio *HealthCheckStableDuration*, *HealthCheckRetryTimeout* e *UpgradeHealthCheckInterval*, consentono di verificare se l'aggiornamento in un dominio di aggiornamento viene considerato riuscito o non riuscito.

![Processo di aggiornamento per un'applicazione di Service Fabric][image]


## Passaggi successivi

[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

[Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) descrive la procedura di aggiornamento di un'applicazione tramite PowerShell.

Controllare l’aggiornamento dell'applicazione tramite [Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare [Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Per informazioni su come usare funzionalità avanzate durante l'aggiornamento dell'applicazione, vedere [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Per informazioni su come risolvere problemi comuni negli aggiornamenti dell'applicazione, vedere i passaggi indicati in [Risoluzione dei problemi relativi agli aggiornamenti dell'applicazione ](service-fabric-application-upgrade-troubleshooting.md).
 


[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png

<!---HONumber=AcomDC_0921_2016-->
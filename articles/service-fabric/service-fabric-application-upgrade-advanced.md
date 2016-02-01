<properties
   pageTitle="Aggiornamento di un'applicazione: argomenti avanzati | Microsoft Azure"
   description="Questo articolo illustra alcuni degli argomenti avanzati relativi all'aggiornamento di un'applicazione di Service Fabric."
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

# Aggiornamento di un'applicazione di infrastruttura di servizi: argomenti avanzati

## Modalità di aggiornamento manuale

> [AZURE.NOTE]La modalità di aggiornamento UnmonitoredManual deve essere presa in considerazione solo per un aggiornamento non riuscito o sospeso. La modalità di aggiornamento consigliata per le applicazioni di Service Fabric è la modalità monitorata.

Azure Service Fabric offre diverse modalità di aggiornamento per supportare i cluster di sviluppo e di produzione. Ognuna delle opzione di sviluppo è ideale per ambienti diversi.

L'aggiornamento in sequenza di applicazioni in modalità Monitored è il più comune da usare in un ambiente di produzione. Se vengono specificati criteri di aggiornamento, Service Fabric verifica che l'applicazione sia integra prima di procedere con l'aggiornamento.

 L'amministratore dell'applicazione può usare la modalità di aggiornamento in sequenza manuale per avere il controllo totale sull'avanzamento dell'aggiornamento nei vari domini di aggiornamento. Questa modalità è utile per determinate situazioni in cui sono necessari criteri di valutazione dell'integrità più personalizzati o complessi o in cui è presente un aggiornamento non convenzionale, ad esempio un'applicazione che presenta già una perdita di dati.

L'aggiornamento in sequenza automatizzato dell'applicazione è utile infine per ambienti di sviluppo o di test per garantire un ciclo di iterazione rapido durante lo sviluppo dei servizi.

## Modificare la modalità di aggiornamento manuale
**Manual**: arresta l'aggiornamento dell'applicazione in corrispondenza dell'UD corrente e cambia la modalità di aggiornamento in UnmonitoredManual. L'amministratore deve chiamare manualmente **MoveNextApplicationUpgradeDomainAsync** per procedere con l'aggiornamento o per attivare un rollback avviando un nuovo aggiornamento. Quando l'aggiornamento passa alla modalità Manual, resta in tale modalità finché non viene avviato un nuovo aggiornamento. Il comando **GetApplicationUpgradeProgressAsync** restituisce FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING.

## Aggiornare con un pacchetto Diff

Un'applicazione di Service Fabric può essere aggiornata effettuando il provisioning con un pacchetto applicazione completo e autonomo. È possibile aggiornare un'applicazione anche usando un pacchetto Diff contenente solo i file dell'applicazione aggiornati, nonché i file manifesto dell'applicazione e del servizio aggiornati.

Un pacchetto applicazione completo contiene tutti i file necessari per avviare ed eseguire un'applicazione di Service Fabric. Un pacchetto Diff contiene solo i file che sono cambiati tra l'ultimo provisioning e l'aggiornamento corrente, oltre ai file manifesto dell'applicazione e del servizio completi. Gli eventuali riferimenti nel manifesto dell'applicazione o del servizio che non vengono trovati da Service Fabric nel layout della build verranno cercati in ImageStore.

I pacchetti dell'applicazione completi sono necessari per la prima installazione di un'applicazione nel cluster. Gli aggiornamenti successivi possono essere eseguiti con un pacchetto applicazione completo o con un pacchetto Diff.

L'uso di un pacchetto Diff è consigliato nei casi seguenti:

* Se è disponibile un pacchetto dell'applicazione di grandi dimensioni che fa riferimento a più file manifesto del servizio e/o a più pacchetti di codice, di configurazione o di dati.

* Se è disponibile un sistema di distribuzione che genera il layout della build direttamente dal processo di compilazione dell'applicazione. In questo caso, anche se il codice è rimasto invariato, i nuovi assembly compilati avranno un checksum diverso. L'uso del pacchetto applicazione completo richiederebbe l'aggiornamento della versione in tutti i pacchetti di codice. Usando un pacchetto Diff invece è necessario fornire soltanto i file che sono stati modificati e i file manifesto in cui è cambiata la versione.

## Passaggi successivi

[Esercitazione sull'aggiornamento](service-fabric-application-upgrade-tutorial.md)

[Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md)

[Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md)

[Risoluzione dei problemi di aggiornamento delle applicazioni](service-fabric-application-upgrade-troubleshooting.md)

<!---HONumber=AcomDC_0121_2016-->
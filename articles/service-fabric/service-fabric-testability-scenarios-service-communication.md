<properties 
   pageTitle="Testabilità: comunicazione del servizio | Microsoft Azure" 
   description="Le comunicazioni da servizio a servizio sono un punto di integrazione critico di un'applicazione Infrastruttura di servizi. In questo articolo vengono illustrate alcune considerazioni di progettazione e tecniche di test." 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="vturecek" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="08/25/2015"
   ms.author="vturecek"/>

# Scenari di Testabilità di Infrastruttura di servizi: comunicazione tra i servizi

Gli stili dell’architettura orientata ai microservizi e ai servizi affiorano naturalmente in Infrastruttura di servizi. In questi tipi di architetture distribuite, le applicazioni di microservizi con componenti sono in genere costituite da più servizi che devono comunicare tra loro. Anche nel caso più semplice, in genere si dispone di almeno un servizio Web senza stato e di un servizio di archiviazione dei dati con stato che devono comunicare.

La comunicazione da servizio a servizio è un punto di integrazione critico di un'applicazione, in quanto ogni servizio espone un'API remota ad altri servizi. L’utilizzo di un set di limiti di API che coinvolgono l’I/O in genere richiede particolare attenzione e una buona dose di test e di convalida.

Quando questi limiti di servizio sono raggruppati in un sistema distribuito, è necessario fare numerose considerazioni:

 - **Protocollo di trasporto**. Sarà HTTP per una maggiore interoperabilità o un protocollo binario personalizzato per la velocità effettiva massima?
 - **Gestione degli errori**. Come vengono gestiti gli errori permanenti e temporanei e cosa accade quando si sposta un servizio su un nodo diverso?
 - **Timeout e latenza**. Nelle applicazioni a più livelli, in che modo ogni livello di servizio gestisce la latenza nello stack e fino all'utente?

Sia che venga utilizzato uno dei componenti integrati di comunicazione tra i servizi forniti da Infrastruttura di servizi, sia che ne venga creato uno personalizzato, testare l’interazione tra i servizi è una parte essenziale per garantire resilienza nell'applicazione.

## Dove si trova il servizio?

Le istanze del servizio possono spostarsi nel tempo, soprattutto se configurate con la metrica di caricamento per il bilanciamento delle risorse ottimizzato personalizzato. Aggiornamenti, failover, scalabilità e diverse altre situazioni che si verificano per tutta la durata di un sistema distribuito sono i punti in cui Infrastruttura di servizi sposta le istanze del servizio per ottimizzare la disponibilità.

Dal momento che i servizi si spostano all'interno del cluster, esistono due scenari che i client e gli altri servizi devono essere preparati a gestire durante la comunicazione con un servizio:

 + L’istanza del servizio o la replica della partizione si è spostata dall’ultima volta in cui si è stabilita una comunicazione. Si tratta della parte normale del ciclo di vita di un servizio e deve essere prevista nel corso della durata dell’applicazione.
 + L’istanza del servizio o la replica della partizione è in fase di spostamento. Sebbene in Infrastruttura di servizi il failover di un servizio da un nodo a un altro si verifichi molto rapidamente, potrebbe esserci un ritardo nella disponibilità se il componente di comunicazione del servizio è lento ad avviarsi.

La gestione normale di questi scenari è importante per garantire che il sistema funzioni senza problemi. A tale scopo, sono necessarie alcune considerazioni:

+ Ogni servizio a cui è possibile connettersi, dispone di un *indirizzo* che è in ascolto su (HTTP, WebSockets, e così via). Quando un'istanza del servizio o una partizione si sposta, il relativo endpoint indirizzo viene modificato (si sposta in un altro nodo con un indirizzo IP diverso). Se si utilizzano componenti di comunicazione integrati, questi gestiranno automaticamente la nuova risoluzione degli indirizzi del servizio. 
+ Potrebbe esserci un aumento temporaneo della latenza del servizio quando l’istanza del servizio avvia nuovamente il listener, a seconda della rapidità con cui il servizio si apre dopo essere stato spostato.
+ Tutte le connessioni esistenti dovranno essere chiuse e riaperte nuovamente quando il servizio si apre in un nuovo nodo. Un arresto o un riavvio normale del nodo consente il tempo alle connessioni esistenti di essere arrestate normalmente.

### Test: spostare istanze del servizio

Grazie agli strumenti di Testabilità di Infrastruttura di servizi, è possibile creare uno scenario di test per testare queste situazioni in modi diversi.

1. Spostare la replica primaria di un servizio con stato.
 
    La replica primaria di una partizione del servizio con stato può essere spostata per diversi motivi. Utilizzare questa procedura per la replica primaria di una partizione specifica, per osservare in che modo reagiscono i servizi allo spostamento in modo molto controllato.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Arrestare un nodo.

    Quando un nodo viene arrestato, Infrastruttura di servizi sposterà tutte le istanze di servizi o le partizioni del servizio che erano presenti in quel nodo in uno degli altri nodi disponibili nel cluster. Utilizzare questa procedura per testare una situazione in cui un nodo viene perso dal cluster e tutte le istanze del servizio e le repliche in tale nodo devono essere spostate.

    Un nodo può essere arrestato utilizzando il cmdlet PowerShell Stop-ServiceFabricNode:

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node.1

    ```

    
    


### Disponibilità del servizio

Infrastruttura di servizi è una piattaforma progettata per fornire elevata disponibilità dei servizi. Tuttavia, i problemi dell’infrastruttura sottostante possono comunque causare la non disponibilità in casi estremi. Pertanto, è importante testare anche tale scenario.

I servizi con stato utilizzano un sistema basato su quorum per la replica dello stato per l’elevata disponibilità. Ciò significa che per eseguire le operazioni di scrittura deve essere disponibile un quorum di repliche. In rari casi, ad esempio un errore hardware diffuso, un quorum di repliche potrebbe non essere disponibile. In questo caso, non si sarà in grado di eseguire operazioni di scrittura, ma sarà possibile eseguire operazioni di lettura.

### Test: mancata disponibilità delle operazioni di scrittura

Gli strumenti Testabilità di Infrastruttura di servizi consentono di eseguire un’operazione di fault injection che provoca la perdita del quorum per eseguire il test di questo tipo di scenario. Sebbene si tratti di un caso raro, è importante che i client e i servizi che dipendono dal servizio con stato siano preparati a gestire le situazioni in cui non possono eseguire richieste di scrittura al servizio con stato. Allo stesso tempo è importante anche che il servizio con stato stesso sia a conoscenza di questo possibilità e sia in grado di comunicarla normalmente ai chiamanti.

La perdita di quorum può essere indotta mediante il cmdlet PowerShell ServiceFabricPartitionQuorumLoss:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode PartialQuorumLoss -QuorumLossDurationInSeconds 20

```

In questo esempio, viene impostato `QuorumLossMode` su `PartialQuorumLoss`, per indicare che si desidera causare la perdita di quorum senza interrompere tutte le repliche, in modo che le operazioni di lettura continuino a essere possibili. Per testare uno scenario in cui un'intera partizione non è disponibile, è possibile impostare questa opzione su `FullQuorumLoss`.

## Passaggi successivi

[Ulteriori informazioni sulle azioni di Testabilità](service-fabric-testability-actions.md)

[Ulteriori informazioni sugli scenari di Testabilità](service-fabric-testability-scenarios.md)

<!---HONumber=Nov15_HO2-->
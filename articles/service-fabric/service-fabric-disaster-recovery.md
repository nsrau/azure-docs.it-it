---
title: Ripristino di emergenza di Azure Service Fabric | Microsoft Docs
description: Azure Service Fabric offre le funzionalità necessarie per affrontare tutti i tipi di emergenze. L'articolo descrive i tipi di emergenze che si possono verificare e come affrontarli.
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2016
ms.author: seanmck

---
# Ripristino di emergenza in Azure Service Fabric
Un elemento fondamentale di un'applicazione cloud ad alta disponibilità è la sua capacità di resistere a tutti i vari tipi di errori, inclusi quelli al di fuori del controllo dell'utente. L'articolo descrive il layout fisico di un cluster di Azure Service Fabric nel contesto di potenziali emergenze e fornisce indicazioni su come gestire tali emergenze per limitare o eliminare il rischio di tempi di inattività o perdita di dati.

## Layout fisico dei cluster Service Fabric in Azure
Per comprendere i rischi associati ai vari tipi di errori, è utile conoscere la disposizione fisica dei cluster i in Azure.

Quando si crea un cluster Service Fabric in Azure, è necessario scegliere l'area in cui verrà ospitato. L'infrastruttura di Azure esegue quindi il provisioning delle risorse per il cluster all'interno dell'area, in particolare del numero di macchine virtuali (VM) necessario. Di seguito viene spiegato meglio come e dove viene eseguito il provisioning delle VM.

### Domini di errore
Per impostazione predefinita, le VM nel cluster vengono distribuite uniformemente tra gruppi logici definiti domini di errore, che segmentano i computer in base ai potenziali errori nell'hardware dell'host. In particolare, se due VM si trovano in due diversi domini di errore, è certo che non condividono l'unità di alimentazione o il commutatore di rete. Di conseguenza, un guasto alla rete locale o un'interruzione dell'alimentazione di una sola VM non ha ripercussioni sull'altra, così Service Fabric può bilanciare il carico di lavoro della macchina che non risponde all'interno del cluster.

È possibile visualizzare il layout del cluster nei domini di errore tramite la mappa del cluster disponibile in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

![Nodi distribuiti nei domini di errore in Service Fabric Explorer][sfx-cluster-map]

> [!NOTE]
> L'altro asse nella mappa del cluster mostra i domini di aggiornamento, che raggruppano logicamente i nodi in base alle attività di manutenzione pianificata. I cluster Service Fabric in Azure sono sempre suddivisi in cinque domini di aggiornamento.
> 
> 

### Distribuzione geografica
Esistono attualmente [26 aree di Azure in tutto il mondo][azure-regions], più diverse altre già annunciate. Una singola area può contenere uno o più data center fisici a seconda della richiesta e della disponibilità delle posizioni adatte, tra gli altri fattori. Anche nelle aree contenenti diversi data center fisici, tuttavia, non esiste alcuna garanzia che le VM del cluster vengano distribuite uniformemente tra le posizioni fisiche. Per ora infatti il provisioning di tutte le VM in un determinato cluster viene eseguito in un unico luogo fisico.

## Gestione degli errori
Esistono diversi tipi di errori che possono avere ripercussioni sul cluster, ciascuno con una propria soluzione. Questi errori verranno analizzati nell'ordine di probabilità che si verifichino.

### Errori dei singoli computer
Come accennato in precedenza, gli errori dei singoli computer nella VM o nell'hardware o software in un dominio di errore di per sé non rappresentano alcun rischio. Di solito Service Fabric rileva l'errore nel giro di secondi e risponde in base allo stato del cluster. Ad esempio, se il nodo ospita le repliche primarie di una partizione, viene scelta una nuova replica primaria tra le repliche secondarie della partizione. Quando Azure riattiva il computer bloccato, questo si riunisce automaticamente al cluster e torna a elaborare la sua parte di carico di lavoro.

### Diversi errori simultanei dei computer
Sebbene i domini di errore riducano nettamente il rischio che diversi computer si blocchino contemporaneamente, esiste comunque la possibilità che diversi errori casuali blocchino più computer contemporaneamente in un cluster.

Di solito il cluster continua a funzionare fintanto che la maggioranza dei nodi rimane disponibile, sebbene a una capacità inferiore perché le repliche con stato vengono compresse in un numero ridotto di computer e sono disponibili meno istanze senza stato per distribuire il carico.

#### Perdita del quorum
Se la maggioranza delle repliche della partizione di un servizio con stato si blocca, la partizione passerà a uno stato di "perdita del quorum". A quel punto, Service Fabric bloccherà le operazioni di scrittura sulla partizione per garantire che la partizione rimanga coerente e affidabile. È stato scelto infatti di accettare un periodo di indisponibilità per fare in modo che ai client non venga comunicato che i dati sono stati salvati se in realtà non è così. Tenere presente che, se è stato scelto di consentire le letture dalle repliche secondarie per il servizio con stato, sarà possibile continuare a eseguire le operazioni di lettura in questo stato. Una partizione rimane in uno stato di perdita del quorum finché non torna disponibile un numero sufficiente di repliche oppure fino a quando l'amministratore del cluster non forza il sistema a proseguire con l'[API Repair-ServiceFabricPartition][repair-partition-ps].

> [!WARNING]
> Lo svolgimento di quest'operazione quando la replica primaria è inattiva comporta la perdita dei dati.
> 
> 

Anche i servizi di sistema possono subire la perdita del quorum, con un impatto commisurato al servizio in questione. Ad esempio, la perdita del quorum nel servizio di denominazione incide sulla risoluzione dei nomi, mentre la perdita del quorum nel servizio di gestione failover bloccherà la creazione e i failover di nuovi servizi. Si noti che, a differenza dei propri servizi, *non* è consigliabile provare a ripristinare i servizi di sistema. È preferibile attendere invece che le repliche tornino disponibili.

#### Riduzione del rischio di perdita del quorum
È possibile attenuare il rischio di perdita del quorum aumentando le dimensioni del set di repliche di destinazione per il servizio. È opportuno considerare il numero di repliche necessarie in termini di numero di nodi non disponibili che il sistema può tollerare pur rimanendo disponibile per le operazioni di scrittura, tenendo presente che gli aggiornamenti dell'applicazione o del cluster possono rendere i nodi temporaneamente non disponibili, nonché provocare errori hardware.

Considerare i seguenti esempi presupponendo di aver configurato i servizi impostando MinReplicaSetSize su tre, il valore minimo consigliato per i servizi di produzione. Se si imposta TargetReplicaSetSize su tre, ovvero una replica primaria e due secondarie, un errore hardware durante l'aggiornamento (due repliche inattive) causa la perdita del quorum. Il servizio diventa di sola lettura. Se si dispone invece di cinque repliche, il sistema potrà tollerare due errori durante l'aggiornamento (tre repliche inattive) perché le due repliche rimanenti possono formare un quorum nel set di repliche minimo.

### Interruzioni o distruzione del data center
In rari casi, il data center fisico può essere temporaneamente non disponibile a causa di un'interruzione dell'alimentazione o della perdita di connettività di rete. In questi casi, probabilmente anche i cluster e le applicazioni di Service Fabric non saranno disponibili, ma i dati verranno conservati. Per i cluster in esecuzione in Azure, è possibile visualizzare gli aggiornamenti sulle interruzioni del servizio nella [pagina Stato di Azure][azure-status-dashboard].

Nell'evento estremamente improbabile che un intero data center fisico venga distrutto, tutti i cluster di Service Fabric che contiene andranno perduti, insieme al relativo stato.

Per evitare questa eventualità, è di fondamentale importanza eseguire periodicamente il [backup dello stato](service-fabric-reliable-services-backup-restore.md) in un archivio con ridondanza geografica per garantire la possibilità di ripristino. La frequenza del backup dipende dall'obiettivo del punto di ripristino (RPO). Anche se la funzionalità di backup e ripristino non è ancora stata implementata completamente, è necessario implementare un gestore per l'evento `OnDataLoss` per poter eseguire la registrazione in caso di necessità, come riportato di seguito:

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```


### Errori software e altre cause di perdita dei dati
Tra le cause di perdita dei dati, gli errori di codice nei servizi, gli errori umani e le violazioni della sicurezza sono più frequenti rispetto agli errori diffusi del data center. Ad ogni modo, la strategia di ripristino è sempre la stessa: backup regolari di tutti i servizi con stato e pratica nell'esercizio di ripristino dello stato.

## Passaggi successivi
* Informazioni su come simulare diversi errori con il [framework di verificabilità](service-fabric-testability-overview.md).
* Lettura di altre risorse sul ripristino di emergenza e sulla disponibilità elevata. Microsoft ha pubblicato molti documenti su questi argomenti. Sebbene alcuni di questi documenti si riferiscano a tecniche specifiche da applicare in altri prodotti, contengono anche molte procedure consigliate generali valide anche per Service Fabric:
  * [Elenco di controllo della disponibilità](../best-practices-availability-checklist.md)
  * [Esercitazione nel ripristino di emergenza](../sql-database/sql-database-disaster-recovery-drills.md)
  * [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure][dr-ha-guide]

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]: https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png

<!---HONumber=AcomDC_0817_2016-->
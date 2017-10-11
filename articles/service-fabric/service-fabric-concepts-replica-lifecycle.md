---
title: Repliche e istanze in Azure Service Fabric | Microsoft Docs
description: Informazioni sulle repliche, sulle istanze e sui relativi cicli di vita e funzioni
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 794cbed0f5072bcc1c18343b9896aad464861c45
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="replicas-and-instances"></a>Repliche e istanze 
Questo articolo offre una panoramica del ciclo di vita delle repliche dei servizi con stato e delle istanze dei servizi senza stato.

## <a name="instances-of-stateless-services"></a>Istanze di servizi senza stato
Un'istanza di un servizio senza stato è una copia della logica del servizio in esecuzione in uno dei nodi del cluster. Un'istanza all'interno di una partizione viene identificata in modo univoco dal relativo valore InstanceId. Il ciclo di vita di un'istanza può essere modellato in base al diagramma seguente:

![Ciclo di vita dell'istanza](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Dopo aver determinato un posizionamento per l'istanza, Gestione risorse cluster passa a questo stato del ciclo di vita. In questa fase l'istanza viene avviata sul nodo (l'host dell'applicazione viene avviato, l'istanza viene creata e quindi aperta). Al termine dell'avvio, l'istanza passa allo stato Ready (Pronto). 

Se si verifica un arresto anomalo dell'host dell'applicazione o del nodo per questa istanza, passa allo stato Dropped (Eliminato).

### <a name="ready-rd"></a>Ready (RD) (Pronto (RD))
Nello stato Ready (Pronto) l'istanza è attiva nel nodo. Se si tratta di un modello Reliable Service, è stato richiamato RunAsync. 

Se si verifica un arresto anomalo dell'host dell'applicazione o del nodo per questa istanza, passa allo stato Dropped (Eliminato).

### <a name="closing-cl"></a>Closing (CL) (Chiusura (CL))
Nello stato Closing (Chiusura (CL)) Service Fabric esegue l'arresto dell'istanza in questo nodo. Ciò può essere dovuto a vari motivi, ad esempio l'aggiornamento di un'applicazione, il bilanciamento del carico o l'eliminazione del servizio. Al termine del processo di arresto, passa allo stato Dropped (Eliminato).

### <a name="dropped-dd"></a>Dropped (DD) (Eliminato (DD))
Nello stato Dropped (Eliminato) l'istanza non è più in esecuzione nel nodo. In questa fase Service Fabric gestisce i metadati relativi a questa istanza (che verrà eliminata in modo differito).

> [!NOTE]
> È possibile per eseguire la transizione da qualsiasi stato allo stato Dropped (Eliminato) mediante l'opzione ForceRemove in `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Repliche di servizi con stato
Una replica di un servizio con stato è una copia della logica del servizio in esecuzione in uno dei nodi del cluster. La replica gestisce anche una copia dello stato del servizio. Due concetti correlati descrivono il ciclo di vita e il comportamento delle repliche con stato:
- Ciclo di vita della replica
- Ruolo della replica

La discussione seguente illustra i servizi con stato persistente. Per i servizi con stato volatile (o in-memory) gli stati Down (Inaccessibile) e Dropped (Eliminato) sono equivalenti.

![Ciclo di vita della replica](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Una replica InBuild è una replica creata o preparata per l'unione con il set di repliche. A seconda del ruolo della replica, IB presenta semantiche diverse. 

Se si verifica un arresto anomalo nell'host dell'applicazione o del nodo per una replica InBuild, passa allo stato Down (Inaccessibile).

####<a name="primary-inbuild-replicas"></a>Repliche Primary InBuild (InBuild primaria) 
Le repliche Primary InBuild (InBuild primaria) sono le prime repliche di una partizione. Ciò si verifica in genere in fase di creazione della partizione. Le repliche Primary InBuild (InBuild primaria) vengono create anche quando tutte le repliche di una partizione vengono riavviate o eliminate.

####<a name="idlesecondary-inbuild-replicas"></a>Repliche IdleSecondary InBuild (InBuild secondaria inattiva)
Si tratta di nuove repliche create da Gestione risorse Cluster, ma anche di repliche esistenti arrestate che è necessario riaggiungere al set. Queste repliche vengono generate o create dalla primaria prima che possano unirsi al set di repliche come secondarie attive e partecipare al riconoscimento del quorum delle operazioni.

####<a name="activesecondary-inbuild-replicas"></a>Repliche ActiveSecondary InBuild (InBuild secondaria attiva)
In alcune query è possibile osservare questo stato. Si tratta di un'ottimizzazione in cui il set di repliche rimane invariato, ma è necessario creare una replica. La replica stessa segue le transizioni di stato normale del computer (come descritto nella sezione sui ruoli delle repliche).

### <a name="ready-rd"></a>Ready (RD) (Pronto (RD))
Una replica Ready (Pronto) è una replica che partecipa al processo di replica e al riconoscimento del quorum delle operazioni. Lo stato Ready (Pronto) è applicabile alle repliche primarie e secondarie attive.

Se si verifica un arresto anomalo nell'host dell'applicazione o del nodo per una replica Ready (Pronto), passa allo stato Down (Inaccessibile).

### <a name="closing-cl"></a>Closing (CL) (Chiusura (CL))
Una replica passa allo stato Closing (Chiusura) negli scenari seguenti:

- **Arresto del codice per la replica**: Service Fabric potrebbe dover arrestare il codice in esecuzione per una replica. Ciò può essere dovuto a vari motivi, ad esempio in caso di aggiornamento di un'applicazione o un'infrastruttura o per via di un errore segnalato dalla replica e così via. Al termine della chiusura, la replica passa allo stato Down (Inaccessibile). Lo stato persistente associato a questa replica archiviata su disco non viene eliminato.

- **Rimozione della replica dal cluster**: Service Fabric potrebbe dover rimuovere lo stato permanente e arrestare il codice in esecuzione per una replica. Ciò potrebbe essere dovuto a vari motivi, ad esempio un bilanciamento del carico

### <a name="dropped-dd"></a>Dropped (DD) (Eliminato (DD))
Nello stato Dropped (Eliminato) l'istanza non è più in esecuzione nel nodo. Nel nodo non è presente alcuno stato. In questa fase Service Fabric gestisce i metadati relativi a questa istanza (che verrà comunque eliminata).

### <a name="down-d"></a>Down (D) (Inaccessibile (D))
Nello stato Down (Inaccessibile) il codice della replica non è in esecuzione, ma lo stato persistente per la replica è presente su tale nodo. Una replica può essere inaccessibile per diversi motivi, ad esempio in caso di nodo inattivo, arresti anomali nel codice della replica, un aggiornamento di un'applicazione, errori nella replica e così via.

Una replica inattiva viene aperta da Service Fabric in base alle esigenze, ad esempio al termine dell'aggiornamento nel nodo e così via.

Il ruolo della replica non è pertinente nello stato Down (Inaccessibile).

### <a name="opening-op"></a>Opening (OP) (Apertura (OP))
Una replica Down (Inaccessibile) passa allo stato Opening (Apertura) se Service Fabric deve riattivare la replica. Ciò può ad esempio verificarsi in seguito a un aggiornamento del codice per l'applicazione in un nodo e così via. 

Se si verifica un arresto anomalo nell'host dell'applicazione o del nodo per una replica Opening (Apertura), passa allo stato Down (Inaccessibile).

Il ruolo della replica non è pertinente nello stato Opening (Apertura).

### <a name="standby-sb"></a>StandBy (SB)
Una replica in stato StandBy è una replica di un servizio persistente che, da inaccessibile, è stata aperta. Questa replica potrebbe essere usata da Service Fabric se deve aggiungere un'altra replica al set (in quanto contiene già una parte dello stato e il processo di creazione è più veloce). Alla scadenza di StandByReplicaKeepDuration la replica StandBy viene rimossa.

Se si verifica un arresto anomalo nell'host dell'applicazione o del nodo per una replica StandBy, passa allo stato Down (Inaccessibile).

Il ruolo della replica non è pertinente nello stato StandBy.

> [!NOTE]
> Qualsiasi replica non inattiva o eliminata è considerata *attiva*.
>

> [!NOTE]
> È possibile per eseguire la transizione da qualsiasi stato allo stato Dropped (Eliminato) mediante l'opzione ForceRemove in `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Ruolo della replica 
Il ruolo della replica ne determina la funzione nel set di repliche.

- **Primary (P)** (Primario (P)): nel set è presente una replica primaria responsabile dell'esecuzione di operazioni di lettura e scrittura. 
- **ActiveSecondary (S)**: si tratta di repliche che ricevono aggiornamenti di stato dalla replica primaria, li applicano e reinviano riconoscimenti. Nel set sono presenti più repliche secondarie attive, il cui numero determina il numero di errori gestibili dal servizio.
- **IdleSecondary (I)**: queste repliche vengono create dalla replica primaria, ovvero ricevono da essa lo stato prima che possano essere alzate di livello allo stato di repliche secondarie attive. 
- **None (N)** (Nessuno (N)): queste repliche non hanno responsabilità nel set di repliche.
- **Unknown (U)** (Sconosciuto (U)): questo è il ruolo iniziale di una replica prima che riceva una chiamata API ChangeRole da Sevice Fabric.

La sezione seguente descrive le transizioni dei ruoli di replica e alcuni scenari di esempio in cui possono verificarsi:

![Ruolo della replica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: creazione di una nuova replica primaria
- U -> I: creazione di una nuova replica inattiva
- U -> N: eliminazione di una replica standby
- I -> S: innalzamento di livello di una replica secondaria inattiva a una replica secondaria attiva, in modo che i riconoscimenti contribuiscano al quorum.
- I -> P: innalzamento di livello di una replica secondaria inattiva a una replica primaria. Ciò può verificarsi in presenza di riconfigurazioni speciali, quando la replica secondaria inattiva è il candidato ideale per diventare primaria.
- I -> N: eliminazione della replica secondaria inattiva.
- S -> P: innalzamento di livello di una replica secondaria attiva a una replica primaria. Ciò può essere dovuto a un failover della replica primaria o uno spostamento avviato da Gestione risorse cluster in risposta a un aggiornamento dell'applicazione o al bilanciamento del carico e così via.
- S -> N: eliminazione della replica secondaria attiva.
- P -> S: abbassamento di livello della replica primaria. Ciò può essere dovuto a uno spostamento della replica primaria avviato da Gestione risorse cluster in risposta a un aggiornamento dell'applicazione o al bilanciamento del carico e così via.
- P -> N: eliminazione della replica primaria

> [!NOTE]
> Modelli di programmazione di livello superiore, come [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md) nascondono il concetto di ruolo di replica allo sviluppatore. In Reliable Actors la nozione di ruolo non è necessaria, mentre in Reliable Services è estremamente semplificata per la maggior parte degli scenari.
>

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

- [Ciclo di vita di Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)


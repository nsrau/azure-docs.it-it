<properties
   pageTitle="Aggiornamento di un cluster dell'infrastruttura di servizi | Microsoft Azure"
   description="Eseguire l'aggiornamento del codice e/o della configurazione dell'infrastruttura che esegue un cluster dell'infrastruttura di servizi, incluso l'aggiornamento del certificato, l'aggiunta di porte dell'applicazione, di patch del sistema operativo e così via. Possibili risultati degli aggiornamenti."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/23/2015"
   ms.author="chackdan"/>

# Aggiornamento di un cluster dell'infrastruttura di servizi

Un cluster dell'infrastruttura di servizi è una risorsa di proprietà dell'utente parzialmente gestita da Microsoft. Questo articolo descrive ciò che viene gestito automaticamente e ciò che è possibile configurare manualmente.

## Configurazione del cluster gestita automaticamente

Microsoft gestisce il codice e la configurazione dell'infrastruttura eseguita in un cluster, mediante aggiornamenti automatici monitorati al software in base alle specifiche esigenze. Tali aggiornamenti possono riferirsi al codice, alla configurazione o a entrambi gli elementi. Per assicurarsi che gli aggiornamenti causino un impatto minimo o nullo all'applicazione, vengono eseguiti in tre fasi.

### Fase 1: L'aggiornamento viene eseguito usando tutti i criteri di integrità del cluster

Durante questa fase gli aggiornamenti procedono in base a un dominio di aggiornamento alla volta e le applicazioni in esecuzione nel cluster non subiscono tempi di inattività. I criteri di integrità del cluster (ossia una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster) vengono rispettati per la durata dell'aggiornamento.

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento e viene inviato un messaggio di posta elettronica al proprietario della sottoscrizione indicante la necessità di eseguire il rollback dell'aggiornamento di un cluster, con azioni correttive se presenti, e che si eseguirà la fase 2 dopo n giorni (dove n è una variabile). Si tenta di eseguire lo stesso aggiornamento altre volte per escludere gli aggiornamenti non riusciti a causa di problemi di infrastruttura e, dopo n giorni dalla data di invio del messaggio di posta elettronica, si passa alla fase 2.

Se i criteri di integrità del cluster vengono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o una qualsiasi delle repliche in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita. Questo avviene al fine di evitare l'invio di troppi messaggi di posta elettronica. La ricezione di un messaggio deve infatti rappresentare un'eccezione alla norma. Si prevede che la maggior parte degli aggiornamenti del cluster vengano eseguiti senza conseguenze negative per la disponibilità dell'applicazione.

Per informazioni dettagliate sull'impostazione di criteri di integrità personalizzati per il cluster, vedere l'articolo relativo all'[aggiornamento del cluster e ai parametri di integrità](service-fabric-cluster-health-parameters.md).

### Fase 2: L'aggiornamento viene eseguito usando solo i criteri di integrità predefiniti

I criteri di integrità vengono impostati in modo che il numero di applicazioni integre all'inizio dell'aggiornamento rimanga invariato per tutta la durata del processo di aggiornamento. Come nella fase 1, durante questa fase gli aggiornamenti procedono in base a un dominio di aggiornamento alla volta e le applicazioni in esecuzione nel cluster non subiscono tempi di inattività. I criteri di integrità del cluster (ossia una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster) vengono rispettati per la durata dell'aggiornamento.

Se i criteri di integrità del cluster non vengono in effetti soddisfatti, viene eseguito il rollback dell'aggiornamento e viene inviato un messaggio di posta elettronica al proprietario della sottoscrizione indicante la necessità di eseguire il rollback dell'aggiornamento di un cluster, con azioni correttive se presenti, e che si eseguirà la fase 3 dopo n giorni (dove n è una variabile).

Si tenta di eseguire lo stesso aggiornamento altre volte per escludere gli aggiornamenti non riusciti a causa di problemi di infrastruttura. Viene inviato un messaggio di sollecito alcuni giorni prima della scadenza degli n giorni. Dopo che gli n giorni dalla data di invio del messaggio di posta elettronica sono trascorsi, si passa alla fase 3. I messaggi di posta elettronica inviati nella fase 2 devono essere tenuti in alta considerazione e devono essere intraprese azioni correttive.

Se i criteri di integrità del cluster vengono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o una qualsiasi delle repliche in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

### Fase 3: L'aggiornamento viene eseguito usando criteri di integrità aggressivi

Questi criteri di integrità sono pensati per il completamento dell'aggiornamento piuttosto che per l'integrità delle applicazioni. In questa fase verranno completati pochi aggiornamenti del cluster. Se il cluster finisce in questa fase, è molto probabile che l'applicazione risulti non integra e/o perda la disponibilità.

In modo analogo alle altre due fasi, gli aggiornamenti della fase 3 procedono in base a un dominio di aggiornamento alla volta.

Se i criteri di integrità del cluster non vengono in effetti soddisfatti, viene eseguito il rollback dell'aggiornamento. Si tenta di eseguire lo stesso aggiornamento altre volte per escludere gli aggiornamenti non riusciti a causa di problemi di infrastruttura, a questo punto il cluster viene bloccato in modo da non ricevere più supporto e/o aggiornamenti.

Al proprietario della sottoscrizione viene inviato un messaggio di posta elettronica contenente queste informazioni e le azioni correttive. Se la fase 3 ha esito negativo, non si prevede di definire uno stato per il cluster.

Se i criteri di integrità del cluster vengono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o una qualsiasi delle repliche in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

## Configurazione del cluster controllata manualmente

Di seguito sono riportate le configurazioni che è possibile modificare in un cluster attivo.

### Certificati

È possibile aggiornare facilmente i certificati primario o secondario dal portale o eseguendo un comando PUT sulla risorsa servicefabric.cluster.

![CertificateUpgrade][CertificateUpgrade]

### Porte dell'applicazione

A tale scopo, è possibile modificare le proprietà della risorsa del servizio di bilanciamento del carico associate al tipo di nodo. È possibile usare il portale o ARM PowerShell direttamente.

Per aprire una nuova porta in tutte le macchine virtuali in un tipo di nodo, è necessario seguire questa procedura.

1. **Aggiungere un nuovo probe al servizio di bilanciamento del carico appropriato**

    Se il cluster è stato distribuito tramite il portale, il bilanciamento del carico verrà denominato "loadBalancer-0", "loadBalancer-1" e così via, uno per ogni tipo di nodo. Poiché i nomi del servizio di bilanciamento del carico sono univoci solo nell'ambito di un gruppo di risorse, è consigliabile cercarli in un determinato gruppo di risorse.

    ![AddingProbes][AddingProbes]


2. **Aggiungere una nuova regola al servizio di bilanciamento del carico appropriato**

    Se il servizio di bilanciamento del carico è lo stesso, aggiungere una nuova regola usando il probe creato nel passaggio precedente.

    ![AddingLBRules][AddingLBRules]


### Proprietà di posizionamento

  Per ogni tipo di nodo, è possibile aggiungere proprietà di posizionamento personalizzate da usare nelle applicazioni. NodeType è una proprietà predefinita che è possibile usare senza aggiungerla in modo esplicito.

  >[AZURE.NOTE]Per informazioni dettagliate sull'uso delle proprietà di posizionamento, fare riferimento alla [documentazione relativa ai vincoli di posizionamento](service-fabric-placement-constraint.md).

### Metriche della capacità

Per ogni tipo di nodo, è possibile aggiungere metriche della capacità personalizzate da usare nelle applicazioni per creare report sul carico. Per informazioni dettagliate sull'uso di metriche della capacità in base alle quali creare report sul carico, consultare [Panoramica della creazione di report sul carico dinamico](service-fabric-resource-balancer-dynamic-load-reporting.md).

### Applicazione di patch del sistema operativo alle macchine virtuali che costituiscono il cluster
Questa funzionalità verrà rilasciata in seguito. L'applicazione di patch alle macchine virtuali è attualmente un'operazione manuale, che è necessario eseguire gradualmente, in modo da non rendere inattiva più di una macchina virtuale alla volta.

### Aggiornamento del sistema operativo nelle macchine virtuali che costituiscono il cluster
Se è necessario aggiornare l'immagine del sistema operativo in uso, eseguire questa operazione una macchina virtuale alla volta manualmente. Non è attualmente possibile eseguire l'operazione in automatico.


## Passaggi successivi

- Informazioni su come eseguire la [scalabilità orizzontale e verticale del cluster](service-fabric-cluster-scale-up-down.md)
- Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes.png
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png

<!---HONumber=AcomDC_1203_2015-->
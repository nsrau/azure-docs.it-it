<properties
   pageTitle="Aggiornare un cluster di Service Fabric | Microsoft Azure"
   description="Eseguire l'aggiornamento del codice di Service Fabric e/o della configurazione eseguita su un cluster di Service Fabric, incluso l'aggiornamento del certificato, l'aggiunta di porte dell'applicazione, l'applicazione di patch al sistema operativo e così via. Possibili risultati degli aggiornamenti."
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
   ms.date="09/13/2016"
   ms.author="chackdan"/> 


# Aggiornare un cluster di Service Fabric

Un cluster di Azure Service Fabric è una risorsa di proprietà dell'utente parzialmente gestita da Microsoft. Questo articolo descrive ciò che viene gestito automaticamente e ciò che è possibile configurare manualmente.

## Configurazione del cluster gestita automaticamente

Microsoft gestisce il codice dell'infrastruttura e la configurazione eseguita in un cluster e, in base alle esigenze, esegue aggiornamenti monitorati del software in modo automatico. Gli aggiornamenti possono interessare il codice, la configurazione o entrambi. Per assicurarsi che gli aggiornamenti abbiano un impatto minimo o nullo sull'applicazione, vengono eseguiti nelle tre fasi descritte di seguito.

### Fase 1: Un aggiornamento viene eseguito usando tutti i criteri di integrità del cluster

In questa fase gli aggiornamenti interessano un dominio di aggiornamento per volta e le applicazioni in esecuzione sul cluster non subiscono tempi di inattività. I criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione sul cluster) vengono rispettati per l'intera durata dell'aggiornamento.

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Al proprietario della sottoscrizione verrà quindi inviato un messaggio di posta elettronica contenente le informazioni seguenti:

- Notifica che era necessario eseguire il rollback di un aggiornamento del cluster.
- Eventuali azioni correttive suggerite.
- Il numero di giorni (n) prima dell'avvio della fase 2.

Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. Dopo che gli n giorni dalla data di invio del messaggio di posta elettronica sono trascorsi, si passa alla fase 2.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita. in modo da evitare l'invio di troppi messaggi e per far sì che la ricezione di un messaggio rappresenti un'eccezione alla norma. Si prevede che la maggior parte degli aggiornamenti del cluster abbia esito positivo, senza alcun impatto sulla disponibilità dell'applicazione.

### Fase 2: Un aggiornamento viene eseguito usando solo i criteri di integrità predefiniti

I criteri di integrità in questa fase vengono impostati in modo che il numero di applicazioni integre all'inizio dell'aggiornamento rimanga invariato per l'intera durata del processo di aggiornamento. Come nella fase 1, in questa fase gli aggiornamenti interessano un dominio di aggiornamento per volta e le applicazioni in esecuzione sul cluster non subiscono tempi di inattività. I criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione sul cluster) vengono rispettati per l'intera durata dell'aggiornamento.

Se i criteri di integrità del cluster in vigore non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Al proprietario della sottoscrizione verrà quindi inviato un messaggio di posta elettronica contenente le informazioni seguenti:

- Notifica che era necessario eseguire il rollback di un aggiornamento del cluster.
- Eventuali azioni correttive suggerite.
- Il numero di giorni (n) prima dell'avvio della fase 3.

Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. Viene inviato un messaggio di sollecito alcuni giorni prima della scadenza degli n giorni. Dopo che gli n giorni dalla data di invio del messaggio di posta elettronica sono trascorsi, si passa alla fase 3. I messaggi di posta elettronica inviati nella fase 2 devono essere tenuti in alta considerazione e devono essere intraprese azioni correttive.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

### Fase 3: Un aggiornamento viene eseguito usando criteri di integrità aggressivi

Tali criteri di integrità in questa fase sono pensati per il completamento dell'aggiornamento piuttosto che per l'integrità delle applicazioni. In questa fase verranno completati pochi aggiornamenti del cluster. Se il cluster giunge a questa fase, è molto probabile che l'applicazione divenga non integra e/o perda disponibilità.

In modo analogo alle altre due fasi, gli aggiornamenti della fase 3 procedono in base a un dominio di aggiornamento alla volta.

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. A questo punto, il cluster viene bloccato in modo che non riceva più supporto e/o aggiornamenti.

Al proprietario della sottoscrizione viene inviato un messaggio di posta elettronica contenente queste informazioni e le azioni correttive. Se la fase 3 ha esito negativo, si prevede che il cluster non venga impostato su alcun tipo di stato.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

## Configurazioni del cluster controllate manualmente

Di seguito sono riportate le configurazioni che è possibile modificare in un cluster attivo.

### Certificati

È facile aggiungere o eliminare i certificati di cluster e client tramite il portale. Fare riferimento a [questo documento per altre informazioni](service-fabric-cluster-security-update-certs-azure.md)

![Schermata che illustra le identificazioni personali del certificato nel portale di Azure.][CertificateUpgrade] 


### Porte dell'applicazione

È possibile modificare le porte dell'applicazione modificando le proprietà della risorsa del servizio di bilanciamento del carico associate al tipo di nodo. A questo scopo, è possibile usare il portale oppure direttamente PowerShell per Gestione risorse.

Per aprire una nuova porta in tutte le VM di un tipo di nodo, seguire questa procedura.

1. Aggiungere un nuovo probe al servizio di bilanciamento del carico appropriato.

    Se il cluster è stato distribuito tramite il portale, il bilanciamento del carico verrà denominato "LB-nome del gruppo di risorse-NodeTypename", uno per ogni tipo di nodo. Poiché i nomi del servizio di bilanciamento del carico sono univoci solo nell'ambito di un gruppo di risorse, è consigliabile cercarli in uno specifico gruppo di risorse.

    ![Schermata che illustra l'aggiunta di un probe a un servizio di bilanciamento del carico nel portale.][AddingProbes] 

2. Aggiungere una nuova regola al servizio di bilanciamento del carico.

    Aggiungere una nuova regola allo stesso servizio di bilanciamento del carico usando il probe creato nel passaggio precedente.

    ![Schermata che illustra l'aggiunta di una nuova regola a un servizio di bilanciamento del carico nel portale.][AddingLBRules] 


### Proprietà di posizionamento

Per ogni tipo di nodo è possibile aggiungere proprietà di posizionamento personalizzate da usare nelle applicazioni. NodeType è una proprietà predefinita che è possibile usare senza aggiungerla in modo esplicito.

>[AZURE.NOTE] Per informazioni dettagliate sull'uso dei vincoli di posizionamento, le proprietà dei nodi e le relative definizioni, vedere la sezione relativa ai vincoli di posizionamento e proprietà dei nodi nell'articolo di Gestione risorse del cluster di Service Fabric relativo alla [descrizione del cluster](service-fabric-cluster-resource-manager-cluster-description.md).

### Metriche della capacità

Per ogni tipo di nodo è possibile aggiungere metriche di capacità personalizzate da usare nelle applicazioni per creare report sul carico. Per informazioni dettagliate sull'uso di metriche di capacità per la segnalazione del carico, vedere gli articoli di Gestione risorse del cluster di Service Fabric relativi a [descrizione del cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [metriche e carico](service-fabric-cluster-resource-manager-metrics.md).

### Patch del sistema operativo nelle VM che costituiscono il cluster

Questa funzionalità è stata pianificata per il futuro come funzionalità automatizzata. Attualmente, l'applicazione di patch alle VM è un'operazione manuale che deve essere eseguita su una VM per volta, in modo da non rendere inattive più macchine virtuali contemporaneamente.

### Aggiornamenti del sistema operativo nelle VM che costituiscono il cluster

Se è necessario aggiornare l'immagine del sistema operativo nelle macchine virtuali del cluster, eseguire questa operazione su una VM per volta. L'esecuzione dell'aggiornamento è un'operazione manuale, non è attualmente disponibile alcun tipo di automazione.

## Passaggi successivi

- Informazioni su come eseguire la [scalabilità orizzontale e verticale del cluster](service-fabric-cluster-scale-up-down.md)
- Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md)

<!--Image references--> 
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png

<!---HONumber=AcomDC_0921_2016-->
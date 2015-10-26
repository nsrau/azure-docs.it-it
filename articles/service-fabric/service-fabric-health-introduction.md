<properties
   pageTitle="Introduzione al monitoraggio dell'integrità di Service Fabric"
   description="Questo articolo illustra il modello di monitoraggio dell'integrità di Azure Service Fabric, incluse le entità di integrità, la creazione di report e la valutazione."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/03/2015"
   ms.author="oanapl"/>

# Introduzione al monitoraggio dell'integrità di Service Fabric
Con Service Fabric è stato introdotto un modello di integrità che offre funzionalità di valutazione e reporting dell'integrità dettagliate, flessibili ed estendibili. È incluso il monitoraggio praticamente in tempo reale dello stato del cluster e dei servizi in esso eseguiti. È quindi possibile ottenere facilmente informazioni relative all'integrità e intraprendere azioni correttive dei potenziali problemi prima che si propaghino a catena e causino un numero elevato di interruzioni. Il modello tipico prevede che i servizi inviino report basati sulla situazione locale e che le informazioni vengano aggregate per fornire una panoramica generale a livello di cluster.

I componenti di Service Fabric usano questo modello di integrità per segnalare il proprio stato corrente ed è possibile avvalersi dello stesso meccanismo per segnalare l'integrità delle proprie applicazioni. La qualità e il livello di dettaglio dei report sull'integrità specifici delle proprie condizioni personalizzate determineranno il grado di facilità con cui sarà possibile rilevare e risolvere i problemi relativi all'applicazione in esecuzione.

> [AZURE.NOTE]Il sottosistema di integrità è stato introdotto per gli aggiornamenti monitorati. Service Fabric fornisce aggiornamenti monitorati in grado di aggiornare un cluster o un'applicazione senza tempi di inattività, con un intervento minimo o addirittura nessun intervento da parte dell'utente e con la disponibilità completa del cluster e dell'applicazione. A tale scopo, l'aggiornamento verifica l'integrità in base ai criteri di aggiornamento configurati e prosegue solo se l'integrità rientra nelle soglie desiderate. In caso contrario, l'aggiornamento viene sottoposto a rollback o sospeso automaticamente per consentire agli amministratori di risolvere il problema. Per altre informazioni sugli aggiornamenti delle applicazioni, vedere [questo articolo](service-fabric-application-upgrade.md).

## Health Store
In Health Store vengono mantenute le informazioni di integrità relative alle entità del cluster per facilitarne il recupero e la valutazione. Viene implementato come servizio con stato persistente di Service Fabric per garantire la scalabilità e una disponibilità elevata. Fa parte dell'applicazione fabric:/System e diventa disponibile non appena il cluster è operativo.

## Entità e gerarchia di integrità
Le entità di integrità sono organizzate in una gerarchia logica che acquisisce le interazioni e le dipendenze tra le diverse entità. Le entità e la gerarchia vengono generate automaticamente da Health Store in base ai report inviati dai componenti di Service Fabric.

Le entità di integrità corrispondono alle entità di Service Fabric, ovvero un'entità applicazione di integrità corrisponde a un'istanza di applicazione distribuita nel cluster e un'entità nodo di integrità corrisponde a un nodo del cluster di Service Fabric. La gerarchia di integrità acquisisce le interazioni delle entità di sistema e costituisce la base per una valutazione avanzata dell'integrità. Per apprendere i concetti principali su Service Fabric, vedere la relativa [panoramica tecnica](service-fabric-technical-overview.md). Per altre informazioni sull'applicazione, vedere il [modello di applicazione di Service Fabric](service-fabric-application-model.md).

Le entità e la gerarchia di integrità consentono di eseguire in modo efficace il reporting, il debug e il monitoraggio del cluster e delle applicazioni. Il modello di integrità offre un'accurata rappresentazione **granulare** dell'integrità dei numerosi elementi mobili all'interno del cluster.

![Entità di integrità.][1] Le entità di integrità sono organizzate in una gerarchia basata su relazioni padre-figli.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Le entità di integrità sono le seguenti:

- **Cluster**. Rappresenta l'integrità di un cluster di Service Fabric. I report sull'integrità del cluster illustrano le condizioni che influiscono sull'intero cluster e non possono essere ricondotte a uno o più elementi figlio non integri, ad esempio il passaggio del cluster allo stato split brain a causa di problemi di comunicazione o di partizionamento della rete.

- **Nodo**. Rappresenta l'integrità di un nodo di Service Fabric. I report sull'integrità del nodo illustrano le condizioni che influiscono sulla funzionalità del nodo e in genere su tutte le entità distribuite che vi vengono eseguite. Ad esempio, indicano la presenza di un nodo con spazio su disco insufficiente (o con problemi relativi ad altre proprietà a livello di computer come la memoria o le connessioni) oppure l'esistenza di un nodo non attivo. L'entità nodo è identificata dal nome del nodo (stringa).

- **Applicazione**. Rappresenta l'integrità di un'istanza di applicazione in esecuzione nel cluster. I report sull'integrità dell'applicazione illustrano le condizioni che influiscono sull'integrità generale dell'applicazione e non possono essere ricondotte a singoli elementi figlio (servizi o applicazioni distribuite). Ad esempio, indicano l'interazione end-to-end tra diversi servizi nell'applicazione. L'entità applicazione è identificata dal nome dell'applicazione (URI).

- **Servizio**. Rappresenta l'integrità di un servizio in esecuzione nel cluster. I report sull'integrità del servizio illustrano le condizioni che influiscono sull'integrità generale del servizio e non possono essere ricondotte a una partizione o a una replica. Ad esempio, indicano una configurazione del servizio (quale una porta o una condivisione file esterna) che causa problemi per tutte le partizioni. L'entità servizio è identificata dal nome del servizio (URI).

- **Partizione**. Rappresenta l'integrità di una partizione del servizio. I report sull'integrità della partizione illustrano le condizioni che influiscono sull'intero set di repliche. Ad esempio, indicano un numero di repliche al di sotto del conteggio target o una partizione con perdita del quorum. L'entità partizione è identificata dall'ID partizione (GUID).

- **Replica**. Rappresenta l'integrità di una replica di un servizio con stato o di un'istanza di un servizio senza stato. Si tratta dell'unità più piccola di cui i watchdog e i componenti di sistema possono segnalare la condizione per un'applicazione. Ad esempio, nel caso di servizi con stato, la replica primaria può segnalare se non riesce a replicare le operazioni nelle repliche secondarie o se il processo di replica non procede con il ritmo previsto. Un'istanza senza stato può segnalare se le risorse si stanno esaurendo o se presenta problemi di connettività. L'entità replica è identificata dall'ID partizione (GUID) e dall'ID replica o istanza (valore di tipo long).

- **Applicazione distribuita**. Rappresenta l'integrità di un'*applicazione in esecuzione in un nodo*. I report sull'integrità dell'applicazione distribuita illustrano le condizioni specifiche dell'applicazione nel nodo che non possono essere ricondotte ai pacchetti servizio distribuiti nello stesso nodo. Ad esempio, indicano l'impossibilità di scaricare il pacchetto applicazione nel nodo o il verificarsi di un problema durante la configurazione delle entità di sicurezza dell'applicazione nel nodo. L'applicazione distribuita è identificata dal nome dell'applicazione (URI) e dal nome del nodo (stringa).

- **Pacchetto servizio distribuito**. Rappresenta l'integrità di un pacchetto servizio di un'applicazione in esecuzione in un nodo del cluster. Illustra le condizioni specifiche di un pacchetto servizio che non influiscono sugli altri pacchetti servizio nello stesso nodo per la stessa applicazione. Ad esempio, indica l'impossibilità di avviare un pacchetto di codice nel pacchetto servizio o l'impossibilità di leggere il pacchetto di configurazione. Il pacchetto servizio distribuito è identificato dal nome dell'applicazione (URI), dal nome del nodo (stringa) e dal nome del manifesto del servizio (stringa).

La granularità del modello di integrità facilita il rilevamento e l'eliminazione dei problemi. Ad esempio, se un servizio non risponde, è possibile scegliere di segnalare che l'istanza di applicazione non è integra, ma questa non è una soluzione ideale perché il problema potrebbe non riguardare tutti i servizi all'interno dell'applicazione. Il report dovrebbe essere applicato per il servizio non integro oppure, se altre informazioni puntano a una partizione figlio specifica, per tale partizione. I dati verranno esposti automaticamente attraverso la gerarchia e una partizione non integra sarà visibile a livello del servizio e dell'applicazione. Questo consentirà di individuare ed eliminare più rapidamente la causa principale dei problemi.

La gerarchia di integrità è costituita da relazioni padre-figli. Il cluster è costituito da nodi e applicazioni, le applicazioni dispongono di servizi e applicazioni distribuite, mentre le applicazioni distribuite dispongono di pacchetti servizio distribuiti. I servizi dispongono di partizioni e ogni partizione dispone di una o più repliche. Tra i nodi e le entità distribuite esiste una relazione speciale. Se un nodo non è integro in base alla segnalazione effettuata dal relativo componente di sistema autorevole (servizio Failover Manager), il problema interesserà le applicazioni distribuite, i pacchetti servizio e le repliche distribuiti al suo interno.

La gerarchia di integrità rappresenta lo stato più recente del sistema in base agli ultimi report sull'integrità, che forniscono informazioni quasi in tempo reale. I watchdog interni ed esterni possono segnalare la condizione delle stesse entità in base alla logica specifica dell'applicazione o a condizioni monitorate personalizzate. I report utente coesistono con i report di sistema.

Dedicando tempo a pianificare le modalità di reporting e di risposta allo stato di integrità durante la progettazione del servizio, è possibile eseguire il debug, monitorare e successivamente far funzionare più agevolmente i servizi cloud di grandi dimensioni.

## Stati di integrità
Service Fabric usa tre stati di integrità per indicare se un'entità è integra o meno: Ok, Warning ed Error. Qualsiasi report inviato a Health Store deve specificare uno di questi stati. Come risultato della valutazione dell'integrità viene restituito uno di tali stati.

Gli stati di integrità possibili sono i seguenti:

- Ok: l'entità è integra. Non vengono segnalati problemi noti per l'entità o i relativi elementi figlio (se esistenti).

- Warning: l'entità presenta qualche problema, ma è ancora integra, ovvero si verifica un ritardo imprevisto che non causa alcun problema funzionale. In alcuni casi questa condizione può risolversi senza alcun intervento particolare ed è utile per avere visibilità sulle operazioni in corso. In altri casi tale condizione può trasformarsi in un problema serio senza l'intervento dell'utente.

- Error: l'entità non è integra. È consigliabile intervenire per correggere lo stato dell'entità, la quale non funziona correttamente.

- Unknown: l'entità non è presente in Health Store. Questo risultato può venire restituito da query distribuite come quelle per ottenere le applicazioni o i nodi di Service Fabric. Tali query uniscono i risultati provenienti da più componenti di sistema. Se un altro componente di sistema include un'entità che ancora non ha raggiunto Health Store o che è stata rimossa da Health Store, la query unita inserirà lo stato "Unknown" nel risultato relativo all'integrità.

## Criteri di integrità
Health Store applica criteri di integrità per determinare se un'entità è integra in base ai relativi report e agli elementi figlio.

> [AZURE.NOTE]I criteri di integrità possono essere specificati nel manifesto del cluster (per la valutazione dell'integrità del cluster e dei nodi) o nel manifesto dell'applicazione (per la valutazione dell'applicazione e degli eventuali elementi figlio). Le richieste di valutazione dell'integrità possono anche passare in criteri personalizzati, che verranno usati solo per la valutazione in questione.

Per impostazione predefinita, Service Fabric applica regole severe (tutti gli elementi devono essere integri) per le relazioni gerarchiche padre-figli. Se pertanto anche uno solo degli elementi figlio presenta un evento di mancata integrità, l'elemento padre verrà considerato non integro.

### Criteri di integrità del cluster
I criteri di integrità del cluster vengono usati per valutare lo stato di integrità del cluster e dei nodi e possono essere definiti nel manifesto del cluster. Se non sono presenti, vengono applicati i criteri predefiniti, ovvero 0 errori tollerati. Contengono quanto segue:

- **ConsiderWarningAsError**. Specifica se considerare i report sull'integrità di tipo Warning come errori durante la valutazione dell'integrità. Valore predefinito: False.

- **MaxPercentUnhealthyApplications**. Massima percentuale tollerata di applicazioni che possono risultare non integre prima che per il cluster venga impostato lo stato Error.

- **MaxPercentUnhealthyNodes**. Massima percentuale tollerata di nodi che possono risultare non integri prima che per il cluster venga impostato lo stato Error. Nei cluster di grandi dimensioni vi saranno sempre nodi inattivi o sottoposti a riparazioni, pertanto tale percentuale dovrebbe tenere conto anche di tali situazioni.

Di seguito è riportato un estratto del manifesto di un cluster:

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="0" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
  </Section>
</FabricSettings>
```

### Criteri di integrità dell'applicazione
I criteri di integrità dell'applicazione descrivono le modalità con cui viene eseguita la valutazione dell'aggregazione degli eventi e degli stati degli elementi figlio per un'applicazione e i relativi elementi figlio. Possono essere definiti nel manifesto dell'applicazione (ApplicationManifest.xml) nel pacchetto applicazione. Se non vengono specificati, Service Fabric considererà l'entità come non integra se presenta un report sull'integrità o un elemento figlio con stato Warning o Error. I criteri configurabili sono i seguenti:

- **ConsiderWarningAsError**. Specifica se considerare i report sull'integrità di tipo Warning come errori durante la valutazione dell'integrità. Valore predefinito: False.

- **MaxPercentUnhealthyDeployedApplications**. Massima percentuale tollerata di applicazioni distribuite che possono risultare non integre prima che per l'applicazione venga impostato lo stato Error. Tale percentuale viene calcolata dividendo il numero delle applicazioni distribuite non integre per il numero dei nodi in cui tali applicazioni sono attualmente distribuite nel cluster. Il calcolo viene arrotondato per eccesso in modo da tollerare un errore su un numero limitato di nodi. Valore predefinito: 0%.

- **DefaultServiceTypeHealthPolicy**. Specifica i criteri di integrità predefiniti per il tipo di servizio, che sostituiranno i criteri di integrità predefiniti usati per tutti i tipi di servizi nell'applicazione.

- **ServiceTypeHealthPolicyMap**. Mappa con i criteri di integrità per il singolo tipo di servizio, che sostituiranno i criteri di integrità predefiniti per i tipi di servizi specificati. Ad esempio, in un'applicazione contenente un tipo di servizio Gateway senza stato e un tipo di servizio Engine con stato, i criteri di integrità per il servizio senza stato e quelli per il servizio con stato possono essere configurati in modo diverso. Se si specificano criteri per i tipi di servizi, si dispone di un controllo più granulare sull'integrità del servizio.

### Criteri di integrità del tipo di servizio
I criteri di integrità del tipo di servizio specificano come valutare e aggregare gli elementi figlio del servizio. Contengono quanto segue:

- **MaxPercentUnhealthyPartitionsPerService**. Massima percentuale tollerata di partizioni che possono risultare non integre prima che un servizio venga considerato non integro. Valore predefinito: 0%.

- **MaxPercentUnhealthyReplicasPerPartition**. Massima percentuale tollerata di repliche che possono risultare non integre prima che una partizione venga considerata non integra. Valore predefinito: 0%.

- **MaxPercentUnhealthyServices**. Massima percentuale tollerata di servizi che possono risultare non integri prima che l'applicazione venga considerata non integra. Valore predefinito: 0%.

Di seguito è riportato un estratto del manifesto di un'applicazione:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## Valutazione dell'integrità
Gli utenti o i servizi automatizzati possono valutare l'integrità di qualsiasi entità in qualsiasi momento. Per valutare l'integrità di un'entità, Health Store aggrega tutti i report sull'integrità di tale entità e valuta tutti i relativi elementi figlio (se esistenti). L'algoritmo di aggregazione dell'integrità usa i criteri di integrità che specificano come valutare i report e come aggregare gli stati di integrità degli elementi figlio (quando disponibili).

### Aggregazione dei report sull'integrità
Per una stessa entità possono essere disponibili più report sull'integrità inviati da generatori diversi (componenti di sistema o watchdog) relativamente a proprietà diverse. Per l'aggregazione vengono usati i criteri di integrità associati, in particolare il membro ConsiderWarningAsError dei criteri di integrità dell'applicazione o del cluster, che specifica come valutare i report di tipo Warning.

Lo stato di integrità aggregato viene attivato dai report sull'integrità **peggiori** relativi all'entità. Se è presente almeno un report sull'integrità di tipo Error, lo stato di integrità aggregato sarà Error.

![Aggregazione dei report sull'integrità con report di tipo Error.][2]

Il report sull'integrità di tipo Error determina lo stato Error per l'entità.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se non sono presenti report di tipo Error ma uno o più report di tipo Warning, lo stato di integrità aggregato sarà Warning o Error, a seconda del flag dei criteri ConsiderWarningAsError.

![Aggregazione dei report sull'integrità con report di tipo Warning e ConsiderWarningAsError false.][3]

Aggregazione dei report sull'integrità con report di tipo Warning e ConsiderWarningAsError false (valore predefinito).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### Aggregazione dell'integrità degli elementi figlio
Lo stato di integrità aggregato di un'entità riflette gli stati di integrità degli elementi figlio (se esistenti). Per l'algoritmo di aggregazione degli stati degli elementi figlio vengono usati i criteri di integrità applicabili in base al tipo di entità.

![Aggregazione dell'integrità delle entità figlio.][4]

Aggregazione degli elementi figlio in base ai criteri di integrità.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Dopo aver valutato tutti gli elementi figlio, Health Store aggrega gli stati di integrità in base alla massima percentuale di elementi non integri configurata nei criteri secondo il tipo di entità e di elemento figlio.

- Se lo stato è Ok per tutti gli elementi figlio, lo stato di integrità aggregato di tali elementi sarà Ok.

- Se gli elementi figlio hanno Ok e Warning come stati, lo stato di integrità aggregato di tali elementi sarà Warning.

- Se vi sono elementi figlio con stato Error oltre la massima percentuale di elementi figlio non integri consentita, lo stato di integrità aggregato sarà Error.

- Se gli elementi figlio con stato Error non superano la massima percentuale di elementi figlio non integri consentita, lo stato di integrità aggregato sarà Warning.

## Creazione di report sull'integrità
I componenti di sistema e i watchdog interni o esterni possono segnalare tramite report l'integrità delle entità di Service Fabric. I *generatori di report* determinano **localmente** l'integrità dell'entità monitorata in base ad alcune condizioni sottoposte a monitoraggio. Non considerano alcuno stato globale o dato aggregato perché diventerebbero organismi complessi che devono tenere conto di numerosi aspetti per decidere quali informazioni inviare.

Per inviare i dati di integrità a Health Store, i generatori di report devono identificare l'entità interessata e creare un report sull'integrità. Il report può quindi essere inviato tramite API con FabricClient.HealthManager.ReportHealth, tramite PowerShell o tramite REST.

### Report sull'integrità
I report sull'integrità per ognuna delle entità incluse nel cluster contengono le informazioni seguenti:

- SourceId. Stringa che identifica in modo univoco il generatore di report per l'evento di integrità.

- Identificatore dell'entità. Identifica l'entità a cui viene applicato il report. Varia in base al [tipo di entità](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster: nessuno.

  - Nodo: nome del nodo (stringa).

  - Applicazione: nome dell'applicazione (URI). Rappresenta il nome dell'istanza di applicazione distribuita nel cluster.

  - Servizio: nome del servizio (URI). Rappresenta il nome dell'istanza di servizio distribuita nel cluster.

  - Partizione: ID della partizione (GUID). Rappresenta l'identificatore univoco della partizione.

  - Replica: ID della replica del servizio con stato o ID dell'istanza del servizio senza stato (Int64).

  - Applicazione distribuita: nome dell'applicazione (URI) e nome del nodo (stringa).

  - Pacchetto servizio distribuito: nome dell'applicazione (URI), nome del nodo (stringa) e nome del manifesto del servizio (stringa).

- Property. *Stringa* (non un'enumerazione fissa) che consente al generatore di report di categorizzare l'evento di integrità per una proprietà specifica dell'entità. Ad esempio, il generatore di report A può segnalare l'integrità di Nodo01 in base alla proprietà "storage", mentre il generatore di report può segnalare l'integrità di Nodo01 in base alla proprietà "connectivity". Entrambi questi report vengono considerati come eventi di integrità separati in Health Store per l'entità Nodo01.

- Description. Stringa che consente al generatore di report di fornire informazioni dettagliate sull'evento di integrità. SourceId, Property e HealthState dovrebbero descrivere l'intero report. La descrizione aggiunge informazioni leggibili sul report per facilitarne la comprensione da parte degli amministratori e degli utenti.

- HealthState. [Enumerazione](service-fabric-health-introduction.md#health-states) che descrive lo stato di integrità del report. I valori accettati sono OK, Warning ed Error.

- TimeToLive. Intervallo di tempo che indica per quanto è valido il report sull'integrità. Insieme a RemoveWhenExpired, consente a Health Store di sapere come valutare gli eventi scaduti. Per impostazione predefinita, il valore è infinito e quindi il report è sempre valido.

- RemoveWhenExpired. Valore booleano. Se è impostato su true, il report sull'integrità scaduto verrà rimosso automaticamente da Health Store e non inciderà sulla valutazione dell'integrità dell'entità. Viene usato quando il report è valido solo per un periodo di tempo e il generatore non ha necessità di eliminarlo esplicitamente. Viene usato anche per eliminare i report da Health Store. Ad esempio, un watchdog viene modificato e smette di inviare report con la proprietà e l'origine precedenti. Può pertanto inviare un report con un basso valore TimeToLive e RemoveWhenExpired per eliminare qualsiasi stato precedente da Health Store. Se è impostato su false, il report scaduto viene considerato come errore nella valutazione dell'integrità. Segnala a Health Store che l'origine dovrebbe inviare periodicamente informazioni su tale proprietà. In caso contrario, è probabile che vi sia un problema relativo al watchdog. Lo stato di quest'ultimo viene acquisito considerando l'evento come un errore.

- SequenceNumber. Numero intero positivo che deve essere sempre crescente, in quanto rappresenta l'ordine dei report. Viene usato da Health Store per rilevare i report non aggiornati, ricevuti in ritardo a causa di ritardi sulla rete o di altri problemi. I report vengono rifiutati se il numero di sequenza è inferiore o uguale all'ultimo applicato per la stessa entità, origine e proprietà. Il numero di sequenza viene generato automaticamente, se non specificato È necessario inserire il numero di sequenza solo per report relativi alle transizioni di stato, in quanto l'origine deve ricordare i report che ha inviato e rendere persistenti le informazioni per il ripristino in caso di failover.

SourceId, l'identificatore dell'entità, Property e HealthState sono obbligatori per tutti i report sull'integrità. La stringa SourceId non può iniziare con il prefisso "System.", che è riservato per i report di sistema. Per la stessa entità, è disponibile un solo report per la stessa origine e la stessa proprietà. Se vengono generati più report per la stessa origine e la stessa proprietà, si sostituiscono l'uno all'altro sul lato del client di integrità (se sono in batch) o sul lato di Health Store. La sostituzione avviene in base al numero di sequenza, pertanto i report più recenti (con un numero di sequenza più elevato) sostituiranno quelli meno recenti.

### Eventi di integrità
Health Store internamente mantiene gli eventi di integrità, contenenti tutte le informazioni dei report, nonché metadati aggiuntivi come l'ora in cui il report è stato fornito al client di integrità e l'ora in cui è stato modificato sul lato server. Gli eventi di integrità vengono restituiti dalle [query sull'integrità](service-fabric-view-entities-aggregated-health.md#health-queries).

I metadati aggiunti includono quanto segue:

- SourceUtcTimestamp: ora in cui il report è stato fornito al client di integrità (UTC).

- LastModifiedUtcTimestamp: ora in cui il report è stato modificato l'ultima volta sul lato server (UTC).

- IsExpired: flag che indica se il report era scaduto quando la query è stata eseguita da Health Store. Un evento può risultare scaduto solo se RemoveWhenExpired è false, altrimenti l'evento non viene restituito dalla query perché viene rimosso da Health Store.

- LastOkTransitionAt, LastWarningTransitionAt, LastErrorTransitionAt: ultima volta in cui si sono verificate transizioni Ok/Warning/Error. Questi campi forniscono la cronologia della transizione degli stati di integrità per l'evento.

I campi di transizione dello stato possono essere usati per avere segnalazioni più sofisticate o informazioni "cronologiche" sull'evento di integrità. Essi consentono l'uso di scenari come i seguenti:

- Avviso quando lo stato di una proprietà è impostato su Warning/Error da più di X minuti. In questo modo si evitano avvisi per condizioni temporanee. Ad esempio, se si vuole un avviso quando lo stato di integrità rimane impostato su Warning per più di 5 minuti, è possibile usare: (HealthState == Warning and Now - LastWarningTransitionTime
> 5 minuti).

- Avviso solo per le condizioni cambiate negli ultimi X minuti. Se un report segnala lo stato Error anche da prima, può essere ignorato perché il problema era già stato segnalato in precedenza.

- Se una proprietà si alterna tra lo stato Warning e lo stato Error, determinare per quanto tempo è risultata non integra, ovvero non Ok. Ad esempio, se si vuole un avviso quando la proprietà non risulta integra per più di 5 minuti, è possibile usare: (HealthState != Ok and Now - LastOkTransitionTime > 5 minutes).

## Esempio - Report e valutazione dell'integrità dell'applicazione
L'esempio seguente invia un report sull'integrità tramite PowerShell all'applicazione fabric:/WordCount dall'origine MyWatchdog. Il report sull'integrità contiene informazioni relative alla proprietà di integrità Availability con stato di integrità Error e TTL infinito. Viene quindi eseguita una query relativa all'integrità dell'applicazione che restituirà Error come stato di integrità aggregato e l'evento di integrità segnalato come parte dell'elenco degli eventi di integrità.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 5102
                                  SentAt                : 4/15/2015 5:29:15 PM
                                  ReceivedAt            : 4/15/2015 5:29:15 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/15/2015 5:29:15 PM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 130736794527105907
                                  SentAt                : 4/16/2015 5:37:32 PM
                                  ReceivedAt            : 4/16/2015 5:37:32 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Error = 4/16/2015 5:37:32 PM

```

## Uso del modello di integrità
Il modello di integrità consente la scalabilità dei servizi cloud e della piattaforma Service Fabric sottostante perché il monitoraggio e la determinazione dell'integrità vengono distribuiti tra i diversi monitor all'interno del cluster. Altri sistemi dispongono di un unico servizio centralizzato a livello di cluster che analizza tutte le informazioni *potenzialmente* utili emesse dai servizi. Ciò ne impedisce la scalabilità e non consente la raccolta di informazioni molto specifiche per identificare i problemi effettivi e possibili risalendo alla causa principale.

Il modello di integrità viene usato in larga misura per il monitoraggio e la diagnosi, per la valutazione dell'integrità del cluster e dell'applicazione e per aggiornamenti monitorati. Altri servizi usano i dati di integrità per eseguire riparazioni automatiche, generare la cronologia dell'integrità del cluster e inviare avvisi in determinate condizioni.

## Passaggi successivi
[Come visualizzare i report sull'integrità di Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Uso dei report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Aggiunta di report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)

[Come eseguire il monitoraggio e la diagnosi dei servizi localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=Oct15_HO3-->
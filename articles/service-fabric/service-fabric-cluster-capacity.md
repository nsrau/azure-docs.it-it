<properties
   pageTitle="Pianificazione della capacità del cluster di Service Fabric | Microsoft Azure"
   description="Considerazioni sulla pianificazione della capacità del cluster Service Fabric."
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
   ms.date="03/28/2016"
   ms.author="chackdan"/>


# Considerazioni sulla pianificazione della capacità del cluster Service Fabric

La pianificazione della capacità è un passaggio importante per qualsiasi distribuzione di produzione. Ecco alcuni aspetti da considerare nell'ambito di tale processo.

- Numero di tipi di nodo con cui il cluster deve iniziare
- Proprietà di ogni tipo di nodo (dimensioni, primarie, per Internet, numero di VM e così via)
- Caratteristiche di affidabilità e durabilità del cluster

Ogni aspetto verrà ora esaminato brevemente.

## Numero di tipi di nodo con cui il cluster deve iniziare

Prima di tutto è necessario stabilire per che cosa verrà usato il cluster che si sta creando e quali tipologie di applicazioni si intende distribuire nel cluster. Se lo scopo del cluster non è chiaro, è molto probabile che non si sia ancora pronti per iniziare il processo di pianificazione della capacità.

Stabilire il numero di tipi di nodo con cui il cluster deve iniziare. Di ogni tipo di nodo viene eseguito il mapping a un set di scalabilità di macchine virtuali. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. Quindi la decisione relativa al numero di tipi di nodo si basa essenzialmente sulle considerazioni seguenti:

- L'applicazione ha più servizi, alcuni dei quali devono essere pubblici o per Internet? Le applicazioni tipiche contengono un servizio gateway front-end che riceve input da un client e uno o più servizi back-end che comunicano con i servizi front-end. In questo caso quindi si finirà per avere almeno due tipi di nodi.

- I servizi (che costituiscono l'applicazione) hanno esigenze diverse per l'infrastruttura, ad esempio cicli di CPU più elevati o una quantità maggiore di RAM? Ad esempio, si supponga che l'applicazione che si vuole distribuire contenga un servizio front-end e un servizio back-end. Il servizio front-end può essere eseguito in VM più piccole (ad esempio, di dimensioni D2), con porte aperte a Internet, invece il servizio back-end è a elevato utilizzo di calcolo e deve essere eseguito in VM più grandi (ad esempio, di dimensioni D4, D6, D15 e così via), senza connessione a Internet.

 In questo esempio, anche se è possibile decidere di inserire tutti i servizi in un unico tipo di nodo, è consigliabile inserirli in un cluster con due tipi di nodo, perché in questo modo ogni tipo di nodo può avere proprietà distinte, ad esempio la connettività Internet o le dimensioni delle VM. Il numero di VM può essere ridimensionato anche in modo indipendente.

- Poiché non è possibile prevedere cosa accadrà in futuro, è importante basarsi sulle condizioni attuali per decidere il numero di tipi di nodo necessari alle applicazioni per iniziare. Sarà sempre possibile aggiungere o rimuovere i tipi di nodi in seguito. Un cluster di Service Fabric deve avere almeno un tipo di nodo.

## Proprietà di ogni tipo di nodo

I **tipi di nodo** possono essere paragonati ai ruoli nei servizi cloud, poiché definiscono le dimensioni delle VM, il numero di VM e le relative proprietà. Ogni tipo di nodo definito in un cluster di Service Fabric viene configurato come set di scalabilità di macchine virtuali separato. I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Essendo definito come set di scalabilità di macchine virtuali distinto, ogni tipo di nodo può aumentare o ridurre le prestazioni in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse.

Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario, ovvero il primo che si definisce nel portale, deve essere costituito da almeno cinque macchine virtuali per i cluster usati per i carichi di lavoro di produzione (o da almeno tre VM per i cluster di test). Se si sta creando il cluster con un modello di Azure Resource Manager, nella definizione del tipo di nodo sarà presente un attributo **Primario**. Nel tipo di nodo primario vengono inseriti i servizi di sistema di Service Fabric.

### Tipo di nodo primario
Per un cluster con più tipi di nodo, sarà necessario sceglierne uno come primario. Ecco le caratteristiche di un tipo di nodo primario:

- Le dimensioni minime delle macchine virtuali per il tipo di nodo primario sono determinate dal livello di durabilità scelto. Il valore predefinito per il livello di durabilità è Bronze. Per sapere che cos'è il livello di durabilità e su quali valori può essere impostato, scorrere verso il basso.  

- Il numero minimo delle macchine virtuali per il tipo di nodo primario è determinato dal livello di affidabilità scelto. Il valore predefinito per il livello di affidabilità è Silver. Per sapere che cos'è il livello di affidabilità e su quali valori può essere impostato, scorrere verso il basso.

- I servizi di sistema di Service Fabric (ad esempio, il servizio Cluster Manager o il servizio Image Store) vengono inseriti nel tipo di nodo primario e quindi l'affidabilità e la durabilità del cluster vengono determinate dal valore del livello di affidabilità e dal valore del livello di durabilità selezionato per il tipo di nodo primario.

![Screenshot di un cluster con due tipi di nodo][SystemServices]


### Tipo di nodo non primario
Per un cluster con più tipi di nodo, sarà presente un tipo di nodo primario, mentre gli altri saranno non primari. Ecco le caratteristiche di un tipo di nodo non primario:

- Le dimensioni minime delle macchine virtuali per questo tipo di nodo sono determinate dal livello di durabilità scelto. Il valore predefinito per il livello di durabilità è Bronze. Per sapere che cos'è il livello di durabilità e su quali valori può essere impostato, scorrere verso il basso.  

- Il numero minimo di VM per questo tipo di nodo può essere uno. È tuttavia consigliabile scegliere questo numero in base al numero di repliche dell'applicazione o dei servizi che si vuole eseguire in questo tipo di nodo. Il numero di VM in un tipo di nodo può essere aumentato dopo avere distribuito il cluster.


## Caratteristiche di durabilità del cluster

Il livello di durabilità viene usato per indicare al sistema i privilegi delle VM rispetto all'infrastruttura di Azure sottostante. Nel tipo di nodo primario questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di VM (ad esempio, il riavvio di una VM, il re-imaging di una VM o la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi di sistema e i servizi con stato. Nei tipi di nodo non primari questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di VM (ad esempio, il riavvio di una VM, il re-imaging di una VM, la migrazione di una VM e così via) che hanno effetto sui requisiti relativi al quorum per i servizi con stato in esecuzione.

Questo privilegio viene espresso con i valori seguenti:

- Gold: i processi dell'infrastruttura possono essere sospesi per una durata di 2 ore per ogni dominio di aggiornamento

- Silver: i processi dell'infrastruttura possono essere sospesi per una durata di 30 minuti per ogni dominio di aggiornamento

- Bronze: nessun privilegio.

## Caratteristiche di affidabilità del cluster

Il livello di affidabilità viene usato per impostare il numero di repliche dei servizi di sistema che si vuole eseguire in questo cluster nel tipo di nodo primario. I servizi di sistema nel cluster sono tanto più affidabili quanto più elevato è il numero di repliche.

Il livello di affidabilità può avere i valori seguenti.

- Platinum: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a 9

- Gold: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a 7

- Silver: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a 5

- Bronze: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a 3

Si noti che il livello di affidabilità scelto determina il numero minimo di nodi che deve avere il tipo di nodo primario. Il livello non ha effetto sulle dimensioni massime del cluster. È possibile, ad esempio, avere un cluster a 20 nodi, in esecuzione con affidabilità Bronze.

Si noti anche che in qualsiasi momento è possibile scegliere di aggiornare l'affidabilità del cluster passando da un livello a un altro. Così facendo si attiveranno gli aggiornamenti del cluster necessari per modificare il totale di set di repliche dei servizi di sistema.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Una volta completata la pianificazione della capacità e configurato un cluster, leggere quanto segue:
- [Sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md)
- [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

<!---HONumber=AcomDC_0330_2016-->
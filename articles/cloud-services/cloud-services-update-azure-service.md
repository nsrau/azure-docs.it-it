---
title: Come aggiornare un servizio cloud | Documentazione Microsoft
description: "Informazioni su come aggiornare i servizi cloud in Azure. Informazioni su come viene eseguito un aggiornamento in un servizio cloud per assicurare la disponibilità."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: b159d3583c24e36c2803d7d02deca1415669d054
ms.openlocfilehash: ebc5461177df5b5a16ab9b5668f5fda890ee11a4


---
# <a name="how-to-update-a-cloud-service"></a>Come aggiornare un servizio cloud
## <a name="overview"></a>Overview
In generale, l'aggiornamento di un servizio cloud, inclusi i ruoli e il sistema operativo guest, è un processo in tre passaggi. Prima devono essere caricati i file binari e di configurazione per la nuova versione del nuovo servizio cloud o del sistema operativo. Quindi Azure riserva le risorse di calcolo e di rete per il servizio cloud in base ai requisiti della nuova versione del servizio cloud. Infine Azure esegue un aggiornamento in sequenza per aggiornare in modo incrementale il tenant alla nuova versione o al nuovo sistema operativo guest, mantenendo nello stesso tempo la disponibilità. Questo articolo illustra i dettagli dell'ultimo passaggio, l'aggiornamento in sequenza.

## <a name="update-an-azure-service"></a>Aggiornare un servizio di Azure
Azure organizza le istanze del ruolo in raggruppamenti logici chiamati domini di aggiornamento. I domini di aggiornamento sono set logici di istanze del ruolo aggiornate come gruppo.  Azure aggiorna un servizio cloud un dominio di aggiornamento alla volta, per consentire alle istanze negli altri domini di aggiornamento di continuare a gestire il traffico.

Il numero predefinito di domini di aggiornamento è 5. È possibile specificare un numero diverso di domini di aggiornamento includendo l'attributo upgradeDomainCount nel file di definizione del servizio (.csdef). Per altre informazioni sull'attributo upgradeDomainCount, vedere [WebRole Schema](https://msdn.microsoft.com/library/azure/gg557553.aspx) (Schema WebRole) o [WorkerRole Schema](https://msdn.microsoft.com/library/azure/gg557552.aspx) (Schema WorkerRole).

Quando si esegue un aggiornamento sul posto di uno o più ruoli nel servizio, Azure aggiorna i set di istanze del ruolo in base al dominio di aggiornamento a cui appartengono. Azure aggiorna tutte le istanze in un determinato dominio di aggiornamento (arrestandole, aggiornandole, riportandole online), quindi passa al dominio successivo. Arrestando solo le istanze in esecuzione nel dominio di aggiornamento corrente, Azure fa in modo che un aggiornamento venga eseguito con il minor impatto possibile sul servizio in esecuzione. Per altre informazioni, vedere [Come avviene un aggiornamento](#howanupgradeproceeds) .

> [!NOTE]
> Anche se i termini inglesi **update** e **upgrade** hanno un significato leggermente diverso nel contesto di Azure, sono stati entrambi tradotti con il termine italiano "aggiornamento" in relazione ai processi e alle descrizioni delle funzionalità di questo documento.
>
>

Il servizio deve definire almeno due istanze di un ruolo per aggiornarlo sul posto senza tempi di inattività. Se il servizio è costituito da una sola istanza di un ruolo, il servizio non sarà disponibile fino al termine dell'aggiornamento sul posto.

Questo argomento contiene le informazioni seguenti sugli aggiornamenti di Azure:

* [Modifiche consentite al servizio durante un aggiornamento](#AllowedChanges)
* [Come avviene un aggiornamento](#howanupgradeproceeds)
* [Ripristino dello stato precedente di un aggiornamento](#RollbackofanUpdate)
* [Avvio di più operazioni di mutazione durante una distribuzione](#multiplemutatingoperations)
* [Distribuzione di ruoli nei domini di aggiornamento](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Modifiche consentite al servizio durante un aggiornamento
La tabella seguente mostra le modifiche consentite a un servizio durante un aggiornamento:

| Modifiche consentite a hosting, servizi e ruoli | Aggiornamento sul posto | Gestione temporanea (scambio indirizzi VIP) | Eliminazione e ridistribuzione |
| --- | --- | --- | --- |
| Versione del sistema operativo |Sì |Sì |Sì |
| Livello di attendibilità .NET |Sì |Sì |Sì |
| Dimensioni macchina virtuale<sup>1</sup> |Sì<sup>2</sup> |Sì |Sì |
| Impostazioni di archiviazione locali |Solo aumento<sup>2</sup> |Sì |Sì |
| Aggiungere o rimuovere ruoli in un servizio |Sì |Sì |Sì |
| Numero di istanze di un particolare ruolo |Sì |Sì |Sì |
| Numero o tipo di endpoint per un servizio |Sì<sup>2</sup> |No |Sì |
| Nomi e i valori delle impostazioni di configurazione |Sì |Sì |Sì |
| Valori (ma non nomi) delle impostazioni di configurazione |Sì |Sì |Sì |
| Aggiungere nuovi certificati |Sì |Sì |Sì |
| Modificare i certificati esistenti |Sì |Sì |Sì |
| Distribuire nuovo codice |Sì |Sì |Sì |

<sup>1</sup>Modifica delle dimensioni limitata al sottoinsieme di dimensioni disponibili per il servizio cloud.

<sup>2</sup>Richiede Azure SDK 1.5 o versioni successive.

> [!WARNING]
> La modifica della dimensione di una macchina virtuale eliminerà i dati locali.
>
>

Gli elementi seguenti non sono supportati durante un aggiornamento:

* Modifica del nome di un ruolo. Rimuovere e quindi aggiungere il ruolo con il nuovo nome.
* Modifica del conteggio dei domini di aggiornamento.
* Riduzione della dimensione delle risorse locali.

Se intende eseguire altri aggiornamenti alla definizione del servizio, ad esempio la riduzione della dimensione di una risorsa locale, è necessario eseguire invece un aggiornamento dello scambio di indirizzi VIP. Per altre informazioni, vedere [Scambiare la distribuzione](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Come avviene un aggiornamento
È possibile decidere se aggiornare tutti i ruoli del servizio oppure uno solo. In entrambi i casi, tutte le istanze di ogni ruolo che verrà aggiornato e appartiene al primo dominio di aggiornamento verranno arrestate, aggiornate e riportate online. Quando sono di nuovo online, vengono arrestate, aggiornate e riportate online le istanze nel secondo dominio di aggiornamento. Un servizio cloud non può avere più di un aggiornamento attivo per volta. L'aggiornamento viene sempre eseguito usando la versione più recente del servizio cloud.

Il diagramma seguente illustra come avviene l'aggiornamento se si aggiornano tutti i ruoli nel servizio:

![Aggiornare il servizio](media/cloud-services-update-azure-service/IC345879.png "Upgrade service")

Il diagramma successivo illustra come avviene l'aggiornamento se si aggiorna un solo ruolo:

![Aggiornare il ruolo](media/cloud-services-update-azure-service/IC345880.png "Upgrade role")  

Durante un aggiornamento automatico, il controller di infrastruttura di Azure valuta periodicamente l'integrità del servizio cloud per determinare quando è sicuro passare al dominio di aggiornamento successivo. Questa valutazione dell'integrità viene eseguita per ogni singolo ruolo e considera solo le istanze nella versione più recente (ad esempio, le istanze dei domini di aggiornamento già analizzati). Verifica che un numero minimo di istanze del ruolo, per ogni ruolo, abbia raggiunto uno stato finale soddisfacente.

### <a name="role-instance-start-timeout"></a>Timeout dell'avvio dell’istanza del ruolo
Il controller di infrastruttura attende 30 minuti affinché ogni istanza del ruolo raggiunga uno stato avviato. Se la durata del timeout scade, il controller di infrastruttura prosegue all'istanza del ruolo successivo.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Impatto sui dati delle unità durante gli aggiornamenti dei servizi cloud

Quando si aggiorna un servizio da una sola istanza a più istanze, il servizio sarà inattivo durante l'esecuzione dell'aggiornamento a causa della modalità di aggiornamento dei servizi in Azure. Il contratto di servizio che garantisce la disponibilità del servizio si applica solo ai servizi distribuiti con più di una istanza. L'elenco seguente descrive come ogni scenario di aggiornamento di un servizio di Azure influisce sui dati di ogni unità:

|Scenario|Unità C|Unità D|Unità E|
|--------|-------|-------|-------|
|Riavvio VM|Mantenuta|Mantenuta|Mantenuta|
|Riavvio del portale|Mantenuta|Mantenuta|Eliminata|
|Ricreazione dell'immagine del portale|Mantenuta|Eliminata|Eliminata|
|Aggiornamento sul posto|Mantenuta|Mantenuta|Eliminata|
|Migrazione di un nodo|Eliminata|Eliminata|Eliminata|

Si noti che nell'elenco precedente l'unità E: rappresenta l'unità radice del ruolo e non dovrebbe essere hardcoded. Per rappresentare l'unità, usare invece la variabile di ambiente **%RoleRoot%**.

Per ridurre al minimo il tempo di inattività durante l'aggiornamento di un servizio a istanza singola, distribuire un nuovo servizio a più istanze nel server di staging ed eseguire uno scambio di indirizzi VIP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Ripristino dello stato precedente di un aggiornamento
Azure offre flessibilità nella gestione dei servizi durante un aggiornamento perché consente di avviare altre operazioni su un servizio, dopo che la richiesta di aggiornamento iniziale è stata accettata dal controller di infrastruttura di Azure. Un ripristino dello stato precedente può essere eseguito solo quando una modifica della configurazione o un aggiornamento è nello stato **in corso** durante la distribuzione. Un aggiornamento viene considerato in corso finché almeno un'istanza del servizio non è ancora stata aggiornata alla nuova versione. Per verificare se un ripristino dello stato precedente è consentito, controllare che il valore del flag RollbackAllowed, restituito dalle operazioni [Get Deployment](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Get Cloud Service Properties](https://msdn.microsoft.com/library/azure/ee460806.aspx), sia impostato su true.

> [!NOTE]
> Chiamare ripristino dello stato precedente è utile solo per un aggiornamento **sul posto** perché gli aggiornamenti con scambio di indirizzo VIP comportano la sostituzione di un'intera istanza in esecuzione del servizio con un'altra.
>
>

Il ripristino dello stato precedente di un aggiornamento in corso ha gli effetti seguenti sulla distribuzione:

* Le istanze del ruolo che non erano ancora state aggiornate alla nuova versione non vengono aggiornate, perché tali istanze stanno già eseguendo la versione di destinazione del servizio.
* Per le istanze del ruolo che erano già state aggiornate alla nuova versione del file del pacchetto del servizio (\*.cspkg), del file di configurazione del servizio (\*.cscfg) o di entrambi i file viene ripristinata la versione pre-aggiornamento di questi file.

Questa funzionalità viene fornita dalle funzioni seguenti:

* Operazione [Rollback Update Or Upgrade](https://msdn.microsoft.com/library/azure/hh403977.aspx), che può essere chiamata per un aggiornamento della configurazione (attivato chiamando [Change Deployment Configuration](https://msdn.microsoft.com/library/azure/ee460809.aspx)) o per un aggiornamento (attivato chiamando [Upgrade Deployment](https://msdn.microsoft.com/library/azure/ee460793.aspx)) purché sia presente almeno un'istanza nel servizio non ancora aggiornata alla nuova versione.
* Elemento Locked ed elemento RollbackAllowed, restituiti come parte del corpo della risposta delle operazioni [Get Deployment](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Get Cloud Service Properties](https://msdn.microsoft.com/library/azure/ee460806.aspx):

  1. L'elemento Locked consente di rilevare quando un'operazione di mutazione può essere richiamata in una determinata distribuzione.
  2. L'elemento RollbackAllowed consente di rilevare quando l'operazione [Rollback Update Or Upgrade](https://msdn.microsoft.com/library/azure/hh403977.aspx) può essere chiamata in una determinata distribuzione.

  Per eseguire un ripristino dello stato precedente, non è necessario controllare entrambi gli elementi Locked e RollbackAllowed. È sufficiente verificare che RollbackAllowed sia impostato su true. Questi elementi vengono restituiti solo se questi metodi vengono richiamati usando l'intestazione della richiesta impostata su "x-ms-version: 2011-10-01" o versione successiva. Per altre informazioni sul controllo delle versioni delle intestazioni, vedere [Controllo delle versioni di gestione del servizio](https://msdn.microsoft.com/library/azure/gg592580.aspx).

In alcune situazioni un ripristino dello stato precedente di un aggiornamento non è supportato, come nei casi seguenti:

* Riduzione nelle risorse locali: se l'aggiornamento aumenta le risorse locali per un ruolo, la piattaforma Azure non consente il ripristino dello stato precedente.
* Limitazioni della quota: se l'aggiornamento ha comportato un'operazione di riduzione, è possibile che la quota di calcolo non sia più sufficiente per completare l'operazione di ripristino dello stato precedente. A ogni sottoscrizione di Azure è associata una quota che specifica il numero massimo di memorie centrali che possono essere utilizzate da tutti i servizi ospitati appartenenti a tale sottoscrizione. Se l'esecuzione di un ripristino dello stato precedente di un determinato aggiornamento farà superare la quota prevista per la sottoscrizione, il ripristino dello stato precedente non verrà abilitato.
* Race condition: se l'aggiornamento iniziale è stato completato, un ripristino dello stato precedente non è possibile.

Il ripristino dello stato precedente di un aggiornamento può essere utile, ad esempio, se si usa l'operazione [Upgrade Deployment](https://msdn.microsoft.com/library/azure/ee460793.aspx) in modalità manuale per controllare la frequenza con cui un aggiornamento sul posto principale del servizio ospitato di Azure viene distribuito.

Durante la distribuzione dell'aggiornamento chiamare [Upgrade Deployment](https://msdn.microsoft.com/library/azure/ee460793.aspx) in modalità manuale e iniziare ad analizzare i domini di aggiornamento. Se a un certo punto, durante il monitoraggio dell'aggiornamento, si nota che alcune istanze del ruolo nei primi domini di aggiornamento esaminati non rispondono più, chiamare l'operazione [Rollback Update Or Upgrade](https://msdn.microsoft.com/library/azure/hh403977.aspx) nella distribuzione, che lascerà invariate le istanze non ancora aggiornate e ripristinerà il Service Pack e la configurazione precedenti delle istanze aggiornate.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Avvio di più operazioni di mutazione durante una distribuzione
In alcuni casi potrebbe essere necessario avviare più operazioni di mutazione simultanee in una distribuzione in corso. Ad esempio, eseguendo l'aggiornamento di un servizio, mentre l'aggiornamento viene distribuito nel servizio, potrebbe essere necessario apportare alcune modifiche, come il ripristino dello stato precedente dell'aggiornamento, l'applicazione di un aggiornamento diverso o anche l'eliminazione della distribuzione. Questa necessità, ad esempio, potrebbe presentarsi se l'aggiornamento di un servizio contiene un codice con errori che fa arrestare più volte in modo anomalo un'istanza del ruolo aggiornata. In questo caso, il controller di infrastruttura di Azure non potrà far proseguire l'applicazione di tale aggiornamento perché il numero di istanze integre nel dominio aggiornato non è sufficiente. Questa condizione viene chiamata *distribuzione bloccata*. Per sbloccare la distribuzione, ripristinare lo stato precedente dell'aggiornamento o applicare un nuovo aggiornamento sopra quello non riuscito.

Dopo che il controller di infrastruttura di Azure ha ricevuto la richiesta iniziale di aggiornare il servizio, è possibile avviare le operazioni di mutazione successive, vale a dire che non è necessario attendere il completamento dell'operazione iniziale prima di poter avviare un'altra operazione di mutazione.

L'avvio di una seconda operazione di aggiornamento mentre il primo aggiornamento è in corso sarà simile all'operazione di ripristino dello stato precedente. Se il secondo aggiornamento è in modalità automatica, il primo dominio di aggiornamento verrà aggiornato immediatamente e le istanze di più domini di aggiornamento potrebbero passare offline nello stesso momento.

Le operazioni di mutazione sono le seguenti: [Change Deployment Configuration](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Upgrade Deployment](https://msdn.microsoft.com/library/azure/ee460793.aspx), [Update Deployment Status](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Delete Deployment](https://msdn.microsoft.com/library/azure/ee460815.aspx) e [Rollback Update Or Upgrade](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Due operazioni, [Get Deployment](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Get Cloud Service Properties](https://msdn.microsoft.com/library/azure/ee460806.aspx), restituiscono il flag Locked che può essere esaminato per determinare se un'operazione di mutazione può essere richiamata in una determinata distribuzione.

Per chiamare la versione di questi metodi che restituisce il flag Locked, è necessario impostare l'intestazione della richiesta su "x-ms-version: 2011-10-01" o versione successiva. Per altre informazioni sul controllo delle versioni delle intestazioni, vedere [Controllo delle versioni di gestione del servizio](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuzione di ruoli nei domini di aggiornamento
Azure distribuisce in tutti i domini di aggiornamento lo stesso numero di istanze di un ruolo, che è possibile configurare come parte del file di definizione del servizio (.csdef). Il numero massimo di domini di aggiornamento è 20 e quello predefinito è 5. Per altre informazioni su come modificare il file csdef, vedere [Schema di definizione del servizio di Azure (file .csdef)](cloud-services-model-and-package.md#csdef).

Se, ad esempio, il ruolo ha dieci istanze, per impostazione predefinita, ogni dominio di aggiornamento contiene due istanze. Se il ruolo ha 14 istanze, quattro domini di aggiornamento contengono tre istanze e un quinto dominio ne contiene due.

I domini di aggiornamento vengono identificati con un indice in base zero: il primo dominio di aggiornamento ha ID 0, il secondo dominio di aggiornamento ha ID 1 e così via.

Il diagramma seguente illustra come vengono distribuiti due ruoli contenuti in un servizio quando il servizio definisce due domini di aggiornamento. Il servizio esegue otto istanze del ruolo Web e nove istanze del ruolo di lavoro.

![Distribuzione di domini di aggiornamento](media/cloud-services-update-azure-service/IC345533.png "Distribution of Upgrade Domains")

> [!NOTE]
> Si noti che Azure controlla come le istanze vengono allocate nei domini di aggiornamento. Non è possibile specificare quali istanze vengono allocate in ogni dominio.
>
>

## <a name="next-steps"></a>Passaggi successivi
[Come gestire i servizi cloud](cloud-services-how-to-manage.md)  
[Come monitorare i servizi cloud](cloud-services-how-to-monitor.md)  
[Come configurare i servizi cloud](cloud-services-how-to-configure.md)  



<!--HONumber=Nov16_HO3-->



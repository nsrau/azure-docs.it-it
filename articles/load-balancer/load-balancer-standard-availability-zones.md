---
title: Azure Load Balancer Standard e zone di disponibilità | Microsoft Docs
description: Load Balancer Standard e zone di disponibilità
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2018
ms.author: kumud
ms.openlocfilehash: 61e0e7cf960d7eb2294bc294ec1eec9d80428a81
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard e zone di disponibilità

Lo SKU Standard di Azure Load Balancer supporta scenari di [zone di disponibilità](../availability-zones/az-overview.md). Load Balancer Standard introduce diversi nuovi concetti legati alla possibilità di ottimizzare la disponibilità nello scenario end-to-end allineando le risorse con le zone, nonché di distribuirle tra le zone.  Vedere [Zone di disponibilità](../availability-zones/az-overview.md) per indicazioni su cosa sono le zone di disponibilità, quali aree supportano attualmente le zone di disponibilità e altri concetti e prodotti correlati. Le zone di disponibilità in combinazione con Load Balancer Standard costituiscono un set di funzionalità ampio e flessibile per la creazione di molti scenari diversi.  Leggere questo documento per comprendere questi [concetti](#concepts) e le [linee guida di progettazione](#design) degli scenari fondamentali.

>[!NOTE]
> Lo SKU di Load Balancer Standard è attualmente in anteprima. Durante l'anteprima, la funzionalità potrebbe non avere lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Usare lo [SKU di Load Balancer Basic](load-balancer-overview.md) disponibile a livello generale per i servizi di produzione. Per usare l'[anteprima delle zone di disponibilità](https://aka.ms/availabilityzones) con questa anteprima, è richiesto un [accesso separato](https://aka.ms/availabilityzones), inoltre è necessario registrarsi all'[anteprima](#preview-sign-up) di Load Balancer Standard.

## <a name="concepts"></a> Concetti relativi alle zone di disponibilità applicati a Load Balancer

Non esiste una relazione diretta tra le risorse di Load Balancer e l'infrastruttura effettiva; la creazione di un servizio di bilanciamento del carico non crea un'istanza. Le risorse di Load Balancer sono oggetti all'interno dei quali è possibile esprimere in che modo Azure deve programmare la propria infrastruttura multi-tenant predefinita per ottenere lo scenario che si vuole creare.  Questo aspetto è significativo nel contesto delle zone di disponibilità poiché una singola risorsa di Load Balancer può controllare la programmazione dell'infrastruttura in più zone di disponibilità, mentre un servizio con ridondanza della zona viene considerato una risorsa dal punto di vista del cliente.

Le funzioni della risorsa di Load Balancer sono espresse come front-end, regola, probe di integrità e definizione di pool back-end.

Nel contesto delle zone di disponibilità, il comportamento e le proprietà di una risorsa di Load Balancer sono descritte con le espressioni "con ridondanza della zona" o "di zona".  Queste due espressioni descrivono la zonalità di una proprietà.  Nel contesto di Load Balancer, ridondanza della zona significa sempre per *tutte le zone*, mentre di zona significa servizio garantito per una *singola zona*.

Le istanze di Load Balancer pubbliche e interne supportano entrambe scenari con ridondanza della zona e di zona e possono entrambe indirizzare il traffico tra le zone in base alle esigenze (*bilanciamento del carico tra zone*).

Una risorsa di Load Balancer è di per sé regionale e mai di zona.  Una rete virtuale e una subnet sono sempre regionali e mai di zona.

### <a name="frontend"></a>Front-end

Un front-end di Load Balancer è una configurazione IP front-end che fa riferimento a una risorsa di indirizzo IP pubblico o a un indirizzo IP privato all'interno della subnet di una risorsa di rete virtuale.  Costituisce l'endpoint con carico bilanciato in cui viene esposto il servizio.

Una risorsa di Load Balancer può contenere contemporaneamente front-end di zona e con ridondanza della zona.

Quando una risorsa IP pubblica è stata garantita per una zona, la zonalità (o la sua mancanza) non è modificabile.  Se si vuole modificare o omettere la zonalità di un front-end IP pubblico, è necessario ricreare l'IP pubblico nella zona appropriata.  

È possibile modificare la zonalità di un front-end di un'istanza di Load Balancer interna rimuovendo e ricreando il front-end e modificando o omettendo la zonalità.

Quando si usano più front-end, fare riferimento a [Più front-end per Azure Load Balancer](load-balancer-multivip-overview.md) per alcune considerazioni importanti.

#### <a name="zone-redundant-by-default"></a>Ridondanza della zona per impostazione predefinita

In un'area con zone di disponibilità un front-end di Load Balancer Standard è per impostazione predefinita con ridondanza della zona.  Un indirizzo IP front-end singolo non risente degli errori a livello di zona e può essere usato per raggiungere tutti i membri del pool back-end indipendentemente dalla zona. Ciò non implica un percorso dati privo di passaggi, ma tutti i nuovi tentativi o i ripristini avranno esito positivo. Gli schemi di ridondanza DNS non sono necessari. L'indirizzo IP singolo del front-end è gestito contemporaneamente da distribuzioni di infrastrutture indipendenti in ogni zona di disponibilità.  Ridondanza della zona significa che tutti i flussi in ingresso o in uscita vengono gestiti da tutte le zone di disponibilità in un'area contemporaneamente usando un singolo indirizzo IP.

Una o più zone di disponibilità possono avere esito negativo e il percorso dati non risente degli errori finché una zona dell'area rimane integra. La configurazione con ridondanza della zona è la configurazione predefinita e non richiede azioni aggiuntive.  Quando un'area acquisisce la possibilità di supportare le zone di disponibilità, un front-end esistente diventa automaticamente con ridondanza della zona.

Usare lo script seguente per creare un indirizzo IP pubblico con ridondanza della zona per l'istanza di Load Balancer Standard interna. Se si usano modelli di Resource Manager esistenti nella configurazione, aggiungere la sezione **sku** a questi modelli.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Usare lo script seguente per creare un indirizzo IP front-end con ridondanza della zona per l'istanza di Load Balancer Standard interna. Se si usano modelli di Resource Manager esistenti nella configurazione, aggiungere la sezione **sku** a questi modelli.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

#### <a name="optional-zone-guarantee"></a>Garanzia di zona facoltativa

È possibile scegliere di avere un front-end garantito per una singola zona, noto come *front-end di zona*.  Ciò significa che qualsiasi flusso in ingresso o in uscita viene gestito da una singola zona in un'area.  La durata del front-end è legata all'integrità della zona.  Il percorso dati non è interessato dagli errori in zone diverse da quelle in cui è stato garantito. È possibile usare front-end di zona per esporre un indirizzo IP per zona di disponibilità.  Inoltre, è possibile utilizzare direttamente i front-end di zona o, quando il front-end è costituito da indirizzi IP pubblici, integrarli con un prodotto di bilanciamento del carico DNS come [Gestione traffico](../traffic-manager/traffic-manager-overview.md) e usare un singolo nome DNS, che verrà risolto da un client in più indirizzi IP di zona.  È anche possibile ricorrere a questo metodo per esporre endpoint con carico bilanciato per zona, in modo da monitorare singolarmente ogni zona.  Per un approfondimento di questi concetti (con ridondanza della zona e di zona per lo stesso back-end), consultare [Più front-end per Azure Load Balancer](/load-balancer-multivip-overview.md).

Per un front-end di Load Balancer pubblico, è necessario aggiungere un parametro *zones* all'indirizzo IP pubblico a cui fa riferimento la configurazione IP front-end.  

Per un front-end di Load Balancer interno, è necessario aggiungere un parametro *zones* alla configurazione IP front-end dell'istanza di Load Balancer interna. Il front-end di zona fa sì che Load Balancer garantisca un indirizzo IP in una subnet per una zona specifica.

Usare lo script riportato di seguito per creare un indirizzo IP pubblico standard di zona nella zona di disponibilità 1. Se si usano modelli di Resource Manager esistenti nella configurazione, aggiungere la sezione **sku** a questi modelli.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Usare lo script riportato di seguito per creare un front-end di Load Balancer Standard interno nella zona di disponibilità 1.

Se si usano modelli di Resource Manager esistenti nella configurazione, aggiungere la sezione **sku** a questi modelli. Definire anche la proprietà **zones** nella configurazione IP del front-end per la risorsa figlio.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

### <a name="cross-zone-load-balancing"></a>Bilanciamento del carico tra zone

Il bilanciamento del carico tra zone è la possibilità di Load Balancer di raggiungere un endpoint di back-end in qualsiasi zona ed è indipendente dal front-end e dalla relativa zonalità.

Se si vuole allineare e garantire la distribuzione con una singola zona, allineare le risorse di back-end di zona e di front-end di zona alla stessa zona. Non è necessaria alcuna azione.

### <a name="backend"></a>Back-end

Load Balancer può essere usato con le macchine virtuali.  Qualsiasi VM in una singola rete virtuale può fare parte del pool back-end indipendentemente dal fatto che sia stata garantita per una zona o dalla zona per cui è stata garantita.

Se si vogliono allineare e garantire il front-end e il back-end con una singola zona, inserire solo le macchine virtuali all'interno della stessa zona nel rispettivo pool back-end.

Se si vogliono indirizzare le macchine virtuali tra più zone, è sufficiente inserire le VM di più zone nello stesso pool back-end.  Quando si usano set di scalabilità di macchine virtuali, è possibile inserire uno o più set di scalabilità di macchine virtuali nello stesso pool back-end.  Ognuno di questi set di scalabilità di macchine virtuali può essere inserito in una o più zone.

### <a name="outbound-connections"></a>Connessioni in uscita

Le [connessioni in uscita](load-balancer-outbound-connections.md) vengono gestite da tutte le zone e sono automaticamente con ridondanza della zona in un'area con zone di disponibilità quando una macchina virtuale è associata a un'istanza di Load Balancer pubblica e a un front-end con ridondanza della zona.  Le allocazioni SNAT delle porte delle connessioni in uscita sono protette da eventuali errori della zona.  

A sua volta, se la macchina virtuale è associata a un'istanza di Load Balancer pubblica e a un front-end di zona, le connessioni in uscita sono garantite in modo da essere gestite da una singola zona.  La durata delle connessioni in uscita è legata all'integrità della rispettiva zona.

La preallocazione della porta SNAT e l'algoritmo sono gli stessi con o senza zone.

### <a name="health-probes"></a>Probe di integrità

Le definizioni di probe di integrità esistenti rimangono immutate senza zone di disponibilità.  Ma il modello di integrità è stato esteso a livello di infrastruttura. 

Quando si usano front-end con ridondanza della zona, Load Balancer estende il proprio modello di integrità interno per procedere in modo indipendente al probe della raggiungibilità di una macchina virtuale da ogni zona di disponibilità e all'arresto dei percorsi tra zone che potrebbero avere riportato errori senza l'intervento del cliente.  Se un determinato percorso non è disponibile dall'infrastruttura di Load Balancer di una zona per una macchina virtuale in un'altra zona, Load Balancer può rilevare ed evitare questo errore. Altre zone che sono in grado di raggiungere questa macchina virtuale possono continuare a rendere disponibile la macchina virtuale dai rispettivi front-end.  Di conseguenza, è possibile che durante gli eventi di errore ciascuna zona abbia distribuzioni del flusso leggermente diverse, proteggendo al tempo stesso l'integrità generale del servizio end-to-end.

## <a name="design"></a> Considerazioni sulla progettazione

Load Balancer è intenzionalmente flessibile nel contesto delle zone di disponibilità. È possibile scegliere di allinearsi alle zone oppure optare per la ridondanza della zona.  A una maggiore disponibilità può corrispondere un aumento della complessità, pertanto per prestazioni ottimali è richiesta una progettazione orientata alla disponibilità.  Verranno ora prese in esame alcune importanti considerazioni sulla progettazione.

### <a name="automatic-zone-redundancy"></a>Ridondanza automatica della zona

Con Load Balancer è più semplice avere un singolo IP come front-end con ridondanza della zona. Un indirizzo IP con ridondanza della zona può rendere disponibile in modo sicuro una risorsa di zona in qualsiasi zona e può non risentire di uno o più errori a livello di zona fintanto che una zona rimane integra all'interno dell'area. Al contrario, un front-end di zona è una riduzione del servizio a una singola zona e la sua durata è legata alla rispettiva zona.

La ridondanza della zona non implica un percorso dati privo di passaggi o un piano di controllo; si tratta espressamente di un piano dati. I flussi con ridondanza della zona possono usare qualsiasi zona e i flussi di un cliente useranno tutte le zone integre in un'area. In caso di errore di una zona, i flussi di traffico che usano le zone integre in quel dato momento non sono interessati.  I flussi di traffico che usano una zona nel momento in cui si verifica un errore della zona possono risultare interessati, ma le applicazioni possono essere ripristinate e questi flussi possono continuare nelle zone integre rimanenti all'interno dell'area in seguito alla ritrasmissione o al ripristino dopo che Azure ha eseguito la convergenza sull'errore della zona.

### <a name="xzonedesign"></a> Limiti tra zone

È importante comprendere che ogni volta che un servizio end-to-end attraversa le zone, la sua durata è legata non solo a una zona ma potenzialmente a più zone.  Di conseguenza, il servizio end-to-end potrebbe non garantire maggiore disponibilità rispetto alle distribuzioni non di zona.

Evitare di introdurre dipendenze tra zone non intenzionali che rischiano di annullare i vantaggi ottenuti in termini di disponibilità quando si usano le zone di disponibilità.  Quando l'applicazione è costituita da più componenti e si vuole ottenere la resilienza agli errori a livello di zona, è necessario prestare attenzione a garantire che un numero sufficiente di componenti critici non risenta degli errori in caso di problemi con una zona.  Ad esempio, un singolo componente critico per l'applicazione può influire sull'intera applicazione se esiste solo in una zona diversa da quelle che non risentono degli errori.  Inoltre, prendere in considerazione anche il ripristino della zona e come l'applicazione eseguirà la convergenza. Verranno presi in esame alcuni punti chiave che saranno usati come riferimento per eventuali domande relative a scenari specifici.

- Se l'applicazione include due componenti come un indirizzo IP e una macchina virtuale con disco gestito che sono garantiti nella zona 1 e nella zona 2, quando si verifica un errore nella zona 1 il servizio end-to-end ne subirà le ripercussioni.  Attraversare le zone solo se si è pienamente consapevoli di stare creando una modalità di errore potenzialmente pericolosa.

- Se l'applicazione include due componenti come un indirizzo IP e una macchina virtuale con disco gestito che sono garantiti rispettivamente per la ridondanza della zona e nella zona 1, il servizio end-to-end non risentirà degli errori a livello di zona nella zona 2, nella zona 3 o in entrambe, a meno che l'errore non si sia verificato nella zona 1.  Tuttavia, se ci si limita a prestare attenzione solo alla raggiungibilità del front-end si perde di vista l'integrità del servizio.  Prendere in considerazione l'idea di sviluppare un modello di integrità e di capacità più esteso.  È possibile usare in combinazione i concetti di ridondanza della zona e di zona per aumentare il dettaglio delle informazioni e la gestibilità.

- Se l'applicazione include due componenti come un front-end di Load Balancer con ridondanza della zona e un set di scalabilità di macchine virtuali tra zone in tre zone, le risorse nelle zone non interessate dall'errore saranno disponibili, ma il servizio end-to-end potrebbe risultare degradato in termini di capacità durante l'errore della zona. Dal punto di vista dell'infrastruttura, la distribuzione può non risentire di uno o più errori a livello di zona e ciò genera le domande seguenti:
  - Si comprende in che modo l'applicazione valuta questi errori e la capacità degradata?
  - È necessario disporre di misure di sicurezza nel servizio per forzare il failover a una coppia di aree se necessario?
  - Come verrà monitorato, rilevato e mitigato un simile scenario? Potrebbe essere possibile usare la diagnostica di Load Balancer Standard per aumentare il monitoraggio delle prestazioni del servizio end-to-end. Tenere in considerazione quanto è disponibile e ciò che potrebbe essere necessario aumentare per un quadro completo.

- Le zone possono rendere gli errori più facilmente comprensibili e contenuti.  Tuttavia, gli errori a livello di zona non sono diversi dagli altri errori quando si tratta di concetti quali timeout, nuovi tentativi e algoritmi di backoff. Anche se Azure Load Balancer fornisce percorsi con ridondanza della zona e tenta di recuperare rapidamente, a livello di pacchetti in tempo reale, possono verificarsi ritrasmissioni o ripristini all'insorgere di un errore ed è importante comprendere in che modo l'applicazione gestisce gli errori. Lo schema di bilanciamento del carico non subirà le ripercussioni degli errori, ma è necessario pianificare gli aspetti seguenti:
  - Quando in una zona si verifica un errore, il servizio end-to-end lo comprende e se lo stato viene perso in che modo si procederà al ripristino?
  - Quando una zona viene ripristinata, l'applicazione comprende come eseguire la convergenza in modo sicuro?

### <a name="zonalityguidance"></a> Concetti di ridondanza della zona e di zona a confronto

La ridondanza della zona può fornire un'opzione indipendente dalla zona e al tempo stesso resiliente con un singolo indirizzo IP per il servizio.  Può inoltre contribuire a ridurre la complessità.  La ridondanza della zona assicura anche la mobilità tra zone e può essere usata in modo sicuro con le risorse di qualsiasi zona.  Inoltre, è una soluzione a prova di futuro nelle aree prive di zone di disponibilità, che può limitare le modifiche richieste dopo che un'area ottiene le zone di disponibilità.  La sintassi di configurazione per un front-end o un indirizzo IP con ridondanza della zona ha esito positivo in qualsiasi area, incluse quelle senza zone di disponibilità.

Il concetto di zona può fornire una garanzia esplicita a una zona, la cui durata è legata all'integrità della zona. L'associazione di un indirizzo IP di zona o di un front-end di Load Balancer di zona può essere un attributo ragionevole o auspicabile soprattutto se la risorsa collegata è una macchina virtuale di zona nella stessa zona.  Può essere che l'applicazione richieda una conoscenza esplicita della zona in cui si trova una risorsa e si preferisca ragionare sulla disponibilità in zone distinte in modo esplicito.  È possibile scegliere di esporre più front-end di zona per un servizio end-to-end distribuito tra zone (vale a dire, front-end di zona in base alla zona per più set di scalabilità di macchine virtuali di zona).  Inoltre, se i front-end di zona sono indirizzi IP pubblici, è possibile usare questi front-end di zona molteplici per esporre il servizio con [Gestione traffico](../traffic-manager/traffic-manager-overview.md).  In alternativa, si possono usare più front-end di zona per ottenere informazioni dettagliate sulle prestazioni e sull'integrità in base alla zona tramite soluzioni di monitoraggio di terze parti ed esporre il servizio complessivo con un front-end con ridondanza della zona. Si devono gestire le risorse di zona solo con front-end di zona allineati alla stessa zona evitando scenari tra zone potenzialmente dannosi per le risorse di zona.  Le risorse di zona sono disponibili solo nelle aree in cui sono presenti le zone di disponibilità.

Non esiste alcuna indicazione generale che una scelta sia migliore dell'altra senza conoscere il servizio end-to-end.

## <a name="limitations"></a>Limitazioni

- Mentre il piano dati è completamente con ridondanza della zona (a meno che non sia stata specificata una garanzia di zona), le operazioni del piano di controllo non sono completamente con ridondanza della zona.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [zone di disponibilità](../availability-zones/az-overview.md)
- Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md)

---
title: Panoramica di Azure Load Balancer Standard | Microsoft Docs
description: "Panoramica delle funzionalità di Azure Load Balancer Standard"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2018
ms.author: kumud
ms.openlocfilehash: ddbfb415f062396f022f0f58cb975f6e3a5f1807
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Panoramica di Azure Load Balancer Standard (anteprima)

Gli SKU di Azure Load Balancer Standard e IP pubblico Standard, insieme, consentono di compilare architetture altamente scalabili e affidabili. Le applicazioni che usano Load Balancer Standard possono usufruire delle nuove funzionalità. Bassa latenza, velocità effettiva elevata e scalabilità sono disponibili per milioni di flussi per tutte le applicazioni TCP e UDP.

>[!NOTE]
> Lo SKU di Load Balancer Standard è attualmente in anteprima. Durante l'anteprima, la funzionalità potrebbe non avere lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Usare lo [SKU di Load Balancer Basic](load-balancer-overview.md) disponibile a livello generale per i servizi di produzione. Per usare l'[anteprima delle zone di disponibilità](https://aka.ms/availabilityzones) con questa anteprima, è richiesto un [accesso separato](https://aka.ms/availabilityzones), inoltre è necessario registrarsi all'[anteprima](#preview-sign-up) di Load Balancer Standard.

## <a name="why-use-load-balancer-standard"></a>Perché usare Load Balancer Standard?

È possibile usare Load Balancer Standard per l'intera gamma dei data center virtuali. Dalle distribuzioni su scala ridotta alle architetture grandi e complesse a più zone, Load Balancer Standard consente di usufruire dei vantaggi seguenti:

- [Scalabilità di livello aziendale](#enterprisescale) offerta da Load Balancer Standard. Questa funzionalità può essere usata con qualsiasi istanza di macchina virtuale (VM) in una rete virtuale, fino a un massimo di 1.000 istanze di macchina virtuale.

- [Nuove informazioni di diagnostica](#diagnosticinsights) consentono di comprendere, gestire e risolvere i problemi relativi a questo componente fondamentale del data center virtuale. Usare Monitoraggio di Azure (anteprima) per visualizzare, filtrare e raggruppare la nuova metrica multidimensionale per la misurazione continua dell'integrità del percorso dati. Monitorare i dati dal front-end alla macchina virtuale, i probe di integrità degli endpoint, i tentativi di connessione TCP e le connessioni in uscita.

- I [gruppi di sicurezza di rete](#nsg) sono ora necessari per qualsiasi istanza VM associata a SKU di Load Balancer Standard o di IP pubblico Standard. I gruppi di sicurezza di rete (NGS) garantiscono una sicurezza avanzata per lo scenario.

- Le [porte a disponibilità elevata offrono grande affidabilità](#highreliability) e scalabilità per le appliance virtuali di rete (NVA) e altri scenari di applicazione. Le porte a disponibilità elevata eseguono il bilanciamento del carico di tutte le porte per un pool di istanze VM in un front-end del servizio di bilanciamento del carico interno (ILB) di Azure.

- Le [connessioni in uscita](#outboundconnections) ora usano un nuovo modello di allocazione porte SNAT ( Source Network Address Translation) che offre un maggiore livello di resilienza e scalabilità.

- È possibile usare [Load Balancer Standard con le zone di disponibilità](#availabilityzones) per costruire architetture di zona e con ridondanza della zona. Entrambe le architetture possono includere il bilanciamento del carico tra zone. È possibile ottenere le ridondanza della zona senza dipendenza dai record DNS. Un singolo indirizzo IP è con ridondanza della zona per impostazione predefinita.  Un singolo indirizzo IP può raggiungere qualsiasi macchina virtuale in una rete virtuale all'interno di un'area che include tutte le zone di disponibilità.


Load Balancer Standard è compatibile con configurazioni interne o pubbliche che supportano gli scenari principali seguenti:

- Bilanciamento del carico del traffico in ingresso a istanze di back-end integre.
- Port forwarding del traffico in ingresso a una singola istanza di back-end.
- Conversione del traffico in uscita da un indirizzo IP privato all'interno della rete virtuale a un indirizzo IP pubblico.

### <a name = "enterprisescale"></a>Scalabilità di livello aziendale

 Load Balancer Standard consente di progettare il data center virtuale ad alte prestazioni e di supportare qualsiasi applicazione TCP o UDP. Usare le istanze VM autonome o al massimo 1.000 istanze di set di scalabilità di macchine virtuali in un pool back-end. Continuare a usare la bassa latenza di inoltro, le prestazioni elevate della velocità effettiva e la scalabilità a milioni di flussi in un servizio di Azure completamente gestito.
 
Load Balancer Standard è in grado di inoltrare il traffico a qualsiasi istanza VM nella rete virtuale di un'area. I pool back-end possono includere fino a 1.000 istanze con qualsiasi combinazione dei seguenti scenari di macchine virtuali:

- Macchine virtuali autonome senza set di disponibilità
- Macchine virtuali autonome con set di disponibilità
- Set di scalabilità di macchine virtuali, fino a 1.000 istanze
- Più set di scalabilità di macchine virtuali
- Macchine virtuali insieme a set di scalabilità di macchine virtuali

I set di disponibilità non sono più un requisito. È possibile scegliere di usare i set di disponibilità per gli altri vantaggi che offrono.

### <a name = "diagnosticinsights"></a>Informazioni di diagnostica

Load Balancer Standard offre nuove funzionalità di diagnostica multidimensionale per configurazioni di Load Balancer pubbliche e interne. La nuova metrica è disponibile con Monitoraggio di Azure (anteprima) e usa tutte le funzionalità correlate, inclusa la capacità di integrazione con vari consumer downstream.

| Metrica | DESCRIZIONE |
| --- | --- |
| Disponibilità IP virtuale | Load Balancer Standard esercita continuamente il percorso dati dall'interno di un'area al front-end di Load Balancer e infine allo stack SDN che supporta la macchina virtuale. Finché sono presenti istanze integre, la misurazione segue lo stesso percorso del traffico con bilanciamento del carico dell'applicazione. Viene anche convalidato il percorso dati usato dai clienti. La misurazione è invisibile all'applicazione e non interferisce con altre operazioni.|
| Disponibilità DIP | Load Balancer Standard usa un servizio di probe dell'integrità distribuito che monitora l'integrità dell'endpoint dell'applicazione in base alle impostazioni di configurazione. Questa metrica offre una visualizzazione filtrata, aggregata o per endpoint di ogni singolo endpoint dell'istanza nel pool di Load Balancer.  In questo modo è possibile visualizzare l'integrità dell'applicazione rilevata da Load Balancer, in base alla configurazione del probe di integrità.
| Pacchetti SYN | Load Balancer Standard non termina le connessioni TCP né interagisce con i flussi di pacchetti TCP o UDP. I flussi e i relativi handshake sono sempre tra l'origine e l'istanza VM. Per risolvere meglio i problemi degli scenari del protocollo TCP, è possibile usare contatori di pacchetti SYN per determinare quanti tentativi di connessione TCP vengono eseguiti. La metrica indica il numero di pacchetti SYN TCP ricevuti.|
| Connessioni SNAT | Load Balancer Standard segnala il numero di flussi in uscita mascherati per il front-end dell'indirizzo IP pubblico. Le porte SNAT sono una risorsa esauribile. Questa metrica può indicare l'uso che l'applicazione fa di SNAT per i flussi originati in uscita.  Vengono segnalati i contatori per i flussi SNAT con esito positivo e negativo, che è possibile usare per risolvere i problemi e comprendere l'integrità dei flussi in uscita.|
| Contatori di byte | Load Balancer Standard restituisce i dati elaborati per ogni front-end.|
| Contatori di pacchetti | Load Balancer Standard restituisce i pacchetti elaborati per ogni front-end.|

### <a name = "highreliability"></a>Affidabilità elevata

Configurare le regole del servizio di bilanciamento del carico per ridimensionare l'applicazione e migliorarne l'affidabilità. È possibile configurare regole per le singole porte o usare le porte a disponibilità elevata per bilanciare tutto il traffico indipendentemente dal numero di porta TCP o UDP.  

La nuova funzionalità Porte a disponibilità elevata consente di sbloccare un'ampia gamma di scenari, tra cui la disponibilità e la scalabilità elevate per le appliance virtuali di rete interne. La funzionalità è utile per altri scenari in cui specificare le singole porte è poco pratico o non opportuno. Le porte a disponibilità elevata offrono scalabilità e ridondanza consentendo un numero di istanze adeguato alle esigenze. La configurazione non è più limitata agli scenari di tipo attivo/passivo. Le configurazioni di probe di integrità proteggono il servizio inoltrando il traffico solo alle istanze integre.

I fornitori di appliance virtuali di rete possono offrire scenari resilienti e completamente supportati ai propri clienti. Il singolo punto di guasto viene rimosso e sono supportate più istanze attive per la scalabilità. È possibile ridimensionare a due o più istanze, in base alle funzionalità dell'appliance. Contattare il fornitore dell'appliance virtuale di rete per altre indicazioni riguardo a questi scenari.

### <a name = "availabilityzones"></a>Zone di disponibilità

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

L'uso delle zone di disponibilità permette di migliorare la resilienza dell'applicazione nelle aree geografiche supportate. Le zone di disponibilità sono attualmente in anteprima in aree specifiche e richiedono un ulteriore consenso esplicito.

### <a name="automatic-zone-redundancy"></a>Ridondanza automatica della zona

È possibile scegliere se usare Load Balancer per ottenere un front-end di zona o con ridondanza della zona per ognuna delle applicazioni. Load Balancer Standard consente di creare facilmente la ridondanza della zona. Un singolo indirizzo IP front-end include automaticamente la ridondanza della zona. Un front-end con ridondanza della zona viene servito contemporaneamente da tutte le zone di disponibilità di un'area. Viene creato un percorso dati con ridondanza della zona per le connessioni in ingresso e in uscita. La ridondanza della zona in Azure non richiede più indirizzi IP e record DNS. 

La ridondanza della zona è disponibile per i front-end interni o pubblici. Sia l'indirizzo IP pubblico che l'IP privato del front-end del servizio di bilanciamento del carico interno possono avere la ridondanza della zona.

Usare lo script seguente per creare un indirizzo IP pubblico con ridondanza della zona per il bilanciamento del carico interno. Se si usano modelli di Resource Manager esistenti nella configurazione, aggiungere la sezione **sku** a questi modelli.

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

Usare lo script seguente per creare un indirizzo IP front-end con ridondanza della zona per il bilanciamento del carico interno. Se si usano modelli di Resource Manager esistenti nella configurazione, aggiungere la sezione **sku** a questi modelli.

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

Se il front-end dell'indirizzo IP pubblico include la ridondanza della zona, le connessioni in uscita effettuate da istanze VM acquisiscono automaticamente la ridondanza della zona. Il front-end è protetto dagli errori della zona. Anche l'allocazione della porta SNAT non risente degli errori della zona.

#### <a name="cross-zone-load-balancing"></a>Bilanciamento del carico tra zone

Il bilanciamento del carico tra zone è disponibile all'interno di un'area per il pool back-end e offre la massima flessibilità per le istanze VM. Un front-end invia flussi a tutte le macchine virtuali della rete virtuale, indipendentemente dalla zona di disponibilità dell'istanza VM.

È anche possibile specificare un'area particolare per le istanze front-end e back-end, per allineare il percorso dati e le risorse a una zona specifica.

Le reti virtuali e le subnet non sono mai vincolate a una zona. Si deve solo definire un pool back-end con le istanze VM da usare e la configurazione è completata.

#### <a name="zonal-deployments"></a>Distribuzioni di zona

È possibile allineare il bilanciamento del carico front-end a una zona specifica definendo un front-end di zona. Un front-end di zona viene servito solo dalla zona di disponibilità designata. Quando il front-end viene combinato con istanze VM di zona, è possibile allineare le risorse a zone specifiche.

Un indirizzo IP pubblico creato in una zona specifica esisterà sempre solo in tale zona. Non è possibile modificare la zona di un indirizzo IP pubblico. Per un indirizzo IP pubblico che può essere allegato alle risorse in più zone, creare un indirizzo IP pubblico con ridondanza della zona.

Usare lo script riportato di seguito per creare un indirizzo IP pubblico di zona nella zona di disponibilità 1. Se si usano modelli di Resource Manager esistenti nella configurazione, aggiungere la sezione **sku** a questi modelli.

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

Usare lo script riportato di seguito per creare un front-end di bilanciamento del carico interno nella zona di disponibilità 1.

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

Aggiungere il bilanciamento del carico tra zone per il pool back-end inserendo nel pool le istanze VM presenti in una rete virtuale.

La risorsa Load Balancer Standard include sempre la ridondanza della zona e a livello di area dove sono supportate le zone di disponibilità. È possibile distribuire un indirizzo IP pubblico o il front-end di bilanciamento del carico interno a cui non è stata assegnata una zona in alcuna area. Il supporto per le zone di disponibilità non influisce sulla funzionalità di distribuzione. Se un'area in un secondo tempo ottiene zone di disponibilità, indirizzi IP pubblici già distribuiti o front-end del servizio di bilanciamento del carico interno acquisisce automaticamente la ridondanza della zona. Un percorso dati con ridondanza della zona non implica l'azzeramento della perdita di pacchetti.

### <a name = "nsg"></a>Gruppi di sicurezza di rete

Load Balancer Standard e IP pubblico Standard eseguono il caricamento nella rete virtuale ed è quindi necessario usare i gruppi di sicurezza di rete (NSG). Gli NSG consentono di aggiungere il flusso del traffico all'elenco elementi consentiti. È possibile usare gli NSG per esercitare il pieno controllo sul traffico della distribuzione. Non è più necessario attendere il completamento degli altri flussi di traffico.

Associare gli NSG a subnet o interfacce di rete delle istanze VM nel pool back-end. Usa questa configurazione con Load Balancer Standard e l'IP pubblico Standard quando viene usato come IP pubblico a livello di istanza. Il gruppo di sicurezza di rete deve aggiungere in modo esplicito il traffico che si vuole consentire all'elenco elementi consentiti, in modo tale che il traffico possa scorrere.

Per altre informazioni sui gruppi di sicurezza di rete e su come applicarli allo scenario, vedere [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Connessioni in uscita

Load Balancer Standard offre connessioni in uscita per le macchine virtuali che si trovano all'interno della rete virtuale quando un bilanciamento del carico usa l'architettura SNAT per il mascheramento della porta. L'algoritmo SNAT per il mascheramento della porta incrementa la scalabilità e l'affidabilità.

Quando una risorsa pubblica di Load Balancer è associata alle istanze VM, ogni origine di connessione in uscita viene riscritta. L'origine viene riscritta dallo spazio dell'indirizzo IP privato della rete virtuale all'indirizzo IP pubblico front-end del bilanciamento del carico.

Quando le connessioni in uscita vengono usate con un front-end con ridondanza della zona, hanno a loro volta ridondanza della zona e le allocazioni SNAT delle porte sono protette da eventuali errori della zona.

Il nuovo algoritmo in Load Balancer Standard prealloca le porte SNAT porte alla scheda di rete di ogni macchina virtuale. Quando una scheda di rete viene aggiunta al pool, le porte SNAT sono preallocate in base alle dimensioni del pool. Nella tabella seguente sono riportate le preallocazioni delle porte per sei livelli di dimensioni del pool back-end:

| Dimensioni del pool (istanze VM) | Numero di porte SNAT preallocate |
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1.000 | 32 |

Le porte SNAT non corrispondono direttamente al numero di connessioni in uscita. È possibile riutilizzare una porta SNAT per più destinazioni univoche. Per informazioni dettagliate, vedere l'articolo relativo alle [connessioni in uscita](load-balancer-outbound-connections.md).

Se le dimensioni del pool back-end aumentano e si ha una transizione a un livello superiore, metà delle porte allocate vengono recuperate. Le connessioni associate a una porta recuperata scadono e devono essere ristabilite. I nuovi tentativi di connessione hanno immediatamente esito positivo. Se le dimensioni del pool back-end diminuiscono e si ha una transizione a un livello più basso, il numero di porte SNAT disponibili aumenta. In questo caso, le connessioni esistenti non sono interessate.

Load Balancer Standard ha un'opzione di configurazione aggiuntiva che può essere usata in base alle regole. È possibile stabilire quale front-end usare per l'architettura SNAT con mascheramento della porta quando sono disponibili più front-end.

Quando le istanze VM sono servite solo da Load Balancer Standard, le connessioni SNAT in uscita non sono disponibili. È possibile ripristinare questa funzionalità in modo esplicito assegnando anche le istanze VM a un bilanciamento del carico pubblico. È anche possibile assegnare direttamente gli indirizzi IP pubblici come indirizzi IP pubblici a livello di istanza per ogni istanza VM. Questa opzione di configurazione potrebbe essere richiesta per alcuni scenari di sistema operativo e applicazione. 

### <a name="port-forwarding"></a>Port forwarding

Load Balancer Basic e Standard offrono la possibilità di configurare le regole NAT in ingresso per eseguire il mapping di una porta front-end a una singola istanza back-end. Configurando queste regole, è possibile esporre gli endpoint di Remote Desktop Protocol e SSH o eseguire altri scenari di applicazione.

Load Balancer Standard continua a offrire il port forwarding attraverso le regole NAT in ingresso. Se usate con i front-end con ridondanza della zona, le regole NAT in ingresso acquisiscono la ridondanza della zona e sono protette in caso di errore della zona.

### <a name="multiple-front-ends"></a>Uso di più front-end

Configurare più front-end per una maggiore flessibilità di progettazione quando le applicazioni richiedono più indirizzi IP singoli da esporre, ad esempio per siti Web TLS o endpoint di gruppo di disponibilità AlwaysOn di SQL. 

Load Balancer Standard continua a offrire più front-end quando è necessario esporre un endpoint dell'applicazione specifico in un indirizzo IP univoco.

Per altre informazioni sulla configurazione di più indirizzi IP front-end, vedere [Più indirizzi VIP per Azure Load Balancer](load-balancer-multivip-overview.md).

## <a name = "sku"></a>Informazioni sugli SKU

Gli SKU sono disponibili unicamente con il modello di distribuzione Azure Resource Manager. Con questa versione di anteprima vengono introdotti due SKU per le risorse Load Balancer e IP pubblico: Basic e Standard. Gli SKU si differenziano per capacità, caratteristiche di prestazioni, limitazioni e alcuni comportamenti intrinseci. Entrambi gli SKU consentono l'uso di macchine virtuali. Gli SKU rimangono attributi facoltativi sia per la risorsa Load Balancer che per l'IP pubblico. Se gli SKU vengono omessi in una definizione di scenario, per impostazione predefinita la configurazione usa lo SKU Basic.

>[!IMPORTANT]
>Lo SKU di una risorsa non è modificabile. Non è possibile modificare lo SKU di una risorsa esistente.  

### <a name="load-balancer"></a>Bilanciamento del carico

La [risorsa Load Balancer esistente](load-balancer-overview.md) diventa lo SKU Basic e rimane disponibile a livello generale e invariata.

Lo SKU di Load Balancer Standard è nuovo e attualmente in anteprima. La versione dell'API del 1° agosto 2017 per Microsoft.Network/loadBalancers aggiunge la proprietà **sku** alla definizione della risorsa:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Load Balancer Standard è automaticamente resiliente nella zona nelle aree che offrono le zone di disponibilità. Se Load Balancer è stato dichiarato di zona, non è automaticamente resiliente nella zona.

### <a name="public-ip"></a>IP pubblico

La [risorsa IP pubblico esistente](../virtual-network/virtual-network-ip-addresses-overview-arm.md) diventa lo SKU Basic e rimane disponibile a livello generale con tutte le relative funzionalità, caratteristiche di prestazioni e limitazioni.

Lo SKU dell'IP pubblico Standard è nuovo e attualmente in anteprima. La versione dell'API del 1° agosto 2017 per Microsoft.Network/publicIPAddresses aggiunge la proprietà **sku** alla definizione della risorsa:

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

A differenza dell'IP pubblico Basic, che offre più metodi di allocazione, l'IP pubblico Standard usa sempre l'allocazione statica.

L'IP pubblico Standard è automaticamente resiliente nella zona nelle aree che offrono le zone di disponibilità. Se l'IP pubblico è stato dichiarato di zona, non è automaticamente resiliente nella zona. Un IP pubblico di zona non può essere spostato da una zona all'altra.

## <a name="migration-between-skus"></a>Migrazione tra SKU

Gli SKU non sono modificabili. Seguire i passaggi di questa sezione per passare da uno SKU di risorsa a un altro.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrare dallo SKU Basic a Standard

1. Creare una nuova risorsa Standard, Load Balancer o IP pubblico in base alle esigenze. Ricreare le regole e le definizioni dei probe.

2. Creare un nuovo gruppo di sicurezza di rete o aggiornare quello esistente per la NIC o la subnet per avere un elenco elementi consentiti relativo a probe e traffico con carico bilanciato, nonché per qualsiasi altro tipo di traffico che si vuole consentire.

3. Rimuovere le risorse SKU Basic (Load Balancer e IP pubblici, secondo il caso) da tutte le istanze VM. Assicurarsi di rimuovere anche tutte le istanze VM di un set di disponibilità.

4. Collegare tutte le istanze di macchina virtuale alle nuove risorse SKU Standard.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrare dallo SKU Standard a Basic

1. Creare una nuova risorsa Basic, Load Balancer o IP pubblico in base alle esigenze. Ricreare le regole e le definizioni dei probe. 

2. Rimuovere le risorse SKU Standard (Load Balancer e IP pubblici, secondo il caso) da tutte le istanze VM. Assicurarsi di rimuovere anche tutte le istanze VM di un set di disponibilità.

3. Collegare tutte le istanze di macchina virtuale alle nuove risorse SKU Basic.

>[!IMPORTANT]
>
>Esistono limitazioni relative all'uso degli SKU Basic e Standard.
>
>Le porte a disponibilità elevata e la diagnostica dello SKU Standard sono disponibili unicamente con lo SKU Standard. Non è possibile eseguire la migrazione da SKU Standard a SKU Basic e mantenere queste funzionalità.
>
>Per le risorse di Load Balancer e IP pubblico è necessario usare SKU corrispondenti. Non è possibile avere una combinazione di risorse SKU Basic e risorse SKU Standard. Non è possibile allegare una macchina virtuale, le macchine virtuali di un set di disponibilità o un set di scalabilità di macchine virtuali a entrambi gli SKU contemporaneamente.
>

## <a name="region-availability"></a>Aree di disponibilità

Load Balancer Standard è attualmente disponibile in tutte le aree di cloud pubblico.

>[!IMPORTANT]
> Per un breve periodo di tempo, l'accesso nelle aree escluse dal lancio iniziale (Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale, Stati Uniti centro-occidentali, Europa occidentale, Asia sud-orientale) richiede la registrazione di funzionalità di sottoscrizione aggiuntive (AllowLBPreviewWave2 e AllowLBPreviewWave3).  [Seguire questa procedura](#additionalpreviewregions). Eseguire tutti i passaggi anche se in precedenza è già stata effettuata la registrazione a AllowLBPreview.
> Questo requisito verrà rimosso nelle prossime settimane.

## <a name="sku-service-limits-and-abilities"></a>Limiti e capacità del servizio degli SKU

I [limiti di servizio relativi alla rete](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits) di Azure si applicano in base alla singola area e sottoscrizione. 

Nella tabella seguente vengono confrontati i limiti e le funzionalità degli SKU di Load Balancer Basic e Standard:

| Bilanciamento del carico | Basic | Standard |
| --- | --- | --- |
| Dimensioni del pool back-end | Fino a 100 | Fino a 1.000 |
| Limite del pool back-end | Set di disponibilità | rete virtuale, area |
| Progettazione del pool back-end | Macchine virtuali nel set di disponibilità, set di scalabilità di macchine virtuali nel set di disponibilità | Qualsiasi istanza VM nella rete virtuale |
| Porte a disponibilità elevata | Non supportate | Disponibile |
| Diagnostica | Limitata, solo pubblica | Disponibile |
| Disponibilità IP virtuale  | Non supportate | Disponibile |
| Mobilità IP veloce | Non supportate | Disponibile |
|Scenari di zone di disponibilità | Solo di zona | Di zona, con ridondanza della zona, bilanciamento del carico tra zone |
| Algoritmo SNAT in uscita | Su richiesta | Preallocato |
| Selezione front-end SNAT in uscita | Non configurabile, più candidati | Configurazione facoltativa per ridurre i candidati |
| Gruppo di sicurezza di rete | Facoltativo nella scheda di interfaccia di rete/subnet | Obbligatoria |

Nella tabella seguente vengono confrontati i limiti e le funzionalità degli SKU di IP pubblico Basic e Standard:

| IP pubblico | Basic | Standard |
| --- | --- | --- |
| Scenari di zone di disponibilità | Solo di zona | Con ridondanza della zona (impostazione predefinita), di zonale (facoltativo) | 
| Mobilità IP veloce | Non supportate | Disponibile |
| Disponibilità IP virtuale | Non supportate | Disponibile |
| Counters | Non supportate | Disponibile |
| Gruppo di sicurezza di rete | Facoltativo nella scheda di interfaccia di rete | Obbligatoria |


## <a name="preview-sign-up"></a>Iscrizione all'anteprima

Per partecipare all'anteprima dello SKU di Load Balancer Standard e del relativo SKU di IP pubblico Standard, è necessario registrare la propria sottoscrizione.  La registrazione della sottoscrizione consente di accedere da PowerShell o dall'interfaccia della riga di comando di Azure 2.0. Per effettuare la registrazione, seguire questa procedura:

>[!NOTE]
>La registrazione di Load Balancer Standard può richiedere fino a un'ora prima di essere applicata a livello globale. Se si desidera usare Load Balancer Standard con [zone di disponibilità](https://aka.ms/availabilityzones), è necessaria una [registrazione separata](https://aka.ms/availabilityzones) per le anteprime.

<a name="additionalpreviewregions"></a>
>[!IMPORTANT]
> Per un breve periodo di tempo, l'accesso nelle aree escluse dal lancio iniziale (Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale, Stati Uniti centro-occidentali, Europa occidentale, Asia sud-orientale) richiede la registrazione di funzionalità di sottoscrizione aggiuntive (AllowLBPreviewWave2 e AllowLBPreviewWave3).  I passaggi seguenti sono stati modificati per abilitare le funzionalità di sottoscrizione aggiuntive. Eseguire tutti i passaggi anche se in precedenza è già stata effettuata la registrazione a AllowLBPreview. Questo requisito verrà rimosso nelle prossime settimane.


### <a name="sign-up-by-using-azure-cli-20"></a>Registrazione eseguita con l'interfaccia della riga di comando di Azure 2.0

1. Registrare la funzionalità con il provider:

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave2 --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave3 --namespace Microsoft.Network
    ```
    
2. Il completamento dell'operazione può richiedere fino a 10 minuti. È possibile controllare lo stato dell'operazione con il comando seguente:

    ```cli
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreview']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave2']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave3']" --output json
    ```
    
    Quando lo stato della registrazione della funzionalità è "Registrato" procedere con il passaggio successivo per ciascuna delle funzionalità di sottoscrizione riportate sopra. Esempio:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
4. Completare la registrazione all'anteprima registrando nuovamente la sottoscrizione con il provider di risorse:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    

### <a name="sign-up-by-using-powershell"></a>Registrazione eseguita con PowerShell

1. Registrare la funzionalità con il provider:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```
    
2. Il completamento dell'operazione può richiedere fino a 10 minuti. È possibile controllare lo stato dell'operazione con il comando seguente:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```

  Quando lo stato della registrazione della funzionalità è "Registrato" procedere con il passaggio successivo per ciascuna delle funzionalità di sottoscrizione riportate sopra. Esempio:

    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. Completare la registrazione all'anteprima registrando nuovamente la sottoscrizione con il provider di risorse:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>Prezzi

La fatturazione per lo SKU di Load Balancer Standard si basa sulle regole configurate e sui dati elaborati. Per il periodo di anteprima non sono previsti addebiti. Per altre informazioni, vedere le pagine relative ai prezzi di [Load Balancer](https://aka.ms/lbpreviewpricing) e [IP pubblico](https://aka.ms/lbpreviewpippricing).

I clienti potranno continuare a usufruire dello SKU di Load Balancer Basic senza alcun costo aggiuntivo.

## <a name="limitations"></a>Limitazioni

Durante la fase di anteprima si applicano le limitazioni seguenti, soggette a modifica:

- Al momento le istanze back-end di Load Balancer non possono trovarsi in reti virtuali con peering. Tutte le istanze back-end devono trovarsi nella stessa area.
- Gli SKU non sono modificabili. Non è possibile modificare lo SKU di una risorsa esistente.
- Entrambi gli SKUP possono essere usati con una macchina virtuale autonoma, istanze VM in un set di disponibilità o un set di scalabilità di macchine virtuali. Le combinazioni di macchine virtuali non possono essere usate con entrambi gli SKU contemporaneamente. Una configurazione che contiene una combinazione di SKU non è consentita.
- Se si usa un servizio di bilanciamento del carico interno Standard con un'istanza VM o con qualsiasi parte di un set di disponibilità, le [connessioni SNAT in uscita predefinite](load-balancer-outbound-connections.md) vengono disabilitate. È possibile ripristinare la funzionalità in una macchina virtuale autonoma, nelle istanze VM di un set di disponibilità o in un set di scalabilità di macchine virtuali. È anche possibile ripristinare la possibilità di effettuare connessioni in uscita. Per ripristinare queste capacità, assegnare contemporaneamente un servizio di bilanciamento del carico pubblico Standard o un IP pubblico Standard come IP pubblico a livello di istanza alla stessa istanza VM. Al termine dell'assegnazione, il mascheramento SNAT delle porte per un indirizzo IP pubblico sarà nuovamente disponibile.
- Potrebbe essere necessario raggruppare le istanze VM in set di disponibilità per ottenere la piena scalabilità del pool back-end. In un unico pool back-end è possibile inserire fino a 150 set di disponibilità e macchine virtuali autonome.
- IPv6 non è supportato.
- Nel contesto delle zone di disponibilità non è possibile passare da un front-end di zona a uno con ridondanza della zona o viceversa. Se un front-end viene creato con ridondanza della zona, rimane con ridondanza della zona. Un front-end creato come front-end di zona rimane di zona.
- Nel contesto delle zone di disponibilità, non è possibile spostare un indirizzo IP pubblico di zona da una zona a un'altra.
- Gli [avvisi di Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) non sono attualmente supportati.
- Il portale non supporta ancora le aree di anteprima espanse.  Usare strumenti client come modelli, interfaccia della riga di comando di Azure 2.0 o PowerShell come soluzione alternativa.
- Lo spostamento delle operazioni di sottoscrizione non è supportato.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Basic](load-balancer-overview.md).
- Altre informazioni sulle [zone di disponibilità](../availability-zones/az-overview.md).
- Vedere altre informazioni sui [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
- Informazioni sulle [metriche esposte](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) in [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md).

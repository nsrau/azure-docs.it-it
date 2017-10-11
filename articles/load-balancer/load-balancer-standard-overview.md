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
ms.date: 09/28/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 2728e8b1e190b4ecd0635925b96e97775564a2ee
ms.contentlocale: it-it
ms.lasthandoff: 09/29/2017

---

# <a name="azure-load-balancer-standard-overview-preview"></a>Panoramica di Azure Load Balancer Standard (anteprima)

Gli SKU di Azure Load Balancer Standard e IP pubblico Standard, insieme, consentono di compilare architetture altamente scalabili e affidabili.  Le applicazioni che usano Load Balancer Standard possono sfruttare le nuove funzionalità, ma anche le caratteristiche di bassa latenza, velocità effettiva elevata e scalabilità per milioni di flussi per tutte le applicazioni TCP e UDP.

>[!NOTE]
> Lo SKU di Load Balancer Standard è attualmente in anteprima. Durante l'anteprima, la funzionalità potrebbe non avere lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Usare lo [SKU di Load Balancer Basic](load-balancer-overview.md) disponibile a livello generale per i servizi di produzione.

## <a name="why-use-load-balancer-standard"></a>Perché usare Load Balancer Standard

È possibile usare Load Balancer Standard per l'intera gamma dei data center virtuali, dalle distribuzioni su scala ridotta alle architetture grandi e complesse a più zone, per sfruttare le funzionalità seguenti:

- [Scalabilità di livello aziendale](#enterprisescale) offerta da Load Balancer Standard.  È compatibile con qualsiasi istanza di macchina virtuale in una rete virtuale, fino a un massimo di 1000 istanze di macchina virtuale.

- [Nuove informazioni di diagnostica](#diagnosticinsights) che permettono di comprendere, gestire e risolvere i problemi relativi a questo componente fondamentale del data center virtuale. Usare Monitoraggio di Azure (anteprima) per visualizzare, filtrare e raggruppare la nuova metrica multidimensionale per la misurazione continua dell'integrità del percorso dati, dal front-end alla macchina virtuale, dei probe di integrità degli endpoint, dei tentativi di connessione TCP e delle connessioni in uscita.

- I [gruppi di sicurezza di rete](#nsg) sono ora necessari per qualsiasi istanza di macchina virtuale associata a SKU Standard di Load Balancer o IP pubblico e offrono una sicurezza avanzata.

- Le [porte a disponibilità elevata offrono grande affidabilità](#highreliability) e scalabilità per le appliance virtuali di rete e altri scenari di applicazione. Le porte a disponibilità elevata eseguono il bilanciamento del carico di tutte le porte per un pool di istanze di macchina virtuale in un front-end del servizio di bilanciamento del carico interno.

- Le [connessioni in uscita](#outboundconnections) ora usano un nuovo modello di allocazione porte SNAT che offre maggiore resilienza e scalabilità.

- È possibile usare [Load Balancer Standard con le zone di disponibilità](#availabilityzones) per costruire architetture di zona e con ridondanza della zona, entrambe con bilanciamento del carico tra le zone. È possibile ottenere la ridondanza della zona senza dipendenza nei record DNS: un indirizzo IP singolo include la ridondanza della zona per impostazione predefinita e può raggiungere qualsiasi macchina virtuale in una rete virtuale di un'area, in tutte le zone di disponibilità.


Load Balancer Standard è compatibile con configurazioni interne o pubbliche che continuano a supportare gli scenari principali seguenti:

- Bilanciamento del carico del traffico in ingresso a istanze di back-end integre.
- Port forwarding del traffico in ingresso a una singola istanza di back-end.
- Conversione del traffico in uscita da un indirizzo IP privato all'interno della rete virtuale a un indirizzo IP pubblico.

### <a name = "enterprisescale"></a>Scalabilità di livello aziendale

 Usare Load Balancer Standard nella progettazione del data center virtuale a prestazioni elevate. È possibile usare istanze di macchina virtuale autonome o set di scalabilità di macchine virtuali con un massimo di 1000 istanze in un pool back-end e supportare qualsiasi applicazione TCP o UDP. Load Balancer Standard permette comunque all'applicazione di sfruttare la bassa latenza di inoltro, le prestazioni elevate della velocità effettiva e la scalabilità a milioni di flussi in un servizio di Azure completamente gestito.
 
Load Balancer Standard può inoltrare il traffico a qualsiasi istanza di macchina virtuale nella rete virtuale di un'area. I pool back-end possono includere fino a 1000 istanze con qualsiasi combinazione di:

- macchine virtuali autonome senza set di disponibilità,
- macchine virtuali autonome con set di disponibilità,
- set di scalabilità di macchine virtuali con un massimo di 1000 istanze,
- più set di scalabilità di macchine virtuali,
- macchine virtuali insieme a set di scalabilità di macchine virtuali.

I set di disponibilità non sono più un requisito, ma è possibile scegliere di usarli per altri vantaggi che offrono.

### <a name = "diagnosticinsights"></a>Informazioni di diagnostica

Load Balancer Standard offre nuove funzionalità di diagnostica multidimensionale per configurazioni di Load Balancer pubbliche e interne. La nuova metrica è disponibile tramite Monitoraggio di Azure (anteprima) e utilizza tutte le funzionalità correlate, inclusa la capacità di integrazione con vari consumer downstream.

| Metrica | Descrizione |
| --- | --- |
| Disponibilità IP virtuale | Load Balancer Standard esercita continuamente il percorso dati dall'interno di un'area all'istanza front-end di Load Balancer e infine allo stack SDN che supporta la macchina virtuale. Fin quando sono presenti istanze integre, la misurazione segue lo stesso percorso del traffico con bilanciamento del carico delle applicazioni e convalida il percorso dati usato dai clienti. La misurazione è invisibile all'applicazione e non interferisce con il suo funzionamento.|
| Disponibilità DIP | Load Balancer Standard usa un servizio di probe dell'integrità distribuito che monitora l'integrità dell'endpoint dell'applicazione in base alle impostazioni configurate.  Questa metrica offre una visualizzazione filtrata, aggregata o per endpoint, di ogni singolo endpoint dell'istanza nel pool di Load Balancer.  In questo modo è possibile visualizzare l'integrità dell'applicazione rilevata da Load Balancer, in base alla configurazione del probe di integrità.
| Pacchetti SYN | Load Balancer Standard non termina le connessioni TCP e non interagisce con i flussi di pacchetti TCP o UDP. I flussi e i relativi handshake avvengono sempre tra l'origine e l'istanza di macchina virtuale. Per risolvere meglio tutti i problemi relativi agli scenari del protocollo TCP, è possibile usare questa metrica per determinare in numero di tentativi di connessione TCP che vengono eseguiti. La metrica restituisce il numero di pacchetti SYN TCP ricevuti, che può indicare il tentativo da parte dei client di stabilire connessioni al servizio.|
| Connessioni SNAT | Load Balancer Standard restituisce il numero di connessioni in uscita mascherate per l'indirizzo IP pubblico front-end.  Le porte SNAT sono una risorsa esauribile e questa metrica può fornire un'indicazione di quanto l'applicazione si basi su SNAT per le connessioni originate in uscita.|
| Contatori di byte | Load Balancer Standard restituisce i dati elaborati per ogni front-end.|
| Contatori di pacchetti | Load Balancer Standard restituisce i pacchetti elaborati per ogni front-end.|

### <a name = "highreliability"></a>Affidabilità elevata

Configurare le regole del servizio di bilanciamento del carico per ridimensionare l'applicazione e migliorarne l'affidabilità.  È possibile configurare regole per porte singole o usare le nuove porte a disponibilità elevata per bilanciare il traffico, indipendentemente dal numero di porta TCP o UDP.  

La nuova funzionalità delle porte a disponibilità elevata permette di sbloccare un'ampia gamma di scenari, tra cui la disponibilità elevata e la scalabilità delle appliance virtuali di rete interne e altri scenari in cui non è consigliabile specificare porte singole. Le porte a disponibilità elevata offrono ridondanza e scalabilità consentendo tutte le istanze necessarie, anziché limitarne il numero agli scenari attivi o passivi. Le configurazioni di probe di integrità proteggono il servizio inoltrando il traffico solo alle istanze integre.

I fornitori di appliance virtuali di rete possono offrire scenari resilienti pienamente supportati rimuovendo un singolo punto di errore per i clienti e consentendo più istanze attive per la scalabilità. Se l'appliance consente tali configurazioni, è possibile passare a due o più istanze. Il fornitore di appliance virtuali di rete deve fornire ulteriori indicazioni per tali scenari.

### <a name = "availabilityzones"></a>Zone di disponibilità

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

L'uso delle zone di disponibilità permette di migliorare la resilienza dell'applicazione nelle aree geografiche supportate. Le zone di disponibilità sono attualmente in anteprima in aree specifiche e richiedono un ulteriore consenso esplicito.

### <a name="automatic-zone-redundancy"></a>Ridondanza automatica della zona

È possibile scegliere se usare Load Balancer per ottenere un front-end di zona o con ridondanza della zona per ognuna delle applicazioni.  Load Balancer Standard permette di creare facilmente la ridondanza della zona.  Un indirizzo IP front-end singolo include automaticamente la ridondanza della zona.  Un front-end con ridondanza della zona viene servito contemporaneamente da tutte le zone di disponibilità di un'area. In questo modo si crea un percorso dati con ridondanza della zona per le connessioni in ingresso e in uscita. La ridondanza della zona in Azure non richiede più indirizzi IP e record DNS. 

Tale ridondanza della zona è disponibile per i front-end interni o pubblici. Sia l'indirizzo IP pubblico che l'IP privato del front-end del servizio di bilanciamento del carico interno possono avere la ridondanza della zona.

Creare un indirizzo IP pubblico con ridondanza della zona usando il codice seguente, aggiungendo "sku" ai modelli di Resource Manager esistenti:

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

Creare una configurazione IP front-end con ridondanza della zona per il servizio di bilanciamento del carico interno usando il codice seguente, aggiungendo "sku" ai modelli di Resource Manager esistenti:

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

Se il front-end dell'IP pubblico include la ridondanza della zona, tutte le connessioni in uscita effettuate da istanze di macchina virtuale acquisiscono automaticamente la ridondanza della zona e sono protette da errori della zona.  Gli errori della zona non influiscono sull'allocazione della porta SNAT.

#### <a name="cross-zone-load-balancing"></a>Bilanciamento del carico tra zone

Il bilanciamento del carico tra zone è disponibile all'interno di un'area per il pool back-end e consente la massima flessibilità per le istanze di macchina virtuale.  Un front-end può recapitare flussi a tutte le macchine virtuali nella rete virtuale, indipendentemente dalla zona disponibilità dell'istanza di macchina virtuale.

È anche possibile scegliere di indicare un'area specifica per le istanze front-end e back-end, per allineare il percorso dati e le risorse a una zona specifica.

Date che le reti virtuali e le subnet non sono mai vincolate alla zona, è sufficiente definire un pool back-end con le istanze di macchina virtuale da usare e la configurazione è terminata.

#### <a name="zonal-deployments"></a>Distribuzioni di zona

Facoltativamente, è possibile allineare il front-end a una zona specifica definendo un front-end di zona.  Un front-end di zona viene servito unicamente dalla zona di disponibilità specificata e, se unito a istanze di macchina virtuale di zona, permette di allineare le risorse a zone specifiche.

Creare un indirizzo IP pubblico di zona nella zona di disponibilità 1 usando il codice seguente, aggiungendo "zones" e "sku" ai modelli di Resource Manager esistenti:

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

Creare un front-end del servizio di bilanciamento del carico interno nella zona di disponibilità 1 con il codice seguente, aggiungendo "sku" ai modelli di Resource Manager esistenti e inserendo "zones" nella risorsa figlio della configurazione IP front-end:

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

È anche possibile usare il bilanciamento del carico tra zone per il back-end, inserendo le istanze di macchina virtuale in una rete virtuale in un pool back-end.

La stessa risorsa di Load Balancer Standard include sempre la ridondanza della zona e a livello di area, laddove le zone di disponibilità sono supportate. Un indirizzo IP pubblico o un front-end del servizio di bilanciamento del carico interno a cui non è stata assegnata una zona può essere distribuito in qualsiasi area, indipendentemente dal supporto delle aree di disponibilità. Se un'area ottiene il supporto delle zone di disponibilità in un secondo momento, un indirizzo IP pubblico già distribuito o un front-end del servizio di bilanciamento del carico interno acquisisce automaticamente la ridondanza della zona. Un percorso dati con ridondanza della zona non garantisce l'azzeramento della perdita di pacchetti.

### <a name = "nsg"></a>Gruppi di sicurezza di rete

Load Balancer Standard IP pubblico Standard eseguono il caricamento nella rete virtuale e sono ora necessari i gruppi di sicurezza di rete. Il gruppo di sicurezza di rete permette di inserire i flussi nell'elenco elementi consentiti e di avere configurazioni in cui i clienti hanno il controllo completo sul traffico destinato alla distribuzione, tramite il gruppo di sicurezza di rete anziché al completamento di altre configurazioni.

È possibile associare un gruppo di sicurezza di rete a subnet o schede di interfaccia di rete delle istanze di macchina virtuale nel pool back-end.  Questo vale per Load Balancer Standard e per l'IP pubblico Standard quando viene usato indirizzo IP pubblico a livello di istanza. Per il funzionamento del flusso, è necessario inserire in modo esplicito nell'elenco elementi consentiti il tipo di traffico che si vuole consentire.

Per altre informazioni sui gruppi di sicurezza di rete e su come applicarli allo scenario, vedere [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Connessioni in uscita

 Load Balancer Standard offre connessioni in uscita alle macchine virtuali all'interno della rete virtuale, se associate a un servizio di bilanciamento del carico che usa il mascheramento SNAT (Source Network Address Translation) delle porte.

Quando una risorsa di Load Balancer pubblica è associata a istanze di macchina virtuale, l'origine di ogni connessione in uscita viene riscritta dallo spazio di indirizzi IP privato della rete virtuale nell'indirizzo IP pubblico del front-end di Load Balancer.  Load Balancer Standard usa un nuovo algoritmo di mascheramento SNAT delle porte per garantire maggiore affidabilità e scalabilità.  

Se usato con un front-end con ridondanza della zona, anche le connessioni in uscita hanno ridondanza della zona e le allocazioni SNAT delle porte sono protette da eventuali errori della zona.

Il nuovo algoritmo di Load Balancer Standard prealloca le porte SNAT a ogni interfaccia di rete della macchina virtuale nel momento in cui vengono aggiunte al pool, in base ai livelli seguenti:

| Dimensioni del pool back-end | Porte SNAT preallocate |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Le porte SNAT non corrispondono direttamente al numero di connessioni. È possibile riutilizzare una porta SNAT per più destinazioni univoche.  Per informazioni dettagliate, vedere l'articolo relativo alle [connessioni in uscita](load-balancer-outbound-connections.md).

Se il pool back-end viene ingrandito e si passa da un livello di dimensione al successivo, viene recuperata la metà delle porte. Tutte le connessioni associate a porte recuperate scadranno e sarà necessario ristabilirle. Qualsiasi nuovo tentativo di connessione avrà immediatamente esito positivo. Se il pool back-end viene ridotto da un livello di dimensione al precedente, in numero di porte SNAT disponibili aumenta e le connessioni esistenti rimangono invariate.

Load Balancer Standard include anche un'opzione di configurazione aggiuntiva basata su una regola singola, per offrire al cliente il controllo sul front-end da usare per il mascheramento SNAT delle porte quando sono disponibili più front-end.

Infine, quando le istanze di macchina virtuale vengono servite soltanto da Load Balancer Standard, le connessioni SNAT in uscita non sono disponibili. È possibile ripristinare in modo esplicito tale capacità assegnando anche le istanze di macchina virtuale a un Load Balancer pubblico oppure assegnando IP pubblici a livello di istanza direttamente a ogni istanza di macchina virtuale. Questa potrebbe essere necessario per alcuni scenari di sistema operativo e applicazione. 

### <a name="port-forwarding"></a>Port forwarding

 Load Balancer Basic e Standard offrono la possibilità di configurare le regole NAT in ingresso per eseguire il mapping di una porta front-end a una singola istanza back-end.  Questa possibilità risulta particolarmente utile per esporre gli endpoint di Remote Desktop Protocol, gli endpoint SSH o una varietà di altri scenari di applicazione.

Load Balancer Standard continua a offrire il port forwarding tramite le regole NAT in ingresso.  Se usate con i front-end con ridondanza della zona, le regole NAT in ingresso acquisiscono la ridondanza della zona e sono protette in caso di errore della zona.

### <a name="multiple-frontends"></a>Più front-end

È possibile configurare più front-end per una maggiore flessibilità di progettazione quando le applicazioni richiedono più indirizzi IP singoli da esporre, ad esempio per siti Web TLS o endpoint di gruppo di disponibilità AlwaysOn di SQL.  Per informazioni dettagliate, vedere [questo articolo](load-balancer-multivip-overview.md).

Load Balancer Standard continua a fornire più front-end quando è opportuno esporre un endpoint dell'applicazione specifico in un indirizzo IP univoco.

 Per informazioni dettagliate, vedere [questo articolo](load-balancer-multivip-overview.md).

## <a name = "sku"></a>Informazioni sugli SKU

Gli SKU sono disponibili unicamente con il modello di distribuzione Azure Resource Manager.  Con questa versione di anteprima vengono introdotti due SKU, Basic e Standard, per le risorse di Load Balancer e IP pubblico.  Gli SKU si differenziano per capacità, caratteristiche di prestazioni, limitazioni e alcuni comportamenti intrinseci. Entrambi gli SKU consentono l'uso di macchine virtuali. Gli SKU rimangono attributi facoltativi sia per la risorsa Load Balancer che per l'IP pubblico. Se tale attributo viene omesso, l'impostazione predefinita è Basic.

>[!IMPORTANT]
>Lo SKU di una risorsa non è modificabile.  Non è possibile modificare lo SKU di una risorsa esistente.  

### <a name="load-balancer"></a>Bilanciamento del carico

La [risorsa di Load Balancer esistente](load-balancer-overview.md) diventa lo SKU Basic e rimane disponibile a livello generale e invariata.

Lo SKU di Load Balancer Standard è una nuova offerta attualmente in anteprima. La versione dell'API 2017-08-01 per Microsoft.Network/loadBalancers introduce gli SKU per la risorsa.

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
Se usato in un'area che supporta le zone di disponibilità, Load Balancer Standard acquisisce automaticamente la resilienza della zona, a meno che non sia stato dichiarato come di zona.

### <a name="public-ip"></a>IP pubblico

La [risorsa IP pubblico esistente](../virtual-network/virtual-network-ip-addresses-overview-arm.md) diventa lo SKU Basic e rimane disponibile a livello generale con tutte le relative capacità, caratteristiche di prestazioni e limitazioni.

Lo SKU dell'IP pubblico Standard è una nuova offerta attualmente in anteprima. La versione dell'API 2017-08-01 per Microsoft.Network/publicIPAddresses introduce gli SKU per la risorsa.

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

A differenza dell'IP pubblico Basic, che offre più metodi di allocazione, l'IP pubblico Standard prevedere sempre l'allocazione statica.

Se usato in un'area che supporta le zone di disponibilità, l'IP pubblico Standard acquisisce automaticamente la resilienza della zona, a meno che non sia stato dichiarato come di zona.

## <a name="migration-between-skus"></a>Migrazione tra SKU

Per passare da uno SKU di risorsa a un altro, seguire questa procedura:

### <a name="migrating-from-basic-to-standard-sku"></a>Migrazione dallo SKU Basic a Standard

1. Creare una nuova risorsa Standard, Load Balancer o IP pubblico in base alle esigenze, e ricreare le regole e le definizioni dei probe.
2. Rimuovere le risorse SKU Basic, Load Balancer o IP pubblico, da tutte le istanze di macchina virtuale, incluse tutte le eventuali istanze di set di disponibilità.
3. Collegare tutte le istanze di macchina virtuale alle nuove risorse SKU Standard.

### <a name="migrating-from-standard-to-basic-sku"></a>Migrazione dallo SKU Standard a Basic:

1. Creare una nuova risorsa Basic, Load Balancer o IP pubblico in base alle esigenze, e ricreare le regole e le definizioni dei probe. 
2. Rimuovere le risorse SKU Standard, Load Balancer o IP pubblico, da tutte le istanze di macchina virtuale, incluse tutte le eventuali istanze di set di disponibilità.
3. Collegare tutte le istanze di macchina virtuale alle nuove risorse SKU Basic.

  >[!NOTE]
  >Le porte a disponibilità elevata e la diagnostica dello SKU Standard sono disponibili unicamente con lo SKU Standard. Non è possibile eseguire la migrazione da Standard a Basic e mantenere tale funzionalità.

Per le risorse di Load Balancer e IP pubblico è necessario usare SKU corrispondenti.  Non è possibile combinare risorse di SKU Standard e Basic o collegare contemporaneamente una macchina virtuale, macchine virtuali in un set di disponibilità o set di scalabilità di macchine virtuali a entrambe.

## <a name="region-availability"></a>Aree di disponibilità

Load Balancer Standard è attualmente disponibile nelle aree seguenti:
- Stati Uniti orientali 2
- Stati Uniti centrali
- Europa settentrionale
- Stati Uniti centro-occidentali
- Europa occidentale
- Asia sudorientale

## <a name="service-limits--abilities-comparison"></a>Confronto tra i limiti e capacità del servizio

I [limiti di servizio relativi alla rete](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits) di Azure si applicano in base alla singola area e sottoscrizione. 

La tabella seguente mette a confronto i limiti e le capacità degli SKU Basic e Standard di Load Balancer:

| Bilanciamento del carico | Basic | Standard |
| --- | --- | --- |
| Dimensioni del pool back-end | Fino a 100 | Fino a 1000 |
| Limite del pool back-end | Set di disponibilità | Rete virtuale, area |
| Progettazione del pool back-end | Macchine virtuali nel set di disponibilità o set di scalabilità di macchine virtuali nel set di disponibilità | Qualsiasi istanza di macchina virtuale nella rete virtuale |
| Porte a disponibilità elevata | Non supportate | Disponibile |
| Diagnostica | Limitata, solo pubblica | Disponibile |
| Disponibilità IP virtuale  | Non supportate | Disponibile |
| Mobilità IP veloce | Non supportate | Disponibile |
|Scenari di zone di disponibilità | Solo di zona | Di zona, con ridondanza della zona, bilanciamento del carico tra zone |
| Algoritmo SNAT in uscita | Su richiesta | Preallocato |
| Selezione front-end SNAT in uscita | Non configurabile, più candidati | Configurazione facoltativa per ridurre i candidati |
| Gruppo di sicurezza di rete | Facoltativo nella scheda di interfaccia di rete/subnet | Obbligatorio |

La tabella seguente mette a confronto i limiti e le capacità degli SKU Basic e Standard dell'IP pubblico:

| IP pubblico | Basic | Standard |
| --- | --- | --- |
| Scenari di zone di disponibilità | Solo di zona | Con ridondanza della zona (impostazione predefinita), di zonale (facoltativo) | 
| Mobilità IP veloce | Non supportate | Disponibile |
| Disponibilità IP virtuale | Non supportate | Disponibile |
| Counters | Non supportate | Disponibile |
| Gruppo di sicurezza di rete | Facoltativo nella scheda di interfaccia di rete | Obbligatorio |


## <a name="preview-sign-up"></a>Iscrizione all'anteprima

Per partecipare all'anteprima dello SKU di Load Balancer Standard e del relativo SKU di IP pubblico Standard, è necessario registrare la propria sottoscrizione per ottenere l'accesso tramite PowerShell o l'interfaccia della riga di comando di Azure 2.0.

- Iscrizione tramite PowerShell

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

- Iscrizione tramite l'interfaccia della riga di comando di Azure 2.0

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```

>[!NOTE]
>La registrazione della funzionalità Load Balancer Standard potrebbe richiedere fino a un'ora.

>[!NOTE]
>Per usare le zone di disponibilità con Load Balancer e l'IP pubblico, è necessario registrare la propria sottoscrizione anche per l'anteprima delle zone di disponibilità.

## <a name="pricing"></a>Prezzi

Lo SKU di Load Balancer Standard viene fatturato in base alle regole configurate e ai dati elaborati.  Per il periodo di anteprima non sono previsti addebiti.  Per altre informazioni, vedere le pagine relative ai prezzi di [Load Balancer](https://aka.ms/lbpreviewpricing) e [IP pubblico](https://aka.ms/lbpreviewpippricing).

I clienti potranno continuare a usufruire dello SKU di Load Balancer Basic senza alcun costo aggiuntivo.

## <a name="limitations"></a>Limitazioni

Durante la fase di anteprima si applicano le limitazioni seguenti, soggette a modifica:

- Al momento le istanze back-end di Load Balancer non possono trovarsi in reti virtuali con peering. Tutte le istanze back-end devono trovarsi nella stessa area.
- Gli SKU non sono modificabili. Non è possibile modificare lo SKU di una risorsa esistente.
- È possibile usare lo SKU Basic o lo SKU Standard con una macchina virtuale autonoma, con tutte le istanze di macchina virtuale in un set di disponibilità o con un set di scalabilità di macchine virtuali. Non è possibile usare una macchina virtuale autonoma, tutte le istanze di macchina virtuale in un set di disponibilità o un set di scalabilità di macchine virtuali con entrambi gli SKU contemporaneamente. Non è possibile combinare gli SKU.
- Se si usa un servizio di bilanciamento del carico interno Standard con un'istanza di macchina virtuale o con qualsiasi parte di un set di disponibilità, le [connessioni SNAT in uscita predefinite](load-balancer-outbound-connections.md) vengono disabilitate.  Per ripristinare questa capacità in una macchina virtuale autonoma, nelle istanze di macchina virtuale in un set di disponibilità o in un set di scalabilità di macchine virtuali ed eseguire connessioni in uscita, è possibile assegnare contemporaneamente un Load Balancer pubblico Standard o un IP pubblico Standard come IP pubblico a livello di istanza alla stessa istanza di macchina virtuale. Al termine, il mascheramento SNAT delle porte per un indirizzo IP pubblico sarà nuovamente disponibile.
- Potrebbe essere necessario raggruppare le istanze di macchina virtuale in gruppi di disponibilità per ottenere la piena scalabilità del pool back-end. In un unico pool back-end è possibile inserire fino a 150 set di disponibilità e macchine virtuali autonome.
- IPv6 non è supportato.
- Nel contesto delle zone di disponibilità non è possibile passare da un front-end di zona a uno con ridondanza della zona o viceversa. Se viene creato con ridondanza della zona o come di zona, il front-end rimane invariato.
- Nel contesto delle zone di disponibilità, non è possibile spostare un indirizzo IP pubblico di zona da un'area a un'altra.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Basic](load-balancer-overview.md)
- Altre informazioni sulle [zone di disponibilità](../availability-zones/az-overview.md)
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure



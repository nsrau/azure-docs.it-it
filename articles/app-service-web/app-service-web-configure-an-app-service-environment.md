---
title: Come configurare un ambiente del servizio app | Documentazione Microsoft
description: Configurazione, gestione e monitoraggio degli ambienti del servizio app
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85a4c87447681bd21698143b4228d94c0877d1b9
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
<a id="configuring-an-app-service-environment" class="xliff"></a>

# Configurazione di un ambiente del servizio app
<a id="overview" class="xliff"></a>

## Panoramica
A livello generale, un ambiente del servizio app di Azure è costituito da vari componenti principali:

* Risorse di calcolo in esecuzione nel servizio ospitato dell'ambiente del servizio app
* Archiviazione
* Un database
* Una rete virtuale di Azure classica (V1) o di Resource Manager (V2) 
* Una subnet in cui viene eseguito il servizio ospitato dell'ambiente del servizio app

<a id="compute-resources" class="xliff"></a>

### Risorse di calcolo
Le risorse di calcolo vengono usate per quattro pool di risorse.  Ogni ambiente del servizio app contiene un set di front-end e tre pool di lavoro possibili. Non è necessario usare tutti e tre i pool di lavoro. Se si vuole, è possibile usarne solo uno o due.

Gli host nei pool di risorse (front-end e ruoli di lavoro) non sono direttamente accessibili per i tenant. Non è possibile usare Remote Desktop Protocol (RDP) per connettersi a essi, modificarne il provisioning o eseguire attività amministrative.

È possibile impostare la quantità e le dimensioni dei pool di risorse. In un ambiente del servizio app sono disponibili quattro opzioni di dimensioni, denominate da P1 a P4. Per informazioni dettagliate sulle dimensioni e sui relativi prezzi, vedere la sezione "Prezzi" in [Informazioni sul servizio app di Azure](../app-service/app-service-value-prop-what-is.md).
La modifica della quantità o delle dimensioni è denominata operazione di ridimensionamento.  È possibile eseguire una sola operazione di ridimensionamento alla volta.

**Front-end**: i front-end sono gli endpoint HTTP/HTTPS per le app contenute nell'ambiente del servizio app. Nei front-end non vengono eseguiti carichi di lavoro.

* Un ambiente del servizio app include inizialmente due P2, sufficienti per carichi di lavoro di sviluppo e test e carichi di lavoro di produzione di basso livello. Per carichi di lavoro di produzione da moderati a elevati è consigliabile usare P3.
* Per carichi di lavoro di produzione da moderati a elevati, è consigliabile avere almeno quattro P3 per garantire l'esecuzione di un numero sufficiente di front-end durante la manutenzione pianificata. Nelle attività di manutenzione pianificata, verrà arrestato un front-end per volta e la capacità complessiva disponibile di front-end risulterà così ridotta.
* Non è possibile aggiungere immediatamente una nuova istanza front-end. Il provisioning può richiedere da 2 a 3 ore.
* Per ottimizzare ulteriormente il ridimensionamento, è consigliabile monitorare le metriche Percentuale CPU, Percentuale memoria e Richieste attive del pool front-end. Se la percentuale di CPU o memoria è superiore al 70% durante l'esecuzione di P3, aggiungere altri front-end. È consigliabile aggiungere altri front-end anche in caso di valore medio delle richieste attive compreso tra 15.000 e 20.000 per front-end. Durante l'esecuzione di P3, l'obiettivo generale è mantenere le percentuali di CPU e memoria inferiori al 70% e la media delle richieste attive al di sotto di 15.000 richieste per front-end.  

**Ruoli di lavoro**: i ruoli di lavoro sono la posizione in cui le app vengono effettivamente eseguite. Quando si passa a un piano di servizio app superiore, vengono usati i ruoli di lavoro del pool di lavoro associato.

* Non è possibile aggiungere immediatamente ruoli di lavoro. Il provisioning può richiedere da 2 a 3 ore, indipendentemente dal numero di ruoli aggiunti.
* Il ridimensionamento di una risorsa di calcolo di qualsiasi pool richiederà da 2 a 3 ore per ogni dominio di aggiornamento. Sono disponibili 20 domini di aggiornamento in un ambiente del servizio app. Il ridimensionamento del calcolo di un pool di lavoro con 10 istanze, ad esempio, potrebbe richiedere da 20 a 30 ore.
* Se si modificano le dimensioni delle risorse di calcolo usate in un pool di lavoro, si verificheranno avvii a freddo delle app in esecuzione nel pool di lavoro.

Il modo più rapido per modificare le dimensioni delle risorse di calcolo di un pool di lavoro che non esegue alcuna app consiste nel:

* Ridurre il numero di istanze a 0. La deallocazione delle istanze richiederà circa 30 minuti.
* Selezionare le nuove dimensioni di calcolo e il nuovo numero di istanze. A questo punto, il completamento dell'operazione richiederà da 2 a 3 ore.

Se le app richiedono dimensioni delle risorse di calcolo superiori, le indicazioni precedenti non risulteranno utili. Anziché modificare le dimensioni del pool di lavoro che ospita tali app, è possibile popolare un altro pool di lavoro con ruoli di lavoro delle dimensioni desiderate e spostare le app in tale pool.

* Creare le istanze aggiuntive delle dimensioni di calcolo necessarie in un altro pool di lavoro. Questa operazione richiederà da 2 a 3 ore.
* Riassegnare i piani di servizio app che ospitano le app che richiedono dimensioni superiori al pool di lavoro appena configurato. Questa operazione è rapida e richiede meno di un minuto per il completamento.  
* Se le istanze inutilizzate non sono più necessarie, ridurre il primo pool di lavoro. Questa operazione richiede circa 30 minuti.

**Ridimensionamento automatico**: uno degli strumenti che consentono di gestire l'utilizzo delle risorse di calcolo è il ridimensionamento automatico, che può essere usato per pool di lavoro o front-end. È ad esempio possibile aumentare e successivamente ridurre nell'arco della stessa giornata le istanze di qualsiasi tipo di pool o eventualmente aggiungere istanze quando il numero dei ruoli di lavoro disponibili in un pool di lavoro scende al di sotto di una determinata soglia.

Se si vogliono impostare regole di ridimensionamento automatico in base alle metriche del pool di risorse di calcolo, tenere presente il tempo necessario per il provisioning. Per altri dettagli sul ridimensionamento automatico degli ambienti del servizio app, vedere [Come configurare il ridimensionamento automatico in un ambiente del servizio app][ASEAutoscale].

<a id="storage" class="xliff"></a>

### Archiviazione
Ogni ambiente del servizio app è configurato con 500 GB di spazio di archiviazione. Questo spazio viene usato da tutte le app incluse nell'ambiente. Lo spazio di archiviazione fa parte dell'ambiente del servizio app e non è possibile cambiare l'impostazione per usare il proprio spazio di archiviazione. Se si apportano modifiche al routing o alla sicurezza della rete virtuale, è necessario consentire l'accesso ad Archiviazione di Azure. In caso contrario, l'ambiente del servizio app non può funzionare.

<a id="database" class="xliff"></a>

### Database
Il database contiene le informazioni che definiscono l'ambiente, nonché tutti i dettagli sulle app in esso eseguite. Anche questo fa parte della sottoscrizione di Azure. L'utente non ha la possibilità di modificare queste informazioni direttamente. Se si apportano modifiche al routing o alla sicurezza della rete virtuale, è necessario consentire l'accesso a SQL Azure. In caso contrario, l'ambiente del servizio app non può funzionare.

<a id="network" class="xliff"></a>

### Rete
La rete virtuale usata con l'ambiente del servizio app può essere una rete creata durante la creazione dell'ambiente o in precedenza. Se la subnet viene creata durante la creazione dell'ambiente del servizio app, l'ambiente apparterrà allo stesso gruppo di risorse della rete virtuale. Se è necessario che il gruppo di risorse usato dall'ambiente del servizio app sia diverso da quello della rete virtuale, occorre creare l'ambiente del servizio app usando un modello di Resource Manager.

Esistono alcune restrizioni per la rete virtuale usata per un ambiente del servizio app:

* La rete virtuale deve essere una rete virtuale a livello di area.
* È necessaria una subnet con 8 o più indirizzi, in cui viene distribuito l'ambiente del servizio app.
* Se una subnet viene usata per ospitare un ambiente del servizio app, l'intervallo di indirizzi della subnet non può essere modificato. Per questo motivo, è consigliabile che la subnet contenga almeno 64 indirizzi per supportare un'eventuale crescita futura dell'ambiente del servizio app.
* La subnet non può contenere altro oltre all'ambiente del servizio app.

A differenza del servizio ospitato contenente l'ambiente del servizio app, la [rete virtuale][virtualnetwork] e la subnet sono controllate dall'utente.  È possibile amministrare la rete virtuale con l'interfaccia utente Rete virtuale o con PowerShell.  Un ambiente del servizio app può essere distribuito in una rete virtuale classica o di Resource Manager.  L'esperienza a livello di portale e di API è leggermente diversa tra reti virtuali classiche e di Resource Manager, ma l'esperienza dell'ambiente del servizio app è identica.

La rete virtuale usata per ospitare un ambiente del servizio app può usare indirizzi IP RFC1918 privati o indirizzi IP pubblici.  Se si vuole usare un intervallo IP non coperto da RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), la rete virtuale e la subnet usate dall'ambiente del servizio app devono essere create prima di creare l'ambiente.

Poiché il servizio app di Azure viene inserito nella rete virtuale, le app ospitate nell'ambiente del servizio app possono accedere direttamente alle risorse rese disponibili tramite reti private virtuali (VPN) da sito a sito o ExpressRoute. Le app all'interno dell'ambiente del servizio app non richiedono funzionalità di rete aggiuntive per accedere alle risorse disponibili nella rete virtuale che ospita l'ambiente del servizio app. Non è quindi necessario usare le funzionalità Integrazione rete virtuale o Connessioni ibride per accedere alle risorse incluse o connesse alla rete virtuale. È comunque possibile usare entrambe queste funzionalità per accedere a risorse in reti non connesse alla propria rete virtuale.

È ad esempio possibile usare l'integrazione rete virtuale per ottenere l'integrazione con una rete virtuale disponibile nella sottoscrizione ma non connessa alla rete virtuale che include l'ambiente del servizio app. oppure usare la funzionalità Connessioni ibride per accedere a risorse in altre reti, come normalmente possibile.  

Se la rete virtuale è configurata con una VPN ExpressRoute, è necessario tenere conto di alcune esigenze di routing specifiche di un ambiente del servizio app. Alcune configurazioni di route definite dall'utente sono incompatibili con un ambiente del servizio app. Per altri dettagli sull'esecuzione di un ambiente del servizio app in una rete virtuale con ExpressRoute, vedere [Esecuzione di un ambiente del servizio app in una rete virtuale con ExpressRoute][ExpressRoute].

<a id="securing-inbound-traffic" class="xliff"></a>

#### Protezione del traffico in ingresso
Per controllare il traffico in ingresso all'ambiente del servizio app esistono due metodi principali.  È possibile usare gruppi di sicurezza di rete (NSG) per controllare gli indirizzi IP che potranno accedere all'ambiente del servizio app, come descritto nell'articolo [Come controllare il traffico in ingresso a un ambiente del servizio app](app-service-app-service-environment-control-inbound-traffic.md) , nonché configurare l'ambiente del servizio app con un servizio di bilanciamento del carico interno.  Queste funzionalità possono anche essere usate contemporaneamente, se si vuole limitare l'accesso usando gruppi di sicurezza di rete per un ambiente del servizio app con servizio di bilanciamento del carico interno.

Quando si crea un ambiente del servizio app, verrà creato un indirizzo VIP nella rete virtuale.  Esistono due tipi di indirizzo VIP: esterno e interno.  Quando si crea un ambiente del servizio app con un indirizzo VIP esterno, le app nell'ambiente saranno accessibili tramite un indirizzo IP instradabile su Internet. Quando si seleziona un indirizzo VIP interno, l'ambiente del servizio app verrà configurato con un servizio di bilanciamento del carico interno e non sarà accessibile direttamente da Internet.  In un ambiente del servizio app con servizio di bilanciamento del carico interno è comunque necessario un indirizzo VIP esterno, ma viene usato solo per l'accesso a scopo di gestione e di manutenzione di Azure.  

Durante la creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno si specifica il sottodominio usato dall'ambiente ed è necessario gestire un proprio DNS per il sottodominio specificato.  Poiché si imposta il nome del sottodominio, è necessario anche gestire il certificato usato per l'accesso HTTPS.  Al termine della creazione dell'ambiente del servizio app verrà richiesto di specificare il certificato.  Per altre informazioni sulla creazione e sull'uso di un ambiente del servizio app con servizio di bilanciamento del carico interno, vedere [Uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app][ILBASE]. 

<a id="portal" class="xliff"></a>

## di Microsoft Azure
È possibile gestire e monitorare l'ambiente del servizio app usando l'interfaccia utente del portale di Azure. Se è stato creato un ambiente del servizio app, nella barra laterale verrà probabilmente visualizzato il simbolo dei servizi app, usato per rappresentare gli ambienti del servizio app nel portale di Azure:

![Simbolo degli ambienti del servizio app][1]

Per aprire l'interfaccia utente in cui sono elencati tutti gli ambienti del servizio app disponibili, è possibile usare l'icona oppure selezionare la freccia di espansione (simbolo ">") nella parte inferiore della barra laterale e quindi Ambienti del servizio app. Se si seleziona uno degli ambienti del servizio app elencati, verrà visualizzata l'interfaccia utente usata per monitorare e gestire tale ambiente.

![Interfaccia utente per il monitoraggio e la gestione di un ambiente del servizio app][2]

Il primo pannello mostra alcune proprietà dell'ambiente del servizio app e un grafico di metriche per ogni pool di risorse. Alcune delle proprietà visualizzate nel blocco **Informazioni di base** sono anche collegamenti ipertestuali che consentono di aprire il pannello associato. Se si seleziona il nome **Rete virtuale** , ad esempio, verrà aperta l'interfaccia utente associata alla rete virtuale in cui viene eseguito l'ambiente del servizio app. I collegamenti **Piani di servizio app** e **App** consentono di aprire i pannelli contenenti gli elementi corrispondenti inclusi nell'ambiente del servizio app.  

<a id="monitoring" class="xliff"></a>

### Monitoraggio
I grafici consentono di visualizzare un'ampia gamma di metriche delle prestazioni in ogni pool di risorse. Per il pool front-end, è possibile monitorare l'utilizzo medio di CPU e memoria. Per i pool di lavoro, è possibile monitorare la quantità usata e la quantità disponibile.

I ruoli di lavoro in un pool di lavoro possono essere usati da più piani di servizio app. Poiché il carico di lavoro non viene distribuito nello stesso modo in cui viene distribuito per i server front-end, l'utilizzo di CPU e memoria non offre informazioni particolarmente utili. È più importante tenere traccia del numero dei ruoli di lavoro usati e disponibili, soprattutto se si gestisce il sistema perché venga usato da altri.  

Tutte le metriche di cui può essere tenuta traccia nei grafici possono essere usate anche per impostare avvisi. L'impostazione di avvisi funziona come altrove nel servizio app. È possibile impostare un avviso nel riquadro interfaccia utente **Avvisi** oppure direttamente nell'interfaccia utente delle singole metriche selezionando **Aggiungi avviso**.

![Interfaccia utente delle metriche][3]

Le metriche illustrate sopra sono le metriche dell'ambiente del servizio app. Sono disponibili metriche anche a livello del piano di servizio app. In questo caso, il monitoraggio di CPU e memoria risulta particolarmente utile.

In un ambiente del servizio app, tutti i piani di servizio app sono dedicati. Di conseguenza, le uniche app in esecuzione negli host allocati al piano di servizio app sono le app incluse in quello stesso piano. Per verificare i dettagli del piano di servizio app, visualizzare il piano da uno degli elenchi nell'interfaccia utente dell'ambiente del servizio app oppure da **Browse App Service plans** (Esplora piani di servizio app), in cui sono elencati tutti i piani.   

<a id="settings" class="xliff"></a>

### Impostazioni
Il pannello dell'ambiente del servizio app include una sezione **Impostazioni** che contiene diverse funzionalità importanti.

**Impostazioni** > **Proprietà**: il pannello **Impostazioni** viene aperto automaticamente quando si visualizza il pannello dell'ambiente del servizio app. Nella parte superiore è disponibile la voce **Proprietà**. In questa sezione sono presenti alcune voci visualizzate anche nella sezione **Informazioni di base**. Le voci particolarmente utili sono **Indirizzo IP virtuale** e **Indirizzo IP in uscita**.

![Pannello Impostazioni e Proprietà][4]

**Impostazioni** > **Indirizzi IP**: quando si crea un'app IP SSL (Secure Sockets Layer) nell'ambiente del servizio app, è necessario un indirizzo IP SSL. Per ottenerne uno, è necessario che l'ambiente del servizio app possieda indirizzi IP SSL da allocare. Quando viene creato, l'ambiente del servizio app ha un indirizzo IP SSL a tale scopo, ma è possibile aggiungerne altri. Per gli indirizzi IP SSL aggiuntivi è previsto un addebito, come indicato nella sezione relativa alle connessioni SSL in [Prezzi di Servizio app ][AppServicePricing]. Il prezzo indicato è il prezzo aggiuntivo per la connessione IP SSL.

**Impostazioni** > **Pool front end** / **Pool di lavoro**: ogni pannello dei pool di risorse consente di visualizzare informazioni relative al pool specifico, nonché i controlli per il ridimensionamento completo del pool.  

Il pannello di base per ogni pool di risorse mostra un grafico con le metriche relative al pool di risorse specifico. Esattamente come per i grafici del pannello dell'ambiente del servizio app, è possibile passare al grafico e impostare gli avvisi desiderati. La procedura di configurazione di un avviso dal pannello dell'ambiente del servizio app per un pool di risorse specifico equivale alla stessa operazione nel pool di risorse. Dal pannello **Impostazioni** del pool di lavoro è possibile accedere a tutti i piani di servizio app o le app in esecuzione nel pool di lavoro.

![Interfaccia utente Impostazioni dei pool di lavoro][5]

<a id="portal-scale-capabilities" class="xliff"></a>

### Funzionalità di ridimensionamento del portale
Le operazioni di ridimensionamento disponibili sono tre:

* Modifica del numero di indirizzi IP dell'ambiente del servizio app disponibili per l'utilizzo con IP SSL
* Modifica delle dimensioni della risorsa di calcolo usata in un pool di risorse
* Modifica del numero di risorse di calcolo usate in un pool di risorse, sia manualmente che tramite ridimensionamento automatico

Nel portale sono disponibili tre modi per controllare il numero di server disponibili nei pool di risorse:

* Operazione di ridimensionamento dalla parte superiore del pannello principale dell'ambiente del servizio app. È possibile apportare più modifiche alla configurazione di scalabilità dei pool di lavoro e front-end. Tutte le modifiche verranno applicate in un'unica operazione.
* Operazione di ridimensionamento manuale dal pannello **Ridimensionamento** del singolo pool di risorse, disponibile in **Impostazioni**.
* Ridimensionamento automatico, impostato dal pannello **Piano** del singolo pool di risorse.

Per eseguire l'operazione di ridimensionamento nel pannello dell'ambiente del servizio app, trascinare il dispositivo di scorrimento sulla quantità desiderata e salvare. Questa interfaccia utente supporta anche la modifica delle dimensioni.  

![Interfaccia utente Piano][6]

Per usare le funzionalità di ridimensionamento automatico o manuale in un pool di risorse specifico, passare a **Impostazioni** > **Pool front end** / **Pool di lavoro**, a seconda del caso. Aprire quindi il pool che si vuole modificare. Passare a **Impostazioni** > **Aumenta istanze** o **Impostazioni** > **Aumenta prestazioni**. Il pannello **Aumenta istanze** consente di controllare la quantità di istanze. **Aumenta prestazioni** consente di controllare le dimensioni delle risorse.  

![Interfaccia utente Impostazione Piano][7]

<a id="fault-tolerance-considerations" class="xliff"></a>

## Considerazioni sulla tolleranza di errore
Un ambiente del servizio app può essere configurato per usare fino a 55 risorse di calcolo totali. Di queste 55 risorse di calcolo, solo 50 possono essere usate per ospitare i carichi di lavoro. I motivi sono due. Esiste un minimo di 2 risorse di calcolo front-end.  Ne rimangono quindi 53 per supportare l'allocazione dei pool di lavoro. Per garantire la tolleranza di errore, è necessario avere una risorsa di calcolo aggiuntiva allocata in base alle regole seguenti:

* Per ogni pool di lavoro è necessaria almeno 1 risorsa di calcolo aggiuntiva non disponibile per l'assegnazione di un carico di lavoro.
* Quando la quantità di risorse di calcolo in un pool di lavoro supera un determinato valore, è necessaria un'altra risorsa di calcolo per la tolleranza di errore. Ciò non si verifica per il pool front-end.

All'interno di un singolo pool di lavoro, i requisiti di tolleranza di errore prevedono che per un determinato valore X di risorse assegnate a un pool di lavoro:

* Se X è compreso tra 2 e 20, la quantità di risorse di calcolo che è possibile usare per i carichi di lavoro è X-1.
* Se X è compreso tra 21 e 40, la quantità di risorse di calcolo che è possibile usare per i carichi di lavoro è X-2.
* Se X è compreso tra 41 e 53, la quantità di risorse di calcolo che è possibile usare per i carichi di lavoro è X-3.

Il footprint minimo include 2 server front-end e 2 ruoli di lavoro.  In base alle indicazioni precedenti, ecco alcuni esempi di chiarimento:  

* Se in un singolo pool sono presenti 30 ruoli di lavoro, è possibile usarne 28 per ospitare i carichi di lavoro.
* Se in un singolo pool sono presenti 2 ruoli di lavoro, è possibile usarne 1 per ospitare i carichi di lavoro.
* Se in un singolo pool sono presenti 20 ruoli di lavoro, è possibile usarne 19 per ospitare i carichi di lavoro.  
* Se in un singolo pool sono presenti 21 ruoli di lavoro, è possibile usarne comunque solo 19 per ospitare i carichi di lavoro.  

L'aspetto della tolleranza di errore è importante e deve essere tenuto in considerazione in caso di ridimensionamento al di sopra di determinate soglie. Se si vuole aggiungere capacità a partire da 20 istanze, passare a 22 o più perché con 21 non viene aggiunta altra capacità. Lo stesso vale in caso di superamento di 40 istanze. Anche in questo caso, il valore successivo per aggiungere capacità è 42.  

<a id="deleting-an-app-service-environment" class="xliff"></a>

## Eliminazione di un ambiente del servizio app
Per eliminare un ambiente del servizio app, è sufficiente usare l'azione **Elimina** nella parte superiore del pannello dell'ambiente del servizio app. In questo caso, verrà richiesto di immettere il nome dell'ambiente del servizio app per confermare che si vuole procedere con l'operazione. Si noti che quando si elimina un ambiente del servizio app, viene eliminato anche tutto il relativo contenuto.  

![Interfaccia utente per l'eliminazione di un ambiente del servizio app][9]  

<a id="getting-started" class="xliff"></a>

## Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere [Come creare un ambiente del servizio app](app-service-web-how-to-create-an-app-service-environment.md).

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/


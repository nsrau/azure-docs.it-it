<properties 
   pageTitle="Profili nidificati di Gestione Traffico | Microsoft Azure"
   description="Questo articolo descrive la funzionalità ";Profili nidificati"; di Gestione traffico di Azure"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="sewhee" />

# Profili nidificati di Gestione traffico

Gestione traffico include diversi metodi di routing del traffico, che consentono di controllare la modalità con cui Gestione traffico sceglie quale endpoint deve ricevere traffico da ogni utente finale. Questi metodi sono descritti in [Metodi di routing del traffico di Gestione traffico](traffic-manager-routing-methods.md) e consentono di soddisfare i requisiti più comuni di routing del traffico.

Ciascun profilo di Gestione traffico specifica un solo metodo di routing del traffico. Alcune volte, tuttavia, le applicazioni più complesse richiedono un sistema di routing del traffico più sofisticato che possa essere fornito da un singolo profilo di Gestione traffico.

Per supportare queste applicazioni complesse, in Gestione traffico i profili possono essere combinati o *nidificati*, consentendo di sfruttare i vantaggi dell'uso di più metodi di routing del traffico da parte di una singola applicazione. I profili nidificati consentono la creazione di schemi di routing del traffico più flessibili e potenti per supportare le esigenze di distribuzioni più grandi e complesse.

I profili nidificati consentono anche di ignorare il comportamento predefinito di Gestione traffico in alcuni casi, ad esempio il routing del traffico all'interno di un'area o durante il failover quando si usa il metodo di routing del traffico Prestazioni.

Nella parte restante di questa pagina viene illustrata, tramite una sequenza di esempi, la modalità d'uso dei profili nidificati di Gestione traffico in una vasta gamma di scenari. Alla fine dell'articolo sono presenti domande frequenti sui profili nidificati

## Esempio 1: Combinazione di metodi di routing del traffico "Prestazioni" e "Ponderato"

Si supponga che l'applicazione venga distribuita in più aree di Azure: Stati Uniti occidentali, Europa occidentale e Asia orientale. Si userà il metodo di routing del traffico "Prestazioni" di Gestione traffico per distribuire il traffico nell'area più vicina all'utente.

![Profilo singolo di Gestione traffico][1]

Si supponga ora di voler valutare un aggiornamento del servizio con un numero ridotto di utenti prima di distribuirlo in modo più ampio. A tale scopo si vuole usare il metodo di routing del traffico "Ponderato", che può indirizzare una piccola percentuale di traffico alla distribuzione di prova. Con un singolo profilo non è possibile combinare i metodi di routing del traffico "Ponderato" e "Prestazioni". Con i profili nidificati è possibile usare entrambi i metodi.

Per farlo, procedere nel modo seguente: si supponga di voler valutare la nuova distribuzione in Europa occidentale. Impostare la distribuzione di prova con la distribuzione di produzione esistente e creare un profilo di Gestione traffico usando solo questi due endpoint e il metodo di routing del traffico "Ponderato". Aggiungere quindi questo profilo "figlio" come endpoint al profilo "padre", che continua a usare il metodo di routing del traffico Prestazioni e contiene anche le altre distribuzioni globali come endpoint.

Il diagramma seguente illustra questo esempio:

![Profili nidificati di Gestione traffico][2]

In questo modo, il traffico indirizzato tramite il profilo padre verrà distribuito in più aree, come di consueto. In Europa occidentale il traffico verrà indirizzato alla produzione e alle distribuzioni di prova in base ai pesi assegnati.

Si noti che, quando il profilo padre usa il metodo di routing del traffico "Prestazioni", deve essere nota la posizione di ogni endpoint. Per gli endpoint nidificati, come per gli endpoint esterni, questa posizione deve essere specificata come parte della configurazione dell'endpoint. Scegliere l'area di Azure più vicina alla distribuzione. Le opzioni disponibili sono le aree di Azure, ovvero le posizioni supportate dalla tabella della latenza di Internet. Per altre informazioni, vedere [Metodo di routing del traffico "Prestazioni" di Gestione traffico](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## Esempio 2: Monitoraggio degli endpoint nei profili nidificati

Gestione traffico esegue continuamente il monitoraggio dell'integrità di ogni endpoint di servizio. Se un endpoint è stato identificato come danneggiato, Gestione traffico indirizzerà gli utenti ad endpoint alternativi, mantenendo in tal modo la disponibilità complessiva del servizio. Il comportamento di monitoraggio e failover degli endpoint si applica a tutti i metodi di routing del traffico. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md).

Per i profili nidificati si applicano alcune specifiche regole di monitoraggio degli endpoint. Quando un profilo padre viene configurato con un profilo figlio come endpoint nidificato, l'elemento padre non esegue direttamente i controlli dell'integrità sull'elemento figlio. L'integrità degli endpoint del profilo figlio viene usata invece per calcolare l'integrità complessiva del profilo figlio e queste informazioni vengono propagate alla gerarchia dei profili nidificati per determinare l'integrità dell'endpoint nidificato all'interno del profilo padre. In tal modo, si determina se il profilo padre indirizzerà il traffico all'elemento figlio. I dettagli completi sull'esatta modalità di calcolo dell'integrità dell'endpoint nidificato nel profilo padre a partire dall'integrità del profilo figlio sono riportati [di seguito](#faq).

Tornando all'esempio 1, si supponga che la distribuzione di produzione in Europa occidentale abbia esito negativo. Per impostazione predefinita il profilo "figlio" indirizzerà tutto il traffico alla distribuzione di prova. Se anche questa operazione ha esito negativo, il profilo padre stabilirà che, poiché tutti gli endpoint figlio sono danneggiati, il profilo figlio non deve ricevere traffico ed eseguirà un failover di tutto il traffico dell'Europa occidentale ad altre aree.

![Failover dei profili nidificati (comportamento predefinito)][3]

È possibile che questa soluzione risulti soddisfacente o che non si voglia usare la distribuzione di prova come failover per tutto il traffico dell'Europa occidentale, preferendo eseguire il failover ad altre aree se la distribuzione di produzione in Europa occidentale ha esito negativo, *indipendentemente* dall'integrità della distribuzione di prova. È anche possibile l'operazione seguente: durante la configurazione del profilo figlio come endpoint nel profilo padre, è possibile specificare il parametro "MinChildEndpoints", che determina il numero minimo di endpoint che devono essere disponibili nel profilo figlio. Al di sotto di questa soglia, il cui valore predefinito è 1, il profilo padre considererà l'intero profilo figlio non disponibile e indirizzerà il traffico agli altri endpoint del profilo padre.

Nell'esempio riportato di seguito viene illustrato quanto segue: quando MinChildEndpoints è impostato su 2, se la distribuzione in Europa occidentale ha esito negativo, il profilo padre stabilirà che il profilo figlio non deve ricevere traffico e gli utenti verranno indirizzati ad altre aree.

![Failover dei profili nidificati con 'MinChildEndpoints' = 2][4]

Si noti che quando il profilo figlio usa il metodo di routing del traffico "Priorità", tutto il traffico a tale elemento figlio viene ricevuto da un singolo endpoint. In questo caso è quindi probabilmente inutile impostare MinChildEndpoints su un valore diverso da "1".

## Esempio 3: Aree di failover con priorità con metodo di routing del traffico "Prestazioni"

Quando un singolo profilo usa il metodo di routing del traffico "Prestazioni", se un endpoint (ad esempio Europa occidentale) ha esito negativo, tutto il traffico normalmente indirizzato a tale endpoint viene invece distribuito tra gli altri endpoint in tutte le aree. Si tratta del comportamento predefinito per il metodo di routing del traffico "Prestazioni", progettato per evitare il sovraccarico dell'endpoint successivo più vicino e una conseguente serie di errori a catena.

![Routing del traffico "Prestazioni" con failover predefinito][5]

Si supponga tuttavia di preferire che il failover del traffico dell'Europa occidentale venga eseguito negli Stati Uniti occidentali e che il traffico venga indirizzato altrove solo se entrambi gli endpoint non sono disponibili. A tale scopo, creare un profilo figlio che usa il metodo di routing del traffico "Priorità", come illustrato di seguito:

![Routing del traffico "Prestazioni" con failover preferenziale][6]

Poiché l'endpoint dell'Europa occidentale ha priorità più elevata rispetto all'endpoint degli Stati Uniti occidentali, tutto il traffico verrà inviato all'endpoint dell'Europa occidentale quando entrambe le aree sono online. Se l'Europa occidentale ha esito negativo, il relativo traffico viene indirizzato agli Stati Uniti occidentali. Solo nel caso in cui anche gli Stati Uniti occidentali abbiano esito negativo, il traffico dell'Europa occidentale verrebbe indirizzato all'Asia orientale.

È possibile ripetere questo modello per tutte le aree, sostituendo i 3 endpoint nel profilo padre con 3 profili figlio, ognuno con una sequenza di failover con priorità.

## Esempio 4: Controllo del metodo di routing del traffico "Prestazioni" tra più endpoint nella stessa area

Si supponga che il metodo di routing del traffico "Prestazioni" venga usato in un profilo con più di un endpoint in una determinata area, ad esempio gli Stati Uniti occidentali. Per impostazione predefinita il traffico indirizzato a tale area verrà distribuito in modo uniforme tra tutti gli endpoint disponibili nell'area.

![Routing del traffico "Prestazioni" con distribuzione del traffico nell'area (comportamento predefinito)][7]

Questa impostazione predefinita può essere modificata tramite i profili nidificati di Gestione traffico. Anziché aggiungere più endpoint negli Stati Uniti occidentali, gli endpoint possono essere inclusi in un profilo figlio separato e il profilo figlio può essere aggiunto all'elemento padre come unico endpoint negli Stati Uniti occidentali. Le impostazioni del profilo figlio possono quindi essere usate per controllare la distribuzione del traffico negli Stati Uniti occidentali, ad esempio abilitando il routing del traffico basato sulla priorità o sul peso all'interno di tale area.

![Routing del traffico "Prestazioni" con distribuzione personalizzata del traffico nell'area][8]

## Esempio 5: Impostazioni di monitoraggio per ogni endpoint

Si supponga di usare Gestione traffico per facilitare la migrazione di traffico tra un sito Web legacy locale e una nuova versione basata su cloud ospitata in Azure. Per il sito legacy si vuole usare la home page (percorso "/") per monitorare l'integrità del sito, ma per la nuova versione basata su cloud si vuole implementare una pagina di monitoraggio personalizzata che include controlli aggiuntivi (percorso "/monitor.aspx").

![Monitoraggio degli endpoint di Gestione traffico (comportamento predefinito)][9]

Le impostazioni di monitoraggio in un profilo di Gestione traffico si applicano a tutti gli endpoint all'interno del profilo, pertanto è necessario aver usato in precedenza lo stesso percorso in entrambi i siti. Con i profili nidificati di Gestione traffico è ora possibile usare un profilo figlio per sito per definire diverse impostazioni di monitoraggio per ogni sito:

![Monitoraggio degli endpoint di Gestione traffico con impostazioni per ogni endpoint][10]

## Domande frequenti

### Come si configurano i profili nidificati?

I profili nidificati di Gestione traffico possono essere configurati usando Azure Resource Manager (ARM), le API REST di Azure Service Management (ASM), i cmdlet di PowerShell e i comandi multipiattaforma di Azure CLI. Sono supportati anche nel portale di Azure, ma non nel portale "classico".

### Quanti livelli di nidificazione supporta Gestione traffico?
È possibile nidificare i profili fino a 10 livelli. I "loop" non sono consentiti.

### È possibile combinare altri tipi di endpoint con profili figlio nidificati nello stesso profilo di Gestione traffico?

Sì. Non esistono restrizioni sulla modalità di combinazione di tipi diversi di endpoint all'interno di un profilo.

### Come viene applicato il modello di fatturazione per i profili nidificati?

L'uso di profili nidificati non ha alcun impatto negativo sui prezzi.

La fatturazione di Gestione traffico include due componenti: i controlli dell'integrità degli endpoint e milioni di query DNS (per informazioni dettagliate, vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/traffic-manager/).) La fatturazione si applica ai profili nidificati nel modo seguente:

- Controlli dell'integrità degli endpoint: non è previsto alcun addebito per un profilo figlio configurato come endpoint in un profilo padre. Gli endpoint nel profilo figlio che eseguono il monitoraggio dei servizi sottostanti vengono fatturati nel modo consueto.

- Query DNS: ogni query viene conteggiata una sola volta. Una query in un profilo padre che restituisce un endpoint da un profilo figlio viene fatturata solo per il profilo padre.

### I profili nidificati influiscono sulle prestazioni?

No, quando si usano i profili nidificati non si verifica alcun impatto sulle prestazioni.

I server dei nomi di Gestione traffico attraversano internamente la gerarchia dei profili durante l'elaborazione di ogni query DNS, in modo che una query DNS a un profilo padre possa ricevere una risposta DNS con un endpoint da un profilo figlio.

Viene usato pertanto un singolo record CNAME, lo stesso di quando si usa un singolo profilo di Gestione traffico. **Non** è necessaria una sequenza di record CNAME, uno per ogni profilo nella gerarchia, pertanto non si verifica alcuna riduzione delle prestazioni.

### Come viene calcolata da Gestione traffico l'integrità di un endpoint nidificato in un profilo padre, in base all'integrità del profilo figlio?

Quando un profilo padre viene configurato con un profilo figlio come endpoint nidificato, l'elemento padre non esegue direttamente i controlli dell'integrità sull'elemento figlio. L'integrità degli endpoint del profilo figlio viene usata invece per calcolare l'integrità complessiva del profilo figlio e queste informazioni vengono propagate alla gerarchia dei profili nidificati per determinare l'integrità dell'endpoint nidificato all'interno del profilo padre. In tal modo, si determina se il profilo padre indirizzerà il traffico all'elemento figlio.

La tabella seguente descrive il comportamento dei controlli dell'integrità di Gestione traffico per un endpoint nidificato in un profilo padre che punta a un profilo figlio.

|Stato di monitoraggio del profilo figlio|Stato di monitoraggio dell'endpoint padre|Note|
|---|---|---|
|Disabilitato. Il profilo figlio è stato disabilitato dall'utente.|Arrestato|Lo stato dell'endpoint padre è Stopped, non Disabled. Lo stato Disabled è usato esclusivamente per indicare che l'utente ha disabilitato l'endpoint nel profilo padre.|
|Danneggiato. Almeno uno degli endpoint del profilo figlio è nello stato Danneggiato.|Online: il numero di endpoint Online nel profilo figlio è pari almeno al valore di MinChildEndpoints. CheckingEndpoint: il numero di endpoint Online e CheckingEndpoint nel profilo figlio è pari almeno al valore di MinChildEndpoints. Danneggiato: negli altri casi.|Il traffico viene indirizzato a un endpoint con stato CheckingEndpoint. Se il valore di MinChildEndpoints è troppo elevato, l'endpoint sarà sempre danneggiato.|
|Online. Almeno uno degli endpoint del profilo figlio è nello stato Online e nessun endpoint è nello stato Danneggiato.|Vedere sopra.||
|CheckingEndpoints. Almeno uno degli endpoint del profilo figlio è nello stato "CheckingEndpoint" e nessun endpoint è nello stato "Online" o "Danneggiato".|Come sopra.||
|Inattivo. Tutti gli endpoint del profilo figlio sono nello stato Disabilitato o Arrestato oppure si tratta di un profilo senza endpoint.|Arrestato||


## Passaggi successivi

Altre informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md)

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md)

<!--Image references-->  
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

<!---HONumber=AcomDC_0824_2016-->
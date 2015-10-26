<properties 
   pageTitle="Che cos'è un gruppo di sicurezza di rete"
   description="Informazioni sui gruppi di sicurezza di rete"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="telmos" />

# Che cos'è un gruppo di sicurezza di rete

È possibile usare un gruppo di sicurezza di rete per controllare il traffico verso una o più istanze di macchina virtuale (VM) in una rete virtuale. Un NSG contiene le regole di controllo di accesso che consentono o negano il traffico in base alla direzione del traffico, al protocollo, all’indirizzo e alla porta di origine e all’indirizzo e alla porta di destinazione. Le regole di un gruppo di sicurezza di rete possono essere modificate in qualsiasi momento e le modifiche vengono applicate a tutte le istanze associate.

>[AZURE.WARNING]Gli NSG possono essere utilizzati solo nelle reti virtuali regionali. Se si sta cercando di proteggere gli endpoint in una distribuzione senza una rete virtuale o che utilizza una rete virtuale associata a un set di affinità, consultare [Che cos'è un elenco di controllo di accesso (ACL) di endpoint?](./virtual-networks-acl.md). È inoltre possibile [eseguire la migrazione della rete virtuale a una rete virtuale di area](./virtual-networks-migrate-to-regional-vnet.md).

![NSG](./media/virtual-network-nsg-overview/figure1.png)

La figura precedente illustrata una rete virtuale con due subnet, con un NSG associato a ogni subnet per il controllo del traffico.

>[AZURE.NOTE]Gli elenchi di controllo di accesso basati su endpoint e i gruppi di sicurezza di rete non sono supportati nella stessa istanza di macchina virtuale. Se si vuole usare un gruppo di sicurezza di rete ed è già presente un elenco di controllo di accesso basato su endpoint, rimuovere prima l'elenco di controllo di accesso. Per informazioni su come procedere, vedere [Gestione degli elenchi di controllo di accesso (ACL) per gli endpoint tramite PowerShell](virtual-networks-acl-powershell.md).

## Funzionamento di un gruppo di sicurezza di rete

I gruppi di sicurezza di rete sono diversi dagli elenchi di controllo di accesso basati su endpoint. Gli elenchi di controllo di accesso basati su endpoint funzionano solo sulla porta pubblica esposta tramite l'endpoint di input. Un gruppo di sicurezza di rete funziona su una o più istanze di macchina virtuale e controlla tutto il traffico in ingresso e in uscita nella macchina virtuale.

Un gruppo di sicurezza di rete ha un valore *Nome* impostato, è associato a un valore *Area* e include un'etichetta descrittiva. Contiene due tipi di regole, **In ingresso** e **In uscita**. Le regole In ingresso vengono applicate ai pacchetti in ingresso in una macchina virtuale, mentre le regole In uscita vengono applicate ai pacchetti in uscita dalla macchina virtuale. Le regole vengono applicate nell'host in cui si trova la macchina virtuale. Un pacchetto in ingresso o in uscita deve soddisfare una regola **Consenti** per essere autorizzato, in caso contrario verrà eliminato.

Le regole vengono elaborate nell'ordine di priorità. Ad esempio, una regola con un numero di priorità più basso (come 100) viene elaborata prima delle regole con numeri di priorità più alti (come 200). Dopo che viene trovata una corrispondenza, non vengono elaborate altre regole.

Una regola specifica quanto segue:

- **Nome:** identificatore univoco per la regola

- **Tipo:** In ingresso/In uscita

- **Priorità:** <You can specify an integer between 100 and 4096>

- **Indirizzo IP di origine:** CIDR dell'intervallo di indirizzi IP di origine

- **Intervallo porte di origine:** <integer or range between 0 and 65536>

- **Intervallo IP di destinazione:** CIDR dell'intervallo di indirizzi IP di destinazione

- **Intervallo porte di destinazione:** <integer or range between 0 and 65536>

- **Protocollo:** <è consentito TCP, UDP o "*">

- **Accesso:** Consenti/Nega

### Regole predefinite

Un gruppo di sicurezza di rete contiene regole predefinite. Le regole predefinite non possono essere eliminate, ma poiché hanno la priorità più bassa, è possibile eseguirne l'override con le regole create dall'utente. Le regole predefinite descrivono le impostazioni consigliate dalla piattaforma. Come illustrato dalle regole predefinite seguenti, il traffico che origina e termina in una rete virtuale è consentito sia in ingresso che in uscita.

Mentre la connettività a Internet è consentita per la direzione in uscita, per impostazione predefinita è bloccata per la direzione in ingresso. È disponibile una regola predefinita per consentire al servizio di bilanciamento del carico di Azure di eseguire il probe dell'integrità della macchina virtuale. È possibile eseguire l'override di questa regola se la macchina virtuale o il set di macchine virtuali del gruppo di sicurezza di rete non fa parte del set con carico bilanciato.

Le regole predefinite sono:

**In ingresso**

| Nome | Priorità | IP di origine | Porta di origine | IP di destinazione | Porta di destinazione | Protocollo | Accesso |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| CONSENTI RETE VIRTUALE IN INGRESSO | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | CONSENTI |
| CONSENTI BILANCIAMENTO DEL CARICO DI AZURE IN INGRESSO | 65001 | AZURE\_LOADBALANCER | * | * | * | * | CONSENTI |
| NEGA TUTTO IN INGRESSO | 65500 | * | * | * | * | * | NEGA |

**In uscita**

| Nome | Priorità | IP di origine | Porta di origine | IP di destinazione | Porta di destinazione | Protocollo | Accesso |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| CONSENTI RETE VIRTUALE IN USCITA | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | CONSENTI |
| CONSENTI INTERNET IN USCITA | 65001 | * | * | INTERNET | * | * | CONSENTI |
| NEGA TUTTO IN USCITA | 65500 | * | * | * | * | * | NEGA |

### Tag predefiniti

I tag predefiniti sono identificatori forniti dal sistema per risolvere una categoria di indirizzi IP. I tag predefiniti possono essere specificati in regole definite dal cliente. Di seguito sono illustrati i tag predefiniti:

- **VIRTUAL\_NETWORK:** questo tag predefinito identifica tutto lo spazio di indirizzi della rete. Include lo spazio di indirizzi della rete virtuale (CIDR di IP in Azure), nonché tutto lo spazio di indirizzi locale connesso (reti locali). Include anche gli spazi di indirizzi tra reti virtuali.

- **AZURE\_LOADBALANCER:** questo tag predefinito identifica il bilanciamento del carico dell'infrastruttura di Azure. Viene convertito in un IP del data center di Azure da cui avranno origine i probe di integrità di Azure. È necessario solo se la macchina virtuale o il set di macchine virtuali associato al gruppo di sicurezza di rete fa parte di un set con carico bilanciato.

- **INTERNET:** questo tag predefinito identifica lo spazio di indirizzi IP esterno alla rete virtuale e raggiungibile tramite la rete Internet pubblica. Questo intervallo include anche lo spazio di IP pubblici appartenenti ad Azure.

### Traffico ICMP

Le regole del gruppo di sicurezza di rete correnti consentono solo i protocolli *TCP* o *UDP*. Non esiste un tag specifico per *ICMP*. Il traffico ICMP è tuttavia consentito in una rete virtuale per impostazione predefinita tramite le regole della rete virtuale in ingresso che consentono il traffico da/verso qualsiasi porta e protocollo all'interno della rete virtuale.

## Associazione di gruppi di sicurezza di rete

È possibile associare un NSG a VM, schede di rete e subnet.

- **Associazione di un NSG a una VM.** Quando si associa un NSG a una VM, le regole di accesso alla rete nell’NSG vengono applicate a tutto il traffico verso e dalla VM. 

- **Associazione di un NSG a una scheda di rete.** Quando si associa un NSG a una scheda di rete, le regole di accesso di rete nell’NSG vengono applicate solo a tale scheda di rete. Ciò significa che in una VM con più schede di rete, se un NSG viene applicato a una singola scheda di rete, non influisce sul traffico associato alle altre schede di rete.

- **Associazione di un NSG a una subnet**. Quando un NSG viene associato a una subnet, le regole di accesso alla rete dell’NSG vengono applicate a tutte le VM nella subnet.

È possibile associare NSG diversi a una VM, a una scheda di rete utilizzata dalla VM e alla subnet a cui è associata la scheda di rete. In questo caso, tutte le regole di accesso alla rete vengono applicate al traffico nell'ordine seguente:

- **Traffico in ingresso**
	1. NSG della subnet.
	2. NSG della scheda di rete.
	3. NSG della VM.
- **Traffico in uscita**
	1. NSG della VM.
	2. NSG della scheda di rete.
	3. NSG della subnet.

![Elenchi di controllo di accesso e gruppi di sicurezza di rete](./media/virtual-network-nsg-overview/figure2.png)

>[AZURE.NOTE]Anche se è possibile associare solo un singolo NSG a una subnet, una VM o una scheda di rete; è possibile associare lo stesso NSG a quante risorse si desidera.

## Considerazioni sulla progettazione

È necessario comprendere come le VM comunicano con i servizi di infrastruttura e i servizi PaaS ospitati da Azure quando si progettano gli NSG. La maggior parte dei servizi PaaS di Azure, ad esempio database SQL e archiviazione, sono accessibili solo tramite un indirizzo Internet pubblico. Lo stesso vale per le probe di bilanciamento del carico.

Uno scenario comune in Azure è la separazione dei ruoli di macchine virtuali e PaaS nelle subnet in base al fatto che questi oggetti richiedano l'accesso a internet o meno. In questo scenario, potrebbe esserci una subnet con macchine virtuali o istanze del ruolo che richiedono l'accesso ai servizi Paas di Azure, ad esempio database SQL e archiviazione, ma che non richiedono le comunicazioni in ingresso o in uscita all’Internet pubblico.

Tenere presente la seguente regola del gruppo di sicurezza di rete per tale scenario:

| Nome | Priorità | IP di origine | Porta di origine | IP di destinazione | Porta di destinazione | Protocollo | Accesso |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|INTERNET NON DISPONIBILE|100| VIRTUAL\_NETWORK|& #42;|INTERNET|& #42;|TCP|NEGA| 

Poiché la regola consiste nel negare gli accessi ad Internet dalla rete virtuale, le macchine virtuali non saranno in grado di accedere a qualsiasi servizio PaaS di Azure che richieda un endpoint Internet pubblico, ad esempio database SQL.

Invece di utilizzare una regola di negazione, bisognerebbe prendere in considerazione di utilizzare una regola per consentire l'accesso dalla rete virtuale a Internet, ma che neghi l'accesso da Internet alla rete virtuale, come illustrato di seguito:

| Nome | Priorità | IP di origine | Porta di origine | IP di destinazione | Porta di destinazione | Protocollo | Accesso |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|A INTERNET|100| VIRTUAL\_NETWORK|& #42;|INTERNET|& #42;|TCP|CONSENTI|
|DA INTERNET|110| INTERNET|& #42;|VIRTUAL\_NETWORK|& #42;|TCP|NEGA| 

>[AZURE.WARNING]Azure usa una subnet speciale definita subnet **gateway** per gestire il gateway VPN con altre reti virtuali e reti locali. Se si associa un gruppo di sicurezza di rete a questa subnet, il gateway VPN smetterà di funzionare come previsto. Non associare i gruppi di sicurezza di rete alle subnet del gateway!

È inoltre necessario prendere in considerazione le regole speciali elencate di seguito. Assicurarsi di non bloccare il traffico consentito da tali regole, in caso contrario l'infrastruttura non sarà in grado di comunicare con i servizi essenziali di Azure.

- **IP virtuale del nodo host:** i servizi di infrastruttura di base come DHCP, DNS e il monitoraggio dello stato vengono forniti mediante l'indirizzo IP dell'host virtualizzato 168.63.129.16. Questo indirizzo IP pubblico appartiene a Microsoft e sarà l'unico indirizzo IP virtualizzato usato in tutte le aree a questo scopo. Questo indirizzo IP è mappato all'indirizzo IP fisico del computer server (nodo host) che ospita la macchina virtuale. Il nodo host svolge la funzione di inoltro DHCP, di resolver ricorsivo DNS e di origine probe per il probe di integrità del bilanciamento del carico e per il probe di integrità del computer. La comunicazione con questo indirizzo IP non deve essere considerata come un attacco.

- **Licenze (servizio di gestione delle chiavi):** le immagini Windows in esecuzione sulle macchine virtuali devono essere concesse in licenza. A tale scopo viene inviata una richiesta di licenza ai server host del servizio di gestione delle chiavi che gestiscono le query di questo tipo. Questo avverrà sempre sulla porta in uscita 1688.

## Limiti

È necessario considerare le seguenti limitazioni quando si progettano gli NSG.

|**Descrizione**|**Limite**|
|---|---|
|Numero di NSG che è possibile associare a una subnet, una VM o una scheda di rete|1|
|NSG per area per sottoscrizione|100|
|Regole NSG per NSG|200|

Assicurarsi di visualizzare tutti i [limiti relativi ai servizi di rete in Azure](../azure-subscription-service-limits/#networking-limits) prima di progettare la soluzione.

## Passaggi successivi

- [Distribuire gli NSG nel modello di distribuzione classica](virtual-networks-create-nsg-classic-ps.md).
- [Distribuire gli NSG nella Gestione risorse](virtual-networks-create-nsg-arm-pportal.md).

<!---HONumber=Oct15_HO3-->
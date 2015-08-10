<properties
   pageTitle="Domande frequenti su ExpressRoute"
   description="In Domande frequenti su ExpressRoute sono disponibili informazioni su servizi di Azure supportati, costi, dati e connessioni, Contratto di servizio, provider e località, larghezza di banda e altri dettagli tecnici."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="cherylmc"/>

# Domande frequenti su ExpressRoute


## Che cos'è ExpressRoute?
ExpressRoute è un servizio di Azure che permette di creare connessioni private tra i data center Microsoft e l'infrastruttura disponibile localmente o in una struttura con percorso condiviso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica e offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

### Quali sono i vantaggi dell'uso di ExpressRoute e delle connessioni di rete private?
Le connessioni ExpressRoute non sfruttano la rete Internet pubblica e offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e latenze minori e coerenti rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso di connessioni ExpressRoute per trasferire dati tra dispositivi locali e Azure può produrre vantaggi significativi in termini di costi.

### Quali servizi cloud Microsoft sono supportati in ExpressRoute?
ExpressRoute supporta attualmente la maggior parte dei servizi di Microsoft Azure. È stato annunciato il supporto per i servizi di Office 365 in ExpressRoute. Gli aggiornamenti relativi alla disponibilità generale saranno presto disponibili.

### Dove è disponibile in servizio?
Per informazioni sulla località e la disponibilità del servizio, vedere [Partner e località per ExpressRoute](expressroute-locations.md).

### In che modo è possibile usare ExpressRoute per connettersi a Microsoft se non è disponibile una relazione con uno dei partner del gestore ExpressRoute?
Si può selezionare un gestore regionale e connessioni Ethernet via terra in una delle sedi supportate del provider di Exchange. È quindi possibile connettersi ad Azure nella sede del provider di Exchange. Per verificare se il provider di rete è presente in una delle località di Exchange, vedere l'ultima sezione della pagina [Partner e località per ExpressRoute](expressroute-locations.md). Sarà quindi possibile ordinare un circuito ExpressRoute tramite il provider di Exchange per connettersi a Azure.

### Quanto costa ExpressRoute?
Per informazioni sui prezzi, vedere [Dettagli prezzi](http://azure.microsoft.com/pricing/details/expressroute/).

### Se si acquista un circuito ExpressRoute di una determinata larghezza di banda, la connessione VPN acquistata dal provider di servizi di rete deve essere della stessa velocità?
No. È possibile acquistare una connessione VPN di qualsiasi velocità dal provider di servizi. Tuttavia, la connessione ad Azure sarà limitata alla larghezza di banda del circuito ExpressRoute acquistato.

### Se si paga per un circuito ExpressRoute di una determinata larghezza di banda, si ha la possibilità di potenziarlo fino a velocità più elevate, se necessario?
Sì. I circuiti ExpressRoute sono configurati per supportare i casi in cui è possibile potenziare fino al doppio il limite di larghezza di banda acquistato, senza alcun costo aggiuntivo. Contattare il provider di servizi per verificare se supporta questa funzionalità.

### Si può usare la stessa connessione di rete privata con la rete virtuale e altri servizi di Azure contemporaneamente?
Sì. Dopo l'installazione, il circuito ExpressRoute consentirà di accedere ai servizi all'interno di una rete virtuale e ad altri servizi di Azure contemporaneamente. La connessione alle reti virtuali verrà effettuata tramite il percorso di peering privato e ad altri servizi tramite il percorso di peering pubblico.

### ExpressRoute offre un contratto di servizio?
Per altre informazioni, vedere la [pagina relativa al contratto di servizio per ExpressRoute](http://azure.microsoft.com/support/legal/sla/).

## Servizi di Azure supportati
La maggior parte dei servizi di Azure è supportata tramite ExpressRoute.

La connettività alle macchine virtuali e ai servizi cloud distribuiti nelle reti virtuali è supportata in un percorso di peering privato.

I siti Web di Azure sono supportati tramite il percorso di peering pubblico.

Tutti gli altri servizi sono accessibili tramite il percorso di peering pubblico. Le eccezioni sono le seguenti:

**Gli elementi seguenti non sono supportati:**

- RETE CDN
- Test di carico di Visual Studio Online
- Multi-Factor Authentication

## Dati e connessioni

### Esistono limiti alla quantità di dati trasferibili usando ExpressRoute?
Non esistono limiti alla quantità di dati trasferibili. Per informazioni sulle tariffe relative alle larghezze di banda, vedere [Dettagli prezzi](http://azure.microsoft.com/pricing/details/expressroute/).

### Quali sono le velocità di connessione supportate da ExpressRoute?
Offerte relative alle larghezze di banda supportate:

|**Provider**|**Larghezza di banda**|
|---|---|
|**Provider di rete**|10 Mbps, 50 Mbps, 100 Mbps, 500 Mbps, 1 Gbps|
|**Provider di Exchange**|200 Mbps, 500 Mbps, 1Gbps, 10Gbps|

### Quali provider del servizio sono disponibili?
Per l'elenco dei provider di servizi e delle relative località, vedere [Partner e località per ExpressRoute](expressroute-locations.md).

## Dettagli tecnici

### Quali sono i requisiti tecnici per la connessione della posizione locale ad Azure?
Per informazioni sui requisiti, vedere la [pagina dei prerequisiti di ExpressRoute](expressroute-prerequisites.md).

### Le connessioni a ExpressRoute sono ridondanti?
Sì. Ogni circuito ExpressRoute ha una coppia ridondante di connessioni incrociate configurate per garantire una disponibilità elevata.

### In caso di errore di un collegamento a ExpressRoute, verrà persa la connettività?
In caso di errore di una delle connessioni incrociate, la connettività non verrà persa. È attualmente disponibile una connessione ridondante per supportare il carico della rete. Si possono creare più circuiti in sedi di peering diverse per ottenere una maggiore resilienza agli errori.

### Per permettere il funzionamento del servizio, si devono configurare entrambi i collegamenti?
Se si effettua la connessione tramite un provider di servizi di rete, tale provider di servizi di rete gestirà automaticamente la configurazione di collegamenti ridondanti. Se ci si connette tramite un provider di Exchange, sarà necessario configurare entrambi i collegamenti. Il Contratto di servizio sarà considerato nullo se il circuito non viene configurato per la ridondanza.

### Si può estendere una delle reti VLAN ad Azure tramite ExpressRoute?
No. Non sono supportate estensioni alla connettività di livello 2 in Azure.

### La sottoscrizione può includere più di un circuito ExpressRoute?
Sì. La sottoscrizione può includere più di un circuito ExpressRoute. Il limite predefinito per il numero di circuiti dedicati è impostato su 10. Se necessario, contattare il supporto tecnico Microsoft per aumentare il limite.

### Si possono usare circuiti ExpressRoute di diversi provider di servizi?
Sì. È possibile usare i circuiti ExpressRoute con molti provider di servizi. Ogni circuito ExpressRoute sarà associato a un solo provider di servizi.

### Come si connettono le reti virtuali a un circuito ExpressRoute?
Di seguito è illustrata la procedura di base.

- È necessario stabilire un circuito ExpressRoute e richiedere al provider di servizi di abilitarlo.
- Se si usa un provider di Exchange, è necessario configurare il BGP per il peering privato.
- È necessario collegare la rete virtuale al circuito ExpressRoute.

Per informazioni, vedere le esercitazioni seguenti:

- [Configurare una connessione ExpressRoute tramite un provider di servizi di rete](expressroute-configuring-nsps.md)
- [Configurare una connessione ExpressRoute mediante un provider di Exchange](expressroute-configuring-exps.md)
- [Configurare una rete virtuale e un gateway per ExpressRoute](expressroute-configuring-vnet-gateway.md)

### Sono previsti limiti di connettività per il circuito ExpressRoute?
Sì. La pagina [Partner e località per ExpressRoute](expressroute-locations.md) offre una panoramica dei limiti di connettività per un circuito ExpressRoute. La connettività per un circuito ExpressRoute è limitata a una singola area geopolitica. È possibile espandere la connettività per superare le aree geopolitiche abilitando la funzionalità Premium di ExpressRoute.

### Un circuito ExpressRoute può essere collegato a più reti virtuali?
Sì. È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute.

### Sono disponibili più sottoscrizioni di Azure che contengono reti virtuali. È possibile connettere reti virtuali di sottoscrizioni distinte a un singolo circuito ExpressRoute?
Sì. È possibile autorizzare fino a 10 sottoscrizioni di Azure a usare un unico circuito ExpressRoute. Per aumentare questo limite, abilitare la funzionalità Premium di ExpressRoute.

Per altri dettagli, vedere [Condivisione di un circuito ExpressRoute tra più sottoscrizioni](expressroute-share-circuit.md).

### Le reti virtuali connesse allo stesso circuito sono isolate tra loro?
No. Tutte le reti virtuali collegate allo stesso circuito ExpressRoute appartengono allo stesso dominio di routing e non sono isolate tra loro dal punto di vista del routing. Se è necessario l'isolamento delle route, creare un circuito ExpressRoute separato.

### È possibile connettere una sola rete virtuale a più circuiti ExpressRoute?
Sì. È possibile collegare una singola rete virtuale con un massimo di quattro circuiti ExpressRoute. Tutti i circuiti ExpressRoute devono trovarsi nello stesso continente. È possibile ordinarli tramite diversi provider di servizi e in località differenti.

### Si può accedere a Internet usando le reti virtuali connesse a circuiti ExpressRoute?
Sì. Se non è stata eseguita la pubblicazione di route predefinite (0.0.0.0/0) o prefissi di route Internet tramite la sessione BGP, sarà possibile connettersi a Internet da una rete virtuale collegata a un circuito ExpressRoute.

### Si può bloccare la connettività Internet per le reti virtuali connesse a circuiti ExpressRoute?
Sì. È possibile pubblicare route predefinite (0.0.0.0/0) per bloccare tutta la connettività Internet per le macchine virtuali distribuite in una rete virtuale e indirizzare tutto il traffico in uscita attraverso il circuito ExpressRoute. Si noti che se si pubblicano route predefinite, il traffico verrà forzato verso i servizi offerti tramite peering pubblico, ad esempio l'archiviazione di Azure e database SQL, nella sede locale. Sarà necessario configurare i router in modo che restituiscano traffico ad Azure tramite il percorso di peering pubblico su Internet.

### Le reti virtuali collegate allo stesso circuito ExpressRoute possono comunicare tra loro?
Sì. Le macchine virtuali distribuite in reti virtuali connesse allo stesso circuito ExpressRoute possono comunicare tra loro.

### È possibile usare la connettività da sito per reti virtuali insieme a ExpressRoute?
Sì. ExpressRoute può coesistere con VPN da sito a sito.

### È possibile spostare una rete virtuale da una configurazione da sito a sito o da punto a sito, in modo da usare ExpressRoute?
Sì. Sarà necessario creare un gateway di ExpressRoute nella rete virtuale. Il processo comporterà un breve tempo di inattività.

### Cosa si deve fare per connettersi al servizio di archiviazione di Azure tramite ExpressRoute?
Si deve stabilire un circuito ExpressRoute e configurare le route per il peering pubblico.

### Sono previsti limiti per i numeri di route pubblicabili?
Sì. Sono accettati al massimo 4000 prefissi di route per il peering privato e pubblico. Se si abilita la funzionalità Premium di ExpressRoute, sarà possibile aumentare questo valore fino a 10.000 route.

### Sono previste limitazioni per gli intervalli IP pubblicabili tramite la sessione BGP?
I prefissi pubblicati tramite BGP devono essere pari a /29 o superiori (da /28 a /8).

I prefissi privati (RFC1918) saranno esclusi tramite filtro dalla sessione BGP per il peering pubblico.

### Cosa succede se si superano i limiti per BGP?
Le sessioni BGP saranno rimosse. Saranno ripristinate quando il numero di prefissi tornerà sotto il limite consentito.

### Dopo la pubblicazione della route predefinita (0.0.0.0/0) alle reti virtuali, non è possibile attivare Windows in esecuzione nelle macchine virtuali di Azure. Come si risolve questo problema?
I passaggi seguenti permettono ad Azure di riconoscere la richiesta di attivazione:

1. Stabilire il peering pubblico per il circuito ExpressRoute.
2. Eseguire una ricerca DNS e trovare l'indirizzo IP di **kms.core.windows.net**
3. Eseguire una delle operazioni seguenti in modo che il Servizio di gestione delle chiavi riconosca che la richiesta di attivazione proviene da Azure e soddisfi la richiesta.
	- Nella rete locale reindirizzare il traffico destinato all'indirizzo IP (ottenuto nel passaggio 2) ad Azure tramite il peering pubblico.
	- Richiedere al provider di servizi di rete di reindirizzare il traffico ad Azure tramite il peering pubblico.

### È possibile modificare la larghezza di banda di un circuito ExpressRoute?
Sì. È possibile aumentare la larghezza di banda di un circuito ExpressRoute senza doverla eliminare. Sarà necessario contattare il provider di connettività per assicurare che aggiorni le limitazioni della propria rete in modo da supportare l'incremento della larghezza di banda. Non sarà tuttavia possibile ridurre la larghezza di banda di un circuito ExpressRoute. Per ridurre la larghezza di banda sarà necessario eliminare e creare di nuovo un circuito ExpressRoute.

### Come si modifica la larghezza di banda di un circuito ExpressRoute?
È possibile aggiornare la larghezza di banda del circuito ExpressRoute usando l'API dedicata per l'aggiornamento del circuito e il cmdlet di PowerShell.

## ExpressRoute Premium

### Che cos'è ExpressRoute Premium?
ExpressRoute Premium include le funzionalità seguenti.

 - Aumento del limite delle tabelle di routing da 4000 a 10.000 route per il peering pubblico e il peering privato.
 - Incremento del numero di VNet che possono essere connesse al circuito ExpressRoute (il valore predefinito è 10). Per informazioni dettagliate, vedere la tabella seguente.
 - Connettività globale sulla rete di base Microsoft. È ora possibile collegare una VNet in un'area geopolitica a un circuito ExpressRoute in un'altra area. **Esempio:** è possibile collegare una VNet creata in Europa occidentale a un circuito ExpressRoute creato a Silicon Valley.

### Quante VNet possono essere collegate a un circuito ExpressRoute se è stato abilitato ExpressRoute Premium?
La tabella seguente illustra i limiti incrementati per il numero di VNet collegabili a un circuito ExpressRoute. Il limite predefinito è 10.

**Limiti per i circuiti creati tramite i provider di servizi di rete**

| **Dimensioni circuito** | **Numero di collegamenti VNet per l'installazione predefinita** | **Numero di collegamenti VNet con ExpressRoute Premium** |
|--------------|----------------------------------------|-----------------------------------------------|
| 10 Mbps | 10 | Non supportato |
| 50 Mbps | 10 | 20 |
| 100 Mbps | 10 | 25 |
| 500 Mbps | 10 | 40 |
| 1 Gbps | 10 | 50
|


**Limiti per i circuiti creati tramite i provider di Exchange**

| **Dimensioni circuito** | **Numero di collegamenti VNet per l'installazione predefinita** | **Numero di collegamenti VNet con ExpressRoute Premium** |
|--------------|-----------------------------------|------------------------------------------------|
| 200 Mbps | 10 | 25 |
| 500 Mbps | 10 | 40 |
| 1 Gbps | 10 | 50 |
| 10 Gbps | 10 | 100 |



### Come si abilita ExpressRoute Premium?
Le funzionalità di ExpressRoute Premium possono essere abilitate quando si attiva la funzionalità e possono essere arrestate tramite l'aggiornamento dello stato del circuito. È possibile abilitare ExpressRoute Premium in fase di creazione del circuito oppure chiamare l'API dedicata per l'aggiornamento del circuito o il cmdlet di PowerShell per abilitare ExpressRoute Premium.

### Come si disabilita ExpressRoute Premium?
È possibile disabilitare ExpressRoute Premium chiamando l'API dedicata per l'aggiornamento del circuito o il cmdlet di PowerShell. Prima di disabilitare ExpressRoute Premium, è necessario assicurarsi di avere ridotto le esigenze a livello di connettività in modo che rispettino i limiti predefiniti. La richiesta di disabilitazione di ExpressRoute Premium avrà esito negativo se l'utilizzo supera i limiti predefiniti.

### È possibile scegliere solo alcune funzionalità di ExpressRoute Premium?
No. Non sarà possibile selezionare le funzionalità necessarie. Quando si attiva ExpressRoute Premium, vengono abilitate tutte le funzionalità.

### Quanto costa ExpressRoute Premium?
Per informazioni sui costi, vedere [Dettagli prezzi](http://azure.microsoft.com/pricing/details/expressroute/).

### È necessario un pagamento aggiuntivo per ExpressRoute Premium, oltre agli addebiti per la versione standard di ExpressRoute?
Sì. Gli addebiti per ExpressRoute Premium vengono sommati agli addebiti per ExpressRoute e agli addebiti richiesti dal provider di connettività.

### ExpressRoute Premium funziona con i modelli di tipo provider di Exchange e provider di servizi di rete?
Sì. ExpressRoute Premium è supportato con circuiti ExpressRoute connessi tramite provider di Exchange e provider di servizi di rete.


## ExpressRoute e Office 365

### Come si crea un circuito di ExpressRoute per la connessione a servizi di Office 365?

1. Per assicurarsi di soddisfare i requisiti previsti, vedere la [pagina relativa ai prerequisiti di ExpressRoute](expressroute-prerequisites.md).
2. Per assicurarsi di soddisfare le esigenze specifiche a livello di connettività, vedere l'elenco di provider di servizi e località in [Partner e località per ExpressRoute](expressroute-locations.md).
3. Per pianificare i requisiti relativi alla capacità, vedere la pagina relativa a [pianificazione e perfezionamento delle prestazioni di rete per Office 365](http://aka.ms/tune/)
4. Per configurare la connettività, eseguire i passaggi elencati nei flussi di lavoro seguenti.

	- [Configurare una connessione ExpressRoute tramite un provider di servizi di rete](expressroute-configuring-nsps.md)
	- [Configurare una connessione ExpressRoute mediante un provider di Exchange](expressroute-configuring-exps.md)

### I circuiti ExpressRoute esistenti possono supportare la connettività ai servizi di Office 365?
Sì. È possibile configurare i circuiti ExpressRoute esistenti per supportare la connettività ai servizi di Office 365. Assicurarsi che la capacità disponibile sia sufficiente per la connessione ai servizi di Office 365. Per pianificare i requisiti a livello di connettività, vedere la pagina relativa a [pianificazione e perfezionamento delle prestazioni di rete per Office 365](http://aka.ms/tune/).

Per informazioni, vedere le esercitazioni seguenti:

- [Configurare una connessione ExpressRoute tramite un provider di servizi di rete](expressroute-configuring-nsps.md)
- [Configurare una connessione ExpressRoute mediante un provider di Exchange](expressroute-configuring-exps.md)

### A quali servizi di Office 365 è possibile accedere tramite una connessione ExpressRoute?

**Sono supportati i servizi di Office 365 seguenti**

- Exchange Online ed Exchange Online Protection
- SharePoint Online
- Skype for Business Online
- Office Online
- Azure Active Directory e Azure AD Sync
- Office 365 Video
- Power BI
- Delve
- Project Online

**Non sono supportati i servizi di Office 365 seguenti**

- Yammer
- Download di Office 365 ProPlus Client
- Accesso al provider di identità locale
- Servizio Office 365 (gestito da 21 Vianet) in Cina

È possibile connettersi a questi servizi tramite Internet.

### Quanto costa ExpressRoute per Office 365?
La connessione a Office 365 tramite ExpressRoute non comporta costi aggiuntivi. Per informazioni dettagliate sui costi di ExpressRoute, vedere la [pagina relativa ai prezzi](http://azure.microsoft.com/pricing/details/expressroute/).

### In quali aree è supportato ExpressRoute per Office 365?
Per altre informazioni sull'elenco di partner e località in cui è supportato ExpressRoute, vedere [Partner e località per ExpressRoute](expressroute-locations.md).

### È possibile usare i provider di servizi di rete e i provider di Exchange per la connessione ai servizi di Office 365?
La connettività ai servizi di Office 365 è supportata sia tramite i provider di servizi di rete che tramite i provider di Exchange. Per altre informazioni sull'elenco di partner e località supportati, vedere [Partner e località per ExpressRoute](expressroute-locations.md).

### È possibile accedere a Office 365 tramite Internet anche se ExpressRoute è stato configurato per l'organizzazione specifica?
Sì. Gli endpoint di Office 365 sono raggiungibili tramite Internet anche se ExpressRoute è stato configurato per la rete specifica. Se ci si trova in una località configurata per la connessione ai servizi di Office 365 tramite ExpressRoute, la connessione verrà effettuata tramite ExpressRoute.
 

<!---HONumber=July15_HO5-->
<properties
   pageTitle="Panoramica di Gestione traffico"
   description="Questo articolo contiene la panoramica tecnica di Gestione traffico."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/23/2015"
   ms.author="joaoma" />

# Panoramica di Gestione traffico

Con Gestione traffico di Microsoft Azure è possibile controllare la distribuzione del traffico utente agli endpoint specificati, che possono includere servizi cloud, siti Web e altri endpoint di Azure. Gestione traffico applica un motore dei criteri intelligente alle query DNS (Domain Name System) relative ai nomi di dominio delle risorse Internet. I servizi cloud o i siti Web di Azure possono essere eseguiti in data center dislocati in diverse parti del mondo.

Con Gestione traffico è possibile:

-**Migliorare la disponibilità di applicazioni critiche:** Gestione traffico permette di migliorare la disponibilità di applicazioni critiche monitorando gli endpoint in Azure e fornendo funzionalità di failover automatico quando diventa inattivo un servizio cloud di Azure, un sito Web di Azure o un'altra posizione.

-**Migliorare la velocità di risposta per applicazioni a prestazioni elevate:** con Azure è possibile eseguire servizi cloud o siti Web in data center dislocati in tutto il mondo. Gestione traffico permette di migliorare la velocità di risposta delle applicazioni e i tempi di recapito del contenuto indirizzando gli utenti finali all'endpoint con la latenza di rete più bassa dal client.

-**Eseguire aggiornamenti e manutenzione dei servizi senza tempi di inattività:** Gestione traffico supporta scenari estesi per distribuzioni ibride sul cloud e locali, inclusi gli scenari di tipo "espansione sul cloud", "migrazione sul cloud" e "failover sul cloud". Per la manutenzione pianificata, l'endpoint in Gestione traffico deve essere disabilitato e si deve attendere che completi la manutenzione delle connessioni esistenti. Quando nell'endpoint non è più presente traffico, il servizio può essere aggiornato e verificato. Al termine della procedura, è possibile abilitare nuovamente l'endpoint in Gestione traffico. In questo modo è possibile gestire e aggiornare i servizi senza tempi di inattività per i client.

-**Distribuire il traffico in distribuzioni complesse e di grandi dimensioni:** con i profili annidati, in cui un profilo di Gestione traffico può avere un altro profilo di Gestione traffico come endpoint, è possibile creare configurazioni che consentono di ottimizzare le prestazioni e di effettuare distribuzioni più complesse e di grandi dimensioni. Per altre informazioni, vedere [Profili annidati](#Nested).

## Modalità di funzionamento di Gestione traffico

Quando si configura un profilo di Gestione traffico, le impostazioni specificate offrono le informazioni necessarie per determinare quale endpoint deve soddisfare la richiesta in base a una query DNS. Il traffico dell'endpoint non viene effettivamente indirizzato attraverso Gestione traffico.

La *Figura 1* mostra il modo in cui Gestione traffico indirizza gli utenti a un set di endpoint. I numeri nella Figura 1 corrispondono alle seguenti descrizioni numerate:

![Modalità di funzionamento di Gestione traffico](./media/traffic-manager/IC740854.jpg)

Figura 1

1. **Traffico utente a un nome di dominio aziendale:** il client richiede informazioni mediante il nome di dominio aziendale. L'obiettivo è risolvere un nome DNS in un indirizzo IP. I domini aziendali devono essere riservati attraverso le normali registrazioni dei nomi di dominio Internet mantenute all'esterno di Gestione traffico. Nella Figura 1 il dominio aziendale di esempio è www.contoso.com.
1. **Dal nome di dominio aziendale al nome di dominio di Gestione traffico:** tramite il record di risorse DNS per il dominio aziendale viene fatto riferimento a un nome di dominio di Gestione traffico mantenuto in Gestione traffico di Azure. Questa condizione viene ottenuta mediante un record di risorse CNAME che esegue il mapping del nome di dominio aziendale al nome di dominio di Gestione traffico. Nell'esempio il nome di dominio di Gestione traffico è contoso.trafficmanager.net.
1. **Nome di dominio e profilo di Gestione traffico:** il nome di dominio di Gestione traffico fa parte del relativo profilo. Il server DNS dell'utente invia una nuova query DNS per il nome di dominio di Gestione traffico (in questo caso contoso.trafficmanager.net), che viene ricevuta dai server dei nomi DNS di Gestione traffico.
1. **Elaborazione delle regole del profilo di Gestione traffico:** Gestione traffico usa il metodo di bilanciamento del carico specificato e lo stato del monitoraggio per determinare quale endpoint di Azure o di altro tipo soddisferà la richiesta.
1. **Invio del nome di dominio dell'endpoint all'utente:** Gestione traffico restituisce un record CNAME che esegue il mapping del nome di dominio di Gestione traffico al nome di dominio dell'endpoint. Il server DNS dell'utente risolve il nome di dominio dell'endpoint nel relativo indirizzo IP e lo invia all'utente.
1. **L'endpoint viene chiamato dall'utente:** l'endpoint restituito viene chiamato direttamente dall'utente mediante il relativo indirizzo IP.

Poiché il dominio aziendale e l'indirizzo IP risolto vengono memorizzati nella cache del computer client, l'utente continua a interagire con l'endpoint scelto fino alla scadenza della voce cache DNS locale. È importante notare che le voci host DNS vengono memorizzate nella cache del client DNS per tutta la durata del periodo TTL (Time-to-Live). Recuperando le voci host dalla cache del client DNS, viene ignorato il profilo di Gestione traffico causando ritardi nella connessione se l'endpoint diventa non disponibile prima della scadenza della durata TTL. Se la durata TTL di una voce host DNS nella cache scade e il computer client deve risolvere nuovamente il nome di dominio aziendale, viene inviata una nuova query DNS. Il computer client può ricevere l'indirizzo IP di un endpoint diverso a seconda del metodo di bilanciamento del carico applicato e dell'integrità degli endpoint al momento della richiesta.

## Come implementare Gestione traffico


La *Figura 2* mostra i passaggi, in ordine, necessari per l'implementazione di Gestione traffico. Questi passaggi possono essere eseguiti in un ordine leggermente diverso, se si conoscono con precisione la configurazione e le procedure consigliate di Gestione traffico. I numeri nella Figura 2 corrispondono alle seguenti descrizioni numerate:

![Modalità di configurazione di Gestione traffico](./media/traffic-manager/IC740855.jpg)

Figura 2

1. **Distribuire i servizi cloud di Azure, siti Web di Azure o altri endpoint nell'ambiente di produzione.** Quando si crea un profilo di Gestione traffico, è necessario associarlo a una sottoscrizione. È quindi possibile aggiungere gli endpoint per i servizi cloud e i siti Web di livello Standard in fase di produzione che fanno parte della stessa sottoscrizione. Se un endpoint si trova nell'ambiente di gestione temporanea e non in un ambiente di produzione di Azure o nella stessa sottoscrizione, può essere aggiunto come endpoint esterno. Per altre informazioni sui servizi cloud, vedere [Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Per altre informazioni sui servizi Web, vedere [Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).
1. **Decidere un nome per il dominio di Gestione traffico.** Considerare un nome per il dominio con un prefisso univoco. La seconda parte del dominio, trafficmanager.net, è fissa. Per altre informazioni, vedere [Procedure consigliate](#bkmk_TrafficManagerBestPracticesProfile).
1. **Decidere la configurazione di monitoraggio da usare.** Gestione traffico consente di monitorare gli endpoint per verificare che siano online, indipendentemente dal metodo di bilanciamento del carico. Al termine della configurazione delle impostazioni di monitoraggio, tramite Gestione traffico il traffico non verrà indirizzato agli endpoint offline in base al sistema di monitoraggio, a meno che non venga rilevato che tutti gli endpoint sono offline o che non sia possibile rilevare lo stato di nessuno degli endpoint contenuti nel profilo. Per altre informazioni sul monitoraggio, vedere [Informazioni sul monitoraggio di Gestione traffico](https://msdn.microsoft.com/library/azure/dn339013.aspx).
1. **Decidere il metodo di bilanciamento del carico da usare.** Sono disponibili tre metodi diversi di bilanciamento del carico. Valutare con attenzione il metodo che meglio soddisfa le proprie esigenze. Se si presenta la necessità di cambiare il metodo successivamente, è possibile effettuare questa operazione in qualsiasi momento. Si noti inoltre che per ogni metodo sono necessari passaggi di configurazione leggermente diversi. Per informazioni sui metodi di bilanciamento del carico, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione traffico.](https://msdn.microsoft.com/library/azure/dn339010.aspx).
1. **Creare il profilo e configurare le impostazioni.** Per creare il profilo di Gestione traffico e configurare le impostazioni, è possibile usare le API REST, Windows PowerShell o il portale di gestione. Per altre informazioni, vedere [Come configurare le impostazioni di Gestione traffico](#Configure). I seguenti passaggi presuppongono l'uso di Creazione rapida nel portale di gestione.
	- 	**Creare il profilo di Gestione traffico:** per creare un profilo mediante Creazione rapida nel portale di gestione, vedere [Creare un profilo di Gestione traffico mediante Creazione rapida](https://msdn.microsoft.com/library/azure/dn339012.aspx).
	- 	**Configurare le impostazioni del metodo di bilanciamento del carico:** durante la Creazione rapida, è necessario selezionare il metodo di bilanciamento del carico per il profilo. Questa impostazione può essere modificata in qualsiasi momento una volta completati i passaggi di Creazione rapida. Per i passaggi di configurazione, vedere l'argomento corrispondente al metodo di bilanciamento del carico in uso: [Configurare il bilanciamento del carico delle prestazioni](https://msdn.microsoft.com/library/azure/hh744835.aspx)
	
	[AZURE.NOTE]**Il metodo di bilanciamento del carico Round robin supporta ora anche la distribuzione ponderata del traffico di rete. In questo momento è tuttavia necessario usare le API REST o Windows PowerShell per configurare i pesi. Per altre informazioni e una configurazione di esempio, vedere l'articolo relativo agli [endpoint esterni di Gestione traffico di Azure e al metodo Round robin ponderato tramite PowerShell](https://msdn.microsoft.com/library/azure/hh744829.aspx) nel blog di Azure.**
	- 	**Configurare gli endpoint:** gli endpoint non vengono configurati durante la Creazione rapida. Dopo aver creato il profilo e specificato il metodo di bilanciamento del carico, è necessario comunicare gli endpoint a Gestione traffico.
	- 	**Configurare le impostazioni di monitoraggio:** le impostazioni di monitoraggio non vengono configurate durante la Creazione rapida. Dopo aver creato il profilo e specificato il metodo di bilanciamento del carico, è necessario consentire a Gestione traffico la gestione del monitoraggio. Per i passaggi di configurazione del monitoraggio, vedere [Configurare il monitoraggio di Gestione traffico](configure-traffic-manager-monitoring.md)..
1. **Testare il profilo di Gestione traffico.** Verificare che il profilo e il dominio funzionino come previsto. Per informazioni su come eseguire questa operazione, vedere [Test delle impostazioni di Gestione traffico](testing-traffic-manager-settings.md)..
1. **Puntare il record della risorsa DNS del nome di dominio aziendale al profilo per attivarlo.** Per altre informazioni, vedere [Puntare un dominio Internet della società a un dominio di Traffic Manager](point-a-company-internet-domain-to-a-traffic-manager-domain.md)..

Mediante l'esempio riportato nella Figura 1, modificare il record di risorse DNS nei server per puntare il nome di dominio aziendale al nome di dominio di Gestione traffico:

    www.contoso.com IN CNAME contoso.trafficmanager.net


## Come configurare le impostazioni di Gestione traffico


È possibile configurare le impostazioni di Gestione traffico usando il portale di gestione, le API REST e i cmdlet di Windows PowerShell.

Nonostante ogni elemento dell'API REST non sia visibile nel portale di gestione, molte impostazioni sono disponibili usando uno dei metodi. Per altre informazioni sull'uso delle API REST, vedere [Operazioni per Gestione traffico (documentazione di riferimento sulle API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).


Per altre informazioni sui cmdlet di Windows PowerShell per Gestione traffico, vedere [Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).


[AZURE.NOTE] **Non sono attualmente disponibili informazioni di supporto per la configurazione degli endpoint esterni (tipo = 'Any'), dei pesi per il metodo di bilanciamento del carico Round robin e dei profili annidati con il portale di gestione. È necessario usare l'API REST (vedere Create Definition) oppure Windows PowerShell (vedere Add-AzureTrafficManagerEndpoint).**

### Configurazione delle impostazioni nel portale di gestione


Nel portale di gestione è possibile creare il profilo di Gestione traffico mediante Creazione rapida. Tramite Creazione rapida è possibile creare un profilo di base. Dopo la creazione del profilo, è possibile configurare impostazioni aggiuntive o modificare quelle configurate in precedenza. Per altre informazioni sulla creazione del profilo di Gestione traffico mediante Creazione rapida, vedere Creare un profilo di Gestione traffico mediante Creazione rapida.

[Creare un profilo di Gestione traffico mediante Creazione rapida](https://msdn.microsoft.com/library/azure/dn339012.aspx)

Nel portale di gestione è possibile configurare le seguenti impostazioni:

-**Prefisso DNS:** prefisso univoco creato. I profili vengono visualizzati nel portale di gestione in base al prefisso.

-**Durata TTL del DNS:** il valore TTL del DNS determina la frequenza con cui il server dei nomi della cache locale del client esegue query nel sistema DNS di Gestione traffico di Azure per ottenere voci DNS aggiornate.

-**Sottoscrizione:** selezionare la sottoscrizione a cui il profilo corrisponderà. Tenere presente che questa opzione viene visualizzata solo se si dispone di più sottoscrizioni.

-**Metodo di bilanciamento del carico:** metodo di gestione desiderato del bilanciamento del carico da parte di Gestione traffico.

-**Ordine di failover:** ordine degli endpoint in caso di uso del metodo di bilanciamento del carico failover.

-**Monitoraggio:** le impostazioni di monitoraggio contengono il protocollo (HTTP o HTTPS), la porta, il percorso relativo e il nome del file.

### Configurazione delle impostazioni tramite le API REST


È possibile creare e configurare il profilo di Gestione traffico mediante le API REST. Per altre informazioni, vedere [Operazioni per Gestione traffico (documentazione di riferimento sulle API REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

-**Profilo:** in un profilo è contenuto il prefisso di un nome di dominio creato. Ogni profilo corrisponde alla sottoscrizione. È possibile creare più profili per sottoscrizione. Il nome del profilo è visibile nel portale di gestione. Il nome creato, che è contenuto nel profilo, è definito dominio di Gestione traffico.

-**Definizione:** in una definizione sono contenute le impostazioni dei criteri e dei monitoraggi. Una definizione corrisponde a un profilo. È possibile disporre di una sola definizione per profilo. Nonostante molte delle impostazioni contenute nella definizione vengano visualizzate e possano essere configurate nel portale di gestione, la definizione non viene visualizzata nel portale di gestione.

-**Opzioni DNS:** si trovano in ogni definizione. Si tratta del punto in cui viene configurata la durata TTL del DNS.

-**Monitoraggi:** in ogni definizione sono disponibili le impostazioni di monitoraggio. In questa area vengono configurati il protocollo, la porta, il percorso relativo e il nome del file. Le impostazioni di monitoraggio vengono visualizzate e possono essere configurate nel portale di gestione. Per altre informazioni, vedere [Monitoraggio di Gestione traffico](https://msdn.microsoft.com/library/azure/dn339013.aspx).

-**Criteri:** in ogni definizione sono disponibili le impostazioni dei criteri. Nei criteri vengono specificati i metodi di bilanciamento del carico e gli endpoint. Nonostante alcune delle impostazioni dei criteri vengano visualizzate e possano essere configurate nel portale di gestione, i criteri non vengono visualizzati nel portale di gestione. Per altre informazioni, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione traffico](about-traffic-manager-balancing-methods.md)..

## Configurazione delle impostazioni tramite Windows PowerShell

È possibile creare e configurare il profilo di Gestione traffico mediante Windows PowerShell. Per altre informazioni, vedere [Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## <a name=bkmk_TrafficManagerBestPracticesProfile>Best practices</a>

- **Rendere i prefissi univoci e di facile comprensione:** il nome DNS del profilo di Gestione traffico deve essere univoco. È possibile controllare solo la prima parte del nome DNS. Il nome di dominio di Gestione traffico viene usato unicamente a scopo di identificazione e indirizzamento delle richieste del client. L'utente finale non visualizzerà mai questi nomi nei computer client. Tuttavia, poiché i profili vengono identificati da questo nome di dominio, è importante essere in grado di distinguerlo rapidamente dagli altri nomi di dominio elencati nel portale di gestione.
- **Usare punti per conferire univocità o rendere i nomi di dominio leggibili:** è possibile usare punti per separare le diverse parti del prefisso del nome di dominio.  Se si intende creare più criteri in Gestione traffico, usare una gerarchia coerente per differenziare un servizio da un altro. Ad esempio, Contoso dispone di servizi globali per la gestione di utilità, fatturazione e Web. I tre criteri sono web.contoso.trafficmanager.net, bill.contoso.trafficmanager.net e util.contoso.trafficmanager.net. Quando si configurano i servizi cloud o i siti Web, usare nomi in cui sia incluso il percorso. Ad esempio, web-us-contoso.cloudapp.net e web-asia-contoso.cloudapp.net. Le limitazioni sono quelle imposte dal servizio DNS. Si supponga che un nome di dominio sia dato da una sequenza di etichette separate da punti (label.label.label.label.ecc.). Attualmente, i limiti relativi ai nomi di dominio di Gestione traffico sono:
	- Ogni etichetta può essere composta da un massimo di 63 caratteri.
	- Non possono essere presenti più di 40 etichette in totale. Poiché due etichette provengono da trafficmanager.net, ne rimangono 38 per il prefisso.
	- L'intero nome di dominio può essere composto da un massimo di 253 caratteri. Tenere presente che trafficmanager.net richiede 19 caratteri.
- **Durata TTL del DNS:-** il valore TTL del DNS determina la frequenza con cui il server dei nomi della cache locale del client esegue query nel sistema DNS di Gestione traffico di Azure per ottenere voci DNS aggiornate. In questo periodo di tempo eventuali modifiche apportate in Gestione traffico, ad esempio al profilo o alla disponibilità degli endpoint, verranno aggiornate nell'intero sistema globale dei server DNS. È consigliabile mantenere l'impostazione sul valore predefinito di 300 secondi (5 minuti). Un numero superiore determina l'aumento della durata di memorizzazione nella cache delle risposte DNS di Gestione traffico tramite i resolver DNS e i client e la conseguente riduzione della latenza delle query DNS complessiva. Tuttavia, se è necessario un failover molto rapido, è preferibile configurare un valore inferiore.
- **Gli endpoint devono trovarsi in una singola sottoscrizione:-** tutti gli endpoint devono trovarsi nella stessa sottoscrizione in cui si crea il profilo. È possibile aggiungere a un profilo endpoint da diverse sottoscrizioni come endpoint esterni, ma Azure non li rimuoverà automaticamente se si disabilita o si elimina il servizio associato. Gli endpoint esterni rimangono quindi nel profilo di Gestione traffico e continueranno a essere fatturati, a meno che non li si rimuova manualmente.

-**Solo servizi di produzione:-** sono disponibili solo gli endpoint in un ambiente di produzione. Non è possibile indirizzare endpoint in esecuzione in un ambiente di gestione temporanea. Tenere presente che se si esegue uno scambio dell'indirizzo IP virtuale (VIP) mentre il traffico viene indirizzato tramite un profilo, il traffico userà l'endpoint appena scambiato nell'ambiente di produzione.

-**Denominare gli endpoint in modo da agevolarne l'identificazione:-** considerare il prefisso DNS che si vuole usare. Il nome DNS viene usato perché è certo che sia univoco in una sottoscrizione, mentre per il nome del servizio cloud o del sito Web non si ha questa garanzia. Per evitare confusione, assegnare a un servizio cloud o a un sito Web un nome e un prefisso DNS uguali o simili. Se si dispone di oltre 20 servizi cloud e siti Web, una denominazione non corretta potrebbe rendere difficoltosa la ricerca dell'endpoint corretto e la gestione dei profili.

-**Tutti gli endpoint in un profilo devono eseguire le stesse operazioni e servire le stesse porte:-** se si combinano gli endpoint, è più probabile che un client chiami un endpoint che non è in grado di soddisfare la richiesta.

-**Per tutti i servizi cloud in un profilo devono essere usate le stesse impostazioni di monitoraggio:-**  è possibile scegliere solo un percorso e un file per monitorare tutti gli endpoint in una definizione specifica. È possibile immettere "/" nella casella di testo Nome file e percorso relativo in modo che tramite il monitoraggio venga effettuato il tentativo di accesso al nome file e al percorso predefiniti.

-**Disabilitare gli endpoint per le modifiche temporanee, anziché modificare la configurazione:**  in molti casi, è preferibile portare un endpoint offline. Anziché rimuovere l'endpoint dal profilo, disabilitare semplicemente il singolo endpoint nel profilo. Questo fa sì che l'endpoint continui a far parte del profilo, sebbene il comportamento di quest'ultimo potrebbe far sembrare il contrario. Questa operazione è particolarmente utile per rimuovere temporaneamente un endpoint che si trova in modalità di manutenzione o è in corso di ridistribuzione. Quando l'endpoint è di nuovo attivo e in esecuzione, è possibile abilitarlo. Per altre informazioni, vedere [Disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md).

-**Archiviazione: -**  la progettazione della posizione e della distribuzione dell'archiviazione è un aspetto fondamentale dell'uso di Gestione traffico. Pensare alla transazione end-to-end e al flusso dei dati quando si progettano e distribuiscono applicazioni per Gestione traffico.

-**SQL Azure -** Come per la progettazione dell'archiviazione, analizzare lo stato dell'applicazione e i requisiti dei dati quando si estendono gli endpoint a più aree geografiche.

## <a name="Nested">Profili annidati</a>

La procedura nota come 'profili annidati' consente di specificare il nome di un altro profilo di Gestione traffico come endpoint. Il nome del profilo di Gestione traffico è il relativo nome DNS, ad esempio contoso-europe.trafficmanager.net.

Questa procedura consente di configurare Gestione traffico in modo che le query in ingresso del nome DNS siano analizzate in set di livelli per garantire che il client richiedente sia indirizzato al set di endpoint corretto. Nella Figura 3 viene visualizzato un esempio.

![Esempio di profili di Gestione traffico](./media/traffic-manager/IC751072.png)

**Figura 3**

È possibile annidare fino a 10 livelli e ogni profilo può essere configurato con un metodo di bilanciamento del carico differente.

Ad esempio, si possono creare configurazione per i seguenti elementi:

-A livello superiore (profilo di Gestione traffico con mapping al nome DNS esterno), è possibile configurare il profilo con il metodo di bilanciamento del carico delle prestazioni.

-A livello intermedio, un set di profili di Gestione traffico rappresenta vari data center e usa il metodo di bilanciamento del carico Round robin.

-A livello inferiore, un set di endpoint del servizio cloud in ogni data center gestisce le richieste di traffico dell'utente.

Il risultato è che gli utenti vengono indirizzati a un data center adeguato per l'area geografica in base alle prestazioni e a un servizio cloud all'interno del data center in base a una distribuzione del carico ponderata o equivalente. Ad esempio, la definizione del peso può essere usata per distribuire una ridotta percentuale di traffico a una distribuzione nuova o di prova a scopo di test o di invio di commenti e suggerimenti da parte del cliente.

La Figura 4 illustra la configurazione.

![Esempio di profili di Gestione traffico a più livelli](./media/traffic-manager/IC751073.png)

**Figura 4**

Nella figura 4 il profilo di Gestione traffico nel livello superiore è un profilo padre e i profili di Gestione traffico nel livello intermedio sono profili figlio.

Se Gestione traffico indirizza gli utenti a un profilo figlio con un numero ridotto di endpoint integri, è possibile che venga eseguito l'overload di tali endpoint e si generino problemi di prestazioni. Per evitare il verificarsi di questa situazione, è possibile configurare il profilo padre di Gestione traffico con una determinata soglia di endpoint integri che determina se qualsiasi endpoint nei profili figlio di tale padre può ricevere traffico. Se ad esempio si desidera garantire che siano presenti almeno tre endpoint integri nei profili figlio, il valore di soglia deve essere impostato su 3. Nell'esempio della figura 4 il profilo di Gestione traffico di livello superiore deve essere configurato in base a tale soglia.

Per aggiungere un profilo di Gestione traffico come endpoint e configurare il numero minimo di endpoint integri, è necessario usare REST (vedere [Creare la definizione](http://go.microsoft.com/fwlink/p/?LinkId=400772)) o Windows PowerShell (vedere [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). Non è possibile usare il portale di gestione.

## Figure di Gestione traffico


Se si vogliono scaricare le figure presenti in questo argomento come diapositive di PowerPoint da usare per una presentazione su Gestione traffico o da modificare per altri scopi, vedere la pagina relativa alle [figure di Gestione traffico nella documentazione MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Vedere anche

[Attività di configurazione di Gestione traffico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Servizi cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operazioni relative a Gestione traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

<!--HONumber=49-->
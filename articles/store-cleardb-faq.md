<properties
	pageTitle="Domande frequenti per i database MySQL ClearDB con il Servizio app di Azure | Microsoft Azure"
	description="Risposte a domande comuni sull'uso di database MySQL ClearDB con il Servizio app di Azure."
	documentationCenter="php"
	services=""
	authors="sunbuild"
	manager="yochayk"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="sumuth"/>

# Domande frequenti per i database MySQL ClearDB con il Servizio app di Azure

Queste domande frequenti offrono risposte a domande comuni sull'uso e l'acquisto di database MySQL ClearDB per App Web di Azure.

## Quali sono le opzioni disponibili per MySQL in Azure?

Sono disponibili diverse opzioni:

* [Database MySQL condiviso ClearDB](/marketplace/partners/cleardb/databases/)

* [Cluster Premium MySQL ClearDB](/marketplace/partners/cleardb-clusters/cluster/)

* [Cluster MySQL in esecuzione su una VM di Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Istanza singola di MySQL in esecuzione su una VM di Azure](virtual-machines/virtual-machines-mysql-windows-server-2008r2.md)

ClearDB è un servizio di hosting MySQL e gestisce l'infrastruttura di MySQL per l'utente. Quando si esegue il proprio cluster MySQL o un database in una macchina virtuale di Azure è necessario configurare il server MySQL e mantenerlo aggiornato con le patch.

## È necessaria una carta di credito per l'app Web e il modello MySQL in Azure Marketplace?

Questo dipende dal tipo di sottoscrizione in uso. Di seguito sono riportati alcuni tipi di sottoscrizione di uso comune:

* [Pagamento a consumo](/offers/ms-azr-0003p/): richiede una carta di credito e quando si acquista un database MySQL a pagamento i costi verranno addebitati sulla carta di credito.

* [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/): include crediti da usare con i servizi di Microsoft Azure ma non consente l'acquisto di risorse di terze parti. Per acquistare servizi di terze parti o un database MySQL a pagamento, è necessario usare una sottoscrizione abilitata con carta di credito. Per le app Web è possibile creare un database MySQL ClearDB gratuito.

* [Sottoscrizione MSDN](/pricing/member-offers/msdn-benefits/) e **Pagamento in base al consumo per sviluppo/test MSDN**: analogamente alla versione di valutazione gratuita, una sottoscrizione MSDN richiede una carta di credito per l'acquisto di una soluzione a pagamento MySQL da ClearDB.

* [Contratto Enterprise](/pricing/enterprise-agreement/): ai clienti con contratto Enterprise vengono addebitati ogni trimestre i costi per tutti gli acquisti di Azure Marketplace (di terze parti) in una fattura separata e consolidata. Per eventuali acquisti effettuati in Marketplace, verranno addebitati costi al di fuori dell'impegno monetario. Si noti che al momento Azure Store non è disponibile per i clienti iscritti in Azerbaigian, Croazia, Norvegia e Porto Rico.

*   [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): è possibile creare solo database ClearDB gratuiti per le app Web. Non sono previsti limiti al numero di database MySQL ClearDB gratuiti che è possibile creare. Si noti che i database gratuiti non devono essere usati per le app Web di produzione, in quanto il servizio è previsto solo a scopo di valutazione.

## Perché è stato ricevuto un addebito di $ 3,50 per un'app Web + MySQL da Azure Marketplace?

L'opzione predefinita del database è Titan, che ha un costo di $ 3,50. Durante la creazione del database non viene visualizzato il costo e si potrebbe acquistare erroneamente un database che non si voleva acquistare. L'esperienza è in corso di miglioramento, ma finché non è disponibile una soluzione è necessario controllare tutti i piani tariffari selezionati per app Web e database prima di scegliere **Crea** e avviare la distribuzione delle risorse.

## Si esegue MySQL sulla propria macchina virtuale di Azure. È possibile connettere la propria app Web di Azure al database?

Sì. È possibile connettere la propria app Web al database, purché la VM di Azure abbia accesso remoto all'app Web. Per informazioni dettagliate fare clic qui.

## In quali paesi sono supportati i cluster MySQL Premium ClearDB?

I [cluster MySQL Premium ClearDB](/marketplace/partners/cleardb-clusters/cluster/) sono disponibili in tutte le aree di Azure in tutto il mondo ad eccezione dell'India, dell'Australia, del Brasile meridionale e della Cina.

## È possibile creare un nuovo cluster prima di creare un database con la soluzione cluster Premium ClearDB?

No, la creazione di cluster ClearDB vuoti non è supportata. Il portale di Azure consente di creare database in un cluster, che può creare un nuovo cluster contemporaneamente.

## Si riceve un avviso se si cerca di eliminare un database MySQL ClearDB usato da una delle applicazioni?

No, Azure non invierà alcuna notifica se si elimina un acquisto di Marketplace da cui dipende l'applicazione.

## In quali aree è possibile creare database ClearDB?

Azure Marketplace non è disponibile per i clienti iscritti in Azerbaigian, Croazia, Norvegia e Porto Rico. ClearDB non è disponibile in queste aree.

## Quale piano tariffario scegliere per un'app Web e un database di produzione?

Usare il piano tariffario Basic o superiore per le app Web. Per ClearDB è consigliabile un piano Saturn o Jupiter. Esaminare le funzioni e le limitazioni di ogni piano tariffario sia per le [app Web](/pricing/details/app-service/) che per i [database MySQL ClearDB](/marketplace/partners/cleardb/databases/) per scegliere il più adatto alle proprie esigenze.

## Come si aggiorna il database ClearDB da un piano a un altro?

È possibile usare la [procedura guidata di aggiornamento di ClearDB](https://www.cleardb.com/store/azure/upgrade). Non esiste attualmente un percorso di aggiornamento nel portale di Azure.

## Chi è possibile contattare per ottenere aiuto quando il database presenta un problema?

Per tutti i problemi relativi al database, contattare il [supporto ClearDB](https://www.cleardb.com/developers/help/support). Tenersi pronti a comunicare le informazioni relative alla sottoscrizione di Azure.

## È possibile acquistare WordPress scalabile con una sottoscrizione con contratto Enterprise?

Il processo è lo stesso per tutte le sottoscrizioni. Andare in Azure Marketplace nel [portale di Azure](https://portal.azure.com/) e selezionare [WordPress scalabile](https://portal.azure.com/?feature.customportal=false#create/WordPress.ScalableWordPress) per iniziare a creare l'app. WordPress scalabile supporta solo i piani tariffari Saturn e Jupiter di ClearDB e i crediti con contratto Enterprise si applicheranno sia all'applicazione Web in esecuzione con il piano tariffario Standard per app Web che al database MySQL ClearDB (condiviso) a pagamento.[/marketplace/faq/](/marketplace/faq/) Si riceverà un addebito per il proprio contratto Enterprise ogni trimestre per tutti gli acquisti di Store in una fattura separata e consolidata.

## È possibile trasferire un database ClearDB da una sottoscrizione con carta di credito a una sottoscrizione con contratto Enterprise?

I database ClearDB esistenti usano la carta di credito associata con le sottoscrizioni esistenti. Per usare una sottoscrizione con contratto Enterprise è necessario eseguire la migrazione dei dati in un nuovo database:

* Acquistare un nuovo database ClearDB con la sottoscrizione con contratto Enterprise.
* Eseguire la migrazione dei dati nel nuovo database.
* Aggiornare l'applicazione per usare il nuovo database.
* Eliminare il vecchio database ClearDB.

Quando si crea una nuova app Web con MySQL (ClearDB) o un database MySQL (ClearDB), la sottoscrizione che si sceglie determina la modalità di pagamento per il servizio. Si noti che con una sottoscrizione con contratto Enterprise non verrà bloccato l'approvvigionamento dei servizi di terze parti, ad esempio ClearDB, nel portale di Azure. Le sottoscrizioni con contratto Enterprise vengono addebitate al di fuori dell'impegno monetario e vengono fatturate su base trimestrale e in modo posticipato. I clienti con contratto Enterprise devono impostare un metodo di pagamento, ad esempio una carta di credito, per pagare i servizi Marketplace di terze parti.

## Dove è possibile visualizzare gli addebiti per le risorse di ClearDB in una sottoscrizione con contratto Enterprise?

Per i clienti con contratto Enterprise diretti, gli addebiti di Azure Marketplace sono visibili in Enterprise Portal. Si noti che tutti gli acquisti e l'uso di Marketplace vengono addebitati al di fuori dell'impegno monetario e vengono fatturati su base trimestrale e in modo posticipato. I clienti con contratto Enterprise devono pagare direttamente i provider di servizi di terze parti e possono farlo attivando un metodo di pagamento, ad esempio una carta di credito, con il proprio account contratto Enterprise.

I clienti con contratto Enterprise indiretti possono trovare le proprie sottoscrizioni di Azure Marketplace nella pagina **Gestisci sottoscrizioni** di Enterprise Portal, tuttavia i prezzi sono nascosti. I clienti devono contattare il proprio LSP per informazioni sugli addebiti di Marketplace.

L'accesso ad Azure Marketplace per i servizi di terze parti può essere gestito dagli amministratori della registrazione con contratto Enterprise di Azure. Questi ultimi possono disabilitare o riabilitare l'accesso ad acquisti di terze parti dallo Store nell'opzione per la gestione degli account e delle sottoscrizioni nella sezione Account di Enterprise Portal.

## Chi contattare per domande sulla fatturazione per i servizi ClearDB nella sottoscrizione con contratto Enterprise?

Contattare il [supporto per contratti Enterprise](http://aka.ms/AzureEntSupport) per quanto riguarda la fatturazione relativa alla registrazione con contratto Enterprise. Il team di supporto del portale per contratti Enterprise risponderà alla domanda oppure offrirà aiuto per la risoluzione del problema.

## Altre informazioni

[Domande frequenti su Azure Marketplace](/marketplace/faq/)

<!---HONumber=AcomDC_0211_2016-->
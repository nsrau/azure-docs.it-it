<properties pageTitle="Eseguire Cassandra con Linux in Azure" description="Spiega come eseguire un cluster Cassandra in Linux in Macchine virtuali di Azure." services="virtual-machines" documentationCenter="nodejs" authors="hanuk" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/01/2014" ms.author="hanuk"/>





<h1><a id = ""></a>Esecuzione di Cassandra con Linux in Azure e accesso da Node.js </h1>
**Autore:** Hanu Kommalapati

## Sommario##

- [Panoramica][]
- [Distribuzione in un'area singola][]
- [Testare il cluster Cassandra in un'area singola][]
- [Distribuzione in più aree][]
- [Testare il cluster Cassandra in più aree][]
- [Testare il cluster Cassandra da Node.js][]
- [Conclusioni][]

##<a id="overview"> </a>Panoramica ##
Microsoft Azure è una piattaforma cloud aperta che esegue software sia Microsoft che non Microsoft, inclusi sistemi operativi, server applicazioni, middleware di messaggistica, oltre a database SQL e NoSQL da modelli sia commerciali che open source. La compilazione di servizi resilienti su cloud pubblici, incluso Azure, richiede un'attenta pianificazione e un'architettura mirata sia per i server applicazioni che per i livelli di archiviazione. L'architettura di archiviazione distribuita di Cassandra aiuta a compilare con facilità sistemi a disponibilità elevata a tolleranza di errore del cluster. Cassandra è un database NoSQL con scalabilità cloud gestito da Apache Software Foundation all'indirizzo cassandra.apache.org. Cassandra, essendo scritto in Java, viene eseguito su piattaforme sia Windows che Linux. 

Lo scopo di questo articolo è mostrare la distribuzione di Cassandra in Ubuntu come cluster di uno solo o di più data center sfruttando le macchine virtuali di Microsoft Azure e le reti virtuali. La distribuzione del cluster per i carichi di lavoro ottimizzati per la produzione esula dall'ambito di questo articolo perché richiede una configurazione nodi a più dischi, una progettazione appropriata della topologia ad anello e la modellazione dati per supportare i requisiti necessari di replica, coerenza dei dati, velocità effettiva e disponibilità elevata. 

Questo articolo usa un approccio di base per spiegare che cosa comporta la compilazione del cluster Cassandra rispetto a Docker, Chef o Puppet, semplificando notevolmente la distribuzione dell'infrastruttura.  

##<a id="depmodels"> </a>Modelli di distribuzione ##
La rete di Microsoft Azure consente di distribuire cluster privati isolati, il cui accesso può essere limitato per ottenere una sicurezza di rete con granularità fine.  Poiché lo scopo di questo articolo è illustrare la distribuzione di Cassandra a un livello di base, non si parlerà del livello di coerenza e della progettazione delle risorse di archiviazione ottimale per la velocità effettiva. Ecco i requisiti di rete per l'ipotetico cluster:

- I sistemi esterni non possono accedere al database Cassandra da Azure o all'esterno di Azure
- Il cluster Cassandra deve trovarsi dietro un bilanciamento del carico per il traffico Thrift
- Distribuire i nodi di Cassandra in due gruppi in ogni data center per una migliore disponibilità del cluster 
- Bloccare il cluster in modo che solo la server farm applicazioni abbia accesso diretto al database
- Nessun endpoint di rete pubblico diverso da SSH
- Ogni nodo di Cassandra richiede un indirizzo IP interno fisso

Cassandra può essere distribuito in una singola area di Azure o in più aree a seconda della natura distribuita del carico di lavoro. Il modello di distribuzione in più aree può essere usato per gestire gli utenti finali più vicini a una determinata area geografica con la stessa infrastruttura di Cassandra. La replica dei nodi predefiniti di Cassandra consente di sincronizzare le scritture multimaster derivanti da più data center e presenta alle applicazioni una visualizzazione coerente dei dati. La distribuzione in più aree è utile anche per attenuare i rischi causati dalle interruzioni di Servizi di Azure. La coerenza perfezionabile e la topologia di replica di Cassandra saranno di aiuto anche per soddisfare diverse esigenze RPO delle applicazioni. 


###<a id="oneregion"> </a>Distribuzione in un'area singola ###
Si inizierà con una distribuzione in una singola area e si raccoglieranno le istruzioni per creare un modello a più aree. La rete virtuale di Azure verrà usata per creare subnet isolate per poter soddisfare i requisiti di sicurezza della rete citati sopra.  Il processo descritto nella creazione della distribuzione in una singola area usa Ubuntu 14.04 LTS e Cassandra 2.08. Il processo può però essere facilmente adottato anche per le altre varianti di Linux. Di seguito sono riportate alcune caratteristiche di sistema della distribuzione in una singola area.  

**Disponibilità elevata:** i nodi di Cassandra mostrati nella figura 1 sono distribuiti in due set di disponibilità in modo che siano presenti in più domini di errore per una disponibilità elevata. Delle macchine virtuali indicate con ogni set di disponibilità viene eseguito il mapping a 2 domini di errore. In Microsoft Azure il concetto di dominio di errore si riferisce alla gestione del tempo di inattività non pianificato (ad esempio, errori hardware o software), mentre il concetto di dominio di aggiornamento (ad esempio, applicazione di patch/aggiornamenti del sistema operativo host o guest, aggiornamenti di applicazioni) si riferisce alla gestione del tempo di inattività pianificato. Per il ruolo dei domini di errore e di aggiornamento per garantire una disponibilità elevata, vedere [Ripristino di emergenza e disponibilità elevata per applicazioni Azure](http://msdn.microsoft.com/it-it/library/dn251004.aspx). 

![Single region deployment](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

Figura 1: Distribuzione in un'area singola

Al momento della stesura di questo articolo, Azure non consente il mapping esplicito di un gruppo di macchine virtuali a un dominio di errore specifico. Quindi, anche con il modello di distribuzione mostrato nella figura 1, è statisticamente probabile che venga eseguito il mapping di tutte le macchine virtuali a due domini di errore invece che a quattro. 

**Bilanciamento del carico del traffico Thrift:** le librerie client Thrift nel server Web si connettono al cluster con un dispositivo di bilanciamento del carico interno. Pertanto, è necessario aggiungere tale dispositivo alla subnet dei dati (vedere la figura 1) nel contesto del servizio cloud che ospita il cluster Cassandra. Una volta definito il bilanciamento del carico interno, per ogni nodo è necessario aggiungere l'endpoint con carico bilanciato con le annotazioni di un set con carico bilanciato con il nome del dispositivo di bilanciamento del carico definito in precedenza. Per altri dettagli, vedere [Bilanciamento del carico interno di Azure ](http://msdn.microsoft.com/it-it/library/azure/dn690121.aspx).

**Semi del cluster:** è importante selezionare i nodi con disponibilità più elevata per i semi perché i nuovi nodi comunicheranno con i nodi dei semi per individuare la topologia del cluster. In ogni set di disponibilità viene designato un nodo come nodo dei semi per evitare un singolo punto di errore. 

**Fattore di replica e livello di coerenza:** l'elevata disponibilità e la durabilità dei dati predefinite di Cassandra sono caratterizzate dal fattore di replica (numero di copie di ogni riga archiviate nel cluster) e dal livello di coerenza (numero di repliche da leggere/scrivere prima di restituire il risultato al chiamante). Il fattore di replica viene specificato durante la creazione di KEYSPACE (simile a un database relazionale), mentre il livello di coerenza viene specificato durante l'invio della query CRUD. Per i dettagli sulla coerenza e la formula per il calcolo del quorum, vedere la documentazione di Cassandra nella pagina relativa alla [configurazione per la coerenza](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html).

Cassandra supporta due tipi di modelli di integrità dei dati: coerenza e coerenza finale. Il fattore di replica e il livello di coerenza determineranno insieme se i dati saranno coerenti non appena l'operazione di scrittura sarà completata o se saranno coerenti alla fine. Ad esempio, se si specifica QUORUM come livello di coerenza, la coerenza dei dati sarà sempre garantita, mentre con un altro livello di coerenza, al di sotto del numero di repliche da scrivere necessarie per ottenere QUORUM (ad esempio, ONE), i dati risulteranno coerenti alla fine. 

Con il cluster a 8 nodi illustrato in precedenza, con un fattore di replica pari a 3 e un livello di coerenza in lettura/scrittura QUORUM (2 nodi vengono letti o scritti per coerenza), sarebbe possibile perdere un massimo di 1 nodo per gruppo di repliche prima che l'applicazione individuasse l'errore. Ciò presuppone che tutti gli spazi chiave abbiano ben bilanciato le richieste in lettura/scrittura.  I parametri seguenti verranno usati per il cluster distribuito: 

Configurazione del cluster Cassandra in un'area singola:

<table>
<tr>

<th>Parametro cluster</th><th>Valore</th><th>Osservazioni</th></tr>
<tr><td>Number of Nodes (N) </td><td>8</td><td>Numero totale di nodi nel cluster</td></tr>
<tr><td>Replication Factor (RF)</td><td>	3 </td><td>	Numero di repliche di una determinata riga </td></tr>
<tr><td>Consistency Level (Write)</td><td>	QUORUM[(RF/2) +1 = 2] [Il risultato della formula viene arrotondato per difetto] </td><td> Scrive al massimo 2 repliche prima che la risposta venga inviata al chiamante. La terza replica viene scritta in modo coerente alla fine. </td></tr>
<tr><td>Consistency Level (Read)	</td><td>QUORUM [(RF/2) +1 = 2] [Il risultato della formula viene arrotondato per difetto]</td><td>	Legge 2 repliche prima di inviare la risposta al chiamante.</td></tr>
<tr><td>Replication Strategy </td><td>	NetworkTopologyStrategy [per altre informazioni, vedere la pagina relativa alla [replica dei dati](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) nella documentazione di Cassandra]</td><td>	Riconosce la topologia della distribuzione e inserisce le repliche nei nodi in modo che tutte le repliche non finiscano nello stesso rack</td></tr>
<tr><td>Snitch	</td><td>GossipingPropertyFileSnitch [per altre informazioni, vedere la pagina relativa agli [snitch](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) nella documentazione di Cassandra]</td><td>	NetworkTopologyStrategy usa il concetto di snitch per conoscere la topologia. GossipingPropertyFileSnitch offre un maggior controllo sul mapping di ogni nodo al data center e al rack. Il cluster usa quindi il protocollo gossip per propagare queste informazioni. Questo è molto più semplice nell'impostazione IP dinamico relativa a PropertyFileSnitch </td></tr>
</TABLE>

**Considerazioni relative ad Azure per il cluster Cassandra:** la funzionalità Macchine virtuali di Microsoft Azure usa l'archiviazione BLOB di Azure per il salvataggio permanente dei dischi. Archiviazione di Azure salva 3 repliche di ogni disco per garantire una durabilità elevata. Ciò significa che ogni riga di dati inserita in una tabella di Cassandra è già archiviata in 3 repliche e quindi la coerenza dei dati è già stata presa in considerazione anche se il fattore di replica è 1. Il problema principale con un fattore di replica pari a 1 è che ci sarà un tempo di inattività dell'applicazione anche in caso di errore di un solo nodo di Cassandra. Se tuttavia un nodo è inattivo a causa dei problemi (ad esempio, errori di hardware o di software di sistema) riconosciuti dal controller di infrastruttura di Azure, verrà sostituito da un nuovo nodo che userà le stesse unità di archiviazione. Il provisioning di un nuovo nodo con cui sostituire quello vecchio può richiedere alcuni minuti. Similmente alle attività di manutenzione pianificate, come le modifiche al sistema operativo guest, gli aggiornamenti di Cassandra e le modifiche alle applicazioni, il controller di infrastruttura di Azure esegue aggiornamenti in sequenza dei nodi nel cluster. Anche gli aggiornamenti in sequenza possono rendere inattivi alcuni nodi contemporaneamente e quindi può verificarsi un breve periodo di inattività per alcune partizioni. I dati tuttavia non verranno persi a causa della ridondanza predefinita di Archiviazione di Azure. 

Per i sistemi distribuiti in Azure che non richiedono una disponibilità elevata (ad esempio, 99,9 circa che equivale a 8,76 ore/anno; per i dettagli, vedere la pagina relativa alla [disponibilità elevata](http://en.wikipedia.org/wiki/High_availability)) è possibile l'esecuzione con RF=1 e Consistency Level=ONE.  Per le applicazioni con requisiti di disponibilità elevata, RF=3 e Consistency Level=QUORUM consentiranno di tollerare il tempo di inattività di uno dei nodi delle repliche. RF=1 nelle distribuzioni tradizionali (ad esempio, locali) non può essere usato a causa della possibile perdita di dati causata da problemi come gli errori dei dischi.   

## Distribuzione in più aree ##
Il modello descritto di replica e coerenza con riconoscimento del data center di Cassandra è di aiuto nella distribuzione in più aree predefinita, senza necessità di alcuno strumento esterno. La differenza con i tradizionali database relazionali, in cui la configurazione per il mirroring del database per le scritture multimaster può essere molto complessa, è considerevole. Cassandra in una configurazione a più aree può essere utile negli scenari d'uso seguenti: 

**Distribuzione basata sulla prossimità:** le applicazioni multi-tenant, con un mapping ben definito degli utenti tenant all'area, possono usufruire delle basse latenze del cluster in più aree. Un sistema LMS per gli istituti didattici, ad esempio, può distribuire un cluster distribuito nelle aree Stati Uniti Orientali e Stati Uniti Occidentali per gestire le attività transazionali e di analisi dei rispettivi campus. I dati possono essere coerenti localmente in fase di lettura e di scrittura e possono essere coerenti alla fine in entrambe le aree. Altri esempi di validi casi d'uso per questo modello di distribuzione sono la distribuzione di contenuti multimediali, l'e-commerce e qualsiasi altro scenario che coinvolga utenti geograficamente concentrati. 

**Disponibilità elevata:** la ridondanza è un fattore chiave per avere disponibilità elevata di software e hardware. Per informazioni dettagliate, vedere l'articolo relativo alla compilazione di sistemi cloud affidabili in Microsoft Azure. In Microsoft Azure, il solo modo affidabile per ottenere una reale ridondanza è la distribuzione del cluster in più aree. Le applicazioni possono essere distribuite in modalità attivo/attivo o attivo/passivo e, se una delle aree è inattiva, Gestione traffico di Azure può reindirizzare il traffico all'area attiva. Se con la distribuzione in un'area singola la disponibilità è 99,9, con una distribuzione in due aree si può ottenere una disponibilità di 99,9999 calcolata con la formula: (1-(1-0,999) * (1-0,999))*100). Per informazioni dettagliate, vedere la sezione precedente. 

**Ripristino di emergenza:** il cluster Cassandra in più aree, se progettato correttamente, può sostenere gravi interruzioni del data center. Se un'area è inattiva, l'applicazione distribuita nelle altre aree può iniziare a gestire gli utenti finali. Come le altre implementazioni di continuità aziendale, l'applicazione deve tollerare alcune perdite di dati derivanti dai dati nella pipeline asincrona. Cassandra tuttavia esegue il ripristino molto più rapidamente dei tradizionali processi di ripristino del database. La figura 2 mostra il tipico modello di distribuzione in più aree con otto nodi in ogni area. Le due immagini sono speculari e simmetriche. I progetti veri e propri dipenderanno dal tipo di carico di lavoro (ad esempio, transazionale o analitico), RPO, RTO, coerenza dei dati e requisiti di disponibilità. 

![Multi region deployment](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

Figura 2: Distribuzione di cassandra in più aree

### Integrazione della rete ###
I set di macchine virtuali distribuiti in reti private in due aree diverse comunicano tra loro con un tunnel VPN. Il tunnel VPN connette due gateway software di cui viene effettuato il provisioning durante il processo di distribuzione in rete. Entrambe le aree hanno un'architettura di rete simile a livello di subnet Web e di dati. La rete di Azure consente di creare tutte le subnet necessarie e di applicare gli elenchi ACL come richiesto dalla sicurezza di rete. Quando si progetta la topologia del cluster, è necessario tenere in considerazione la latenza della comunicazione tra data center e l'impatto economico del traffico di rete. 

### Coerenza dei dati per la distribuzione in più data center ###
Le distribuzioni devono riconoscere l'impatto della topologia del cluster sulla velocità effettiva e sulla disponibilità elevata. Il fattore di replica e il livello di coerenza devono essere selezionati in modo che il quorum non dipenda dalla disponibilità di tutti i data center. 
Per un sistema che richiede una coerenza elevata, un livello di coerenza LOCAL_QUORUM (per letture e scritture) garantirà che le letture e le scritture locali vengano soddisfatte dai nodi locali mentre i dati vengono replicati in modo asincrono nei data center remoti.  La tabella 2 riepiloga i dettagli della configurazione per il cluster in più aree, che verrà illustrata più avanti in questo articolo. 

**Configurazione del cluster Cassandra in due aree**

<table>
<tr><th>Parametro cluster </th><th>Valore	</th><th>Osservazioni </th></tr>
<tr><td>Number of Nodes (N) </td><td>	8 + 8	</td><td> Numero totale di nodi nel cluster </td></tr>
<tr><td>Replication Factor (RF)</td><td>	3 	</td><td>Numero di repliche di una determinata riga </td></tr>
<tr><td>Consistency Level (Write)	</td><td>LOCAL_QUORUM[(sum(RF/2) +1) = 4] [Il risultato della formula viene arrotondato per difetto]	</td><td>2 nodi verranno scritti nel primo data center in modo sincrono. Gli altri 2 nodi necessari per il quorum verranno scritti in modo asincrono nel secondo data center. </td></tr>
<tr><td>Consistency Level (Read)</td><td>	LOCAL_QUORUM[((RF/2) +1) = 2] [Il risultato della formula viene arrotondato per difetto]	</td><td>Le richieste di lettura vengono soddisfatte da una sola area. 2 nodi vengono letti prima che la risposta venga inviata nuovamente al client.  </td></tr>
<tr><td>Replication Strategy </td><td>	NetworkTopologyStrategy [per altre informazioni, vedere la pagina relativa alla [replica dei dati](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) nella documentazione di Cassandra] </td><td>	Riconosce la topologia della distribuzione e inserisce le repliche nei nodi in modo che tutte le repliche non finiscano nello stesso rack  </td></tr>
<tr><td>Snitch</td><td> GossipingPropertyFileSnitch [per altre informazioni, vedere la pagina relativa agli [snitch](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) nella documentazione di Cassandra] </td><td>NetworkTopologyStrategy usa il concetto di snitch per conoscere la topologia. GossipingPropertyFileSnitch offre un maggior controllo sul mapping di ogni nodo al data center e al rack. Il cluster usa quindi il protocollo gossip per propagare queste informazioni. Questo è molto più semplice nell'impostazione IP dinamico relativa a PropertyFileSnitch </td></tr> 
</table> 

##CONFIGURAZIONE SOFTWARE##
Durante la distribuzione vengono usate le seguenti versioni software:

<table>
<tr><th>Software</th><th>Origine</th><th>Versione</th></tr>
<tr><td>JRE	</td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA	</td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu	</td><td>[Portale di Microsoft Azure](http://azure.microsoft.com) </td><td>14.04 LTS</td></tr>
</table>

Poiché il download di JRE richiede di accettare manualmente la licenza Oracle, per semplificare la distribuzione, scaricare tutto il software necessario nel desktop per caricarlo in seguito nell'immagine modello di Ubuntu che verrà creata come precursore della distribuzione cluster. 

Scaricare il software citato sopra in una directory per i download nota (ad esempio, %TEMP%/downloads in Windows o ~/downloads in Linux o Mac) nel desktop locale. 

### CREARE UNA MACCHINA VIRTUALE UBUNTU ###
In questo passaggio del processo verrà creata l'immagine di Ubuntu con i prerequisiti software per poter riusare l'immagine per il provisioning di più nodi di Cassandra.  
####PASSAGGIO 1: Generare la coppia di chiavi SSH####
In fase di provisioning Azure richiede una chiave pubblica X509 con codifica PEM o DER. Generare una coppia di chiavi pubbliche/private seguendo le istruzioni contenute in Come usare SSH con Linux in Azure. Se si prevede di usare putty.exe come client SSH in Windows o Linux, è necessario convertire la chiave privata RSA con codifica PEM nel formato PPK mediante puttygen.exe. Le istruzioni relative a questa operazione sono disponibili nella pagina Web precedente. 

####PASSAGGIO 2: Creare una macchina virtuale modello Ubuntu####
Per creare la macchina virtuale modello, accedere al portale azure.microsoft.com e usare la seguente sequenza: Fare clic su NUOVO, CALCOLO, MACCHINA VIRTUALE, DA RACCOLTA, UBUNTU, Ubuntu Server 14.04 LTS e quindi fare clic sulla freccia destra. Per un'esercitazione che descrive come creare una macchina virtuale Linux, vedere Creazione di una macchina virtuale che esegue Linux.

Immettere le informazioni seguenti nella schermata n. 1 "Configurazione macchina virtuale": 

<table>
<tr><th>NOME CAMPO              </td><td>       VALORE CAMPO               </td><td>         OSSERVAZIONI                </td><tr>
<tr><td>DATA DI RILASCIO VERSIONE    </td><td> Selezionare una data nella casella a discesa</td><td></td><tr>
<tr><td>NOME MACCHINA VIRTUALE    </td><td> cass-template	               </td><td> Nome host della macchina virtuale </td><tr>
<tr><td>LIVELLO	                 </td><td> STANDARD	                       </td><td> Lasciare l'impostazione predefinita              </td><tr>
<tr><td>DIMENSIONI	                 </td><td> A1                              </td><td>Selezionare la macchina virtuale in base alle esigenze di IO; per questo scopo, lasciare l'impostazione predefinita </td><tr>
<tr><td> NUOVO NOME UTENTE	         </td><td> localadmin	                   </td><td> "admin" è un nome utente riservato in Ubuntu 12.xx e versioni successive</td><tr>
<tr><td> AUTENTICAZIONE	     </td><td> Fare clic sulla casella di controllo                 </td><td>Selezionare se si desidera applicare la protezione con una chiave SSH </td><tr>
<tr><td> CERTIFICATO	         </td><td> nome file del certificato di chiave pubblica </td><td> Usare la chiave pubblica generata in precedenza</td><tr>
<tr><td> New Password	</td><td> password complessa </td><td> </td><tr>
<tr><td> Confirm Password	</td><td> password complessa </td><td></td><tr>
</table>

Immettere le informazioni seguenti nella schermata n. 2 "Configurazione macchina virtuale": 

<table>
<tr><th>NOME CAMPO             </th><th> VALORE CAMPO	                   </th><th> OSSERVAZIONI                                 </th></tr>
<tr><td> SERVIZIO CLOUD	</td><td> Crea un nuovo servizio cloud	</td><td>Il servizio cloud è un contenitore per le risorse di calcolo come le macchine virtuali</td></tr>
<tr><td> NOME DNS DEL SERVIZIO CLOUD:	</td><td>ubuntu-template.cloudapp.net	</td><td>Assegnare un nome bilanciamento del carico indipendente dal computer</td></tr>
<tr><td> AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE </td><td>	Stati Uniti occidentali	</td><td> Selezionare un'area da cui le applicazioni Web accedono al cluster Cassandra</td></tr>
<tr><td>ACCOUNT DI ARCHIVIAZIONE </td><td>	Usare l'impostazione predefinita.	</td><td>Usare l'account di archiviazione predefinito o un account di archiviazione creato in precedenza in una determinata area</td></tr>
<tr><td>SET DI DISPONIBILITÀ </td><td>	Nessuno </td><td>	Lasciare vuoto</td></tr>
<tr><td>ENDPOINT	</td><td>Usare l'impostazione predefinita. </td><td>	Usare la configurazione SSH predefinita </td></tr>
</table>

Fare clic sulla freccia destra, lasciare le impostazioni predefinite nella schermata n. 3 e fare clic sul pulsante con il segno di spunta per completare il processo di provisioning della macchina virtuale. Dopo alcuni minuti, la macchina virtuale "ubuntu-template" sarà in stato "In esecuzione". 

###INSTALLARE IL SOFTWARE NECESSARIO###
####PASSAGGIO 1: Caricare i file tarball ####
Con scp o pscp, copiare il software scaricato in precedenza nella directory ~/downloads con il seguente formato del comando: 

#####pscp server-jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz #####

Ripetere il comando precedente per JRE e per i bit di Cassandra. 

####PASSAGGIO 2: Preparare la struttura di directory ed estrarre gli archivi####
Accedere alla macchina virtuale e creare la struttura di directory ed estrarre il software come utente con privilegi avanzati con lo script bash seguente:

	#!/bin/bash
	CASS_INSTALL_DIR="/opt/cassandra"
	JRE_INSTALL_DIR="/opt/java"
	CASS_DATA_DIR="/var/lib/cassandra"
	CASS_LOG_DIR="/var/log/cassandra"
	DOWNLOADS_DIR="~/downloads"
	JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
	CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
	SVC_USER="localadmin"
	
	RESET_ERROR=1
	MKDIR_ERROR=2
	
	reset_installation ()
	{
	   rm -rf $CASS_INSTALL_DIR 2> /dev/null
	   rm -rf $JRE_INSTALL_DIR 2> /dev/null
	   rm -rf $CASS_DATA_DIR 2> /dev/null
	   rm -rf $CASS_LOG_DIR 2> /dev/null
	}
	make_dir ()
	{
	   if [ -z "$1" ]
	   then
	      echo "make_dir: invalid directory name"
	      exit $MKDIR_ERROR
	   fi
	   
	   if [ -d "$1" ]
	   then
	      echo "make_dir: directory already exists"
	      exit $MKDIR_ERROR
	   fi
	
	   mkdir $1 2>/dev/null
	   if [ $? != 0 ]
	   then
	      echo "directory creation failed"
	      exit $MKDIR_ERROR
	   fi
	}
	
	unzip()
	{
	   if [ $# == 2 ]
	   then
	      tar xzf $1 -C $2
	   else
	      echo "archive error"
	   fi
	   
	}
	
	if [ -n "$1" ]
	then
	   SVC_USER=$1
	fi
	
	reset_installation 
	make_dir $CASS_INSTALL_DIR
	make_dir $JRE_INSTALL_DIR
	make_dir $CASS_DATA_DIR
	make_dir $CASS_LOG_DIR
	
	#unzip JRE and Cassandra 
	unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
	unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR
	
	#Change the ownership to the service credentials
	
	chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
	chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
	echo "edit /etc/profile to add JRE to the PATH"
	echo "installation is complete"


Se si incolla questo script in una finestra di vim, rimuovere il ritorno a capo ('\r") con il comando seguente:

	tr -d '\r' <infile.sh >outfile.sh

####Passaggio 3: Modificare il profilo etc/####
Aggiungere il codice seguente alla fine: 

	JAVA_HOME=/opt/java/jdk1.8.0_05 
	CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
	PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
	export JAVA_HOME
	export CASS_HOME
	export PATH

####Passaggio 4: Installare JNA per i sistemi di produzione####
Usare la sequenza di comandi seguente: 
Il comando seguente installerà jna-3.2.7.jar e jna-platform-3.2.7.jar nella directory /usr/share.java
sudo apt-get install libjna-java 

Creare collegamenti simbolici nella directory $CASS_HOME/lib in modo che lo script di avvio di Cassandra possa trovare questi file jar:

	ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

	ln -s /usr/share/java/jna-platrom-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####Passaggio 5: Configurare cassandra.yaml####
Modificare cassandra.yaml in ogni macchina virtuale per poter effettuare la reflection della configurazione necessaria per tutte le macchine virtuali [questo passaggio verrà perfezionato durante il provisioning vero e proprio]: 

<table>
<tr><th>Nome campo   </th><th> Valore  </th><th>	Osservazioni </th></tr>
<tr><td>cluster_name </td><td>	"CustomerService"	</td><td> Usare il nome che rispecchia la distribuzione</td></tr> 
<tr><td>listen_address	</td><td>[lasciare vuoto]	</td><td> Eliminare "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[lasciare vuoto]	</td><td> Eliminare "localhost" </td></tr>
<tr><td>seeds	</td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"	</td><td>Elenco di tutti gli indirizzi IP designati come semi.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Viene usato da NetworkTopologyStrateg per derivare il data center e il rack della macchina virtuale</td></tr>
</table>

####Passaggio 6: Acquisire l'immagine della macchina virtuale####
Accedere alla macchina virtuale con il nome host (hk-cas-template.cloudapp.net) e la chiave privata SSH creata in precedenza. Per informazioni dettagliate su come accedere con il comando SSH o putty.exe, vedere Come usare SSH con Linux in Azure. 

Per acquisire l'immagine, eseguire la sequenza di azioni seguente:
#####1. Effettuare il deprovisioning#####
Usare il comando "sudo waagent -deprovision+user" per rimuovere le informazioni specifiche dell'istanza della macchina virtuale. Per altri dettagli sul processo di acquisizione di un'immagine, vedere [Acquisire un'immagine di una macchina virtuale che esegue Linux](http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-linux-capture-image/). 

#####2: Arrestare la macchina virtuale#####
Assicurarsi che la macchina virtuale sia evidenziata e fare clic sul collegamento ARRESTA nella barra dei comandi in basso.

#####3: Acquisire l'immagine#####
Assicurarsi che la macchina virtuale sia evidenziata e fare clic sul collegamento ACQUISISCI nella barra dei comandi in basso. Nella schermata successiva assegnare un NOME IMMAGINE (ad esempio, hk-cas-2-08-ub-14-04-2014071) e una DESCRIZIONE IMMAGINE appropriata e fare clic sul segno di spunta per terminare il processo di ACQUISIZIONE.

Dopo alcuni secondi l'immagine dovrebbe essere disponibile nella sezione IMMAGINI PERSONALI della raccolta immagini. La macchina virtuale di origine verrà automaticamente eliminata una volta acquisita l'immagine. 

##Processo di distribuzione in un'area singola##
**Passaggio 1: Creare la rete virtuale**
Accedere al portale di gestione e creare una rete virtuale con gli attributi elencati nella tabella. Per i passaggi dettagliati del processo, vedere [Configurare una rete virtuale solo cloud nel portale di gestione](http://msdn.microsoft.com/it-it/library/azure/dn631643.aspx).      

<table>
<tr><th>Nome attributo macchina virtuale</th><th>Valore</th><th>Osservazioni</th></tr>
<tr><td>Nome</td><td>vnet-cass-west-us</td><td></td></tr>	
<tr><td>Area</td><td>Stati Uniti occidentali</td><td></td></tr>	
<tr><td>Server DNS	</td><td>Nessuno</td><td>Ignorare questo attributo perché non si userà un server DNS</td></tr>
<tr><td>Configura una VPN Point-to-Site</td><td>Nessuno</td><td> Ignorare questo attributo</td></tr>
<tr><td>Configura una VPN Site-to-Site</td><td>Nessuno</td><td> Ignorare questo attributo</td></tr>
<tr><td>Spazio di indirizzi</td><td>10.1.0.0/16</td><td></td></tr>	
<tr><td>IP iniziale</td><td>10.1.0.0</td><td></td></tr>	
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Aggiungere le subnet seguenti: 

<table>
<tr><th>Nome</th><th>IP iniziale</th><th>CIDR</th><th>Osservazioni</th></tr>
<tr><td>Web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Subnet per la Web farm</td></tr>
<tr><td>dati</td><td>10.1.2.0</td><td>/24 (251)</td><td>Subnet per i nodi del database</td></tr>
</table>

Le subnet dati e Web possono essere protette con i gruppi di sicurezza di rete che esulano dall'ambito di questo articolo.  

**Passaggio 2: Effettuare il provisioning delle macchine virtuali** 
Con l'immagine creata in precedenza, si creeranno le macchine virtuali seguenti nel server cloud "hk-c-svc-west" e le si assocerà alle rispettive subnet, come indicato di seguito: 

<table>
<tr><th>Nome computer    </th><th>Subnet	</th><th>Indirizzo IP	</th><th>Set di disponibilità</th><th>DC/Rack</th><th>Seme?</th></tr>
<tr><td>hk-c1-west-us	</td><td>dati	</td><td>10.1.2.4	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1 </td><td>Sì</td></tr>
<tr><td>hk-c2-west-us	</td><td>dati	</td><td>10.1.2.5	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1	</td><td>No </td></tr>
<tr><td>hk-c3-west-us	</td><td>dati	</td><td>10.1.2.6	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>Sì</td></tr>
<tr><td>hk-c4-west-us	</td><td>dati	</td><td>10.1.2.7	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>No </td></tr>
<tr><td>hk-c5-west-us	</td><td>dati	</td><td>10.1.2.8	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>Sì</td></tr>
<tr><td>hk-c6-west-us	</td><td>dati	</td><td>10.1.2.9	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>No </td></tr>
<tr><td>hk-c7-west-us	</td><td>dati	</td><td>10.1.2.10	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>Sì</td></tr>
<tr><td>hk-c8-west-us	</td><td>dati	</td><td>10.1.2.11	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>No </td></tr>
<tr><td>hk-w1-west-us	</td><td>Web	</td><td>10.1.1.4	</td><td>hk-w-aset-1	</td><td>                       </td><td>N/D</td></tr>
<tr><td>hk-w2-west-us	</td><td>Web	</td><td>10.1.1.5	</td><td>hk-w-aset-1	</td><td>                       </td><td>N/D</td></tr>
</table>

Per creare l'elenco precedente di macchine virtuali, è necessario eseguire il processo seguente:

1.  Creare un servizio cloud vuoto in una determinata area
2.	Creare una macchina virtuale dall'immagine acquisita in precedenza e collegarla alla rete virtuale creata in precedenza e ripetere questo passaggio per tutte le macchine virtuali
3.	Aggiungere al servizio cloud un dispositivo di bilanciamento del carico interno e collegarlo alla subnet "dati"
4.	Per ogni macchina virtuale creata in precedenza, aggiungere un endpoint con carico bilanciato per il traffico Thrift con un set con carico bilanciato connesso al dispositivo di bilanciamento del carico creato in precedenza

Il processo precedente può essere eseguito con il portale di gestione di Azure. Usare un computer Windows (usare una macchina virtuale in Azure se non si ha accesso a un computer Windows) e usare il seguente script PowerShell per il provisioning automatico di tutte le 8 macchine virtuali.

**List1: script PowerShell per il provisioning delle macchine virtuali**
		
		#Tested with Azure Powershell - November 2014	
		#This powershell script deployes a number of VMs from an existing image inside an Azure region
		#Import your Azure subscription into the current Powershell session before proceeding
		#The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template
		
		#fundamental variables - change these to reflect your subscription
		$country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
		$numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
		$subscriptionName = "Azure_subscription_name"; 
		$vmSize="ExtraSmall"; $imageName="your_linux_image_name"
		$ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"
		
		#generated variables
		$serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"
		
		$vmNames = @()
		for ($i=0; $i -lt $numVMs; $i++)
		{
		   $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
		}
		
		#select an Azure subscription already imported into Powershell session
		Select-AzureSubscription -SubscriptionName $subscriptionName -Current
		Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount
		
		#create an empty cloud service
		New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
		Write-Host "Created $serviceName"
		
		$VMList= @()   # stores the list of azure vm configuration objects
		#create the list of VMs
		foreach($vmName in $vmNames)
		{
		   $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
		   Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
		   Set-AzureSubnet "data"
		}
		
		New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList
		
		#Create internal load balancer
		Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
		Write-Host "Created $ilbName"
		#Add add the thrift endpoint to the internal load balancer for all the VMs
		foreach($vmName in $vmNames)
		{
		    Get-AzureVM -ServiceName $serviceName -Name $vmName |
		        Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName | 
		        Update-AzureVM 
		
		    Write-Host "created $vmName"     
		}

**Passaggio 3: Configurare Cassandra in ogni macchina virtuale**

Accedere alla macchina virtuale ed eseguire la procedura seguente: 

* Modificare $CASS_HOME/conf/cassandra-rackdc.properties per specificare le proprietà del data center e del rack:
      
       dc =EASTUS, rack =rack1

* Modificare cassandra.yaml per configurare i nodi dei semi come segue:
     
       Semi: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Passaggio 4: Avviare le macchine virtuali e testare il cluster**

Accedere a uno dei nodi (ad esempio, hk-c1-west-us) ed eseguire il comando seguente per visualizzare lo stato del cluster: 
       
       nodetool -h 10.1.2.4 -p 7199 status 

Per un cluster a 8 nodi i dati visualizzati saranno simili ai seguenti: 

<table>
<tr><th>Status</th></th>Address	</th><th>Load	</th><th>Tokens	</th><th>Owns </th><th>Host ID	</th><th>Rack</th></tr>
<tr><th>UN	</td><td>10.1.2.4 	</td><td>87.81 KB	</td><td>256	</td><td>38,0%	</td><td>Guid (removed)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.5 	</td><td>41.08 KB	</td><td>256	</td><td>68,9%	</td><td>Guid (removed)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.6 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>Guid (removed)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.7 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>Guid (removed)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.8 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>Guid (removed)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.9 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>Guid (removed)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.10 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid (removed)</td><td>rack4</td></tr>
<tr><th>UN	</td><td>10.1.2.11 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid (removed)</td><td>rack4</td></tr>
</table>

##<a id="testone"> </a>Testare il cluster in un'area singola##
Usare i passaggi seguenti per testare il cluster:

1.    Con il commandlet Get-AzureInternalLoadbalancer dei comandi Powershell, ottenere l'indirizzo IP del dispositivo di bilanciamento del carico interno (ad esempio, 10.1.2.101). Di seguito viene mostrata la sintassi del comando: Get-AzureLoadbalancer -ServiceName "hk-c-svc-west-us" [visualizza i dettagli del dispositivo di bilanciamento di carico interno con l'indirizzo IP]
2.	Accedere alla macchina virtuale della Web farm (ad esempio, hk-w1-west-us) con Putty o SSH
3.	Eseguire $CASS_HOME/bin/cqlsh 10.1.2.101 9160 
4.	Usare i seguenti comandi CQL per verificare se il cluster è in funzione:

		CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };	
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		
		SELECT * FROM Customers;

I dati visualizzati saranno simili ai seguenti:

<table>
  <tr><th> customer_id </th><th> firstname </th><th> lastname </th></tr>
  <tr><td> 1 </td><td> John </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Doe </td></tr>
</table>

Si noti che il keyspace creato nel passaggio 4 usa SimpleStrategy con replication_factor pari a 3. SimpleStrategy è consigliato per le distribuzioni in un data center singolo, NetworkTopologyStrategy invece per le distribuzioni in più data center. Un replication_factor pari a 3 garantirà la tolleranza per gli errori dei nodi. 

##<a id="tworegion"> </a>Processo di distribuzione in più aree##
Verrà sfruttata la distribuzione in un'area singola completata e si ripeterà lo stesso processo per l'installazione della seconda area. La differenza principale tra la distribuzione in un'area singola e in più aree è la configurazione del tunnel VPN per la comunicazione tra le aree. Si inizierà con l'installazione di rete, il provisioning delle macchine virtuali e la configurazione di Cassandra. 

###Passaggio 1: Creare la rete virtuale nella seconda area###
Accedere al portale di gestione e creare una rete virtuale con gli attributi elencati nella tabella. Per i passaggi dettagliati del processo, vedere [Configurare una rete virtuale solo cloud nel portale di gestione](http://msdn.microsoft.com/it-it/library/azure/dn631643.aspx).      

<table>
<tr><th>Nome attributo    </th><th>Valore	</th><th>Osservazioni</th></tr>
<tr><td>Nome	</td><td>vnet-cass-east-us</td><td></td></tr>	
<tr><td>Area	</td><td>Stati Uniti orientali</td><td></td></tr>	
<tr><td>Server DNS		</td><td></td><td>Ignorare questo attributo perché non si userà un server DNS</td></tr>
<tr><td>Configura una VPN Point-to-Site</td><td></td><td>		Ignorare questo attributo</td></tr>
<tr><td>Configura una VPN Site-to-Site</td><td></td><td>		Ignorare questo attributo</td></tr>
<tr><td>Spazio di indirizzi	</td><td>10.2.0.0/16</td><td></td></tr>	
<tr><td>IP iniziale	</td><td>10.2.0.0	</td><td></td></tr>
<tr><td>CIDR	</td><td>/16 (65531)</td><td></td></tr>
</table>	

Aggiungere le subnet seguenti: 
<table>
<tr><th>Nome    </th><th>IP iniziale	</th><th>CIDR	</th><th>Osservazioni</th></tr>
<tr><td>Web	</td><td>10.2.1.0	</td><td>/24 (251)	</td><td>Subnet per la Web farm</td></tr>
<tr><td>dati	</td><td>10.2.2.0	</td><td>/24 (251)	</td><td>Subnet per i nodi del database</td></tr>
</table>


###Passaggio 2: Creazione delle reti locali###
Una rete locale nella rete virtuale di Azure è uno spazio di indirizzi proxy che esegue il mapping a un sito remoto, incluso un cloud privato o un'altra area di Azure. Questo spazio di indirizzi proxy è associato a un gateway remoto per il routing della rete alle destinazioni di rete appropriate. Per le istruzioni per stabilire la connessione tra reti virtuali, vedere [Configurare una connessione tra reti virtuali](http://msdn.microsoft.com/it-it/library/azure/dn690122.aspx). 

Creare due reti locali con i dettagli seguenti:

<table>
<tr><th>Nome rete          </th><th>Indirizzo gateway VPN	</th><th>Spazio di indirizzi	</th><th>Osservazioni</th></tr>
<tr><td>hk-lnet-map-to-east-us</td><td>	23.1.1.1	</td><td>10.2.0.0/16	</td><td>Durante la creazione della rete locale assegnare un indirizzo gateway segnaposto. Il vero indirizzo gateway verrà inserito una volta creato il gateway. Verificare che lo spazio di indirizzi corrisponda esattamente alla rispettiva rete virtuale remota, in questo caso la rete virtuale creata nell'area Stati Uniti orientali.</td></tr>
<tr><td>hk-lnet-map-to-west-us	</td><td>23.2.2.2	</td><td>10.1.0.0/16	</td><td>Durante la creazione della rete locale assegnare un indirizzo gateway segnaposto. Il vero indirizzo gateway verrà inserito una volta creato il gateway. Verificare che lo spazio di indirizzi corrisponda esattamente alla rispettiva rete virtuale remota, in questo caso la rete virtuale creata nell'area Stati Uniti occidentali.</td></tr>
</table>


###Passaggio 3: Eseguire il mapping della rete "locale" alle rispettive reti virtuali###
Nel portale di gestione dei servizi selezionare ogni rete virtuale, fare clic su "Configura", selezionare "Connetti alla rete locale" e selezionare le reti locali con i seguenti dettagli: 

<table>
<tr><th>Rete virtuale </th><th>Rete locale</th></tr>
<tr><td>hk-vnet-west-us	</td><td>hk-lnet-map-to-east-us</td></tr>
<tr><td>hk-vnet-east-us	</td><td>hk-lnet-map-to-west-us</td></tr>
</table>

###Passaggio 4: Creare i gateway nella rete virtuale 1 e nella rete virtuale 2###
Nel dashboard di entrambe le reti virtuali fare clic su CREA GATEWAY per attivare il processo di provisioning dei gateway VPN. Dopo alcuni minuti nel dashboard di ogni rete virtuale verrà visualizzato l'effettivo indirizzo gateway. 
###Passaggio 5: Aggiornare le reti "locali" con i rispettivi indirizzi "gateway"###
Modificare entrambe le reti locali per poter sostituire l'indirizzo IP gateway segnaposto con il vero indirizzo IP dei gateway di cui è appena stato effettuato il provisioning. Usare il mapping seguente: 

<table>
<tr><th>Rete locale    </th><th>Gateway di rete virtuale</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Gateway di hk-vnet-west-us</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Gateway di hk-vnet-east-us</td></tr>
</table>

###Passaggio 6: Aggiornare la chiave condivisa###
Usare lo script Powershell seguente per aggiornare la chiave IPSec di ogni gateway VPN [usare la chiave SAKE per entrambi i gateway]: 
Set-AzureVNetGatewayKey -VNetName hk-vnet-east-us -LocalNetworkSiteName hk-lnet-map-to-west-us -SharedKey D9E76BKK
Set-AzureVNetGatewayKey -VNetName hk-vnet-west-us -LocalNetworkSiteName hk-lnet-map-to-east-us -SharedKey D9E76BKK 

###Passaggio 6: Stabilire la connessione tra reti virtuali###
Nel portale di gestione servizi di Azure usare il menu "DASHBOARD" di entrambe le reti virtuali per stabilire la connessione da gateway a gateway. Usare le voci del menu "CONNETTI" nella barra degli strumenti in basso. Dopo alcuni minuti nel dashboard apparirà la visualizzazione grafica dei dettagli della connessione.

###Passaggio 7: Creare le macchine virtuali nell'area #2 ###
Creare l'immagine Ubuntu descritta nella distribuzione nell'area n. 1 seguendo gli stessi passaggi o copiare il file VHD dell'immagine nell'account di archiviazione di Azure dell'area n. 2 e creare l'immagine. Usare questa immagine e creare il seguente elenco di macchine virtuali in un nuovo servizio cloud hk-c-svc-east-us: 

<table>
<tr></th>Nome computer </th><th>Subnet</th><th>Indirizzo IP</th><th>Set di disponibilità</th><th>DC/Rack</th><th>Seme?</th></tr>
<tr><td>hk-c1-east-us	</td><td>dati	</td><td>10.2.2.4	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack1	</td><td>Sì</td></tr>
<tr><td>hk-c2-east-us	</td><td>dati	</td><td>10.2.2.5	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack1	</td><td>No </td></tr>
<tr><td>hk-c3-east-us	</td><td>dati	</td><td>10.2.2.6	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack2	</td><td>Sì</td></tr>
<tr><td>hk-c5-east-us	</td><td>dati	</td><td>10.2.2.8	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack3	</td><td>Sì</td></tr>
<tr><td>hk-c6-east-us	</td><td>dati	</td><td>10.2.2.9	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack3	</td><td>No </td></tr>
<tr><td>hk-c7-east-us  </td><td>dati	</td><td>10.2.2.10	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack4	</td><td>Sì</td></tr>
<tr><td>hk-c8-east-us	</td><td>dati	</td><td>10.2.2.11	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack4	</td><td>No </td></tr>
<tr><td>hk-w1-east-us	</td><td>Web	</td><td>10.2.1.4	</td><td>hk-w-aset-1	</td><td>	N/D</td><td>N/D</td></tr>
<tr><td>hk-w2-east-us	</td><td>Web	</td><td>10.2.1.5	</td><td>hk-w-aset-1	</td><td>	N/D</td><td>N/D</td></tr>
</table>

Seguire le stesse istruzioni dell'area n. 1, ma usare lo spazio di indirizzi 10.2.xxx.xxx. 
###Passaggio 8: Configurare Cassandra in ogni macchina virtuale###
Accedere alla macchina virtuale ed eseguire la procedura seguente: 

1. Modificare $CASS_HOME/conf/cassandra-rackdc.properties per specificare le proprietà del data center e del rack nel formato:
    dc =EASTUS
    rack =rack1
2. Modificare cassandra.yaml per configurare i nodi dei semi: 
    Semi: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"
###Passaggio 9: Avviare Cassandra###
Accedere a ogni macchina virtuale e avviare Cassandra in background eseguendo il comando seguente:
$CASS_HOME/bin/cassandra

##<a id="testtwo"> </a>Testare il cluster in più aree##
Per ora Cassandra è stato distribuito in 16 nodi con 8 nodi in ogni area di Azure. Questi nodi sono nello stesso cluster grazie al nome cluster comune e alla configurazione dei nodi dei semi. Usare il processo seguente per testare il cluster:
###Passaggio 1: Ottenere l'IP di bilanciamento del carico interno per entrambe le aree con PowerShell### 
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  

    Prendere nota degli indirizzi IP (ad esempio, west - 10.1.2.101, east - 10.2.2.101) visualizzati.

###Passaggio 2: Eseguire quanto segue nell'area occidentale dopo l'accesso a hk-w1-west-us###
1.    Eseguire $CASS_HOME/bin/cqlsh 10.1.2.101 9160 
2.	Eseguire i comandi CQL seguenti:

		CREATE KEYSPACE customers_ks
		WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

I dati visualizzati saranno simili ai seguenti:

<table>
<tr><th>customer_id </th><th>firstname</th><th>Lastname</th></tr>
<tr><td>1</td><td>John</td><td>Doe</td></tr>
<tr><td>2</td><td>Jane</td><td>Doe</td></tr>
</table>

###Passaggio 3: Eseguire quanto segue nell'area orientale dopo l'accesso a hk-w1-east-us:###
1.    Eseguire $CASS_HOME/bin/cqlsh 10.2.2.101 9160 
2.	Eseguire i comandi CQL seguenti:

		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

I dati visualizzati saranno uguali a quelli dell'area occidentale:

<table>
<tr><th>customer_id </th><th>firstname</th><th>Lastname</th></tr>
<tr><td>1</td><td>John</td><td>Doe</td></tr>
<tr><td>2</td><td>Jane</td><td>Doe</td></tr>
</table>

Inserire ancora alcuni dati per vedere che verranno replicati nella parte west-us del cluster. 

##<a id="testnode"> </a>Testare il cluster Cassandra da Node.js##
Con una delle macchine virtuali Linux create nel livello "Web" in precedenza, verrà eseguito un semplice script Node.js per leggere i dati inseriti in precedenza 

**Passaggio 1: Installare Node.js e il client Cassandra**

1. Installare Node.js e NPM
2. Installare il pacchetto di nodi "cassandra-client" con NPM
3. Eseguire lo script seguente al prompt della shell. Viene visualizzata la stringa json dei dati recuperati: 

		var pooledCon = require('cassandra-client').PooledConnection;
		var ksName = "custsupport_ks";
		var cfName = "customers_cf";
		var hostList = ['internal_loadbalancer_ip:9160'];
		var ksConOptions = { hosts: hostList,
		                     keyspace: ksName, use_bigints: false };
		
		function createKeyspace(callback){
		   var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
		   var sysConOptions = { hosts: hostList,  
		                         keyspace: 'system', use_bigints: false };
		   var con = new pooledCon(sysConOptions);
		   con.execute(cql,[],function(err) {
		   if (err) {
		     console.log("Failed to create Keyspace: " + ksName);
		     console.log(err);
		   }
		   else {
		     console.log("Created Keyspace: " + ksName);
		     callback(ksConOptions, populateCustomerData);
		   }
		   });
		   con.shutdown();
		} 
		
		function createColumnFamily(ksConOptions, callback){
		  var params = ['customers_cf','custid','varint','custname',
		                'text','custaddress','text'];
		  var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
		var con =  new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) {
		         console.log("Failed to create column family: " + params[0]);
		         console.log(err);
		      }
		      else {
		         console.log("Created column family: " + params[0]);
		         callback();
		      }
		  });
		  con.shutdown();
		} 
		
		//populate Data
		function populateCustomerData() {
		   var params = ['John','Infinity Dr, TX', 1];
		   updateCustomer(ksConOptions,params);
		
		   params = ['Tom','Fermat Ln, WA', 2];
		   updateCustomer(ksConOptions,params);
		}
		
		//update will also insert the record if none exists
		function updateCustomer(ksConOptions,params)
		{
		  var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) console.log(err);
		      else console.log("Inserted customer : " + params[0]);
		  });
		  con.shutdown();
		}
		
		//read the two rows inserted above
		function readCustomer(ksConOptions)
		{
		  var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,[],function(err,rows) {
		      if (err) 
		         console.log(err);
		      else 
		         for (var i=0; i<rows.length; i++)
		            console.log(JSON.stringify(rows[i]));
		    });
		   con.shutdown();
		}
		
		//exectue the code
		createKeyspace(createColumnFamily);
		readCustomer(ksConOptions)


##<a id="conclusion"> </a>Conclusioni##
Microsoft Azure è una piattaforma flessibile che consente di eseguire software sia Microsoft che open source, come è stato illustrato in questa esercitazione. I cluster Cassandra a disponibilità elevata possono essere distribuiti in un data center singolo distribuendo i nodi dei cluster in più domini di errore. I cluster Cassandra possono essere distribuiti anche in più area di Azure geograficamente distanti per sistemi a prova di emergenza. La combinazione di Azure e Cassandra consente di creare servizi cloud a scalabilità elevata, a disponibilità elevata e ripristinabili in caso di emergenza, necessari per gli odierni servizi su scala Internet.  

[Panoramica]: #overview
[Distribuzione in un'area singola]: #oneregion
[Testare il cluster Cassandra in un'area singola]: #testone
[Distribuzione in più aree]: #tworegion
[Testare il cluster Cassandra in più aree]: #testtwo
[Testare il cluster Cassandra da Node.js]: #testnode
[Conclusioni]: #conclusion

##Riferimenti##
- [http://cassandra.apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com) 
- [http://www.nodejs.org](http://www.nodejs.org) 



<!--HONumber=42-->

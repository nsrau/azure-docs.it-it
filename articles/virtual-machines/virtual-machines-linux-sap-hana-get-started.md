<properties
   pageTitle="Guida introduttiva per l'installazione manuale di SAP HANA nelle macchine virtuali di Azure | Microsoft Azure"
   description="Guida introduttiva per l'installazione manuale di SAP HANA nelle macchine virtuali di Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/> 
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/> 

# Guida introduttiva per l'installazione manuale di SAP HANA a istanza singola nelle macchine virtuali di Azure

## Introduzione

Questa guida introduttiva consente di configurare un sistema prototipo/dimostrativo con SAP HANA a istanza singola nelle macchine virtuali di Azure mediante un'installazione manuale di SAP NetWeaver 7.5 e SAP HANA SP12. La guida presuppone che il lettore abbia familiarità con nozioni fondamentali di IaaS di Azure come la distribuzione di macchine virtuali o di reti virtuali tramite il portale di Azure o PowerShell/interfaccia della riga di comando, inclusa la possibilità di usare modelli JSON. Si presuppone anche che il lettore abbia familiarità con SAP HANA, SAP NetWeaver e la relativa installazione locale.

Il lettore deve conoscere la documentazione generale di SAP-Azure citata nella sezione delle informazioni generali al termine dell'articolo.

Data la limitazione ai sistemi non di produzione, questa guida non tratta argomenti quali disponibilità elevata, backup, ripristino di emergenza, prestazioni elevate o considerazioni speciali sulla sicurezza.

La configurazione di esempio è stata eseguita usando due macchine virtuali per ottenere un'installazione di SAP NetWeaver distribuita tramite il modello di Azure Resource Manager, dato che SAP-Linux-Azure è supportato solo in Azure Resource Manager e non nel modello classico. Collegamenti ad altre informazioni su Azure Resource Manager sono disponibili nella sezione delle informazioni generali al termine di questo articolo.

Di seguito sono indicate le due macchine virtuali di test usate per l'installazione di esempio:

* hana-appsrv (tipo DS3) per ospitare l'istanza di NW 7.5 ASCS + PAS
* hana-dbsrv (tipo GS4) per ospitare HANA SP12
* le due macchine virtuali appartengono alla stessa rete virtuale di Azure (azure-hana-test-vnet)
* in entrambi i casi, il sistema operativo è SLES 12 SP1


A partire da luglio 2016, tuttavia, SAP HANA è completamente supportato solo per i sistemi di produzione OLAP (BW) nelle macchine virtuali di Azure di tipo GS5. Per scopi di test in cui non si richiede il supporto ufficiale di SAP è possibile usare un tipo più piccolo, ad esempio GS4. Per SAP HANA in Azure è necessario usare sempre l'archiviazione Premium di Azure per i file di dati e di log di HANA. Vedere la sezione "Configurazione dei dischi" più avanti. Per altri dettagli sui prodotti SAP supportati in Azure, vedere la sezione delle informazioni generali al termine di questo articolo.


La guida descrive due modalità diverse per installare manualmente SAP HANA in macchine virtuali di Azure:

* installare SAP HANA tramite SAP Software Provisioning Manager (SWPM) nell'ambito di un'installazione di NetWeaver distribuita nel passaggio "istanza del database"
* installare SAP HANA usando lo strumento hdblcm di HANA Lifecycle Manager e installare NetWeaver in un secondo momento

È anche possibile usare SWPM e installare tutti i componenti, ovvero SAP HANA, server applicazioni SAP, istanza ASCS, SAP GUI, in una singola macchina virtuale. Questa opzione non è descritta in questo articolo, ma gli aspetti da prendere in considerazione sono gli stessi.

Prima di avviare un'installazione, è necessario vedere la sezione disponibile dopo gli elenchi di controllo di seguito in merito alla configurazione delle macchine virtuali di test di Azure per evitare diversi errori di base che si verificano quando si usa solo una configurazione di macchine virtuali di Azure predefinita.


## Elenco di controllo per l'installazione di SAP HANA tramite SAP SWPM

Di seguito è indicato un semplice elenco di controllo degli elementi fondamentali correlati all'installazione manuale di SAP HANA a istanza singola per un ambiente prototipo/dimostrativo tramite SAP SWPM con un'installazione di SAP NW 7.5 distribuita. I singoli elementi vengono spiegati e illustrati nei dettagli sotto forma di screenshot nell'articolo:

* creare una rete virtuale di Azure che includerà le due macchine virtuali di test
* distribuire due macchine virtuali di Azure con sistema operativo SLES 12 SP1 tramite il modello di Azure Resource Manager
* collegare due dischi di archiviazione Standard alla VM del server app, ad esempio 75 GB e 500 GB
* collegare quattro dischi alla VM del server di database HANA, ovvero 2 dischi di archiviazione Standard come per la VM del server app e 2 dischi di archiviazione Premium, ad esempio 2 x 512 GB
* a seconda dei requisiti in termini di dimensioni e/o di velocità effettiva, collegare più dischi e creare volumi con striping tramite lvm o mdadm a livello di sistema operativo nella macchina virtuale
* creare file system XFS nei dischi collegati e nei volumi logici
* montare i nuovi file system XFS a livello di sistema operativo. Usare un file system per tutto il software SAP e l'altro ad esempio per la directory sapmnt e forse i backup. Nel server di database SAP HANA montare i file system XFS nei dischi di archiviazione Premium come /hana e /usr/sap. Queste operazioni sono necessarie per evitare di riempire il file system radice, di dimensioni limitate nelle VM Linux di Azure
* immettere gli indirizzi IP locali delle macchine virtuali di test in /etc/hosts
* immettere il parametro nofail in /etc/fstab
* impostare i parametri del kernel in base alla nota SAP HANA-SLES-12. Vedere i dettagli più avanti nella sezione dei parametri del kernel
* aggiungere spazio di swapping
* se si vuole, installare un desktop con interfaccia grafica nelle macchine virtuali di test. In caso contrario, usare un'installazione sapinst remota
* scaricare il software SAP da SAP Service Marketplace
* installare l'istanza di SAP ASCS nella macchina virtuale del server app
* condividere la directory sapmnt tramite NFS tra le VM di test. La VM del server app è il server NFS.
* installare l'istanza del database incluso HANA tramite SWPM nella VM del server di database
* installare PAS nella macchina virtuale del server app
* avviare SAP MC e connettersi ad esempio tramite SAP GUI / HANA Studio



## Elenco di controllo per l'installazione di SAP HANA tramite hdblcm

Di seguito è indicato un semplice elenco di controllo degli elementi fondamentali correlati all'installazione manuale di SAP HANA a istanza singola per un ambiente prototipo/dimostrativo tramite SAP SWPM con un'installazione di SAP NW 7.5 distribuita. I singoli elementi vengono spiegati e illustrati nei dettagli sotto forma di screenshot nell'articolo:

* creare una rete virtuale di Azure che includerà le due macchine virtuali di test
* distribuire due macchine virtuali di Azure con sistema operativo SLES 12 SP1 tramite il modello di Azure Resource Manager
* collegare due dischi di archiviazione Standard alla VM del server app, ad esempio 75 GB e 500 GB
* collegare quattro dischi alla VM del server di database HANA, ovvero 2 dischi di archiviazione Standard come per la VM del server app e 2 dischi di archiviazione Premium, ad esempio 2 x 512 GB
* a seconda dei requisiti in termini di dimensioni e/o di velocità effettiva, collegare più dischi e creare volumi con striping tramite lvm o mdadm a livello di sistema operativo nella macchina virtuale
* creare file system XFS nei dischi collegati e nei volumi logici
* montare i nuovi file system XFS a livello di sistema operativo. Usare un file system per tutto il software SAP e l'altro ad esempio per la directory sapmnt e forse i backup. Nel server di database SAP HANA montare i file system XFS nei dischi di archiviazione Premium come /hana e /usr/sap. Queste operazioni sono necessarie per evitare di riempire il file system radice, di dimensioni limitate nelle VM Linux di Azure
* immettere gli indirizzi IP locali delle macchine virtuali di test in /etc/hosts
* immettere il parametro nofail in /etc/fstab
* impostare i parametri del kernel in base alla nota SAP HANA-SLES-12. Vedere i dettagli più avanti nella sezione dei parametri del kernel
* aggiungere spazio di swapping
* se si vuole, installare un desktop con interfaccia grafica nelle macchine virtuali di test. In caso contrario, usare un'installazione sapinst remota
* scaricare il software SAP da SAP Service Marketplace
* creare il gruppo "sapsys" con ID gruppo 1001 nella macchina virtuale del server di database di HANA
* installare SAP HANA nella macchina virtuale del server di database usando hdblcm
* installare l'istanza di SAP ASCS nella macchina virtuale del server app
* condividere la directory sapmnt tramite NFS tra le VM di test. La VM del server app è il server NFS.
* installare l'istanza del database incluso HANA tramite SWPM nella VM del server di database
* installare PAS nella macchina virtuale del server app
* avviare SAP MC e connettersi ad esempio tramite SAP GUI / HANA Studio




## Preparare le macchine virtuali di Azure per l'installazione manuale di SAP HANA

Questo capitolo sulla preparazione delle macchine virtuali di Azure per l'installazione manuale di SAP HANA è costituito da cinque sezioni che trattano gli argomenti seguenti:

* Configurazione dei dischi
* Parametri del kernel
* File system
* /etc/hosts
* /etc/fstab


### Configurazione dei dischi

Il file system radice di una VM Linux in Azure ha dimensioni limitate. È quindi necessario collegare altro spazio su disco a una macchina virtuale per l'esecuzione di SAP. Nel caso di una macchina virtuale del server app SAP usata in un ambiente prototipo/dimostrativo puro è possibile usare dischi di archiviazione Standard di Azure. Per i file di dati e di log del database di SAP HANA è invece necessario usare dischi di archiviazione Premium di Azure anche in uno scenario non di produzione.

Alcuni dettagli su come collegare dischi a una VM Linux sono disponibili [qui](virtual-machines-linux-add-disk.md)

Non è possibile usare il caching dei dischi di Azure per i dischi che verranno usati per archiviare i log delle transazioni HANA. Per i file di dati di HANA è possibile usare il caching di lettura. Essendo HANA un database in memoria, è il modello di uso generale a determinare in che misura la cache di lettura a livello del disco di Azure migliorerà le prestazioni, ad esempio avvio di HANA e lettura dei dati dal disco nella memoria.

Per dettagli sull'archiviazione Premium di Azure, vedere [qui](../storage/storage-premium-storage.md)

[Qui](https://github.com/Azure/azure-quickstart-templates) sono disponibili modelli JSON di esempio per la creazione di macchine virtuali. "101-vm-simple-linux" illustra l'aspetto di un modello di base, inclusa la sezione di archiviazione che consente di aggiungere un disco dati da 100 GB.

[Questo articolo](virtual-machines-linux-sap-on-suse-quickstart.md) include alcune informazioni su come trovare un'immagine SUSE tramite PowerShell o l'interfaccia della riga di comando e l'importanza di collegare un disco tramite UUID.


A seconda delle dimensioni del sistema e dei requisiti in termini di velocità effettiva potrebbe essere necessario collegare più dischi anziché uno e successivamente creare un set di striping tra tali dischi a livello di sistema operativo. Il set di striping tra più dischi di Azure viene creato per i due motivi seguenti:

* aumentare la velocità effettiva
* è necessario un unico file system > 1 TB perché il limite di dimensione del disco di Azure è di 1 TB a partire da luglio 2016


Altre informazioni sui due strumenti principali per configurare lo striping sono disponibili qui:

[Articolo sull'uso di mdadm per configurare RAID software Linux in una macchina virtuale di Azure](virtual-machines-linux-configure-raid.md)

[Articolo sulla configurazione di Logical Volume Manager (LVM) in una macchina virtuale Linux in Azure](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg) 

Nell'ambiente di test sono stati collegati due dischi di archiviazione Standard di Azure alla macchina virtuale del server app SAP. Uno è stato usato per archiviare tutto il software SAP per l'installazione (ad esempio NetWeaver 7.5, SAP GUI, SAP HANA e così via) e l'altro per avere a disposizione spazio sufficiente per altre esigenze (ad esempio backup, dati di test) e per la directory sapmnt (ad esempio profili SAP) da condividere tra tutte le macchine virtuali appartenenti alla stessa infrastruttura SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg) 

A differenza della macchina virtuale del server app, alla macchina virtuale del server SAP HANA sono stati collegati quattro dischi. Anche in questo caso, due dischi sono stati usati per il software SAP (è anche possibile condividere il disco del software SAP tramite NFS) e per avere spazio sufficiente per altre esigenze, ad esempio il backup. I due dischi aggiuntivi sono dischi di archiviazione Premium di Azure per i file di dati e di log di SAP HANA, nonché per la directory /usr/sap.


### Parametri del kernel


SAP HANA richiede impostazioni specifiche del kernel Linux che non fanno parte delle immagini della raccolta standard di Azure e devono essere definite manualmente. Esiste una nota SAP specifica che descrive le impostazioni.


Nota SAP "SAP HANA DB: Recommended OS settings for SLES 12 / SLES for SAP Applications 12" (Database SAP HANA: impostazioni del sistema operativo consigliate per SLES 12 / SLES for SAP Applications 12): [Nota SAP 2205917](https://launchpad.support.sap.com/#/notes/2205917)

Un altro argomento relativo alla cache della pagina correlato all'esecuzione di SAP HANA in SLES è disponibile [qui](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) nel capitolo 6.1 Kernel: Page-Cache Limit (Kernel: limite della cache della pagina)

È anche disponibile una nota SAP relativa al limite della cache della pagina [Nota SAP 1557506](https://service.sap.com/sap/support/notes/1557506)

SLES 12 ha un nuovo strumento che sostituisce la precedente utilità sapconf. Si tratta di "tuned-adm" ed è disponibile un apposito profilo di SAP HANA. Per altre informazioni su questo strumento fare clic sui due collegamenti seguenti.

La documentazione SLES su tuned-adm profile sap-hana è reperibile [qui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

La documentazione SLES su tuned-adm profile sap-hana, capitolo 6.2 Tuning Systems for SAP Workloads with tuned-adm (Ottimizzare i sistemi per i carichi di lavoro SAP con tuned-adm), è disponibile [qui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg) 

Qui si può osservare come "tuned-adm" ha modificato i valori di transparent\_hugepage e di numa\_balancing in base alle impostazioni di SAP HANA obbligatorie.


Per rendere permanenti le impostazioni del kernel di SAP HANA è necessario usare grub2 in SLES 12. Altre informazioni su grub2 sono disponibili [qui](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg) 

Questo screenshot illustra come le impostazioni del kernel sono state modificate nel file di configurazione e quindi "compilate" tramite grub2-mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg) 

È anche possibile modificare le impostazioni tramite Yast e le impostazioni dei parametri del kernel del caricatore di avvio.


### File system 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg) 

Qui si possono osservare i due file system che sono stati creati nella macchina virtuale del server app di SAP oltre ai due dischi di archiviazione Standard di Azure collegati. Entrambi i file system sono di tipo XFS e montati in /sapdata e /sapsoftware.

Non è obbligatorio procedere in questo modo. Esistono diverse opzioni per strutturare lo spazio su disco. L'aspetto più importante è evitare che il file system radice esaurisca lo spazio.


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg) 

Per quanto riguarda la macchina virtuale del database di SAP HANA è importante sapere che durante l'installazione di un database tramite sapinst (swpm) e usando solo l'opzione di installazione "tipica" semplice, il percorso di installazione predefinito sarà /hana e /usr/sap. Il percorso predefinito per il backup del log di SAP HANA è ad esempio /usr/sap. Anche in questo caso è fondamentale evitare che il file system radice esaurisca lo spazio. È quindi necessario verificare che ci sia spazio sufficiente in /hana e /usr/sap prima di installare SAP HANA tramite swpm.

[Questo articolo](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) di SAP descrive il layout del file system standard di SAP HANA


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg) 

Durante l'installazione di SAP NetWeaver in un'immagine della raccolta standard di Azure per SLES 12, verrà visualizzato un messaggio che indica che non è disponibile spazio di swapping. Per eliminare questo messaggio è ad esempio possibile aggiungere manualmente un file di scambio come descritto in questo documento tramite dd, mkswap e swapon. Cercare "Adding a Swap File Manually" (Aggiunta manuale di un file di scambio) [in questo articolo](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)

Un'altra opzione consiste nel configurare lo spazio di swapping tramite l'agente di macchine virtuali Linux. Altre informazioni sono disponibili [qui](virtual-machines-linux-agent-user-guide.md)


### /etc/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg) 

Un altro aspetto importante prima di iniziare a installare SAP consiste nell'includere i nomi host e gli indirizzi IP delle macchine virtuali SAP nel file /etc/hosts. È necessario distribuire tutte le macchine virtuali SAP all'interno di una rete virtuale di Azure e quindi usare gli indirizzi IP interni.

### /etc/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg) 

Durante la fase di test si è rivelato opportuno aggiungere il parametro nofail a fstab. Se si verificano problemi con i dischi, la macchina virtuale sarà comunque operativa e non si bloccherà nel processo di avvio. È tuttavia necessario prestare attenzione perché in questo caso potrebbe non essere disponibile spazio aggiuntivo sul disco e i processi potrebbero riempire il file system radice. In assenza di /hana, SAP HANA non verrà tuttavia avviato affatto.


## Installare il desktop con interfaccia grafica Gnome in SLES 12

Questo capitolo è costituito da due sezioni che trattano gli argomenti seguenti:

* Installazione del desktop Gnome e di xrdp in SLES 12
* Esecuzione di SAP MC basata su Java con Firefox in SLES 12

È anche possibile usare alternative come Xterminal e VNC, ma a partire da settembre 2016 questo articolo descriverà solo xrdp.

### Installazione del desktop Gnome e di xrdp in SLES 12

In particolare per gli utenti di Microsoft Windows che intendono usare un desktop con interfaccia grafica direttamente nelle macchine virtuali Linux SAP per eseguire Firefox, Sapinst, SAP GUI, SAP MC o HANA Studio e magari connettersi alla macchina virtuale tramite RDP da un computer Microsoft Windows, esiste un modo semplice per ottenere questo risultato. Anche se questa operazione potrebbe non essere sempre appropriata, ad esempio per un server di database di produzione, è accettabile per un ambiente prototipo/dimostrativo puro. Di seguito sono illustrati i passaggi per installare il desktop Gnome in una macchina virtuale di Azure SLES 12:

installare il desktop Gnome con il comando seguente, ad esempio in una finestra PuTTY:

   zypper in -t pattern gnome-basic

installare quindi xrdp per consentire la connessione alla macchina virtuale tramite RDP:

   zypper in xrdp

modificare /etc/sysconfig/windowmanager e impostare la gestione predefinita delle finestre su Gnome:

   DEFAULT\_WM="gnome"

eseguire chkconfig per far sì che xrdp venga avviato automaticamente dopo un riavvio:

  chkconfig -level 3 xrdp on

se si verifica un problema con la connessione RDP, provare a riavviare (forse da una finestra PuTTY):

  /etc/xrdp/xrdp.sh restart

nel caso in cui il riavvio di xrdp come indicato in precedenza non funzioni, verificare se è presente un file con estensione pid e rimuoverlo:

  in /var/run cercare xrdp.pid, rimuoverlo e provare a riavviare di nuovo



### SAP MC


Se si avvia SAP MC con interfaccia grafica basato su Java da Firefox in esecuzione in una macchina virtuale di Azure per SLES 12 dopo l'installazione del desktop Gnome come descritto nella sezione precedente, verrà generato un errore a causa del plug-in Java mancante nel browser.

L'URL per avviare SAP MC è <server>:5<numero\_istanza>13

Altri dettagli sono disponibili [qui](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg) 

Nello screenshot precedente è visibile il possibile messaggio di errore generato quando manca il plug-in Java del browser.

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg) 

Un'opzione per risolvere il problema consiste semplicemente nell'installare il plug-in mancante tramite Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg) 

Immettendo di nuovo l'URL di SAP MC, la prima volta verrà visualizzata una piccola finestra di dialogo che chiede di attivare il plug-in.


Un altro problema che potrebbe verificarsi è un messaggio di errore relativo a un file mancante, ovvero javafx.properties. Il problema è molto probabilmente correlato all'installazione di Java 1.8, necessaria per SAP GUI 7.4

La versione di IBM Java disponibile con Yast non include questo file. La soluzione è scaricare Java da Oracle. È disponibile un articolo che parla del problema specifico [qui](https://scn.sap.com/thread/3908306)



## Installazione manuale di SAP HANA tramite SWPM nell'ambito di un'installazione di NetWeaver 7.5


L'elenco di screenshot seguente illustra i principali passaggi di installazione di SAP NetWeaver 7.5 e SAP HANA SP12 tramite SWPM (sapinst). Nell'ambito di un'installazione di NW 7.5, SWPM consente anche di installare il database HANA come istanza singola.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg) 

Per l'ambiente di test di esempio è stato installato un solo server app ABAP. È stata usata l'opzione "Distributed System" (Sistema distribuito) per installare l'istanza ASCS e l'istanza del server app primario in una macchina virtuale di Azure e SAP HANA come sistema di database in un'altra macchina virtuale di Azure.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg) 

Quando l'istanza ASCS sarà stata installata nella macchina virtuale del server app e visualizzata in verde in SAP MC, sarà necessario condividere la directory sapmnt (che include ad esempio la directory dei profili SAP) con la macchina virtuale del server di database SAP HANA. La procedura di installazione del database deve accedere a queste informazioni. Il modo migliore consiste nell'usare NFS che può essere configurato con Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg) 

Nella macchina virtuale del server app, la directory sapmnt deve essere condivisa tramite NFS usando le opzioni "rw" e "no\_root\_squash". I valori predefiniti sono "ro" e "root\_squash", che potrebbero provocare problemi durante l'installazione dell'istanza di database.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg) 

Nella macchina virtuale del server di database SAP HANA, la condivisione di sapmnt dalla macchina virtuale del server app deve essere configurata tramite "client NFS", ad esempio con l'aiuto di Yast


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg) 

È quindi necessario accedere alla macchina virtuale del server di database SAP HANA e avviare SWPM per eseguire il passaggio successivo di un'installazione di NW 7.5 distribuita, ovvero l'istanza di database.


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg) 

L'installazione di SAP HANA non prevede in realtà l'immissione di molti dati dopo aver selezionato la modalità "tipica". Oltre al percorso del supporto di installazione è necessario immettere un SID DB, il nome host, il numero di istanza e la password dell'amministratore di sistema del database.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg) 

Il passaggio successivo consiste nel digitare la password per lo schema DBACOCKPIT.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg) 

Viene quindi visualizzata la domanda relativa alla password dello schema SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg) 

Al termine dovrebbero essere visibili solo segni di spunta verdi accanto a ogni fase del processo di installazione del database e la piccola finestra di messaggio visualizzerà "Execution of ..... Database Instance has completed". (Esecuzione dell'istanza di database ..... completata).

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg) 

Al termine dell'installazione, SAP MC visualizzerà l'istanza di database in verde e l'elenco completo dei processi di SAP HANA, ad esempio hdbindexserver e hdbcompileserver


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg) 

Questo screenshot indica parti della struttura di file in /hana/shared creata da SWPM durante l'installazione di HANA. Non è possibile specificare un percorso diverso. È quindi estremamente importante montare spazio su disco aggiuntivo in /hana prima di installare SAP HANA tramite SWPM per evitare che il file system radice esaurisca lo spazio disponibile.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg) 

Qui è visibile la stessa struttura descritta in precedenza per la directory /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg) 

L'ultimo passaggio dell'installazione distribuita di ABAP è l'istanza primaria del server applicazioni


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg) 

Dopo aver installato PAS e SAP GUI è possibile verificare tramite la transazione "dbacockpit" che l'installazione di SAP HANA sia stata eseguita correttamente.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg) 

Come ultimo passaggio è possibile installare SAP HANA Studio nella macchina virtuale del server applicazioni SAP e connettersi all'istanza di SAP HANA in esecuzione nella macchina virtuale del server di database.




## Installazione manuale di SAP HANA tramite lo strumento hdblcm di HANA Lifecycle Manager


Oltre a installare SAP HANA nell'ambito di un'installazione distribuita tramite SWPM è anche possibile installare prima HANA autonomo tramite hdblcm e successivamente, ad esempio, SAP NetWeaver 7.5. L'elenco di screenshot seguente illustra la procedura.

Di seguito sono indicate tre fonti di informazioni sullo strumento hdblcm di HANA:

[Choosing the Correct SAP HANA HDBLCM for Your Task (Scelta dello strumento HDBLCM di SAP HANA per la propria attività)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[SAP HANA Lifecycle Management Tools (Strumenti di SAP HANA Lifecycle Management)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA Server Installation and Update Guide (Guida all'installazione e all'aggiornamento del server SAP HANA)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg) 

Per evitare di incorrere in problemi in un secondo momento con l'impostazione di un ID gruppo predefinito per l'utente <SID HANA>adm (creato dallo strumento hdblcm), è necessario definire un nuovo gruppo denominato "sapsys" con ID gruppo 1001 prima di installare SAP HANA usando hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg) 

Quando si avvia hdblcm per la prima volta, è disponibile un menu di avvio semplice in cui è necessario selezionare la voce 1 "Install new System" (Installa nuovo sistema)



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg) 

In questo screenshot sono visibili tutte le opzioni principali immesse in precedenza. Importante: le directory indicate per volumi di dati e log HANA, nonché il percorso di installazione (/hana/shared in questo esempio) e usr/sap NON devono far parte del file system radice, ma dei dischi di dati di Azure collegati alla macchina virtuale come descritto nella sezione relativa alla configurazione della macchina virtuale di Azure. È così possibile evitare di esaurire lo spazio nel file system radice. Si può anche osservare che l'utente amministratore di HANA ha l'ID utente 1005 e appartiene al gruppo sapsys (ID 1001) definito prima dell'installazione.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg) 

È possibile esaminare i dettagli dell'utente <SID HANA>adm (azdadm in questo esempio) in /etc/passwd



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg) 

Dopo l'installazione di SAP HANA tramite hdblcm, i dettagli dell'amministratore saranno visibili in SAP HANA Studio. Lo schema SAPABAP1 che include ad esempio tutte le tabelle di SAP NetWeaver non è ancora disponibile.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg) 

Dopo l'installazione di SAP HANA è possibile installare SAP NetWeaver. In questo esempio è stata eseguita una "installazione distribuita" tramite SWPM come descritto nella sezione corrispondente illustrata in precedenza. Quando si installa l'istanza di database tramite SWPM è necessario immettere semplicemente gli stessi dati indicati in precedenza con hdblcm, ad esempio nome host, SID HANA, numero di istanza. SWPM userà quindi l'installazione di HANA esistente e aggiungerà altri schemi.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg) 

Di seguito è riportata l'immagine del passaggio di installazione di SWPM in cui è necessario immettere i dati riguardanti lo schema DBACOCKPIT.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg) 

SWPM prevede quindi l'immissione dei dati sullo schema SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg) 

Dopo aver terminato l'installazione dell'istanza di database con SWPM, lo schema SAPABAP1 sarà visibile in HANA Studio.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg) 

Dopo l'installazione del server applicazioni SAP e di SAP GUI sarà possibile verificare l'istanza di database HANA con la transazione "dbacockpit".




## Informazioni generali per le certificazioni SAP in Azure, l'esecuzione di SAP HANA in Azure e il download del software SAP

* documentazione generale relativa all'esecuzione di SAP in Azure con sistema operativo Windows in modalità classica: [Uso di SAP su macchine virtuali Windows in Azure](virtual-machines-windows-classic-sap-get-started.md)

* informazioni sui modelli SAP esistenti per l'uso da parte dei clienti: [Azure Quickstart Templates for SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/) (Modelli di avvio rapido di Azure per SAP)

* documentazione generale relativa all'esecuzione di SAP in Azure con sistema operativo Linux nel modello di Azure Resource Manager: [Uso di SAP in macchine virtuali (VM) Linux](virtual-machines-linux-sap-get-started.md)

* directory di hardware certificato SAP HANA che elenca i tipi di macchine virtuali di Azure supportati per la produzione: [Certified SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Directory di hardware certificato SAP HANA®)

* informazioni sulle dimensioni delle macchine virtuali, in particolare per i carichi di lavoro Linux: [Dimensioni delle macchine virtuali in Azure](virtual-machines-linux-sizes.md)

* Nota SAP che elenca tutti prodotti SAP supportati in Azure e i tipi di macchine virtuali di Azure supportati per SAP: [Nota SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* Nota SAP sul "monitoraggio avanzato" di SAP con macchine virtuali Linux in Azure: [Nota SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)

* Offerta per SAP HANA in istanze grandi di Azure. È importante comprendere che SAP HANA non viene eseguito in macchine virtuali di Azure, ma in un ambiente ibrido in cui i server app SAP vengono eseguiti in macchine virtuali di Azure, mentre SAP HANA viene eseguito in server bare metal: [Nota SAP 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)

* Nota SAP con informazioni su SAPOSCOL in Linux: [Nota SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)

* Metriche chiave del monitoraggio per SAP in Microsoft Azure: [Nota SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Informazioni su Azure Resource Manager: [Panoramica di Azure Resource Manager](../resource-group-overview.md)

* Informazioni sulla distribuzione di macchine virtuali Linux tramite modelli: [Distribuire e gestire le macchine virtuali usando modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-deploy-templates.md)

* Confronto dei modelli di distribuzione tra Azure Resource Manager e distribuzione classica: [Confronto tra distribuzione di Azure Resource Manager e classica: comprensione dei modelli di implementazione e dello stato delle risorse](../resource-manager-deployment-model.md)

* Scaricare NetWeaver 7.5 per Linux/HANA da SAP Service Marketplace: ![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Scaricare HANA SP12 Platform Edition da SAP Service Marketplace: ![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

<!---HONumber=AcomDC_0921_2016-->
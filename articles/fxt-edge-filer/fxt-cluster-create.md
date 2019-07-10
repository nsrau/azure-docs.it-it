---
title: Creazione del cluster Azure FXT Edge Filer di Microsoft
description: Come creare un cluster di cache di archiviazione ibrida con Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 94ec2b088940f4f1f683a4f88ae312879d909bc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543619"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Esercitazione: Creare il cluster Azure FXT Edge Filer

Dopo aver installato e inizializzato i nodi hardware Azure FXT Edge Filer per la cache, usare il software del cluster FXT per creare il cluster di cache. 

Questa esercitazione illustra i passaggi per configurare i nodi hardware come cluster. 

In questa esercitazione si apprenderà: 

> [!div class="checklist"]
> * Le informazioni necessarie prima di iniziare a creare il cluster
> * La differenza tra la rete di gestione del cluster, la rete di cluster e la rete lato client
> * Come connettersi a un nodo del cluster 
> * Come creare un cluster iniziale con un nodo Azure FXT Edge Filer
> * Come accedere al pannello di controllo del cluster per configurare le impostazioni del cluster

Questa procedura richiede da 15 a 45 minuti, a seconda delle attività di ricerca da eseguire per identificare gli IP indirizzi e le risorse di rete.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, completare questi prerequisiti:

* Installare i sistemi hardware Azure FXT Edge Filer nel data center 

  È richiesto solo un nodo per la creazione del cluster, ma è necessario [aggiungere almeno altri due nodi](fxt-add-nodes.md) prima di configurare il cluster e prepararlo per l'uso. 

* Connettere i cavi di alimentazione e di rete appropriati al sistema  
* Attivare almeno un nodo Azure FXT Edge Filer e [impostare la relativa password radice](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Raccogliere le informazioni per il cluster 

Per creare il cluster Azure FXT Edge Filer sono necessarie le informazioni seguenti:

* Nome cluster

* Password amministrativa da impostare per il cluster

* Indirizzi IP:

  * Un singolo indirizzo IP per la gestione del cluster e netmask e router da utilizzare per la rete di gestione
  * Il primo e l'ultimo indirizzo IP in un intervallo contiguo di indirizzi IP per le comunicazioni del cluster (da nodo a nodo). Vedere la [distribuzione degli indirizzi IP](#ip-address-distribution)riportata di seguito per informazioni dettagliate. 
  * (Gli indirizzi IP lato client vengono impostati dopo la creazione del cluster).

* Informazioni relative all'infrastruttura di rete:

  * L'indirizzo IP di un server DNS per il cluster
  * Il nome del dominio DNS per il cluster
  * Il nome o l'indirizzo IP per i server NTP del cluster (uno oppure tre o più server) 
  * Se si vuole abilitare Link Aggregation IEEE 802.1AX-2008 sulle interfacce del cluster
  * Se si abilita Link Aggregation, se si usa o meno Dynamic Aggregation IEEE 802.3ad (LACP)

È possibile configurare questi elementi dell'infrastruttura di rete dopo aver creato il cluster, ma è preferibile farlo al momento della creazione. 

### <a name="ip-address-distribution"></a>Distribuzione dell'indirizzo IP

Il cluster di cache di archiviazione ibrida Azure FXT Edge Filer usa gli indirizzi IP in tre categorie:

* IP di gestione: Un singolo indirizzo IP per la gestione del cluster

  Questo indirizzo viene usato come punto di ingresso per accedere alle utilità di configurazione del cluster (il pannello di controllo basato sul Web o l'API XML-RPC). Questo indirizzo viene assegnato automaticamente al nodo primario del cluster e si sposta automaticamente se il nodo primario viene modificato.

  È possibile usare altri indirizzi IP per accedere al pannello di controllo, ma l'indirizzo IP di gestione è progettato per offrire un accesso anche se i singoli nodi eseguono il failover.

* Rete di cluster: Un intervallo di indirizzi IP per le comunicazioni del cluster

  La rete di cluster viene usata per le comunicazioni tra i nodi del cluster e per recuperare i file dall'archiviazione back-end (core filer).

  **Procedura consigliata:** Allocare un indirizzo IP per ogni porta fisica usata per le comunicazioni del cluster in ogni nodo Azure FXT Edge Filer. Il cluster assegna automaticamente ai singoli nodi gli indirizzi nell'intervallo specificato.

* Rete lato client: L'intervallo di indirizzi IP usati dai client per richiedere e scrivere file

  Gli indirizzi di rete client vengono usati dai client per accedere ai dati core filer tramite il cluster. Ad esempio, un client NFS potrebbe montare uno di questi indirizzi.

  **Procedura consigliata:** Allocare un indirizzo IP per ogni porta fisica usata per le comunicazioni del client su ogni nodo della serie FXT.

  Il cluster distribuisce gli indirizzi IP lato client tra i relativi nodi costituenti nel modo più uniforme possibile.

  Per semplicità, molti amministratori configurano un singolo nome DNS con una configurazione di DNS round robin (RRDNS) per facilitare la distribuzione delle richieste client nell'intervallo di indirizzi. Questa configurazione consente inoltre a tutti i client di utilizzare lo stesso comando mount per accedere al cluster. Per altre informazioni, vedere [Configurare un DNS](fxt-configure-network.md#configure-dns-for-load-balancing).

Per creare un nuovo cluster è necessario specificare l'indirizzo IP di gestione e un intervallo di indirizzi di rete di cluster. Gli indirizzi lato client vengono specificati dopo la creazione del cluster.

## <a name="connect-to-the-first-node"></a>Connettersi al primo nodo

È possibile connettersi a qualsiasi nodo FXT installato e usare il relativo software del sistema operativo per configurare il cluster.

Se non è stato già fatto, attivare almeno uno dei nodi FXT per il cluster e assicurarsi che disponga di una connessione di rete e di un indirizzo IP. È necessario impostare una nuova password radice per attivare il nodo, quindi seguire i passaggi descritti in [Impostare le password dell'hardware](fxt-node-password.md) se questa operazione non è già stata eseguita.

Per verificare la connessione di rete, assicurarsi che i LED del collegamento di rete del nodo siano illuminati (e, se necessario, gli indicatori sullo switch di rete a cui è collegato). Gli indicatori LED sono descritti nella sezione [Monitorare lo stato dell'hardware di Azure FXT Edge Filer](fxt-monitor.md).

Quando il nodo si avvia, richiederà un indirizzo IP. Se è connesso a un server DHCP, accetta l'indirizzo IP offerto da DHCP. (Questo indirizzo IP è temporaneo. Verrà modificato al momento della creazione del cluster.)

Se non è connesso a un server DHCP o non riceve una risposta, il nodo utilizzerà il software Bonjour per impostare un indirizzo IP autoassegnato nel formato 169.254. \*. \*. Tuttavia, è necessario impostare un indirizzo IP statico temporaneo su una delle schede di rete del nodo prima di usarlo per la creazione di un cluster. Le istruzioni sono incluse in questo documento legacy; contattare il servizio e il supporto tecnico Microsoft per informazioni aggiornate: [Appendice A: Impostazione di un indirizzo IP statico in un nodo FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Trovare l'indirizzo IP

Connettersi al nodo Azure FXT Edge Filer per trovarne l'indirizzo IP. È possibile usare un cavo seriale, una connessione diretta alle porte USB e VGA oppure connettersi tramite un commutatore tastiera, video e mouse. (Per i dettagli sulla connessione alla porta vedere [Impostare le password iniziali](fxt-node-password.md).)

Dopo la connessione, accedere con il nome utente `root` e la password impostata la prima volta al primo avvio del nodo.  

Dopo l'accesso, è necessario determinare l'indirizzo IP del nodo.

Usare il comando `ifconfig` per visualizzare gli indirizzi assegnati a questo sistema.

Ad esempio, il comando `ifconfig | grep -B5 inet` cerca le porte con gli indirizzi Internet e fornisce cinque righe di contesto per visualizzare l'identificatore di porta.

Annotare qualsiasi indirizzo IP mostrato nel report ifconfig. Gli indirizzi in elenco contenenti nomi di porte come e0a o e0b sono opzioni valide. Non usare gli indirizzi IP in elenco con nomi come e7*, in quanto tali nomi vengono usati solo per le porte del servizio iDRAC/IPMI.  

## <a name="load-the-cluster-configuration-wizard"></a>Caricare la configurazione guidata del cluster

Usare lo strumento di configurazione di cluster basato su browser per creare il cluster. 

Immettere l'indirizzo IP per il nodo in un Web browser. Se il browser restituisce un messaggio sulla non attendibilità del sito Web, accedere comunque al sito. (I singoli nodi Azure FXT Edge Filer non dispongono di certificati di sicurezza rilasciati dall'autorità di certificazione).

![Pagina di accesso del pannello di controllo nella finestra del browser](media/fxt-cluster-create/unconfigured-node-gui.png)

Lasciare vuoti i campi **Nome utente** e **Password**. Fare clic su **Accedi** per caricare la pagina di creazione del cluster.

![Schermata di configurazione iniziale per un nodo non configurato nel pannello di controllo di interfaccia utente grafica basato su browser. Mostra le opzioni per aggiornare il software, configurare manualmente un cluster o configurare un cluster da un file di configurazione.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Creare il cluster

Lo strumento di configurazione del cluster guida l'utente attraverso una serie di schermate per creare il cluster Azure FXT Edge Filer. Prima di iniziare, assicurarsi di aver letto il paragrafo sulle [informazioni necessarie](#gather-information-for-the-cluster). 

### <a name="creation-options"></a>Opzioni di creazione

La prima schermata offre tre opzioni. Usare l'opzione di configurazione manuale a meno che non si disponga di istruzioni speciali da parte del personale del supporto.

Fare clic su **I will configure the cluster manually** (Configurerò il cluster in modo manuale) per caricare la nuova schermata delle opzioni di configurazione del cluster. 

Le altre opzioni vengono usate raramente:

* "Update the system image" (Aggiorna l'immagine del sistema) richiede l'installazione di un nuovo software del sistema operativo prima della creazione del cluster. (La versione software attualmente installata è elencata nella parte superiore della schermata). È necessario specificare il file del pacchetto software, ovvero un URL e un nome utente/password, oppure caricare un file dal computer. 

* L'opzione file di configurazione del cluster viene usata talvolta dal Supporto tecnico Microsoft. 

## <a name="cluster-options"></a>Opzioni cluster

Nella schermata successiva viene richiesto di configurare le opzioni per il nuovo cluster.

La pagina è divisa in due sezioni principali, **Configurazione di base** e **Configurazione di rete**. La sezione della configurazione di rete contiene anche due sottosezioni: una per la rete di **Gestione** e una per la rete di **Cluster**.

### <a name="basic-configuration"></a>Configurazione di base

Nella sezione superiore, inserire le informazioni di base per il nuovo cluster.

![Dettaglio della sezione "Configurazione di base" nella pagina GUI del browser. Mostra tre campi (nome cluster, password amministratore, conferma password)](media/fxt-cluster-create/basic-configuration.png) 

* **Nome cluster**: immettere un nome univoco per il cluster.

  Il nome del cluster deve soddisfare questi criteri:
  
  * Lunghezza da 1 a 16 caratteri
  * Può includere lettere, numeri, trattini (-) e caratteri di sottolineatura (_) 
  * Non deve includere altri segni di punteggiatura o caratteri speciali
  
  È possibile modificare questo nome in un secondo momento nella pagina di configurazione **Cluster** > **Setup generale**. (Per altre informazioni sulle impostazioni del cluster vedere la [Guida alla configurazione del cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), non presente in questa documentazione.)

  > [!NOTE] 
  > Il nome del cluster viene usato per identificare le informazioni di sistema caricate per supportare il monitoraggio o la risoluzione dei problemi, pertanto è utile includere il nome della società.

* **Password amministratore**: impostare la password per l'utente amministratore predefinito, `admin`.
  
  È necessario impostare singoli account utente per ogni persona che gestisce il cluster, ma non è possibile rimuovere l'utente `admin`. Se è necessario creare utenti aggiuntivi, accedere come `admin`.
 
  È possibile modificare la password per `admin` nella pagina delle impostazioni **	Amministrazione** > **Utenti** del pannello di controllo del cluster. Per informazioni dettagliate, vedere la documentazione relativa agli **Utenti** nella [Guida alla configurazione del cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Configurazione di rete

La sezione **Networking** richiede di specificare l'infrastruttura di rete usata dal cluster. 

Esistono due reti separate da configurare:

* La *rete di gestione* offre l'accesso di amministratore al cluster per la configurazione e il monitoraggio. L'indirizzo IP specificato qui viene usato durante la connessione al pannello di controllo o per l'accesso SSH. 

  La maggior parte dei cluster usa solo un indirizzo IP di gestione singolo, ma se si vuole aggiungere interfacce è possibile farlo dopo la creazione del cluster.

* La *rete di cluster* viene usata per le comunicazioni tra i nodi del cluster e tra i nodi del cluster e l'archiviazione back-end (core filer).

La rete lato client viene configurata in un secondo momento, dopo la creazione del cluster.

Questa sezione include anche la configurazione per i server DNS e NTP utilizzati da entrambe le reti.

### <a name="configure-the-management-network"></a>Configurare la rete di gestione

Le impostazioni nella sezione **Gestione** sono relative alla rete che offre l'accesso di amministratore al cluster.

![dettaglio della sezione "Gestione", con campi per le 5 opzioni e una casella di controllo per la rete di gestione da 1 Gb](media/fxt-cluster-create/management-network-filled.png)

* **IP di gestione**: specificare l'indirizzo IP che verrà utilizzato per accedere al pannello di controllo del cluster. Questo indirizzo verrà richiesto dal nodo primario del cluster, ma si sposterà automaticamente in un nodo integro se il nodo primario originale diventa non disponibile.

  La maggior parte dei cluster utilizza un solo indirizzo IP di gestione. È possibile aggiungerne più di uno dalla pagina delle impostazioni **Cluster** > **Administrative Network** (Rete amministrativa) dopo aver creato il cluster. Altre informazioni nella [Guida alla configurazione del cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Netmask**: specificare la netmask per la rete di gestione.

* **Router**: immettere l'indirizzo gateway predefinito usato dalla rete di gestione.

* **Tag VLAN (facoltativo)** : se il cluster usa i tag VLAN, specificare il tag per la rete di gestione.

  Le impostazioni VLAN aggiuntive vengono configurate nella pagina delle impostazioni **Cluster** > **VLAN**. Per altre informazioni consultare [Uso di VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) e [Cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) nella guida alla configurazione del cluster.

* **MTU**: se necessario, regolare l'unità massima di trasmissione (MTU) per la rete di gestione del cluster.

* **Usare la rete MGMT da 1 G**: selezionare questa casella se si vuole assegnare le due porte di rete da 1 GbE sui nodi FXT solo alla rete di gestione. (È necessario disporre di porte 25 GbE/10 GbE disponibili per tutto il traffico rimanente). Se non si seleziona questa casella, la rete di gestione usa la porta con la velocità massima disponibile. 

### <a name="configure-the-cluster-network"></a>Configurare la rete di cluster 

Le impostazioni di rete del cluster si applicano al traffico tra i nodi del cluster e tra i nodi del cluster e i core filer.

![dettaglio della sezione "Cluster", con i campi per immettere i sei valori](media/fxt-cluster-create/cluster-network-filled.png)

* **Primo IP** e **Ultimo IP**: immettere gli indirizzi IP che definiscono l'intervallo da utilizzare per le comunicazioni interne del cluster. Gli indirizzi IP usati in questo caso devono essere contigui e non assegnati da DHCP.

  Dopo aver creato il cluster, è possibile aggiungere altri indirizzi IP. Usare la pagina delle impostazioni di **Cluster** > **Reti cluster** ([documentazione della Guida alla configurazione del cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  Il valore in **Number of IPs in Range** (Numero di IP nell'intervallo) viene calcolato e mostrato automaticamente.

* **Non-mgmt netmask (optional)** (Netmask non MGMT (facoltativo)): specificare la netmask per la rete di cluster. 

  Il sistema suggerisce automaticamente il valore netmask immesso per la rete di gestione; modificarlo se necessario.

* **Cluster router (optional)** (Router del cluster (facoltativo)): specificare l'indirizzo gateway predefinito usato dalla rete di cluster. 

  Il sistema suggerisce automaticamente lo stesso indirizzo gateway fornito per la rete di gestione.

* **Cluster VLAN tag (optional)** (Tag VLAN del cluster (facoltativo)): se il cluster usa i tag VLAN, specificare il tag per la rete di cluster.

* **Non-mgmt MTU (optional)** (MTU non MGMT (facoltativo)): se necessario, regolare l'unità massima di trasmissione (MTU) per la rete di cluster.

### <a name="configure-cluster-dns-and-ntp"></a>Configurare DNS e NTP del cluster 

Nella sezione **Cluster** sono presenti campi per specificare i server DNS e NTP e per abilitare Link Aggregation. Queste impostazioni si applicano a tutte le reti usate dal cluster.

![Dettagli della sezione per la configurazione di DNS/NTP, con tre campi per i server DNS, campi per il dominio DNS e per la ricerca DNS, tre campi per i server NTP e un menu a discesa per le opzioni di Link Aggregation](media/fxt-cluster-create/dns-ntp-filled.png)

* **Server DNS**: immettere l'indirizzo IP di uno o più server Domain Name System (DNS).

  Il DNS è consigliato per tutti i cluster ed è necessario se si vuole usare SMB, AD o Kerberos. 
  
  Per ottenere prestazioni ottimali, configurare il server DNS del cluster per il bilanciamento del carico round robin come descritto in [Configurare il DNS per il cluster Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing).

* **Dominio DNS**: immettere il nome di dominio di rete che verrà usato dal cluster.

* **Ricerca DNS**: facoltativamente, immettere i nomi di dominio aggiuntivi per cui il sistema deve eseguire la ricerca per risolvere le query DNS. È possibile aggiungere fino a sei i nomi di dominio, separati da spazi.

* **Server NTP**: specificare uno o tre rete server Network Time Protocol (NTP) negli appositi campi. È possibile usare i nomi host o gli indirizzi IP.

* **Link aggregation**: consente di personalizzare la gestione di vari tipi di traffico da parte delle porte ethernet nei nodi FXT del cluster. Per altre informazioni, leggere [Link Aggregation](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) nella Guida alla configurazione del cluster.

### <a name="click-the-create-button"></a>Fare clic sul pulsante Crea

Dopo aver fornito tutte le impostazioni necessarie nel modulo, fare clic sul pulsante **Crea cluster**.

![parte inferiore del modulo compilato con il cursore posizionato sul pulsante Crea in basso a destra](media/fxt-cluster-create/create-cluster.png)

Il sistema visualizza un messaggio durante la creazione del cluster.

![messaggio dello stato di configurazione del cluster nel browser: "The FXT node is now creating the cluster. (Creazione del cluster da parte del nodo FXT in corso.) Questa operazione può richiedere alcuni minuti. When the cluster is created, visit this link to complete the configuration." (Quando la creazione del cluster è terminata, visitare questo collegamento per completare la configurazione".) con collegamento ipertestuale su "visitare questo collegamento"](media/fxt-cluster-create/creating-message.png)

Dopo qualche istante, è possibile fare clic sul collegamento contenuto nel messaggio per passare al pannello di controllo del cluster. (Questo collegamento consente di visualizzare l'indirizzo IP specificato in **IP di gestione**.) Dopo aver fatto clic sul pulsante Crea, l'attivazione del collegamento richiede da 15 secondi a un minuto. Se l'interfaccia Web non si carica, attendere alcuni secondi e quindi fare di nuovo clic sul collegamento. 

La creazione del cluster richiede un minuto o più, ma è possibile accedere al pannello di controllo durante il processo. È normale che la pagina dashboard del pannello di controllo mostri degli avvisi fino al termine del processo di creazione del cluster. 

## <a name="open-the-settings-pages"></a>Aprire la pagina Impostazioni 

Dopo aver creato il cluster, è necessario personalizzare la configurazione per la rete e il flusso di lavoro. 

Usare l'interfaccia Web del pannello di controllo per configurare il nuovo cluster. Fare clic sul collegamento dalla schermata di stato relativa alla creazione del cluster o passare all'indirizzo IP di gestione impostato nel cluster.

Accedere all'interfaccia Web usando il nome utente `admin` e la password impostata durante la creazione del cluster.

![Web browser con i campi di accesso del pannello di controllo](media/fxt-cluster-create/admin-login.png)

Il pannello di controllo si apre e mostra la pagina **Dashboard**. Al termine della creazione del cluster, tutti i messaggi di avviso devono essere cancellati dal display.

Fare clic sulla scheda **Impostazioni** per configurare il cluster.

Nella scheda **Impostazioni** la barra laterale sinistra mostra un menu di pagine di configurazione. Le pagine sono organizzate per categoria. Fare clic sui controlli + o - nella parte superiore del nome della categoria per espandere o nascondere le singole pagine.

![Scheda Impostazioni del pannello di controllo (nel browser) che visualizza la pagina Cluster > Setup generale](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Procedura di configurazione del cluster

A questo punto del processo, il cluster esiste ma ha un solo nodo e non dispone né di indirizzi IP lato client né di una archiviazione back-end. Sono necessari passaggi di configurazione aggiuntivi per passare da un cluster appena creato a un sistema di cache pronto per gestire il flusso di lavoro.

### <a name="required-configuration"></a>Configurazione necessaria

Questi passaggi sono necessari per la maggior parte o per tutti i cluster. 

* Aggiungere i nodi al cluster 

  Lo standard è di tre nodi ma molti cluster di produzione ne hanno di più, fino a un massimo di 24 nodi.

  Consultare [Aggiungere nodi del cluster](fxt-add-nodes.md) per informazioni su come aggiungere altre unità Azure FXT Edge Filer nel cluster e abilitare la disponibilità elevata.

* Specificare l'archiviazione back-end

  Aggiungere le definizioni *core filer* per ogni sistema di archiviazione back-end che verrà utilizzato dal cluster. Per altre informazioni consultare [Aggiungere archiviazione back-end e configurare lo spazio dei nomi virtuale](fxt-add-storage.md#about-back-end-storage).

* Configurare l'accesso client e lo spazio dei nomi virtuale 

  Creare almeno un server virtuale (vserver) e assegnargli un intervallo di indirizzi IP per i computer client da usare. È anche necessario configurare lo spazio dei nomi del cluster (talvolta denominato Global Namespace o GNS), una funzionalità del file system virtuale che consente di eseguire il mapping delle esportazioni di archiviazione back-end in percorsi virtuali. Lo spazio dei nomi del cluster offre ai client una struttura del file system coerente e accessibile anche se si passa a un supporto di archiviazione back-end. Lo spazio dei nomi può offrire anche una gerarchia di archiviazione virtuale intuitiva per i contenitori BLOB di Azure o altra archiviazione di oggetti cloud supportata.

  Per informazioni dettagliate, consultare [Configurare lo spazio dei nomi](fxt-add-storage.md#configure-the-namespace). Questo passaggio include:
  * Creazione di vservers
  * Configurazione delle giunzioni tra la visualizzazione di rete client e l'archiviazione back-end 
  * Definizione degli indirizzi IP gestiti da ogni server virtuale

  > [!Note] 
  > Prima di iniziare a configurare il GNS del cluster è consigliabile una pianificazione significativa. Per assistenza consultare le sezioni [Utilizzo di Global Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) e [Creazione e utilizzo di VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) nella guida alla configurazione del cluster.

* [Regolare le impostazioni di rete](fxt-configure-network.md)

  Sono disponibili diverse impostazioni correlate alla rete da verificare o personalizzare per un nuovo cluster. Consultare [Regolare le impostazioni di rete](fxt-configure-network.md) per informazioni dettagliate su questi elementi:

  * Verifica della configurazione di DNS e NTP 
  * Configurazione dei servizi directory, se necessario 
  * Configurazione delle VLAN
  * Configurazione dei server proxy
  * Aggiunta di indirizzi IP alla rete di cluster
  * Archiviazione di certificati di crittografia

* [Configurare il supporto di monitoraggio](#enable-support)

  È necessario accettare l'informativa sulla privacy per lo strumento di configurazione e configurare contemporaneamente le impostazioni di caricamento del supporto.

  Il cluster può caricare automaticamente i dati per la risoluzione dei problemi relativi al cluster, comprese le statistiche e i file di debug. Questi caricamenti consentono al Supporto tecnico Microsoft di offrire un servizio ottimale. È possibile personalizzare ciò che viene monitorato e, facoltativamente, abilitare il supporto proattivo e il servizio di risoluzione dei problemi remoto.  

### <a name="optional-configuration"></a>Configurazione facoltativa

Questi passaggi non sono necessari per tutti i cluster, ma soltanto per alcuni tipi di flussi di lavoro o per determinati stili di gestione del cluster. 

* Personalizzare le impostazioni dei nodi

  È possibile impostare i nomi dei nodi e configurare le porte IPMI del nodo singolarmente o a livello globale del cluster. Se si configurano queste impostazioni prima di aggiungere nodi al cluster, i nuovi nodi possono rilevare le impostazioni automaticamente quando vengono uniti. Le opzioni sono descritte nel documento legacy di creazione del cluster [Personalizzazione delle impostazioni del nodo](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Alcuni documenti relativi a questo prodotto non sono ancora disponibili nel sito della documentazione di Microsoft Azure. I collegamenti alla [Guida alla configurazione del cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) e alla versione precedente della [Guida alla creazione del cluster](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) verranno visualizzati in un sito Web separato, ospitato da GitHub. 

* Configurare SMB

  Se si vuole consentire l'accesso SMB al cluster e al NFS, è necessario configurare SMB e attivarlo. SMB (talvolta denominato CIFS) viene in genere usato per supportare i client Microsoft Windows.

  La pianificazione e la configurazione di SMB richiede più di un semplice clic su alcuni pulsanti del pannello di controllo. A seconda dei requisiti del sistema, SMB può influire sulla modalità di definizione dei core filer, il numero di vservers creati, la modalità di configurazione delle giunzioni e dello spazio dei nomi, le autorizzazioni di accesso e altre impostazioni.

  Per altre informazioni, leggere la sezione [Configurazione di un accesso SMB](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) della Guida alla configurazione del cluster.

* Installare licenze aggiuntive

  Se si vuole usare un'archiviazione nel cloud diversa da BLOB di Azure, è necessario installare una licenza per funzionalità aggiuntive. Contattare il rappresentante Microsoft per informazioni dettagliate sull'acquisto di una licenza FlashCloud<sup>TM</sup>. I dettagli sono illustrati in [Aggiungere archiviazione back-end e configurare lo spazio dei nomi virtuale](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Abilitare il supporto

Il cluster Azure FXT Edge Filer può caricare automaticamente i dati di supporto sul cluster. Questi caricamenti consentono al personale di fornire un servizio clienti ottimale.

Attenersi alla procedura seguente per configurare il supporto dei caricamenti.

1. Passare alla pagina di impostazioni **Cluster** > **Supporto**. Accettare l'informativa sulla privacy. 

   ![Screenshot che mostra il pannello di controllo e la finestra popup con il pulsante di conferma per accettare l'informativa sulla privacy](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Fare clic sul triangolo a sinistra di **Customer Info** (Info cliente) per espandere la sezione.
1. Fare clic sul pulsante **Revalidate upload information** (Riconvalida informazioni caricamento).
1. Impostare il nome del cluster per il supporto in **Unique Cluster Name** (Nome univoco cluster), assicurandosi che identifichi il cluster in modo univoco per il personale di supporto.
1. Selezionare le caselle relative a **Statistics Monitoring** (Monitoraggio statistiche), **General Information Upload** (Caricamento informazioni generali) e **Crash Information Upload** (Caricamento informazioni arresto anomalo).
1. Fare clic su **Submit**.  

   ![Screenshot con la sezione delle informazioni del cliente completata nella pagina delle impostazioni di supporto](media/fxt-cluster-create/fxt-support-info.png)

1. Fare clic sul triangolo a sinistra di **Secure Proactive Support (SPS)** (Supporto proattivo sicuro (SPS)) per espandere la sezione.
1. Selezionare la casella **Enable SPS Link** (Abilita collegamento SPS).
1. Fare clic su **Submit**.

   ![Schermata contenente la sezione del supporto proattivo sicuro completata nella pagina delle impostazioni di supporto](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il cluster di base e accettato l'informativa sulla privacy, aggiungere i nodi del cluster rimanenti. 

> [!div class="nextstepaction"]
> [Aggiungere nodi del cluster](fxt-add-nodes.md)

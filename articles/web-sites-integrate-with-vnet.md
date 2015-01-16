<properties title="Integrate your Azure Website with an Azure Virtual Network" pageTitle="Integrare il sito Web di Azure con la rete virtuale di Azure" description="Illustra come connettere un sito Web di Azure a una rete virtuale di Azure nuova o esistente" metaKeywords="" services="web-sites,virtual-network" solutions="web,integration,infrastructure" documentationCenter="" authors="cephalin" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="cephalin" />

# Integrazione del sito Web di Azure con una Rete Virtuale di Azure #
Questo documento descrive la funzionalità di anteprima dell'integrazione con una rete virtuale, specificando come configurarla con il proprio sito Web di Azure.  Le Reti Virtuali di Azure consentono di compilare soluzioni ibride con le proprie risorse su Azure e in sede.  

Un'integrazione del genere consente al proprio sito Web di accedere alle risorse presenti nella propria rete virtuale. Al contrario, l'accesso al sito Web dalla rete virtuale non viene concesso.  Uno scenario standard è rappresentato dall'esigenza di accedere dal proprio sito Web a un database o a servizi Web in esecuzione su macchine virtuali in una VNET o in un data center.  La funzionalità non consente di montare un'unità e,  attualmente, non supporta l'attivazione dell'integrazione con i sistemi di autenticazione della propria VNET.  La funzionalità è in stato di anteprima e continuerà a essere migliorata fino a raggiungere lo stato di GA.

Per altre informazioni sulle Reti Virtuali di Azure, consultare le Informazioni generali sulla rete virtuale, in cui vengono illustrati alcuni casi di utilizzo e i vantaggi di una Rete Virtuale di Azure.

## Per iniziare ##
Prima di procedere con la connessione del proprio sito a una rete virtuale, è opportuno prendere in considerazione i seguenti aspetti.

1.	I siti possono connettersi a una rete virtuale solo se sono in esecuzione su un piano di hosting Web al livello di prezzo "Standard".  I siti con livello di prezzo gratuito, condiviso e base non possono connettersi a una rete virtuale.
2.	Se la rete virtuale di destinazione è già presente, sulla stessa deve essere attivata la funzionalità da punto a sito con gateway di routing dinamico per poter eseguire la connessione a un sito Web.  Non è possibile attivare la VPN da punto a sito se il gateway è configurato con il routing statico.
3.	È possibile disporre di un massimo di 5 reti configurate nel proprio piano di hosting Web.  Un sito Web può connettersi a una rete alla volta.  Le 5 reti possono essere usate da un numero qualsiasi di siti Web dello stesso piano di hosting Web.  

È possibile connettersi a una rete virtuale nuova o a una esistente.  In caso di creazione di una nuova rete, un gateway viene preconfigurato per l'utente.  La creazione e la configurazione di una nuova rete virtuale richiede diversi minuti.  

Se il proprio sito Web non è di livello standard, l'interfaccia lo comunica all'utente e consente di accedere ai livelli di prezzo facendo clic sul pulsante corrispondente.

![](./media/web-sites-integrate-with-vnet/upgrade-to-standard.png) 

## Funzionamento del sistema ##
Questa funzionalità usa la tecnologia VPN da punto a sito per connettere il proprio Web di Azure alla propria VNET.  L'architettura di sistema dei siti Web di Azure è di tipo multi-tenant, aspetto che impedisce il provisioning diretto dei siti Web in una VNET, come succede con le macchine virtuali.  Grazie al ricorso alla tecnologia da punto a sito, è possibile consentire l'accesso di rete solo alla macchina virtuale che ospita il sito Web.  L'accesso alla rete è ulteriormente limitato su questi host Web, in modo tale da consentire ai propri siti Web di accedere solo alle reti appositamente configurate a tal fine.  

Permettendo solo a determinati siti Web di connettersi alle proprie reti si evita la creazione di connessioni SMB.  Sebbene sia possibile accedere a risorse remote, non è possibile montare un'unità in remota.

![](./media/web-sites-integrate-with-vnet/how-it-works.png)
 
Se non è stato configurato un server DNS con la propria rete virtuale, è necessario usare gli indirizzi IP.  Assicurarsi di esporre le porte per gli endpoint desiderati attraverso il proprio firewall.  Per testare la connessione, l'unico metodo attualmente disponibile è il ricorso a un sito Web o a un WebJob che effettua una chiamata all'endpoint desiderato.  Strumenti come ping o nslookup al momento non funzionano con la console Kudu.  Presto verranno apportati miglioramenti in tale ambito.  

## Connessione a una rete esistente ##
Per connettere un sito a una rete virtuale, accedere al blade del proprio sito Web, fare clic sul riquadro della rete virtuale nella sezione delle reti e selezionare una delle reti esistenti.

![](./media/web-sites-integrate-with-vnet/connect-to-existing-vnet.png)
 
Se si tratta del primo sito Web della propria sottoscrizione che effettua la connessione alla rete, il sistema crea un certificato per l'autenticazione della VNET.  Per visualizzare il certificato, accedere al portale corrente, passare alla sezione delle reti virtuali, selezionare la rete desiderata e, in seguito, la scheda Certificates.  

Nell'immagine in alto è presente una rete chiamata cantConnectVnet visualizzata in grigio che non può essere selezionata.  I motivi possibili sono due:  la funzionalità VPN non è attivata sulla propria rete o non è stato effettuato il provisioning di un gateway di routing dinamico nella propria VNET.  Se entrambi i criteri sono soddisfatti, è possibile selezionare la VNET per l'integrazione con il proprio sito Web.

## Creazione e connessione a una nuova VNET ##
Oltre a effettuare la connessione a una VNET esistente, è possibile crearne una nuova dall'interfaccia utente del sito Web e connettersi automaticamente a tale VNET.  Per farlo, seguire lo stesso percorso per raggiungere l'interfaccia della rete virtuale e selezionare Crea nuova rete virtuale.  L'interfaccia utente che si apre consente di assegnare un nome alla rete, specificare lo spazio di indirizzi e impostare gli indirizzi per i server DNS usati dalla rete virtuale.

![](./media/web-sites-integrate-with-vnet/create-new-vnet.png)
 
La creazione di una nuova rete virtuale con i gateway configurati può richiedere fino a 30 minuti.  Durante questo intervallo di tempo, viene visualizzato il seguente messaggio che comunica l'operazione in corso.

![](./media/web-sites-integrate-with-vnet/new-vnet-progress.png)

Una volta collegata la rete al sito Web, quest'ultimo può accedere alle risorse della VNET su TCP o UDP.  Se si desidera accedere alle risorse disponibili tramite VPN da sito a sito nella propria VNET dal sistema in sede, è necessario aggiungere le route alla propria rete aziendale per consentire al traffico di passare dalla rete agli indirizzi da punto a sito configurati nella VNET.

Una volta completata correttamente l'integrazione, nel portale è possibile visualizzare i dati principali sulla connessione, disconnettere il sito Web dalla rete e sincronizzare i certificati usati per autenticare la connessione.  Se un certificato è scaduto o è stato revocato, potrebbe essere necessario procedere con la sincronizzazione.  

![](./media/web-sites-integrate-with-vnet/vnet-status-portal.png)

Gestione della connessione alla rete virtuale
Per visualizzare un elenco di tutte le reti virtuali associate ai siti di un piano di hosting Web, visitare il pannello di tale piano.  È possibile disporre di un massimo di 5 reti associate a un piano di hosting Web standard.

Se il piano di hosting Web passa a un livello inferiore (gratuito, condiviso o base), le connessioni alla rete virtuale usate dai siti Web del piano vengono disattivate.  Se, in seguito, il piano torna al livello standard, le connessioni alla rete vengono ripristinate.

Al momento Azure non consente di prelevare una macchina virtuale esistente e trasferirla in una rete virtuale.  Il provisioning della macchina virtuale deve essere effettuato in tale rete virtuale durante la creazione.  

## Accesso alle risorse in sede ##
Durante l'uso di una VNET configurata con una VPN da sito a sito, è necessario effettuare un'altra operazione per accedere alle proprie risorse in sede dal proprio sito Web di Azure.  Le route devono essere aggiunte alla propria rete in sede per consentire al traffico di passare dalla propria rete agli indirizzi da punto a sito configurati nella propria VNET.  Per visualizzare l'intervallo IP per la propria connettività da punto a sito, accedere all'area di rete del portale corrente come mostrato nella seguente immagine.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise.png)

## Certificati ##
Per stabilire una connessione protetta con la propria VNET, è necessario procedere con uno scambio di certificati.  Nella seguente immagine viene visualizzata l'identificazione personale del certificato pubblico generata dai siti Web di Azure per il portale di rete corrente.  

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise-certificate.png)

Se i certificati risultano non sincronizzati per qualunque motivo, ad esempio l'eliminazione involontaria dal portale di rete, la connettività si interrompe.  L'opzione Sync Connection nell'interfaccia della rete virtuale consente di ristabilire la connettività.

Questa opzione deve essere usata anche se si aggiunge un DNS alla propria rete virtuale o una VPN da sito a sito alla propria rete.  

![](./media/web-sites-integrate-with-vnet/vnet-sync-connection.png)

## Confronto con le connessioni ibride ##
I siti Web di Azure offrono una funzionalità chiamata Connessioni ibride che presenta alcuni punti in comune con l'integrazione con la rete virtuale.  Sebbene vi sia una parziale sovrapposizione, nessuna delle due funzionalità può sostituire l'altra.  Le connessioni ibride consentono di stabilire collegamenti a più endpoint di applicazioni in varie reti.  La funzionalità della rete virtuale consente di connettere il proprio sito Web a una rete che, a sua volta, può essere collegata alla propria rete in sede.  Questa configurazione risulta ottimale se le proprie risorse si trovano tutte all'interno di tale rete.  

Un'altra differenza è rappresentata dalla necessità di installare un agente di inoltro per consentire alle connessioni ibride di funzionare.  L'agente deve essere eseguito su un'istanza di Windows Server.  La funzionalità della rete virtuale, al contrario, non richiede alcuna installazione e abilita l'accesso alla risorse remote, indipendentemente dai sistemi operativi host.  

Inoltre, sono attualmente presenti differenze a livello di prezzo tra le due funzionalità.  In effetti, ai livelli più economici, la funzionalità delle connessioni ibride è molto utile per gli scenari di sviluppo e collaudo e fornisce l'accesso a un numero limitato di endpoint.  La funzionalità della rete virtuale fornisce invece un accesso a tutti i contenuti presenti nella VNET o collegati alla stessa.  

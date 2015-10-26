<properties
	pageTitle="Panoramica di Site Recovery" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali e dei server fisici ubicati nei server locali in Azure o in un sito locale secondario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="08/05/2015" 
	ms.author="raynew"/>

#  Panoramica di Site Recovery

Il servizio Site Recovery fornisce un'efficace soluzione di ripristino di emergenza e continuità aziendale che consente di proteggere le macchine virtuali e i server fisici locali orchestrando e automatizzando la replica e il failover in Azure o in un centro dati locale secondario.

- **Semplificare**. Site Recovery consente di semplificare la strategia di continuità aziendale e ripristino di emergenza facilitando la configurazione della replica, l’esecuzione del failover e il ripristino per i carichi di lavoro e le applicazioni locali.
- **Replica**: è possibile replicare i carichi di lavoro locali nell'archiviazione di Azure o in un centro dati secondario. 
- **Insieme di credenziali**: per gestire la replica è necessario configurare un insieme di credenziali di Site Recovery nell'area Azure selezionata. Tutti i metadati restano all'interno di tale area.
- **Metadati**: i dati delle applicazioni non vengono inviati ad Azure. Site Recovery richiede solo metadati, ad esempio nomi di macchina virtuale e VMM, per orchestrare replica e failover. 
- **Connessione ad Azure**: i server di gestione comunicano con Azure in base allo scenario di distribuzione. Ad esempio, se si esegue la replica delle macchine virtuali ubicate in un cloud VMM locale, il server VMM comunica con Site Recovery tramite una connessione HTTPS in uscita crittografata. Non è necessaria alcuna connessione dalle macchine virtuali o dagli host Hyper-V.
- **Replica Hyper-V**: Azure Site Recovery utilizza Replica Hyper-V per il processo di replica e può inoltre utilizzare la replica SAN se si esegue la replica tra due siti VMM locali. Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l’intero disco rigido virtuale. Per la replica continua, vengono replicate solo le modifiche differenziali. Site Recovery supporta il trasferimento di dati offline e funziona con utilità di ottimizzazione WAN.
- **Prezzi**: è possibile ottenere [maggiori informazioni](http://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.


## Scenari di distribuzione

In questa tabella sono riepilogati gli scenari di replica supportati da Site Recovery.

**Replicare in** | **Replicare da (locale)** | **Dettagli** | **Articolo**
---|---|---|---
Azzurro | Sito di Hyper-V | Replicare la macchina virtuale in uno o più server host Hyper-V locali definiti come un sito di Hyper-V in Azure. Non è necessario alcun server VMM | [Ulteriori informazioni](site-recovery-hyper-v-site-to-azure.md)
Azzurro| Server VMM | Replicare le macchine virtuali in uno o più server host Hyper-V locali in un cloud VMM in Azure. | [Ulteriori informazioni](site-recovery-vmm-to-azure.md) 
Azzurro | Server Windows fisico | Replicare un server fisico Windows o Linux in Azure | [Ulteriori informazioni](site-recovery-vmware-to-azure.md)
Azzurro | Macchina virtuale VMware | Replicare le macchine virtuali VMWare locali in Azure | [Ulteriori informazioni](site-recovery-vmware-to-azure.md)
Centro dati secondario | Server VMM | Replicare le macchine virtuali in uno o più server host Hyper-V locali in un cloud VMM in un server VMM secondario in un altro centro dati. | [Ulteriori informazioni](site-recovery-vmm-to-vmm.md)
Centro dati secondario | Server VMM con SAN | Replicare le macchine virtuali in uno o più server host Hyper-V locali ubicati in un cloud VMM in un server VMM secondario in un altro centro dati tramite la replica SAN| [Ulteriori informazioni](site-recovery-vmm-san.md)
Centro dati secondario | Singolo server VMM | Replicare le macchine virtuali nei server host Hyper-V locali in un cloud VMM in un cloud secondario nello stesso server VMM | [Ulteriori informazioni](site-recovery-single-vmm.md) 


## Indicazioni relative al carico di lavoro

Fare riferimento a [questo documento](site-recovery-workload.md) per istruzioni sull'utilizzo di Azure Site Recovery per diversi carichi di lavoro.

## Funzionalità e requisiti 

In questa tabella vengono riepilogate le principali funzionalità di Site Recovery e viene indicato come sono gestite durante la replica in Azure, la replica a un sito secondario tramite la replica Hyper-V predefinita e tramite una rete SAN.

Funzionalità|Replicare in Azure|Replicare in un sito secondario (Replica Hyper-V)|Replicare in un sito secondario (SAN)
---|---|---|---
Replica dei dati|I metadati sui server e le macchine virtuali locali vengono archiviati nell'insieme di credenziali di Site Recovery.</p> <p>I dati replicati vengono archiviati nell'archiviazione di Azure.|I metadati sui server e le macchine virtuali locali vengono archiviati nell'insieme di credenziali di Site Recovery.</p> <p>I dati replicati vengono archiviati nel percorso specificato dal server Hyper-V di destinazione.|I metadati sui server e le macchine virtuali locali vengono archiviati nell'insieme di credenziali di Site Recovery.</p> <p>I dati replicati vengono archiviati nell'array di archiviazione di destinazione.
Requisiti per l'insieme di credenziali|Account Azure con servizio Azure Site Recovery|Account Azure con servizio Azure Site Recovery|Account Azure con servizio Azure Site Recovery
Replica|Replicare la macchina virtuale dall'host di Hyper-V di origine nell'archiviazione di Azure. Failback alla posizione di origine.|Replicare la macchina virtuale dall'host di Hyper-V di origine all’host Hyper-V di destinazione. Failback alla posizione di origine.|Replicare le macchine virtuali dal dispositivo di archiviazione SAN di origine al dispositivo SAN di destinazione. Failback alla posizione di origine.
Macchine virtuali|Disco rigido della macchina virtuale archiviato nell'archiviazione di Azure|Disco rigido della macchina virtuale archiviato nell'host Hyper-V.|Disco rigido della macchina virtuale archiviato nell'array di archiviazione SAN.
Archiviazione di Azure|Necessaria per archiviare i dischi rigidi delle macchine virtuali replicate|Non applicabile|Non applicabile
Array di archiviazione SAN|Non applicabile|Non applicabile|L'array di archiviazione SAN deve essere disponibile sia nel sito di origine che in quello di destinazione e deve essere gestito da VMM.
Server VMM|Server VMM solo nel sito di origine.|Consigliato server VMM nel sito di origine e in quello di destinazione. È possibile replicare tra cloud in un singolo server VMM.|Server VMM nei siti VMM di origine e di destinazione I cloud devono contenere almeno un cluster Hyper-V.
Versione VMM|System Center 2012 R2<p>System Center 2012 SP1|System Center 2012 R2|System Center 2012 R2 con aggiornamento cumulativo 5.0 per VMM
Configurazione di VMM|Impostare i cloud nei siti di origine e destinazione</p><p>Impostare reti VM nei siti di origine e di destinazione<p>Impostare le classificazioni di archiviazione in siti di origine e destinazione<p>Installare il Provider di origine e i server VMM di destinazione|Impostare i cloud nel sito di origine</p><p>Impostare l'archiviazione SAN</p><p>Impostare reti VM nel sito di origine</p><p>Installare il Provider nel server VMM di origine</p><p>Abilitare la protezione della macchina virtuale|Impostare i cloud nei siti di origine e destinazione</p><p>Impostare reti VM in siti di origine e destinazione</p><p>Installare il Provider nel server VMM di origine e destinazione</p><p>Abilitare la protezione della macchina virtuale
Provider di Azure Site Recovery</p><p>Utilizzato per connettersi tramite HTTPS a Site Recovery|Viene installato nel server VMM di origine|Viene installato nei server VMM di origine e di destinazione|Viene installato nei server VMM di origine e di destinazione
Agente di servizi di ripristino Azure</p><p>Utilizzato per connettersi tramite HTTPS a Site Recovery|Installare nei server host Hyper-V|Facoltativo|Facoltativo
Punti di ripristino delle macchine virtuali|I punti di ripristino vengono impostati in base al tempo.</p> <p>Specifica per quanto tempo mantenere un punto di ripristino (0-24 ore).|I punti di ripristino vengono impostati in base alla quantità.</p> <p>Specifica il numero di punti di ripristino aggiuntivi da mantenere (0-15). Per impostazione predefinita, viene creato un punto di ripristino ogni ora|Configurato nelle impostazioni di archiviazione degli array
Mapping di rete|Esegue il mapping delle reti VM alle reti di Azure.</p> <p>Il mapping garantisce che tutte le macchine virtuali di cui viene eseguito il failover nella stessa rete VM di origine possano connettersi dopo il failover. Inoltre, se è configurato un gateway di rete nella rete di Azure di destinazione, le macchine virtuali possono connettersi alle macchine virtuali locali. </p><p>Se il mapping è abilitato, solo le macchine virtuali di cui viene eseguito il failover nello stesso piano di ripristino possono connettersi tra loro dopo il failover in Azure.|Esegue il mapping delle reti VM di origine alle reti VM di destinazione.</p> <p>Il mapping viene usato per inserire macchine virtuali replicate in server host Hyper-V ottimali e garantisce che le macchine virtuali associate alla rete VM di origine siano associate alla rete di destinazione mappata dopo il failover. </p><p>Se il mapping non è abilitato, le macchine virtuali replicate non vengono connesse ad alcuna rete.|Esegue il mapping delle reti VM di origine alle reti VM di destinazione.</p> <p>Il mapping di rete garantisce che le macchine virtuali associate alla rete VM di origine siano associate alla rete di destinazione mappata dopo il failover. </p><p>Se il mapping non è abilitato, le macchine virtuali replicate non vengono connesse ad alcuna rete.
Mapping dell'archiviazione|Non applicabile|Esegue il mapping delle classificazioni di archiviazione nei server VMM di origine alle classificazioni di archiviazione nei server VMM di destinazione.</p> <p>Con il mapping attivo i dischi rigidi della macchina virtuale nella classificazione di archiviazione di origine saranno situati nella classificazione di archiviazione di destinazione dopo il failover.</p><p>Se non è attivato il mapping di archiviazione i dischi rigidi virtuali replicati verranno archiviati nel percorso predefinito nel server host Hyper-V di destinazione.|Mappa tra gli array e i pool di archiviazione nei siti primari e secondari.

## Passaggi successivi

Una volta completata questa panoramica [leggere le procedure consigliate](site-recovery-best-practices.md) che consentono di iniziare la pianificazione della distribuzione.
 

<!---HONumber=Oct15_HO3-->
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
	ms.date="05/10/2015" 
	ms.author="raynew"/>

#  Panoramica di Site Recovery

Il servizio Site Recovery fornisce un'efficace soluzione di ripristino di emergenza e continuità aziendale che consente di proteggere le macchine virtuali e i server fisici locali orchestrando e automatizzando la replica e il failover in Azure o in un centro dati locale secondario.

- **Semplificare**. Site Recovery consente di semplificare la strategia di continuità aziendale e ripristino di emergenza facilitando la configurazione della replica, l’esecuzione del failover e il ripristino per i carichi di lavoro e le applicazioni locali.
- **Replica**: è possibile replicare i carichi di lavoro locali nell'archiviazione di Azure o in un centro dati secondario. 
- **Insieme di credenziali**: per gestire la replica è necessario configurare un insieme di credenziali di Site Recovery nell'area Azure selezionata. Tutti i metadati restano all'interno di tale area.
- **Metadati**: i dati delle applicazioni non vengono inviati ad Azure. Site Recovery richiede solo metadati, ad esempio nomi di macchina virtuale e VMM, per orchestrare replica e failover. 
- **Connessione ad Azure**: i server di gestione comunicano con Azure in base allo scenario di distribuzione. Ad esempio, se si esegue la replica delle macchine virtuali ubicate in un cloud VMM locale, il server VMM comunica con Site Recovery tramite una connessione HTTPS in uscita crittografata. Non è necessaria alcuna connessione dalle macchine virtuali o dagli host Hyper-V.
- **Replica Hyper-V**: Azure Site Recovery utilizza Replica Hyper-V per il processo di replica e può inoltre utilizzare la replica SAN se si esegue la replica tra due siti VMM locali. Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l’intero disco rigido virtuale. Per la replica continua, vengono replicate solo le modifiche differenziali. Site Recovery supporta il trasferimento di dati offline e funziona con utilità di ottimizzazione WAN.
- **Prezzi**: è possibile ottenere [maggiori informazioni](pricing/details/site-recovery) sui prezzi di Site Recovery.


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

Le tecnologie di replica ASR sono compatibili con qualsiasi applicazione in esecuzione in una macchina virtuale. Sono stati eseguiti ulteriori test in collaborazione con i team di prodotto dell’applicazione per ulteriore supporto per ogni applicazione.

**Carico di lavoro** | <p>* * Replicare macchine virtuali Hyper-V * *</p> <p>**(in sito secondario) * *</p> | <p>* * Replicare macchine virtuali Hyper-V * *</p> <p>**(in Azure) * *</p> | <p>* * Replicare macchine virtuali VMware * *</p> <p>**(in sito secondario) * *</p> | <p>**Replicare macchine virtuali VMware **</p><p>**(in Azure)**</p>
---|---|---|---|---
Active Directory, DNS | S | S | S | Presto disponibile 
App Web (IIS, SQL) | S | S | S | Presto disponibile
SCOM | S | S | S | Presto disponibile
SharePoint | S | S | S | Presto disponibile
<p>SAP</p><p>Replicare il sito SAP in Azure per non cluster</p> | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) | Presto disponibile
Exchange (non DAG) | S | Presto disponibile | S | Presto disponibile
Desktop remoto/VDI | S | S | S | Presto disponibile 
<p>Linux</p> <p>(sistema operativo e app)</p> | Y (testato da Microsoft) | Y (testato da Microsoft) | Y (testato da Microsoft) | Presto disponibile 
Dynamics AX | S | S | S | Presto disponibile
Dynamics CRM | Presto disponibile | Presto disponibile | S | Presto disponibile
Oracle | Presto disponibile | Presto disponibile | Y (testato da Microsoft) | Presto disponibile


## Funzionalità e requisiti 

In questa tabella vengono riepilogate le principali funzionalità di Site Recovery e viene indicato come sono gestite durante la replica in Azure, la replica a un sito secondario tramite la replica Hyper-V predefinita e tramite una rete SAN.

<table border="1">
<thead>
<tr>
	<th>Funzionalità</th><th>Replicare in Azure</th>
	<th>Replicare in un sito secondario (Replica Hyper-V)</th>
	<th>Replicare in un sito secondario (SAN)</th>
</tr>
</thead>

<tr>
<td>Replica dei dati</td>
<td><p>I metadati sui server e le macchine virtuali locali vengono archiviati nell'insieme di credenziali di Site Recovery.</p> <p>I dati replicati vengono archiviati nell'archiviazione di Azure.</p></td>
<td><p>I metadati sui server e le macchine virtuali locali vengono archiviati nell'insieme di credenziali di Site Recovery.</p> <p>I dati replicati vengono archiviati nel percorso specificato dal server Hyper-V di destinazione.</p></td>
<td><p>I metadati sui server e le macchine virtuali locali vengono archiviati nell'insieme di credenziali di Site Recovery.</p> <p>I dati replicati vengono archiviati nell'array di archiviazione di destinazione.</p></td>
</tr>

<tr>
<td>Requisiti per l'insieme di credenziali</td>
<td><p>Account Azure con servizio Azure Site Recovery</p></td>
<td><p>Account Azure con servizio Azure Site Recovery</p>
</td><td><p>Account Azure con servizio Azure Site Recovery</p></td>
</tr>

<tr>
<td>Replica</td>
<td>Replicare la macchina virtuale dall'host di Hyper-V di origine nell'archiviazione di Azure. Failback alla posizione di origine.</td>
<td>Replicare la macchina virtuale dall'host di Hyper-V di origine all’host Hyper-V di destinazione. Failback alla posizione di origine.</td>
<td>Replicare le macchine virtuali dal dispositivo di archiviazione SAN di origine al dispositivo SAN di destinazione. Failback alla posizione di origine.</td>
</tr>

<tr>
<td>Macchine virtuali</td>
<td>Disco rigido della macchina virtuale archiviato nell'archiviazione di Azure</td>
<td>Disco rigido della macchina virtuale archiviato nell'host Hyper-V.</td>
<td>Disco rigido della macchina virtuale archiviato nell'array di archiviazione SAN.</td>
</tr>

<tr>
<td>Archiviazione di Azure</td>
<td>Necessaria per archiviare i dischi rigidi delle macchine virtuali replicate</td>
<td>Non applicabile</td>
<td>Non applicabile</td>
</tr>

<tr>
<td>Array di archiviazione SAN</td>
<td><p>Non applicabile</p></td>
<td>Non applicabile</td>
<td>L'array di archiviazione SAN deve essere disponibile sia nel sito di origine che in quello di destinazione e deve essere gestito da VMM.</td>
</tr>

<tr>
<td>Server VMM</td>
<td>Server VMM solo nel sito di origine. </td>
<td>Consigliato server VMM nel sito di origine e in quello di destinazione. È possibile replicare tra cloud in un singolo server VMM.</td>
<td>Server VMM nei siti VMM di origine e di destinazione I cloud devono contenere almeno un cluster Hyper-V.</td>
</tr>

<tr>
<td>Versione VMM</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 con SP1</p><p>System Center 2012 R2</p></td>
<td><p>System Center 2012 R2 con aggiornamento cumulativo 5.0 per VMM</p></td>
</tr>

<tr>
<td>Configurazione di VMM</td>
<td><p>Configurare i cloud nei siti di origine e di destinazione</p><p>Configurare le reti VM nel sito di origine e di destinazione</p><p>Configurare le classificazioni di archiviazione nei siti di origine e di destinazione <p>Installare il provider nei server VMM di origine e di destinazione</p></td>
<td><p>Configurare i cloud nel sito di origine</p><p>Configurare l'archiviazione SAN</p><p>Configurare le reti VM nel sito di origine</p><p>Installare il provider nel server VMM di origine</p><p>Abilitare la protezione delle macchine virtuali</p></td>
<td><p>Configurare i cloud nei siti di origine e di destinazione</p><p>Configurare le reti VM nei siti di origine e di destinazione</p><p>Installare il provider nel server VMM di origine e di destinazione</p><p>Abilitare la protezione delle macchine virtuali</p></td>
</tr>

<tr>
<td><p>Provider di Azure Site Recovery</p><p>Consente la connessione tramite HTTPS a Site Recovery</p></td>
<td>Viene installato nel server VMM di origine</td>
<td>Viene installato nei server VMM di origine e di destinazione</td>
<td>Viene installato nei server VMM di origine e di destinazione</td>
</tr>

<tr>
<td><p>Agente di Servizi di ripristino di Azure</p><p>Consente la connessione tramite HTTPS a Site Recovery</p></td>
<td>Installare nei server host Hyper-V</td>
<td>Facoltativo</td>
<td>Facoltativo</td>
</tr>

<tr>
<td>Punti di ripristino delle macchine virtuali</td>
<td><p>I punti di ripristino vengono impostati in base al tempo.</p> <p>Specifica per quanto tempo mantenere un punto di ripristino (0-24 ore).</p></td>
<td><p>I punti di ripristino vengono impostati in base alla quantità.</p> <p>Specifica il numero di punti di ripristino aggiuntivi da mantenere (0-15). Per impostazione predefinita, viene creato un punto di ripristino ogni ora</p></td>
<td>Configurato nelle impostazioni di archiviazione degli array</td>
</tr>

<tr>
<td>Mapping di rete</td>
<td><p>Esegue il mapping delle reti VM alle reti di Azure.</p> <p>Il mapping garantisce che tutte le macchine virtuali di cui viene eseguito il failover nella stessa rete VM di origine possano connettersi dopo il failover. Inoltre, se è configurato un gateway di rete nella rete di Azure di destinazione, le macchine virtuali possono connettersi alle macchine virtuali locali. </p><p>Se il mapping è abilitato, solo le macchine virtuali di cui viene eseguito il failover nello stesso piano di ripristino possono connettersi tra loro dopo il failover in Azure.</p></td>
<td><p>Esegue il mapping delle reti VM di origine alle reti VM di destinazione.</p> <p>Il mapping viene usato per inserire macchine virtuali replicate in server host Hyper-V ottimali e garantisce che le macchine virtuali associate alla rete VM di origine siano associate alla rete di destinazione mappata dopo il failover. </p><p>Se il mapping non è abilitato, le macchine virtuali replicate non vengono connesse ad alcuna rete.</p></td>
<td><p>Esegue il mapping delle reti VM di origine alle reti VM di destinazione.</p> <p>Il mapping di rete garantisce che le macchine virtuali associate alla rete VM di origine siano associate alla rete di destinazione mappata dopo il failover. </p><p>Se il mapping non è abilitato, le macchine virtuali replicate non vengono connesse ad alcuna rete.</p></td>
</tr>

<tr>
<td>Mapping dell'archiviazione</td>
<td>Non applicabile</td>
<td><p>Esegue il mapping delle classificazioni di archiviazione nei server VMM di origine alle classificazioni di archiviazione nei server VMM di destinazione.</p> <p>Se il mapping è abilitato, i dischi rigidi delle macchine virtuali nella classificazione di archiviazione di origine verranno inseriti nella classificazione di archiviazione di destinazione dopo il failover.</p><p>Se il mapping dell'archiviazione non è abilitato, i dischi rigidi delle macchine virtuali replicate verranno archiviati nel percorso predefinito nel server host Hyper-V di destinazione.</p></td>
<td><p>Mappa tra gli array e i pool di archiviazione nei siti primari e secondari.</p></td>
</tr>

</table>

## Passaggi successivi

Una volta completata questa panoramica [leggere le procedure consigliate](site-recovery-best-practices.md) che consentono di iniziare la pianificazione della distribuzione.
 

<!---HONumber=July15_HO4-->
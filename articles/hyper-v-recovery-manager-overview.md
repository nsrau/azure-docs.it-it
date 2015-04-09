<properties 
	pageTitle="Panoramica di Azure Site Recovery" 
	description="Informazioni su come distribuire Azure Site Recovery per proteggere macchine virtuali in locale in un altro sito locale o in Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Panoramica di Azure Site Recovery

<div class="dev-callout"> 
<p>Azure Site Recovery gestisce la replica e il failover in una serie di scenari:</p>


<ul>
<li>**Protezione da sito Hyper-V locale ad Azure con replica Hyper-V**: gestire replica, failover e recupero da un sito locale con uno o più server Hyper-V, ma senza System Center VMM. I dati della macchina virtuale vengono replicati da un server host Hyper-V di origine ad Azure. Leggere <a href="http://go.microsoft.com/fwlink/?LinkId=522298">Introduzione ad Azure Site Recovery: Protezione tra un sito Hyper-V locale e Azure con la replica Hyper-V</a></li>
<li>**Protezione da sito VMM locale a sito VMM locale con replica Hyper-V**: gestire replica, failover e recupero tra siti VMM locali. I dati delle macchine virtuali vengono replicati da un server host Hyper-V di origine a un server host di destinazione. Leggere <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Introduzione ad Azure Site Recovery: Protezione tra due siti VMM locali con la replica Hyper-V</a>.</li>

<li>**Protezione da sito VMM locale a sito VMM locale con replica SAN**: gestisce la replica, il failover e il recupero end-to-end usando la replica basata su array di archiviazione end-to-end tra i dispositivi SAN che ospitano i dati delle macchine virtuali nei siti di origine e destinazione. Leggere <a href="http://go.microsoft.com/fwlink/?LinkId=518683">Introduzione ad Azure Site Recovery: : Protezione tra due siti VMM locali con la replica SAN</a>.</li>

<li>**Protezione da sito VMM locale ad Azure**: gestire replica, failover e recupero tra un sito VMM locale e Azure. I dati replicati della macchina virtuale vengono archiviati nell'archiviazione di Azure. Leggere <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Introduzione ad Azure Site Recovery: protezione tra un sito VMM locale e Azure.</a></li>

<li>**Da sito VMWare locale a sito VMWare locale con InMage**: InMage Scout è una recente acquisizione di Microsoft che fornisce la replica in tempo reale tra siti VMWare locali. Al momento InMage è disponibile come prodotto separato ottenuto tramite una sottoscrizione del servizio Azure Site Recovery. Leggere <a href="http://go.microsoft.com/fwlink/?LinkId=518684">Introduzione ad Azure Site Recovery: protezione tra siti VMWare locali con InMage</a>.</li>
</ul>

<p>Nella tabella delle funzionalità sono riepilogati tutti gli scenari.</p>

<table border="1">
<thead>
<tr>
	<th>Funzionalità</th><th>Da sito locale ad Azure</th>
	<th>Da sito locale a sito locale (Hyper-V)</th>
	<th>Da sito locale a sito locale (SAN)</th>
</tr>
</thead>

<tr>
<td>Dati in Azure Site Recovery</td>
<td><p>I metadati del cloud vengono inviati ad Azure Site Recovery.</p> <p>I dati replicati vengono archiviati nell'archiviazione di Azure.</p></td>
<td><p>I metadati del cloud vengono inviati ad Azure Site Recovery.</p> <p>I dati replicati vengono archiviati nel percorso specificato dal server Hyper-V di destinazione.</p></td>
<td><p>I metadati del cloud vengono inviati ad Azure Site Recovery.</p> <p>I dati replicati vengono archiviati nell'array di archiviazione di destinazione.</p></td>
</tr>

<tr>
<td>Requisiti per l'insieme di credenziali</td>
<td><p>È necessario un account Azure per configurare un insieme di credenziali di Azure Site Recovery. Vedere la <a href="http://aka.ms/try-azure">versione di valutazione gratuita di Azure</a>. Ottenere informazioni dettagliate sui <a href="http://go.microsoft.com/fwlink/?LinkId=378268">prezzi di Azure Site Recovery Manager</a>.</p></td>
<td><p>Account Azure con Azure Site Recovery abilitato.</p>
</td><td><p>Account Azure con Azure Site Recovery abilitato.</p></td>
</tr>

<tr>
<td>Replica</td>
<td>Le macchine virtuali vengono replicate da un server Hyper-V locale di origine all'archiviazione di Azure di destinazione. È possibile configurare la replica inversa per replicare di nuovo alla posizione di origine.</td>
<td>Le macchine virtuali vengono replicate da un server Hyper-V locale di origine a un altro. È possibile configurare la replica inversa per replicare di nuovo alla posizione di origine.</td>
<td>Le macchine virtuali vengono replicate dal dispositivo di archiviazione SAN di origine al dispositivo SAN di destinazione. È possibile configurare la replica inversa per replicare di nuovo alla posizione di origine.</td>
</tr>

<tr>
<td>Archiviazione della macchina virtuale</td>
<td>Disco rigido della macchina virtuale archiviato nell'archiviazione di Azure</td>
<td>Disco rigido della macchina virtuale archiviato nell'host Hyper-V.</td>
<td>Disco rigido della macchina virtuale archiviato nell'array di archiviazione SAN.</td>
</tr>

<tr>
<td>Archiviazione di Azure</td>
<td><p>È richiesto l'account di archiviazione di Azure.</p> <p>L'account deve essere abilitato per la replica geografica, trovarsi nella stessa area geografica in cui si trova il servizio Azure Site Recovery ed essere associato alla stessa sottoscrizione.</p></td>
<td>Non applicabile</td>
<td>Non applicabile</td>
</tr>

<tr>
<td>Array di archiviazione SAN</td>
<td><p>Non applicabile</p></td>
<td>Non applicabile</td>
<td>L'array di archiviazione SAN deve essere disponibile in entrambi i siti di origine e di destinazione e deve essere gestito da VMM. Per altre informazioni, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=518685">Prerequisiti per la distribuzione</a>. </td>
</tr>

<tr>
<td>Server VMM</td>
<td>Richiede un server VMM solo nel sito di origine. Il server VMM deve avere almeno un cloud che contiene almeno un cluster o un server host Hyper-V.</td>
<td>Richiede server VMM di origine e di destinazione con almeno un cloud in ognuno oppure un unico server VMM con due cloud. I cloud devono contenere almeno un cluster o un server host Hyper-V.</td>
<td>Richiede server VMM di origine e di destinazione con almeno un cloud in ognuno. I cloud devono contenere almeno un cluster Hyper-V.</td>
</tr>

<tr>
<td>Versione di System Center di VMM</td>
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
<td>Provider di Azure Site Recovery</td>
<td>Viene installato nel server VMM di origine</td>
<td>Viene installato nei server VMM di origine e di destinazione</td>
<td>Viene installato nei server VMM di origine e di destinazione</td>
</tr>

<tr>
<td>Agente di Servizi di ripristino di Azure</td>
<td>Viene installato nei server host Hyper-V presenti in cloud VMM</td>
<td>Facoltativo</td>
<td>Facoltativo</td>
</tr>

<tr>
<td>Punti di ripristino delle macchine virtuali</td>
<td><p>I punti di ripristino vengono impostati in base al tempo.</p> <p>Specifica per quanto tempo mantenere un punto di ripristino (0-24 ore). Durante la prima ora viene creato un punto di ripristino in base alla formula 90/frequenza_copia. Successivamente, viene creato ogni ora.</p></td>
<td><p>I punti di ripristino vengono impostati in base alla quantità.</p> <p>Specifica il numero di punti di ripristino aggiuntivi da mantenere (0-15). Per impostazione predefinita, viene creato un punto di ripristino ogni ora. Se si imposta su 0, solo l'ultimo punto di ripristino sarà archiviato nel server host Hyper-V di replica.</p></td>
<td>Configurato nelle impostazioni di archiviazione degli array.</td>
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

<p>In caso di domande, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum relativo ai Servizi di ripristino di Azure</a>.</p> 

<!--HONumber=49-->
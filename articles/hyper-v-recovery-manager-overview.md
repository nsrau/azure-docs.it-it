<properties linkid="Azure Site Recovery Overview" urlDisplayName="Azure Site Recovery Overview" pageTitle="Azure Site Recovery Overview" metaKeywords="Azure Site Recovery, on-premises, clouds, Azure, VMM, Hyper-V" description="Deploy Azure Site Recovery to protect virtual machines on Hyper-V host servers that are located in VMM clouds. You can deploy from one on-premises site to another, or from an on-premises site to Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Azure Site Recovery Overview" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Panoramica di Azure Site Recovery

<div class="dev-callout">

<p>Distribuire Azure Site Recovery per proteggere le macchine virtuali in esecuzione in server host Hyper-V presenti in cloud System Center Virtual Machine Manager (VMM). È possibile distribuire Azure Site Recovery in due modi:</p>
<ul>
<li><b>Protezione da sito locale a sito locale:</b> le macchine virtuali vengono replicate da un sito locale a un altro. Le impostazioni di protezione vengono configurate e abilitate negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati da un server host Hyper-V di origine a un server host di destinazione. Azure Site Recovery si occupa di orchestrare il processo. Un'esercitazione per questo scenario è disponibile in [Introduzione ad Azure Site Recovery: Protezione da sito locale a sito locale][Introduzione ad Azure Site Recovery: Protezione da sito locale a sito locale].</li>
<li><b>Protezione da sito locale ad Azure:</b> le macchine virtuali vengono replicate da un sito locale in Microsoft Azure. Le impostazioni di protezione vengono configurate e abilitate negli insiemi di credenziali di Azure Site Recovery. Azure Site Recovery si occupa di orchestrare il processo e i dati delle macchine virtuali replicate vengono archiviati nell'archiviazione di Azure. Un'esercitazione per questo scenario è disponibile in [Introduzione ad Azure Site Recovery: Protezione da sito locale ad Azure][Introduzione ad Azure Site Recovery: Protezione da sito locale ad Azure].</li>
<br>
Nella tabella seguente vengono riepilogate e confrontate le due opzioni di distribuzione.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Funzionalità</th>
<th align="left">Da sito locale ad Azure</th>
<th align="left">Da sito locale a sito locale</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Dati in Azure Site Recovery</td>
<td align="left">I metadati del cloud vengono inviati ad Azure Site Recovery. I dati replicati vengono archiviati nell'archiviazione di Azure.</td>
<td align="left">I metadati del cloud vengono inviati ad Azure Site Recovery. I dati replicati vengono archiviati nel server host Hyper-V di destinazione.</td>
</tr>
<tr class="even">
<td align="left">Requisiti per l'insieme di credenziali</td>
<td align="left"><p>Per l'insieme di credenziali è richiesto un certificato conforme ai <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">requisiti per l'insieme di credenziali</a>.</p>
<p>Una chiave dell'insieme di credenziali viene generata automaticamente. La chiave garantisce l'integrità del traffico tra il provider di Azure Site Recovery nel server VMM e Azure Site Recovery.</p></td>
<td align="left"><p>Per l'insieme di credenziali è richiesto un certificato conforme ai <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">requisiti per l'insieme di credenziali</a>.</p>
<p>Una chiave dell'insieme di credenziali viene generata automaticamente. La chiave garantisce l'integrità del traffico tra il provider di Azure Site Recovery nel server VMM e Azure Site Recovery.</p></td>
</tr>
<tr class="odd">
<td align="left">Replica</td>
<td align="left">Le macchine virtuali vengono replicate da un server Hyper-V locale all'archiviazione di Azure.</td>
<td align="left">Le macchine virtuali vengono replicate da un server Hyper-V locale a un altro.</td>
</tr>
<tr class="even">
<td align="left">Archiviazione della macchina virtuale</td>
<td align="left">Disco rigido della macchina virtuale archiviato nell'archiviazione di Azure</td>
<td align="left">Disco rigido della macchina virtuale archiviato nell'host Hyper-V.</td>
</tr>
<tr class="odd">
<td align="left">Archiviazione di Azure</td>
<td align="left"><p>È richiesto l'account di archiviazione di Azure.</p>
<p>L'account deve essere abilitato per la replica geografica, trovarsi nella stessa area geografica in cui si trova il servizio Azure Site Recovery ed essere associato alla stessa sottoscrizione.</p></td>
<td align="left">Non applicabile</td>
</tr>
<tr class="even">
<td align="left">Server VMM</td>
<td align="left">Richiede solo un server VMM di origine</td>
<td align="left">Richiede server VMM di origine e di destinazione con almeno un cloud in ognuno oppure un unico server VMM con due cloud</td>
</tr>
<tr class="odd">
<td align="left">Versione di System Center del server VMM</td>
<td align="left">System Center 2012 R2</td>
<td align="left"><p>System Center 2012 con SP1</p>
<p>System Center 2012 R2</p></td>
</tr>
<tr class="even">
<td align="left">Provider di Azure Site Recovery</td>
<td align="left">Viene installato nel server VMM di origine</td>
<td align="left">Viene installato nei server VMM di origine e di destinazione</td>
</tr>
<tr class="odd">
<td align="left">Agente di Servizi di ripristino di Azure</td>
<td align="left">Viene installato nei server host Hyper-V presenti in cloud VMM</td>
<td align="left">Facoltativo</td>
</tr>
<tr class="even">
<td align="left">Punti di ripristino delle macchine virtuali</td>
<td align="left"><p>I punti di ripristino vengono impostati in base al tempo.</p>
<p>Specifica per quanto tempo mantenere un punto di ripristino (0-24 ore). Durante la prima ora viene creato un punto di ripristino in base alla formula 90/frequenza_copia. Successivamente, viene creato ogni ora.</p></td>
<td align="left"><p>I punti di ripristino vengono impostati in base alla quantità.</p>
<p>Specifica il numero di punti di ripristino aggiuntivi da mantenere (0-15). Per impostazione predefinita, viene creato un punto di ripristino ogni ora. Se si imposta su 0, solo l'ultimo punto di ripristino sarà archiviato nel server host Hyper-V di replica.</p></td>
</tr>
<tr class="odd">
<td align="left">Mapping di rete</td>
<td align="left"><p>Esegue il mapping delle reti VM alle reti di Azure.</p>
<p>Il mapping garantisce che tutte le macchine virtuali di cui viene eseguito il failover nella stessa rete VM di origine possano connettersi dopo il failover. Inoltre, se è configurato un gateway di rete nella rete di Azure di destinazione, le macchine virtuali possono connettersi alle macchine virtuali locali.</p>
<p>Se il mapping è abilitato, solo le macchine virtuali di cui viene eseguito il failover nello stesso piano di ripristino possono connettersi tra loro dopo il failover in Azure.</p></td>
<td align="left"><p>Esegue il mapping delle reti VM di origine alle reti VM di destinazione.</p>
<p>Il mapping viene usato per inserire macchine virtuali replicate in server host Hyper-V ottimali e garantisce che le macchine virtuali associate alla rete VM di origine siano associate alla rete di destinazione mappata dopo il failover.</p>
<p>Se il mapping non è abilitato, le macchine virtuali replicate non vengono connesse ad alcuna rete.</p></td>
</tr>
<tr class="even">
<td align="left">Mapping dell'archiviazione</td>
<td align="left">Non applicabile</td>
<td align="left"><p>Esegue il mapping delle classificazioni di archiviazione nei server VMM di origine alle classificazioni di archiviazione nei server VMM di destinazione.</p>
<p>Se il mapping è abilitato, i dischi rigidi delle macchine virtuali nella classificazione di archiviazione di origine verranno inseriti nella classificazione di archiviazione di destinazione dopo il failover.</p>
<p>Se il mapping dell'archiviazione non è abilitato, i dischi rigidi delle macchine virtuali replicate verranno archiviati nel percorso predefinito nel server host Hyper-V di destinazione.</p></td>
</tr>
</tbody>
</table>

In caso di domande, vedere il [forum relativo ai Servizi di ripristino di Azure][forum relativo ai Servizi di ripristino di Azure].

</div>

  [Introduzione ad Azure Site Recovery: Protezione da sito locale a sito locale]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Introduzione ad Azure Site Recovery: Protezione da sito locale ad Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [forum relativo ai Servizi di ripristino di Azure]: http://go.microsoft.com/fwlink/?LinkId=313628

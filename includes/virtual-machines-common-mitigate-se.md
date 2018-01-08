
**Ultimo aggiornamento del documento**: 6 gennaio 6:30 PM PST.

La diffusione di recente un [nuova classe di vulnerabilità CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) noto come attacchi al canale laterale speculativo esecuzione ha comportato domande dei clienti che desiderano ottenere maggiore chiarezza.  

L'infrastruttura che esegue Azure e isola i carichi di lavoro cliente da altra è protetto.  Ciò significa che l'applicazione utilizzando queste vulnerabilità non è possibile attacchi da altri clienti in esecuzione in Azure.

## <a name="keeping-your-operating-systems-up-to-date"></a>Aggiornamento dei sistemi operativi

Mentre un aggiornamento del sistema operativo non è necessario per isolare le applicazioni in esecuzione in Azure da altri clienti in esecuzione in Azure, è sempre consigliabile mantenere le versioni del sistema operativo aggiornato. 

Nelle offerte seguenti, ecco le azioni consigliate per aggiornare il sistema operativo: 

<table>
<tr>
<th>Offerta</th> <th>Azione consigliata </th>
</tr>
<tr>
<td>Servizi cloud di Azure </td>  <td>Abilita aggiornamento automatico o assicurarsi che si esegue il sistema operativo Guest più recenti.</td>
</tr>
<tr>
<td>Macchine virtuali Linux di Azure</td> <td>Installare gli aggiornamenti dal provider di sistema operativo, se disponibile. </td>
</tr>
<tr>
<td>Macchine virtuali di Windows Azure </td> <td><ul><li>Verificare che si esegue un'applicazione antivirus supportata prima di installare gli aggiornamenti del sistema operativo. Per informazioni sulla compatibilità, contattare il fornitore di software antivirus. </li> <li> Installare il [rollup della sicurezza gennaio](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </li></ul></td>
</tr>
<tr>
<td>Altri servizi PaaS di Azure</td> <td>Non vi è nessuna azione necessaria per i clienti che utilizzano questi servizi. Azure automaticamente mantiene le versioni del sistema operativo aggiornato. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Informazioni aggiuntive, se si esegue codice non attendibile 

A meno che non si eseguono codice non attendibile, è necessaria alcuna azione aggiuntivi del cliente. Se si consente a codice non attendibile (ad esempio, consentire a uno ai clienti di caricare un file binario o frammenti di codice quindi eseguite nel cloud all'interno dell'applicazione), quindi eseguire i passaggi aggiuntivi seguenti.  


### <a name="windows"></a>Windows 
Se si utilizza Windows e l'hosting di codice non attendibile, è inoltre consigliabile abilitare una funzionalità di Windows denominata Shadowing Kernel virtuale indirizzo (kVA ()) che fornisce protezione aggiuntiva contro le vulnerabilità di canale lato speculativo esecuzione. Questa funzionalità è disattivata per impostazione predefinita e può influire sulle prestazioni se abilitato. Seguire [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) le istruzioni per l'abilitazione di protezioni sul Server. Se si eseguono i servizi Cloud di Azure, verificare che si eseguono WA-GUEST-OS-5.15_201801-01 o WA-GUEST-OS-4.50_201801-01 (disponibile a partire dal 10 gennaio) e abilitare il Registro di sistema della chiave tramite un'attività di avvio.


### <a name="linux"></a>Linux
Se si utilizza Linux e hosting di codice non attendibile, è necessario aggiornare anche Linux per una versione più recente che implementa l'isolamento della tabella pagine kernel (KPTI) che separa le tabelle di pagina utilizzate dal kernel tra quelli che appartengono allo spazio utente. Queste soluzioni richiedono un aggiornamento del sistema operativo Linux e possono essere ottenute dal provider di distribuzione, se disponibile. Il provider del sistema operativo può indicare se le protezioni vengono abilitate o disabilitate per impostazione predefinita.








## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [protezione di Azure i clienti da vulnerabilità CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
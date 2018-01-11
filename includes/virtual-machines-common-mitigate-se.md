
**Ultimo aggiornamento del documento**: 6 gennaio, 18:30 PST.

La divulgazione recente di una [nuova classe di vulnerabilità della CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), definita attacchi del canale laterale per l'esecuzione speculativa, ha suscitato molte domande e richieste di chiarimenti da parte dei clienti.  

L'infrastruttura che esegue Azure e isola i carichi di lavoro dei clienti gli uni da gli altri è protetta.  Gli altri clienti in esecuzione su Azure non possono quindi attaccare l'applicazione di un utente tramite queste vulnerabilità.

## <a name="keeping-your-operating-systems-up-to-date"></a>Mantenere aggiornato il sistema operativo

Benché non sia necessario un aggiornamento del sistema operativo per isolare le applicazioni in esecuzione su Azure da altri clienti in esecuzione su Azure, è sempre consigliabile mantenere aggiornate le versioni del sistema operativo. 

Nelle offerte seguenti sono illustrate le azioni consigliate per aggiornare il sistema operativo: 

<table>
<tr>
<th>Offerta</th> <th>Azione consigliata </th>
</tr>
<tr>
<td>Servizi cloud di Azure </td>  <td>Abilitare l'aggiornamento automatico o assicurarsi che sia in esecuzione il sistema operativo Guest più recente.</td>
</tr>
<tr>
<td>Macchine virtuali Linux in Azure</td> <td>Installare aggiornamenti dal provider del sistema operativo, quando disponibili. </td>
</tr>
<tr>
<td>Macchine virtuali Windows in Azure </td> <td>Verificare che sia in esecuzione un'applicazione antivirus supportata prima di installare aggiornamenti del sistema operativo. Contattare il fornitore di software antivirus per ottenere informazioni sulla compatibilità.<p> Installare il [rollup per la sicurezza di gennaio](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Altri servizi PaaS di Azure</td> <td>Non sono necessarie azioni da parte dei clienti che usano questi servizi. Azure aggiorna automaticamente le versioni del sistema operativo. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Indicazioni aggiuntive in caso di esecuzione di codice non attendibile 

Non sono necessarie azioni aggiuntive da parte dei clienti, a meno che il codice eseguito non sia attendibile. Se si consente codice non attendibile, ad esempio se si permette a un cliente di caricare un file binario o un frammento di codice che viene eseguito sul cloud entro l'applicazione, sarà necessario seguire questa procedura aggiuntiva.  


### <a name="windows"></a>Windows 
Se si usa Windows e si esegue l'hosting di codice non attendibile, è necessario abilitare una funzionalità di Windows definita shadowing dell'indirizzo virtuale del kernel, che offre protezione aggiuntiva dalle vulnerabilità del canale laterale per l'esecuzione speculativa. Questa funzionalità viene disattivata per impostazione predefinita e potrebbe influire sulle prestazioni, se abilitata. Seguire le istruzioni disponibili in [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per l'abilitazione delle protezioni sul server. Se si eseguono Servizi cloud di Azure, verificare che sia in esecuzione WA-GUEST-OS-5.15_201801-01 o WA-GUEST-OS-4.50_201801-01 (disponibile a partire dal 10 gennaio) e abilitare la chiave del Registro di sistema tramite un'attività di avvio.


### <a name="linux"></a>Linux
Se si usa Linux e si esegue l'hosting di codice non attendibile, è necessario aggiornare anche Linux a una versione più recente che implementa l'isolamento di tabelle di pagina del kernel che separa le tabelle di pagina usate dal kernel da quelle appartenente allo spazio utente. Queste mitigazioni richiedono un aggiornamento del sistema operativo Linux e possono essere ottenute dal provider di distribuzione, se disponibili. Il provider del sistema operativo può segnalare se le protezioni sono abilitate o disabilitate per impostazione predefinita.








## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Protezione dei clienti di Azure dalla vulnerabilità della CPU).
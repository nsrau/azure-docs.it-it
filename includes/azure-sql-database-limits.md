<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Risorsa</th>
   <th align="left" valign="middle">Limite predefinito</th>
</tr>
<tr>
   <td valign="middle"><p>Dimensioni database</p></td>
   <td valign="middle"><p>Dipende dal livello di prestazioni <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Account di accesso</p></td>
   <td valign="middle"><p>Dipende dal livello di prestazioni <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Utilizzo della memoria</p></td>
   <td valign="middle"><p>Concessione di memoria di 16 MB per più di 20 secondi</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sessioni</p></td>
   <td valign="middle"><p>Dipende dal livello di prestazioni <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Dimensioni di tempdb</p></td>
   <td valign="middle"><p>5 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Durata della transazione</p></td>
   <td valign="middle"><p>24 ore<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Blocchi per transazione</p></td>
   <td valign="middle"><p>1 milione</p></td>
</tr>
<tr>
   <td valign="middle"><p>Dimensioni di ciascuna transazione</p></td>
   <td valign="middle"><p>2 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Percentuale di spazio totale di log utilizzato per transazione</p></td>
   <td valign="middle"><p>20%</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo di richieste simultanee (thread di lavoro)</p></td>
   <td valign="middle"><p>Dipende dal livello di prestazioni <sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>Database SQL con livelli di prestazioni, ad esempio Basic, Standard e Premium. Standard e Premium inoltre sono suddivise in livelli di prestazioni. Per i limiti di dettagliate per ogni livello e livello di servizio, vedere [livelli di servizio del Database SQL Azure e livelli di prestazioni](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Se la transazione blocca una risorsa richiesta da un'attività di sistema sottostante, quindi la durata massima è 20 secondi.

<!---HONumber=July15_HO5-->
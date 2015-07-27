<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Risorsa</th>
   <th align="left" valign="middle">Limite predefinito</th>
   <th align="left" valign="middle">Limite massimo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Reti virtuali</a><sup>1</sup> per sottoscrizione</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Totale computer<sup>2</sup> per rete virtuale</p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Connessioni TCP simultanee per un'istanza del ruolo o della macchina virtuale</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>Elenchi di controllo di accesso (ACL) per endpoint<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Siti di rete locali per rete virtuale</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>Ogni rete virtuale supporta un solo [gateway di rete virtuale](http://msdn.microsoft.com/library/azure/jj156210.aspx).

<sup>2</sup>Il numero totale di computer include le macchine virtuali e le istanze di ruolo di lavoro/Web.

<sup>3</sup>L'elenco di controllo di accesso (ACL) è supportato negli endpoint di input per le macchine virtuali. Per i ruoli di lavoro/Web è supportato negli endpoint di input e di input dell'istanza.

<!---HONumber=July15_HO3-->
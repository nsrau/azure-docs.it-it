<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Risorsa</th>
   <th align="left" valign="middle">Limite predefinito</th>
   <th align="left" valign="middle">Limite massimo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Macchine virtuali</a> per servizio cloud<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Endpoint di input per servizio cloud<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Quando si crea una macchina virtuale all'esterno di un gruppo di risorse di Azure, viene creato automaticamente un servizio cloud per contenerla. È quindi possibile aggiungere più macchine virtuali nello stesso servizio cloud.

<sup>2</sup>Gli endpoint di input vengono usati per consentire le comunicazioni con le macchine virtuali esterne al servizio cloud contenitore. Le macchine virtuali nello stesso servizio cloud consentono automaticamente le comunicazioni tra tutte le porte UDP e TCP per le comunicazioni interne.

<!---HONumber=62-->
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

<sup>1</sup>Macchine virtuali create in Gestione dei servizi (anziché Resource Manager) viene automaticamente archiviato in un servizio cloud. È possibile aggiungere più macchine virtuali al servizio cloud per il bilanciamento del carico e disponibilità. Vedere [Come connettere le macchine virtuali con una rete virtuale o un servizio cloud](../virtual-machines/cloud-services-connect-virtual-machine.md).

<sup>2</sup>Endpoint di input consentire le comunicazioni a una macchina virtuale da fuori servizio cloud della macchina virtuale. Le macchine virtuali nello stesso servizio cloud o rete virtuale può comunicare automaticamente tra loro. Vedere [Come configurare gli endpoint in una macchina virtuale](../virtual-machines/virtual-machines-set-up-endpoints.md).

<!---HONumber=July15_HO5-->
<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Risorsa<sup>1</sup></th>
   <th align="left" valign="middle">Limite predefinito</th>
</tr>
<tr>
   <td valign="middle"><p>TB per account di archiviazione</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Dimensione massima di un singolo contenitore BLOB o di una singola tabella o coda</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione</p></td>
   <td valign="middle"><p>L'unico limite è quello relativo alla capacità dell'account di archiviazione (500 TB)</p></td>
</tr>
<tr>
   <td valign="middle"><p>Dimensione massima di una condivisione file</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo di file in una condivisione file</p></td>
   <td valign="middle"><p>L'unico limite è quello relativo alla capacità totale della condivisione file (5 TB)</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo di IOPS da 8 KB per disco persistente (livello Basic)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo di IOPS da 8 KB per disco persistente (livello Standard)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Frequenza di richiesta totale (presupponendo oggetti con dimensione di 1 KB) per account di archiviazione</p></td>
   <td valign="middle"><p>Fino a 20.000 entità o messaggi al secondo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Velocità effettiva da raggiungere per BLOB singolo</p></td>
   <td valign="middle"><p>Fino a 60 MB al secondo o fino 500 richieste al secondo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Velocità effettiva da raggiungere per coda singola (messaggi di 1 KB)</p></td>
   <td valign="middle"><p>Fino a 2000 messaggi al secondo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Velocità effettiva da raggiungere per partizione di tabella singola (entità di 1 KB)</p></td>
   <td valign="middle"><p>Fino a 2000 entità al secondo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo in entrata per account di archiviazione (aree US)</p></td>
   <td valign="middle"><p>10 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 20 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo in uscita per account di archiviazione (aree US)</p></td>
   <td valign="middle"><p>20 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 30 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo in entrata per account di archiviazione (aree europee e asiatiche)</p></td>
   <td valign="middle"><p>5 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 10 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo in uscita per account di archiviazione (aree europee e asiatiche)</p></td>
   <td valign="middle"><p>10 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 15 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
</table>

<sup>1</sup>Per altre informazioni su questi limiti, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../articles/storage/storage-scalability-targets.md).

<sup>2</sup>Per le macchine virtuali del livello Basic, non posizionare oltre 66 VHD (20.000/300) a uso intensivo in un account di archiviazione per evitare il limite della frequenza di richieste totale, pari a 20.000. Per le macchine virtuali del livello Standard, non posizionare oltre 40 VHD (20.000/500) a uso intensivo in un account di archiviazione. Per altre informazioni, vedere la pagina [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

<sup>3</sup>GRS è l'acronimo di [Geo Redundant Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx). LRS è l'acronimo di [Locally Redundant Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx). Si noti che l'archiviazione con ridondanza geografica è anche localmente ridondante.

<!---HONumber=62-->
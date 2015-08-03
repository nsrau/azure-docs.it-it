<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Risorsa</th>
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
   <td valign="middle"><p>Max 8 KB IOPS per disco permanente (macchina virtuale di livello base)</p></td>
   <td valign="middle"><p>300<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Max 8 KB IOPS per disco permanente (macchina virtuale di livello Standard)</p></td>
   <td valign="middle"><p>500<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Frequenza di richiesta totale (presupponendo oggetti con dimensione di 1 KB) per account di archiviazione</p></td>
   <td valign="middle"><p>Fino a 20.000 IOPS, entità al secondo o messaggi al secondo</p></td>
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
   <td valign="middle"><p>Velocità effettiva da raggiungere per singolo file</p></td>
   <td valign="middle"><p>Fino a 60 MB al secondo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo in entrata <sup>2</sup> per account di archiviazione (aree US)</p></td>
   <td valign="middle"><p>10 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 20 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo in uscita <sup>2</sup> per account di archiviazione (aree US)</p></td>
   <td valign="middle"><p>20 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 30 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo in entrata <sup>2</sup> per account di archiviazione (aree europee e asiatiche)</p></td>
   <td valign="middle"><p>5 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 10 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
<tr>
   <td valign="middle"><p>Numero massimo in uscita <sup>2</sup> per account di archiviazione (aree europee e asiatiche)</p></td>
   <td valign="middle"><p>10 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 15 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
</table>

<sup>1</sup>il limite di velocità richiesta totale per un account di archiviazione è 20.000 al secondo. Se una macchina virtuale utilizza il numero massimo di IOPS per disco, per evitare la limitazione possibili, assicurarsi che il numero totale di IOPS in tutti i dischi rigidi virtuali delle macchine virtuali non superi il limite di account di archiviazione (20.000 IOPS).

È possibile calcolare approssimativamente il numero di dischi a elevato utilizzati supportato da un singolo account di archiviazione del limite della transazione. Per una VM di livello base, il numero massimo di dischi a elevato utilizzati è su 66 (20.000/300 di IOPS per disco) e per una macchina virtuale di livello Standard, ad esempio, è circa 40 (IOPS 20.000/500 per disco). Si noti tuttavia che l'account di archiviazione può supportare un numero maggiore di dischi, se non sono tutti molto utilizzato nello stesso momento.

<sup>2</sup>Si intendono in *ingresso* tutti i dati (richieste) inviati a un account di archiviazione. Si intendono in *uscita* tutti i dati (risposte) ricevuti da un account di archiviazione.

<sup>3</sup>GRS si intende l'archiviazione con ridondanza geografica, mentre LRS si riferisce a archiviazione con ridondanza locale.

<!---HONumber=July15_HO4-->
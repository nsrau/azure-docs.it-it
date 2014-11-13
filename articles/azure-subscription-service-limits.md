<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Limiti, quote e vincoli relativi alle sottoscrizioni e ai servizi di Microsoft Azure" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Fornisce un elenco dei limiti pi&ugrave; comuni relativi alle sottoscrizioni e ai servizi di Azure insieme ai valori massimi" metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Sottoscrizione di Azure e limiti dei servizi, quote e vincoli

Nel seguente documento vengono illustrati alcuni dei limiti più comuni di Microsoft Azure. Si noti che al momento nel documento non vengono trattati tutti i servizi di Azure. Nel tempo questi limiti verranno ampliati e aggiornati in modo da coprire un maggior numero di servizi della piattaforma.

-   [Limiti relativi alle sottoscrizioni][Limiti relativi alle sottoscrizioni]
-   [Limiti relativi ai servizi cloud][Limiti relativi ai servizi cloud]
-   [Limiti relativi alle macchine virtuali][Limiti relativi alle macchine virtuali]
-   [Limiti relativi ai siti Web][Limiti relativi ai siti Web]
-   [Limiti relativi alle funzionalità di rete][Limiti relativi alle funzionalità di rete]
-   [Limiti relativi all'archiviazione][Limiti relativi all'archiviazione]
-   [Limiti relativi all'anteprima di DocumentDB][Limiti relativi all'anteprima di DocumentDB]
-   [Limiti relativi al database SQL][Limiti relativi al database SQL]
-   [Limiti relativi ai servizi multimediali][Limiti relativi ai servizi multimediali]
-   [Limiti relativi al bus di servizio][Limiti relativi al bus di servizio]

> [WACOM.NOTE] Per aumentare il limite oltre quello **predefinito**, è possibile [aprire una richiesta di assistenza clienti online senza alcun addebito][aprire una richiesta di assistenza clienti online senza alcun addebito]. I limiti non possono essere aumentati oltre il valore **Limite massimo** definito nelle tabelle seguenti. Se non è presente nessuna colonna **Limite massimo**, per la risorsa specificata non sono disponibili limiti regolabili.

## <a name="subscription"></a>Limiti relativi alle sottoscrizioni

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Risorsa</th>
<th align="left">Limite predefinito</th>
<th align="left">Limite massimo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Core per <a href="http://msdn.microsoft.com/it-it/library/azure/hh531793.aspx">sottoscrizione</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/it-it/library/azure/gg456328.aspx">Coamministratori</a> per sottoscrizione</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/it-it/documentation/articles/storage-whatis-account/">Account di archiviazione</a> per sottoscrizione</p></td>
<td align="left"><p>100</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/it-it/documentation/articles/cloud-services-what-is/">Servizi cloud</a> per sottoscrizione</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Reti virtuali</a> per sottoscrizione<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/it-it/library/jj157100.aspx">Reti locali</a> per sottoscrizione</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Database SQL per sottoscrizione</p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>Server di database SQL logici per sottoscrizione</p></td>
<td align="left"><p>6</p></td>
<td align="left"><p>150</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Server DNS per sottoscrizione</p></td>
<td align="left"><p>9</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>IP riservati per sottoscrizione</p></td>
<td align="left"><p>5</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Certificati di servizi ospitati per sottoscrizione</p></td>
<td align="left"><p>400</p></td>
<td align="left"><p>400</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/it-it/library/azure/jj156085.aspx">Gruppi di affinità</a> per sottoscrizione</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Le istanze di dimensioni estremamente ridotte vengono conteggiate come un core ai fini del limite dei core anche viene usato un core parziale.

<sup>2</sup>Ogni rete virtuale supporta un solo gateway di rete virtuale.

## <a name="webworkerlimits"></a>Limiti relativi ai servizi cloud

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Risorsa</th>
<th align="left">Limite predefinito</th>
<th align="left">Limite massimo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/it-it/documentation/articles/cloud-services-what-is/">Ruoli di lavoro/Web per distribuzione<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/it-it/library/gg557552.aspx#InstanceInputEndpoint">Endpoint di input istanza</a> per distribuzione</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/it-it/library/gg557552.aspx#InputEndpoint">Endpoint di input</a> per distribuzione</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/it-it/library/gg557552.aspx#InternalEndpoint">Endpoint interni</a> per distribuzione</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Per ogni servizio cloud con ruoli di lavoro/Web ci possono essere due distribuzioni, una per la produzione e l'altra per la gestione temporanea. Si noti, inoltre, che questo limite si riferisce al numero di ruoli distinti (configurazione) e non al numero di istanze per ruolo (scalabilità).

## <a name="vmlimits"></a>Limiti relativi alle macchine virtuali

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Risorsa</th>
<th align="left">Limite predefinito</th>
<th align="left">Limite massimo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/it-it/documentation/services/virtual-machines/">Macchine virtuali</a> per servizio cloud<sup>1</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Endpoint di input per servizio cloud<sup>2</sup></p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>150</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Quando si crea una macchina virtuale, viene creato automaticamente un servizio cloud per contenerla. È quindi possibile aggiungere più macchine virtuali nello stesso servizio cloud.

<sup>2</sup>Gli endpoint di input vengono usati per consentire le comunicazioni con le macchine virtuali esterne al servizio cloud contenitore. Le macchine virtuali nello stesso servizio cloud consentono automaticamente le comunicazioni tra tutte le porte UDP e TCP per le comunicazioni interne.

## <a name="websiteslimits"></a>Limiti relativi ai siti Web

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## <a name="networkinglimits"></a>Limiti relativi alle funzionalità di rete

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Risorsa</th>
<th align="left">Limite predefinito</th>
<th align="left">Limite massimo</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Totale computer<sup>1</sup> per <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">rete virtuale</a><sup>2</sup></p></td>
<td align="left"><p>2048</p></td>
<td align="left"><p>2048</p></td>
</tr>
<tr class="even">
<td align="left"><p>Connessioni TCP simultanee per un'istanza del ruolo o della macchina virtuale</p></td>
<td align="left"><p>500K</p></td>
<td align="left"><p>500K</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Elenchi di controllo di accesso (ACL) per endpoint<sup>3</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Siti di rete locali per rete virtuale</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>10</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Il numero totale di computer include le macchine virtuali e le istanze del ruolo di lavoro/Web.

<sup>2</sup>Ogni rete virtuale supporta un solo [gateway di rete virtuale][gateway di rete virtuale].

<sup>3</sup>L'elenco di controllo di accesso (ACL) è supportato negli endpoint di input per le macchine virtuali. Per i ruoli di lavoro/Web è supportato negli endpoint di input e di input dell'istanza.

## <a name="storagelimits"></a>Limiti relativi all'archiviazione

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Risorsa<sup>1</sup></th>
<th align="left">Limite predefinito</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>TB per account di archiviazione</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="even">
<td align="left"><p>Dimensione massima di un singolo contenitore BLOB o di una singola tabella o coda</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione</p></td>
<td align="left"><p>L'unico limite è quello relativo alla capacità dell'account di archiviazione (500 TB)</p></td>
</tr>
<tr class="even">
<td align="left"><p>Dimensione massima di una condivisione file</p></td>
<td align="left"><p>5 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Numero massimo di file in una condivisione file</p></td>
<td align="left"><p>L'unico limite è quello relativo alla capacità totale della condivisione file (5 TB)</p></td>
</tr>
<tr class="even">
<td align="left"><p>Numero massimo di IOPS da 8 KB per disco persistente (livello Basic)</p></td>
<td align="left"><p>300<sup>2</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>Numero massimo di IOPS da 8 KB per disco persistente (livello Standard)</p></td>
<td align="left"><p>500<sup>2</sup></p></td>
</tr>
<tr class="even">
<td align="left"><p>Frequenza di richiesta totale (presupponendo oggetti con dimensione di 1 KB) per account di archiviazione</p></td>
<td align="left"><p>Fino a 20.000 entità o messaggi al secondo</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Velocità effettiva da raggiungere per BLOB singolo</p></td>
<td align="left"><p>Fino a 60 MB al secondo o fino 500 richieste al secondo</p></td>
</tr>
<tr class="even">
<td align="left"><p>Velocità effettiva da raggiungere per coda singola (messaggi di 1 KB)</p></td>
<td align="left"><p>Fino a 2.000 messaggi al secondo</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Velocità effettiva da raggiungere per partizione di tabella singola (entità di 1 KB)</p></td>
<td align="left"><p>Fino a 2.000 entità al secondo</p></td>
</tr>
<tr class="even">
<td align="left"><p>Numero massimo in entrata per account di archiviazione (aree US)</p></td>
<td align="left"><p>10 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 20 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Numero massimo in uscita per account di archiviazione (aree US)</p></td>
<td align="left"><p>20 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 30 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
<tr class="even">
<td align="left"><p>Numero massimo in entrata per account di archiviazione (aree europee e asiatiche)</p></td>
<td align="left"><p>5 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 10 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Numero massimo in uscita per account di archiviazione (aree europee e asiatiche)</p></td>
<td align="left"><p>10 Gbps se l'archiviazione con ridondanza geografica (GRS)<sup>3</sup> è abilitata, 15 Gbps per archiviazione con ridondanza locale (LRS)</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Per altre informazioni su questi limiti, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure][Obiettivi di scalabilità e prestazioni per Archiviazione di Azure].

<sup>2</sup>Per le macchine virtuali del livello Basic, non posizionare oltre 66 VHD (20.000/300) a uso intensivo in un account di archiviazione per evitare il limite della frequenza di richiesta totale, pari a 20.000. Per le macchine virtuali del livello Standard, non posizionare oltre 40 VHD (20.000/500) a uso intensivo in un account di archiviazione. Per altre informazioni, vedere la pagina [Dimensioni delle macchine virtuali e dei servizi cloud per Azure][Dimensioni delle macchine virtuali e dei servizi cloud per Azure].

<sup>3</sup>GRS è l'acronimo di [Geo Redundant Storage][Geo Redundant Storage]. LRS è l'acronimo di [Locally Redundant Storage][Locally Redundant Storage]. Si noti che l'archiviazione con ridondanza geografica è anche localmente ridondante.

## <a name="documentdblimits"></a>Limiti relativi all'anteprima di DocumentDB

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

## <a name="sqldblimits"></a>Limiti relativi al database SQL

Per i limiti relativi al database SQL, vedere i seguenti argomenti:

-   [Livelli di servizio (edizioni) del database SQL di Azure][Livelli di servizio (edizioni) del database SQL di Azure]
-   [Livelli di servizio e livelli di prestazioni del database SQL di Azure][Livelli di servizio e livelli di prestazioni del database SQL di Azure]
-   [Quote DTU (Database Throughput Unit)][Quote DTU (Database Throughput Unit)]
-   [Limiti delle risorse del database SQL][Limiti delle risorse del database SQL]

## <a name="mediaserviceslimits"></a>Limiti relativi ai servizi multimediali

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## <a name="servicebuslimits"></a>Limiti relativi al bus di servizio

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

## <a name="seealso"></a>Vedere anche

[Informazioni sui limiti e sugli aumenti di Azure][aprire una richiesta di assistenza clienti online senza alcun addebito]

[Dimensioni delle macchine virtuali e dei servizi cloud per Azure][Dimensioni delle macchine virtuali e dei servizi cloud per Azure]

  [Limiti relativi alle sottoscrizioni]: #subscription
  [Limiti relativi ai servizi cloud]: #webworkerlimits
  [Limiti relativi alle macchine virtuali]: #vmlimits
  [Limiti relativi ai siti Web]: #websiteslimits
  [Limiti relativi alle funzionalità di rete]: #networkinglimits
  [Limiti relativi all'archiviazione]: #storagelimits
  [Limiti relativi all'anteprima di DocumentDB]: #documentdblimits
  [Limiti relativi al database SQL]: #sqldblimits
  [Limiti relativi ai servizi multimediali]: #mediaserviceslimits
  [Limiti relativi al bus di servizio]: #servicebuslimits
  [aprire una richiesta di assistenza clienti online senza alcun addebito]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [gateway di rete virtuale]: http://msdn.microsoft.com/it-it/library/azure/jj156210.aspx
  [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Dimensioni delle macchine virtuali e dei servizi cloud per Azure]: http://msdn.microsoft.com/it-it/library/azure/dn197896.aspx
  [Geo Redundant Storage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [Locally Redundant Storage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [Livelli di servizio (edizioni) del database SQL di Azure]: http://msdn.microsoft.com/it-it/library/azure/dn741340.aspx
  [Livelli di servizio e livelli di prestazioni del database SQL di Azure]: http://msdn.microsoft.com/it-it/library/azure/dn741336.aspx
  [Quote DTU (Database Throughput Unit)]: http://msdn.microsoft.com/it-it/library/azure/ee336245.aspx#DTUs
  [Limiti delle risorse del database SQL]: http://msdn.microsoft.com/it-it/library/azure/dn338081.aspx

<properties linkid="azure-subscription-service-limits" urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Provides a list of common Azure subscription and service limits along with maximum values." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Sottoscrizione di Azure e limiti dei servizi, quote e vincoli

Nel seguente documento vengono illustrati alcuni dei limiti più comuni di Microsoft Azure. Si noti che al momento nel documento non vengono trattati tutti i servizi di Azure. Nel tempo questi limiti verranno ampliati e aggiornati in modo da coprire un maggior numero di servizi della piattaforma.

-   [Limiti relativi alle sottoscrizioni][]
-   [Limiti relativi a ruoli di lavoro/Web][]
-   [Limiti relativi alle macchine virtuali][]
-   [Limiti relativi alle funzionalità di rete][]
-   [Limiti relativi all'archiviazione][]
-   [Limiti relativi al database SQL][]

> [WACOM.NOTE] Per aumentare il limite oltre quello **predefinito**, rivolgersi all'[assistenza clienti][]. I limiti non possono essere aumentati oltre il valore **Limite massimo** definito nelle tabelle seguenti. Se non è presente nessuna colonna **Limite massimo**, per la risorsa specificata non sono disponibili limiti regolabili.

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
<td align="left"><p>Core per <a href="http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx">sottoscrizione</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10.000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx">Coamministratori</a> per sottoscrizione</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/">Account di archiviazione</a> per sottoscrizione</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Servizi cloud</a> per sottoscrizione</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Reti virtuali</a> per sottoscrizione<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/jj157100.aspx">Reti locali</a> per sottoscrizione</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
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
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx">Gruppi di affinità</a> per sottoscrizione</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Le istanze di dimensioni estremamente ridotte vengono conteggiate come un core ai fini del limite dei core anche viene usato un core parziale.

<sup>2</sup>Ogni rete virtuale supporta un solo gateway di rete virtuale.

## <a name="webworkerlimits"></a>Limiti relativi a ruoli di lavoro/Web

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
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Ruoli di lavoro/Web per distribuzione<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint">Endpoint di input istanza</a> per distribuzione</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint">Endpoint di input</a> per distribuzione</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint">Endpoint interni</a> per distribuzione</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Per ogni servizio cloud con ruoli di lavoro/Web ci possono essere due distribuzioni, una per la produzione e l'altra per la gestione temporanea.

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
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/services/virtual-machines/">Macchine virtuali</a> per servizio cloud<sup>1</sup></p></td>
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

<sup>2</sup>Ogni rete virtuale supporta un solo [gateway di rete virtuale][].

<sup>3</sup>L'elenco di controllo di accesso (ACL) è supportato negli endpoint di input per le macchine virtuali. Per i ruoli di lavoro/Web è supportato negli endpoint di input e di input dell'istanza.

## <a name="storagelimits"></a>Limiti relativi all'archiviazione<sup>1</sup>

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Risorsa</th>
<th align="left">Limite predefinito</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>TB per account di archiviazione<sup>2</sup></p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>Numero massimo di IOPS per disco persistente</p></td>
<td align="left"><p>500<sup>3</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>Numero massimo di IOPS per account di archiviazione</p></td>
<td align="left"><p>20.000</p></td>
</tr>
<tr class="even">
<td align="left"><p>Numero massimo in entrata per account di archiviazione (aree US)</p></td>
<td align="left"><p>10 Gbps se è abilitato GRS<sup>4</sup>, 20 Gbps se è disabilitato</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Numero massimo in entrata per account di archiviazione (aree europee e asiatiche)</p></td>
<td align="left"><p>5 Gbps se è abilitato GRS<sup>4</sup>, 10 Gbps se è disabilitato</p></td>
</tr>
<tr class="even">
<td align="left"><p>Numero massimo in uscita per account di archiviazione (aree US)</p></td>
<td align="left"><p>20 Gbps se è abilitato GRS<sup>4</sup>, 30 Gbps se è disabilitato</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Numero massimo in uscita per account di archiviazione (aree europee e asiatiche)</p></td>
<td align="left"><p>10 Gbps se è abilitato GRS<sup>4</sup>, 15 Gbps se è disabilitato</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Per altre informazioni su questi limiti, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure][].

<sup>2</sup>Per i BLOB di pagine la capacità usata aumenta solo con le pagine in uso. Ad esempio, nel caso di una macchina virtuale con un disco rigido virtuale da 127 GB di cui solo 30 sono usati dal sistema operativo verranno addebitati solo i costi relativi a 30 GB e non tutti i 127 del disco rigido virtuale.

<sup>3</sup>Non inserire in un account più di 40 dischi rigidi virtuali più usati per evitare di incorrere nel limite delle 20.000 IOPS.

<sup>4</sup>Account di archiviazione con ridondanza geografica

## <a name="sqldblimits"></a>Limiti relativi al database SQL

Per i limiti relativi al database SQL, vedere i seguenti argomenti:

-   [Livelli di servizio (edizioni) del database SQL di Azure][]
-   [Livelli di servizio e livelli di prestazioni del database SQL di Azure][]
-   [Limiti delle risorse del database SQL][]

## <a name="seealso"></a>Vedere anche

[Dimensioni delle macchine virtuali e dei servizi cloud per Azure][]

[Obiettivi di scalabilità e prestazioni per Archiviazione di Azure][]

[Livelli di servizio (edizioni) del database SQL di Azure][]

[Livelli di servizio e livelli di prestazioni del database SQL di Azure][]

[Limiti delle risorse del database SQL][]

  [Limiti relativi alle sottoscrizioni]: #subscription
  [Limiti relativi a ruoli di lavoro/Web]: #webworkerlimits
  [Limiti relativi alle macchine virtuali]: #vmlimits
  [Limiti relativi alle funzionalità di rete]: #networkinglimits
  [Limiti relativi all'archiviazione]: #storagelimits
  [Limiti relativi al database SQL]: #sqldblimits
  [assistenza clienti]: http://azure.microsoft.com/en-us/support/faq/
  [sottoscrizione]: http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx
  [Coamministratori]: http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx
  [Account di archiviazione]: http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/
  [Servizi cloud]: http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/
  [Reti virtuali]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [Reti locali]: http://msdn.microsoft.com/en-us/library/jj157100.aspx
  [Gruppi di affinità]: http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx
  [Endpoint di input istanza]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint
  [Endpoint di input]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint
  [Endpoint interni]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint
  [Macchine virtuali]: http://azure.microsoft.com/en-us/documentation/services/virtual-machines/
  [gateway di rete virtuale]: http://msdn.microsoft.com/en-us/library/azure/jj156210.aspx
  [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Livelli di servizio (edizioni) del database SQL di Azure]: http://msdn.microsoft.com/en-us/library/azure/dn741340.aspx
  [Livelli di servizio e livelli di prestazioni del database SQL di Azure]: http://msdn.microsoft.com/en-us/library/azure/dn741336.aspx
  [Limiti delle risorse del database SQL]: http://msdn.microsoft.com/en-us/library/azure/dn338081.aspx
  [Dimensioni delle macchine virtuali e dei servizi cloud per Azure]: http://msdn.microsoft.com/en-us/library/azure/dn197896.aspx

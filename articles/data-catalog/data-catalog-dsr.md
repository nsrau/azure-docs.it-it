---
title: Origini dati supportate da Catalogo dati di Azure | Microsoft Docs
description: Specifica delle origini dati attualmente supportate.
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: d68ed62022834c9417d820a7a64e30b47f71a1bd
ms.contentlocale: it-it
ms.lasthandoff: 03/31/2017


---

# <a name="azure-data-catalog-supported-data-sources"></a>Origini dati supportate da Catalogo dati di Azure

È possibile pubblicare metadati tramite un'API pubblica, uno strumento di registrazione di tipo ClickOnce o immettendo manualmente le informazioni direttamente nel portale Web di Catalogo dati. La griglia seguente fornisce un riepilogo di tutte le origini supportate oggi dal catalogo e delle relative funzionalità di pubblicazione.  Sono anche elencati gli strumenti dati esterni che ogni origine può avviare dall'interno del portale. La seconda griglia specifica in modo più tecnico le proprietà di connessione delle singole origini dati.


## <a name="list-of-supported-data-sources"></a>Elenco di origini dati supportate

<table>

    <tr>
       <td><b>Oggetto origine dati</b></td>
       <td><b>API</b></td>
       <td><b>Immissione manuale</b></td>
       <td><b>Strumento di registrazione</b></td>
       <td><b>Strumenti di apertura</b></td>
       <td><b>Note</b></td>
    </tr>

    <tr>
      <td>Directory di archivio Azure Data Lake</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File di archivio Azure Data Lake</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>BLOB di Archiviazione di Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directory di archiviazione di Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di archiviazione di Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Directory HDFS</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File HDFS</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella Hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di Hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di Database Oracle</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di Database Oracle</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Altro (asset generico)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dimensione di SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>KPI di SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Misura di SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Report di SQL Server Reporting Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2>Solo server in modalità nativa. Modalità SharePoint non supportata.</font></td>
    </tr>

    <tr>
      <td>Tabella di SQL Server</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione SQL Server</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di SAP Hana</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di DB2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di DB2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File del file system</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directory FTP</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File FTP</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Report HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Endpoint HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Set di entità OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Funzione OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di PostgreSQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di PostgreSQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di SAP Hana</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Oggetto Salesforce</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Elenco SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
  
    <tr>
      <td>Raccolta DocumentDB di Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella ODBC generica</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione ODBC generica</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Per il supporto di altre origini, inviare una richiesta di funzionalità tramite il [forum di Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br>
<br>
## <a name="data-source-reference-specification"></a>Specifica di riferimento per l'origine dati
> [!NOTE]
> La colonna "Struttura DSL" nella tabella seguente elenca solo le proprietà di connessione per il contenitore di proprietà "indirizzo" usate da Azure Data Catalog. Ovvero, l'elenco di proprietà "indirizzo" può contenere altre proprietà di connessione dell'origine dati che Azure Data Catalog mantiene ma non usa.
<table>
    <tr>
       <td><b>Tipo di origine</b></td>
       <td><b>Tipo di asset</b></td>
       <td><b>Tipi di oggetto</b></td>
       <td><b>Struttura DSL<b></td>
    </tr>
    <tr>
      <td>Archivio Azure Data Lake</td>
      <td>Contenitore</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocollo: webhdfs <br>autenticazione: {basic, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Archivio Azure Data Lake</td>
      <td>Tabella</td>
      <td>Directory, file</td>
      <td>
        <font size=2> protocollo: webhdfs <br>autenticazione: {basic, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Archiviazione di Azure</td>
      <td>Contenitore</td>
      <td>Contenitore</td>
      <td>
        <font size=2> protocollo: azure-blobs <br>autenticazione: {azure-access-key} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; contenitore </font>
      </td>
    </tr>
    <tr>
      <td>Archiviazione di Azure</td>
      <td>Tabella</td>
      <td>BLOB, directory</td>
      <td>
        <font size=2> protocollo: azure-blobs <br>autenticazione: {azure-access-key} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; contenitore <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nome </font>
      </td>
    </tr>
    <tr>
      <td>Archiviazione di Azure</td>
      <td>Contenitore</td>
      <td>Contenitore</td>
      <td>
        <font size=2> protocollo: azure-tables <br>autenticazione: {azure-access-key} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account </font>
      </td>
    </tr>
    <tr>
      <td>Archiviazione di Azure</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>
        <font size=2> protocollo: azure-tables <br>autenticazione: {azure-access-key} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nome </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Contenitore</td>
      <td>Cluster virtuale</td>
      <td>
        <font size=2> protocollo: cosmos <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabella</td>
      <td>Flusso, set di flussi, vista</td>
      <td>
        <font size=2> protocollo: cosmos <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Contenitore</td>
      <td>Sito</td>
      <td>
        <font size=2> protocollo: http <br>autenticazione: {none, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        <font size=2> protocollo: http <br>autenticazione: {none, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: db2 <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Tabella</td>
      <td>Tabella, vista</td>
      <td>
        <font size=2> protocollo: db2 <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema </font>
      </td>
    </tr>
    <tr>
      <td>File system</td>
      <td>Tabella</td>
      <td>File</td>
      <td>
        <font size=2> protocollo: file <br>autenticazione: {none, basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; percorso </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabella</td>
      <td>Directory, file</td>
      <td>
        <font size=2> protocollo: ftp <br>autenticazione: {none, basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Contenitore</td>
      <td>HDInsight</td>
      <td>
        <font size=2> protocollo: webhdfs <br>autenticazione: {basic, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Tabella</td>
      <td>Directory, file</td>
      <td>
        <font size=2> protocollo: webhdfs <br>autenticazione: {basic, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: hive <br>autenticazione: {hdinsight, basic, username, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tabella</td>
      <td>Tabella, vista</td>
      <td>
        <font size=2> protocollo: hive <br>autenticazione: {hdinsight, basic, username, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Contenitore</td>
      <td>Sito</td>
      <td>
        <font size=2> protocollo: http <br>autenticazione: {none, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        <font size=2> protocollo: http <br>autenticazione: {none, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Tabella</td>
      <td>Endpoint, file</td>
      <td>
        <font size=2> protocollo: http <br>autenticazione: {none, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: mysql <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabella</td>
      <td>Tabella, vista</td>
      <td>
        <font size=2> protocollo: mysql <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Contenitore</td>
      <td>Contenitore di entità</td>
      <td>
        <font size=2> protocollo: odata <br>autenticazione: {none, basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabella</td>
      <td>Set di entità, funzione</td>
      <td>
        <font size=2> protocollo: odata <br>autenticazione: {none, basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; risorsa </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: oracle <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Tabella</td>
      <td>Tabella, vista</td>
      <td>
        <font size=2> protocollo: oracle <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: postgresql <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Tabella</td>
      <td>Tabella, vista</td>
      <td>
        <font size=2> protocollo: postgresql <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contenitore</td>
      <td>Sito</td>
      <td>
        <font size=2> protocollo: http <br>autenticazione: {none, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Report</td>
      <td>Report, dashboard</td>
      <td>
        <font size=2> protocollo: http <br>autenticazione: {none, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Table</td>
      <td>Data Mashup</td>
      <td>Protocollo 
        <font size=2>: power-query <br>autenticazione: {oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tabella</td>
      <td>Object</td>
      <td>
        <font size=2> protocollo: salesforce-com <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; classe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Contenitore</td>
      <td>Server</td>
      <td>
        <font size=2> protocollo: sap-hana-sql <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>
        <font size=2> protocollo: sap-hana-sql <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabella</td>
      <td>Elenco</td>
      <td>
        <font size=2> protocollo: sharepoint-list <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Comando</td>
      <td>Stored Procedure</td>
      <td>
        <font size=2> protocollo: tds <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Funzione con valori di tabella</td>
      <td>
        <font size=2> protocollo: tds <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: tds <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabella</td>
      <td>Tabella, vista</td>
      <td>
        <font size=2> protocollo: tds <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Comando</td>
      <td>Stored Procedure</td>
      <td>
        <font size=2> protocollo: tds <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Funzione con valori di tabella</td>
      <td>
        <font size=2> protocollo: tds <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: tds <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Table</td>
      <td>Tabella, vista</td>
      <td>
        <font size=2> protocollo: tds <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>Modelli multidimensionali di SQL Server Analysis Services</td>
      <td>Contenitore</td>
      <td>Modello</td>
      <td>
        <font size=2> protocollo: analysis-services <br>autenticazione: {windows, basic, anonymous, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello </font>
      </td>
    </tr>
    <tr>
      <td>Modelli multidimensionali di SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocollo: analysis-services <br>autenticazione: {windows, basic, anonymous, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Modelli multidimensionali di SQL Server Analysis Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocollo: analysis-services <br>autenticazione: {windows, basic, anonymous, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>Modelli multidimensionali di SQL Server Analysis Services</td>
      <td>Tabella</td>
      <td>Dimensione</td>
      <td>
        <font size=2> protocollo: analysis-services <br>autenticazione: {windows, basic, anonymous, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabulare</td>
      <td>Contenitore</td>
      <td>Modello</td>
      <td>
        <font size=2> protocollo: analysis-services <br>autenticazione: {windows, basic, anonymous, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabulare</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocollo: analysis-services <br>autenticazione: {windows, basic, anonymous, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabulare</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocollo: analysis-services <br>autenticazione: {windows, basic, anonymous, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabulare</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>
        <font size=2> protocollo: analysis-services <br>autenticazione: {windows, basic, anonymous, none} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contenitore</td>
      <td>Server</td>
      <td>
        <font size=2> protocollo: reporting-services <br>autenticazione: {windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versione: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Report</td>
      <td>Report</td>
      <td>
        <font size=2> protocollo: reporting-services <br>autenticazione: {windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; percorso <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versione: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: teradata <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabella</td>
      <td>Tabella, vista</td>
      <td>
        <font size=2> protocollo: teradata <br>autenticazione: {protocol, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto </font>
      </td>
    </tr>
    <tr>
      <td>Master Data Services di SQL Server</td>
      <td>Contenitore</td>
      <td>Modello</td>
      <td>
        <font size="2"> protocollo: mssql mds <br>autenticazione: {windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versione </font>
      </td>
    </tr>
    <tr>
      <td>Master Data Services di SQL Server</td>
      <td>Tabella</td>
      <td>Entità</td>
      <td>
        <font size="2"> protocollo: mssql mds <br>autenticazione: {windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versione <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entità </font>
      </td>
    </tr>
    <tr>
      <td>Azure DocumentDB</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>Protocollo 
        <font size=2>: document-db <br>autenticazione: {azure-access-key} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Azure DocumentDB</td>
      <td>Raccolta</td>
      <td>Raccolta</td>
      <td>Protocollo 
        <font size=2>: document-db <br>autenticazione: {azure-access-key} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; raccolta </font>
      </td>
    </tr>
    <tr>
      <td>ODBC generico</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: odbc <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; opzioni <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>ODBC generico</td>
      <td>Tabella</td>
      <td>Tabella, vista</td>
      <td>
        <font size=2> protocollo: odbc <br>autenticazione: {basic, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; opzioni <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema </font>
      </td>
    </tr>
    <tr>
      <td>Altro (nessuna delle opzioni precedenti)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> protocollo: generic-asset <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>


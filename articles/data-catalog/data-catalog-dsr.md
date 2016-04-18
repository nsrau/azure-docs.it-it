<properties
   pageTitle="Origini dati supportate da Catalogo dati di Azure | Microsoft Azure"
   description="Questo articolo elenca tutte le origini dati e i tipi di asset di dati supportati per la registrazione in Azure Data Catalog."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager=""
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/31/2016"
   ms.author="maroche"/>

# Origini dati supportate da Catalogo dati di Azure

Gli utenti di **Azure Data Catalog** possono pubblicare metadati con un'API pubblica, un tool di registrazione delle origini dati di tipo ClickOnce o immettendo manualmente le informazioni direttamente nel portale Web di Azure Data Catalog.

La tabella riportata di seguito fornisce un riepilogo di tutte le origini attualmente supportate da Azure Data Catalog e delle relative funzionalità di pubblicazione. Sono elencati anche gli strumenti dati esterni che ogni origine può avviare dall'interno del portale di Azure Data Catalog.

Ancora oltre è visualizzata una seconda tabella che fornisce una specifica tecnica più ampia di ogni proprietà di connessione delle origini dati e la specifica di riferimento per l'origine dati usata per ogni asset di dati supportato quando si usa l'API di Azure Data Catalog.


## Elenco di origini dati e asset supportati

<table>

    <tr>
       <td><b>Asset di dati</b></td>
       <td><b>API</b></td>
       <td><b>Immissione manuale</b></td>
       <td><b>Strumento di registrazione</b></td>
       <td><b>Strumenti di apertura</b></td>
       <td><b>Note</b></td>
    </tr>

    <tr>
      <td>Directory di archivio Azure Data Lake</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File di archivio Azure Data Lake</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>BLOB di Archiviazione di Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directory di archiviazione di Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directory HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di Database Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di Database Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
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
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dimensione di SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>KPI di SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Misura di SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Report di SQL Server Reporting Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di SAP Hana</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella DB2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione DB2</td>
      <td>✓</td>
      <td></td>
      <td></td>
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
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
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
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista di PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
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
      <td></td>
      <td></td>
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

</table>


<br> <br>

## Specifica di riferimento per l'origine dati

<table>
    <tr>
       <td><b>Tipo di origine</b></td>
       <td><b>Tipo di radice</b></td>
       <td><b>Tipo di oggetto</b></td>
       <td><b>Tipo di oggetto in cui è contenuto</b></td>
       <td><b>Struttura DSR<b></td>
    </tr>


    <tr>
      <td>Archivio Azure Data Lake</td>
      <td>Tabella</td>
      <td>Directory</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocollo: webhdfs
            <br>autenticazione: {basic, oauth}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Archivio Azure Data Lake</td>
      <td>Tabella</td>
      <td>File</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocollo: webhdfs}
            <br>autenticazione: {basic, oauth}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>



    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contenitore</td>
      <td>Server</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: reporting-services
            <br>autenticazione: {windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; version {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Archiviazione di Azure</td>
      <td>Tabella</td>
      <td>BLOB</td>
      <td>Contenitore</td>
      <td>
        <font size=2> protocollo: azure-blobs
            <br>autenticazione: {azure-access-key}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container
            <br>&#160;&#160;&#160;&#160;&#160; name </font>
      </td>
    </tr>

    <tr>
      <td>Archiviazione di Azure</td>
      <td>Tabella</td>
      <td>Directory</td>
      <td>Contenitore</td>
      <td>
        <font size=2> protocollo: azure-blobs
            <br>autenticazione: {azure-access-key}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container
            <br>&#160;&#160;&#160;&#160;&#160; name </font>
      </td>
    </tr>

    <tr>
      <td>Archiviazione di Azure</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Archiviazione di Azure</td>
      <td>
        <font size=2> protocollo: azure-tables
            <br>autenticazione: {azure-access-key}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; name </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Contenitore</td>
      <td>Modello</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: analysis-services
            <br>autenticazione: {windows, basic, anonymous}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>
            <br>* basic/anonymous disponibile solo tramite HTTPS* </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: db2
            <br>autenticazione: {basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: db2
            <br>autenticazione: {basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: db2
            <br>autenticazione: {basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>File system</td>
      <td>Tabella</td>
      <td>File</td>
      <td>Server</td>
      <td>
        <font size=2> protocollo: file
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; path </font>
      </td>
    </tr>

    <tr>
      <td>FTP</td>
      <td>Tabella</td>
      <td>Directory</td>
      <td>Server</td>
      <td>
        <font size=2> protocollo: ftp
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>FTP</td>
      <td>Tabella</td>
      <td>File</td>
      <td>Server</td>
      <td>
        <font size=2> protocollo: ftp
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services tabulare</td>
      <td>Contenitore</td>
      <td>Modello</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: analysis-services
            <br>autenticazione: {windows, basic, anonymous}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>
            <br>* basic/anonymous disponibile solo tramite HTTPS* </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Tabella</td>
      <td>Directory</td>
      <td>HDInsight</td>
      <td>
        <font size=2> protocollo: webhdfs
            <br>autenticazione: {basic, oauth}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Tabella</td>
      <td>File</td>
      <td>HDInsight</td>
      <td>
        <font size=2> protocollo: webhdfs
            <br>autenticazione: {basic, oauth}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Archivio Azure Data Lake</td>
      <td>Contenitore</td>
      <td>Data Lake</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: webhdfs
            <br>autenticazione: {basic, oauth}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: hive
            <br>autenticazione: {hdinsight, basic, username}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: hive
            <br>autenticazione: {hdinsight, basic, username}
            <br>indirizzo: server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Archiviazione di Azure</td>
      <td>Contenitore</td>
      <td>Archiviazione di Azure</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: azure-tables
            <br>autenticazione: {azure-access-key}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Contenitore</td>
      <td>Sito</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: http
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Report</td>
      <td>Report</td>
      <td>Sito</td>
      <td>
        <font size=2> protocollo: http
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Tabella</td>
      <td>Endpoint</td>
      <td>Sito</td>
      <td>
        <font size=2> protocollo: http
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Archiviazione di Azure</td>
      <td>Contenitore</td>
      <td>Contenitore</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: azure-blobs
            <br>autenticazione: {Azure-Access-Key}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: mysql
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: mysql
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Contenitore</td>
      <td>HDInsight</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: webhdfs
            <br>autenticazione: {basic, oauth}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Tabella</td>
      <td>File</td>
      <td>Sito</td>
      <td>
        <font size=2> protocollo: http
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>Contenitore</td>
      <td>Contenitore di entità</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: odata
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: hiveserver2
            <br>autenticazione: {hdinsight, basic, username, none}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; port
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>Tabella</td>
      <td>Set di entità</td>
      <td>Contenitore di entità</td>
      <td>
        <font size=2> protocollo: odata
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; URL
            <br>&#160;&#160;&#160;&#160;&#160; resource </font>
      </td>
    </tr>

    <tr>
      <td>Oracle Database</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: oracle
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Oracle Database</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: oracle
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>Tabella</td>
      <td>Funzione</td>
      <td>Contenitore di entità</td>
      <td>
        <font size=2> protocollo: odata
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; URL
            <br>&#160;&#160;&#160;&#160;&#160; resource </font>
      </td>
    </tr>

    <tr>
      <td>Altri</td>
      <td>Tabella</td>
      <td>Altri</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: generic-asset
            <br>autenticazione: {none, basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>

    <tr>
      <td>PostgreSQL</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: postgresql
            <br>autenticazione: {basic, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>PostgreSQL</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: postgresql
            <br>autenticazione: {basic, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>PostgreSQL</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: postgresql
            <br>autenticazione: {basic, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: mysql
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>Server</td>
      <td>
        <font size=2> protocollo: sap-hana-sql
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Salesforce</td>
      <td>Tabella</td>
      <td>Oggetto</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: salesforce-com
            <br>autenticazione: {basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>

    <tr>
      <td>SharePoint</td>
      <td>Tabella</td>
      <td>Elenco</td>
      <td>Sito</td>
      <td>
        <font size=2> protocollo: sharepoint-list
            <br>autenticazione: {basic, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: tds
            <br>autenticazione: {sql, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: tds
            <br>autenticazione: {sql, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: tds
            <br>autenticazione: {sql, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Tabella</td>
      <td>Funzione con valori di tabella</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: tds
            <br>autenticazione: {sql, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: tds
            <br>autenticazione: {sql, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Oracle Database</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: oracle
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>Modello</td>
      <td>
        <font size=2> protocollo: analysis-services
            <br>autenticazione: {windows, basic, anonymous}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Kpi}
            <br>
            <br>* basic/anonymous disponibile solo tramite HTTPS* </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>Modello</td>
      <td>
        <font size=2> protocollo: analysis-services
            <br>autenticazione: {windows, basic, anonymous}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Tabella</td>
      <td>Dimensione</td>
      <td>Modello</td>
      <td>
        <font size=2> protocollo: analysis-services
            <br>autenticazione: {windows, basic, anonymous}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Dimension} </font>
      </td>
    </tr>

    <tr>
      <td>SAP Hana</td>
      <td>Contenitore</td>
      <td>Server</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: sap-hana-sql
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services tabulare</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Modello</td>
      <td>
        <font size=2> protocollo: analysis-services
            <br>autenticazione: {windows, basic, anonymous}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Table}
            <br>
            <br>* basic/anonymous disponibile solo tramite HTTPS* </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: tds
            <br>autenticazione: {sql, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: tds
            <br>autenticazione: {sql, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Report</td>
      <td>Report</td>
      <td>Server</td>
      <td>
        <font size=2> protocollo: reporting-services
            <br>autenticazione: {windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; version {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>N/D</td>
      <td>
        <font size=2> protocollo: teradata
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Tabella</td>
      <td>Tabella</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: teradata
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Tabella</td>
      <td>Visualizza</td>
      <td>Database</td>
      <td>
        <font size=2> protocollo: teradata
            <br>autenticazione: {protocol, windows}
            <br>indirizzo:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>



</table>

<!---HONumber=AcomDC_0406_2016-->
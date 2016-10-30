<properties
   pageTitle="Anteprima della risoluzione dei problemi di Microsoft Power BI Embedded"
   description="Anteprima della risoluzione dei problemi di Microsoft Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>


# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Anteprima della risoluzione dei problemi di Microsoft Power BI Embedded
Questo articolo fornisce alcune risposte per la risoluzione dei problemi di **Power BI Embedded**.

<a name="connection-string"/>
## <a name="setting-sql-server-connection-strings"></a>Impostare le stringhe di connessione di SQL Server
Per impostare una stringa di connessione di SQL Server, è necessario seguire un formato specifico. Di seguito viene specificata una stringa di connessione di esempio per SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Per altre informazioni sulle stringhe di connessione di SQL Server, vedere gli articoli seguenti:

-   [Stringhe di connessione di SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-   [Proprietà SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## <a name="setting-credentials"></a>Impostazione delle credenziali
Nel caso in cui si abbiano credenziali per un ambiente di sviluppo o di gestione temporanea, ad esempio nome utente e password, potrebbe essere necessario aggiornare le credenziali che corrispondono a una soluzione di produzione.

## <a name="see-also"></a>Vedere anche
- [Esempio introduttivo](power-bi-embedded-get-started-sample.md)
- [Informazioni su Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)



<!--HONumber=Oct16_HO2-->




<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### File di configurazione di esempio per la sicurezza delle stringhe di connessione
Non è corretto inserire la stringa di connessione come valori letterali nel codice C#. È consigliabile inserire la stringa di connessione nel file di configurazione. Lì è possibile modificare la stringa in qualsiasi momento senza dover ricompilare.

Si supponga che il programma compilato C# sia denominato **ConsoleApplication1.exe**, e che questo .exe risieda in una directory **bin\\debug**.

In questo esempio, la maggior parte delle parti della stringa di connessione vengono archiviate in un file di configurazione denominato esattamente **ConsoleApplication1.exe.config**. Questo file di configurazione deve risiedere anche in **bin\\debug**.

Nel codice XML del file di configurazione seguente viene visualizzata una stringa di connessione denominata **ConnectionString4NoUserIDNoPassword**. Il codice C# cerca questa stringa.

È necessario modificare i nomi reali per i segnaposto:

* {your\_serverName\_here}
* {your\_databaseName\_here}

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>

            <connectionStrings>
                <clear />
                <add name="ConnectionString4NoUserIDNoPassword"
                providerName="System.Data.ProviderName"

                connectionString=
                "Server=tcp:{your_serverName_here}.database.windows.net,1433;
                Database={your_databaseName_here};
                Connection Timeout=30;
                Encrypt=True;
                TrustServerCertificate=False;" />
            </connectionStrings>
        </configuration>



Per questa figura è stato scelto di omettere i due parametri:

* ID utente = {your\_userName\_here};
* Password = {your\_password\_here};

È possibile includerli, ma a volte è preferibile far ottenere i valori dal programma tramite inserimento nella tastiera dell'utente. Dipende.

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->
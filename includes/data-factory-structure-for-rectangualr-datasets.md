## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Definizione della struttura per i set di dati rettangolari
La sezione della struttura nei set di dati JSON è una sezione **facoltativa** per le tabelle rettangolari (con righe e colonne) e contiene una raccolta di colonne per la tabella. Usare la sezione della struttura per offrire informazioni sul tipo per le conversioni di tipi o per eseguire il mapping di colonne. Le sezioni seguenti descrivono queste funzionalità in modo più dettagliato. 

Ogni colonna contiene le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| name |Nome della colonna. |Sì |
| type |Tipo di dati della colonna. Vedere la sezione sulle conversioni del tipo di seguito per altre informazioni su quando specificare le informazioni sul tipo |No |
| culture |Cultura basata su .NET da usare quando il tipo è specificato e corrisponde al tipo .NET Datetime o Datetimeoffset. Il valore predefinito è "en-us". |No |
| format |Stringa di formato da usare quando il tipo è specificato e corrisponde al tipo .NET Datetime o Datetimeoffset. |No |

L'esempio seguente mostra la sezione di struttura JSON per una tabella con tre colonne: userid, name e lastlogindate.

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Attenersi alle linee guida seguenti per decidere quando includere informazioni su "structure" e cosa includere nella sezione **structure** .

* **Per le origini dati strutturate** che contengono schemi di dati e informazioni sul tipo, oltre ai dati stessi (origini come SQL Server, Oracle, tabelle di Azure e così via), è necessario specificare la sezione "structure" solo se si desidera eseguire il mapping di colonne di origine specifiche a colonne specifiche nel sink e i relativi nomi non corrispondono (vedere i dettagli nella sezione sul mapping di colonne di seguito). 
  
    Come indicato in precedenza, le informazioni sul tipo sono facoltative nella sezione "structure". Per le origini strutturate, le informazioni sul tipo sono già disponibili come parte della definizione del set di dati nell'archivio dati, pertanto non è necessario includere le informazioni sul tipo quando si include la sezione "structure".
* **Per gli schemi delle origini dati di lettura, in particolare BLOB di Azure**, è possibile scegliere di archiviare i dati senza memorizzare insieme ai dati informazioni sullo schema o sul tipo. Per questi tipi di origini dati è necessario includere la sezione "structure" nei 2 casi seguenti:
  * Si desidera eseguire il mapping di colonne.
  * Quando il set di dati è un'origine in un'attività di copia, è possibile specificare informazioni sul tipo in "structure", che verranno usate da Data factory per la conversione a tipi nativi per il sink. Vedere l'articolo sullo [spostamento di dati da e verso BLOB di Azure](../articles/data-factory/data-factory-azure-blob-connector.md) per altre informazioni.

### <a name="supported-net-based-types"></a>Tipi supportati basati su .NET
Data factory supporta i valori di tipo basati su .NET conformi a CLS per specificare informazioni sul tipo nella sezione "structure" per lo schema su origini dati di lettura come BLOB di Azure.

* Int16
* Int32 
* Int64
* Single
* Double
* Decimale
* Byte[]
* Booleano
* String 
* Guid
* Datetime
* Datetimeoffset
* TimeSpan 

Per Datetime e Datetimeoffset è anche possibile specificare la stringa "culture" e "format" per facilitare l'analisi della stringa Datetime personalizzata. Vedere l'esempio seguente di conversione del tipo.



<!--HONumber=Nov16_HO3-->



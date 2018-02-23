
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>Esempio di programma C#

Le sezioni successive di questo articolo presentano un programma C# che usa ADO.NET per inviare istruzioni Transact-SQL al database SQL. Il programma C# esegue le azioni seguenti:

1. [Connessione al database SQL tramite ADO.NET](#cs_1_connect).
2. [Creazione di tabelle](#cs_2_createtables).
3. [Popolamento delle tabelle con dati, tramite l'emissione di istruzioni T-SQL INSERT](#cs_3_insert).
4. [Aggiornamento dei dati tramite un join](#cs_4_updatejoin).
5. [Eliminazione dei dati tramite un join](#cs_5_deletejoin).
6. [Selezione di righe di dati tramite join](#cs_6_selectrows).
7. Chiusura della connessione, con rilascio di eventuali tabelle temporanee da tempdb.

Il programma C# contiene:

- Codice C# per la connessione al database.
- Metodi che restituiscono il codice sorgente di T-SQL.
- Due metodi che inviano il codice T-SQL al database.

#### <a name="to-compile-and-run"></a>Per la compilazione e l'esecuzione

Questo programma C# è costituito logicamente da un file con estensione cs. In questo caso il programma è suddiviso fisicamente in diversi blocchi di codice, in modo da semplificare la visualizzazione e la comprensione di ogni blocco. Per compilare ed eseguire questo programma, seguire questa procedura:

1. Creare un progetto C# in Visual Studio.
    - Il tipo di progetto deve essere un'applicazione *console*, da un elemento simile alla gerarchia seguente: **Modelli** > **Visual C#** > **Desktop classico di Windows** > **App console (.NET Framework)**.
3. Nel file **Program.cs** cancellare le brevi righe iniziali del codice.
3. In Program.cs copiare e incollare ogni blocco seguente nella stessa sequenza riportata.
4. In Program.cs modificare i valori seguenti nel metodo **Main**:

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. Verificare che sia presente un riferimento all'assembly **System.Data.dll**. Per la verifica, espandere il nodo **Riferimenti** nel riquadro **Esplora soluzioni**.
6. Per compilare il programma in Visual Studio, fare clic sul menu **Compila**.
7. Per eseguire il programma da Visual Studio, fare clic sul pulsante **Start**. L'output del report viene visualizzato in una finestra di cmd.exe.

> [!NOTE]
> È possibile modificare il codice T-SQL per aggiungere un carattere **#** davanti ai nomi di tabella, in modo da crearle come tabelle temporanee in **tempdb**. Ciò può risultare utile per finalità dimostrative, quando non sono disponibili database di test. Le tabelle temporanee vengono eliminate automaticamente alla chiusura della connessione. Eventuali parole chiave REFERENCES per chiavi esterne non vengono applicate per le tabelle temporanee.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>Blocco C# 1: Connessione tramite ADO.NET

- [Avanti](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>Blocco C# 2: T-SQL per la creazione di tabelle

- [Indietro](#cs_1_connect)&nbsp; / &nbsp;[Avanti](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>Diagramma entità-relazione

Le istruzioni CREATE TABLE precedenti usano la parola chiave **REFERENCES** per creare una relazione *chiavi esterne* tra le due tabelle.  Se si usa tempdb, impostare come commento la parola chiave `--REFERENCES` usando una coppia di trattini iniziali.

Il diagramma entità-relazione mostra la relazione tra le due tabelle. I valori nella colonna #tabEmployee.DepartmentCode *figlio* sono limitati ai valori presenti nella colonna #tabDepartment.Department *padre*.

![Diagramma entità-relazione che mostra una chiave esterna](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>Blocco C# 3: T-SQL per l'inserimento di dati

- [Indietro](#cs_2_createtables)&nbsp; / &nbsp;[Avanti](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>Blocco C# 4: T-SQL per l'aggiornamento tramite join

- [Indietro](#cs_3_insert)&nbsp; / &nbsp;[Avanti](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>Blocco C# 5: T-SQL per l'eliminazione tramite join

- [Indietro](#cs_4_updatejoin)&nbsp; / &nbsp;[Avanti](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>Blocco C# 6: T-SQL per la selezione di righe

- [Indietro](#cs_5_deletejoin)&nbsp; / &nbsp;[Avanti](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>Blocco C# 6b: ExecuteReader

- [Indietro](#cs_6_selectrows)&nbsp; / &nbsp;[Avanti](#cs_7_executenonquery)

Questo metodo è progettato per l'esecuzione dell'istruzione T-SQL SELECT compilata dal metodo **Build_6_Tsql_SelectEmployees**.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>Blocco C# 7: ExecuteNonQuery

- [Indietro](#cs_6b_datareader)&nbsp; / &nbsp;[Avanti](#cs_8_output)

Questo metodo viene chiamato per operazioni che modificano il contenuto dei dati delle tabelle senza restituire alcuna riga di dati.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>Blocco C# 8: Output del test effettivo nella console

- [Indietro](#cs_7_executenonquery)

Questa sezione acquisisce l'output inviato dal programma alla console. Solo i valori GUID variano tra le esecuzioni dei test.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```

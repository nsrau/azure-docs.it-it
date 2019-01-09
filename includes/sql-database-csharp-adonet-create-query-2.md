---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: e30651cb0ed7d74082163a92acbc428c21018255
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728577"
---
## <a name="c-program-example"></a>Esempio di programma C#

Le sezioni successive di questo articolo presentano un programma C# che usa ADO.NET per inviare istruzioni Transact-SQL (T-SQL) al database SQL. Il programma C# dimostra le azioni seguenti:

- [Connessione al database SQL con ADO.NET](#cs_1_connect)
- [Metodi che restituiscono istruzioni T-SQL](#cs_2_return)
    - Creare tabelle
    - Popolare le tabelle con dati
    - Aggiornare, eliminare e selezionare i dati
- [Inviare T-SQL al database](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>Diagramma entità-relazione

Le istruzioni `CREATE TABLE` usano la parola chiave **REFERENCES** per creare una relazione *chiavi esterne* tra le due tabelle. Se si usa *tempdb*, impostare come commento la parola chiave `--REFERENCES` usando una coppia di trattini iniziali.

Il diagramma entità-relazione mostra la relazione tra le due tabelle. I valori nella colonna **tabEmployee.DepartmentCode** *figlio* sono limitati ai valori presenti nella colonna **tabDepartment.DepartmentCode** *padre*.

![Diagramma entità-relazione che mostra una chiave esterna](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> È possibile modificare il codice T-SQL per aggiungere un carattere `#` davanti ai nomi di tabella, in modo da crearle come tabelle temporanee in *tempdb*. Ciò risulta utile per finalità dimostrative, quando non sono disponibili database di test. Qualsiasi riferimento alle chiavi esterne non viene applicato durante l'uso e le tabelle temporanee vengono eliminate automaticamente alla chiusura della connessione dopo che il programma termina l'esecuzione.

### <a name="to-compile-and-run"></a>Per la compilazione e l'esecuzione

Il programma C# è logicamente un unico file con estensione cs ed è suddiviso fisicamente in diversi blocchi di codice, in modo da semplificare la comprensione di ogni blocco. Per compilare ed eseguire il programma, seguire questa procedura:

1. Creare un progetto C# in Visual Studio. Il tipo di progetto deve essere un'applicazione *console*, da un elemento simile alla gerarchia seguente: **Modelli** > **Visual C#** > **Desktop classico di Windows** > **App console (.NET Framework)**.

1. Nel file *Program.cs* sostituire le righe iniziali del codice con i passaggi seguenti:

    1. Copiare e incollare i blocchi di codice seguenti nella stessa sequenza in cui sono visualizzati; vedere [Connettersi al database](#cs_1_connect), [Generare T-SQL](#cs_2_return) e [Inviare al database](#cs_3_submit).

    1. Modificare i valori seguenti nel metodo `Main`:

        - *cb.DataSource*
        - *cb.UserID*
        - *cb.Password*
        - *cb.InitialCatalog*

1. Verificare che sia presente un riferimento all'assembly *System.Data.dll*. Per la verifica, espandere il nodo **Riferimenti** nel riquadro **Esplora soluzioni**.

1. Per compilare ed eseguire il programma da Visual Studio, selezionare il pulsante **Start**. L'output del report viene visualizzato in una finestra del programma, anche se i valori GUID variano tra le esecuzioni dei test.

    ```Output
    =================================
    T-SQL to 2 - Create-Tables...
    -1 = rows affected.

    =================================
    T-SQL to 3 - Inserts...
    8 = rows affected.

    =================================
    T-SQL to 4 - Update-Join...
    2 = rows affected.

    =================================
    T-SQL to 5 - Delete-Join...
    2 = rows affected.

    =================================
    Now, SelectEmployees (6)...
    8ddeb8f5-9584-4afe-b7ef-d6bdca02bd35 , Alison , 20 , acct , Accounting
    9ce11981-e674-42f7-928b-6cc004079b03 , Barbara , 17 , hres , Human Resources
    315f5230-ec94-4edd-9b1c-dd45fbb61ee7 , Carol , 22 , acct , Accounting
    fcf4840a-8be3-43f7-a319-52304bf0f48d , Elle , 15 , NULL , NULL
    View the report output here, then press any key to end the program...
    ```

<a name="cs_1_connect"/>

### <a name="connect-to-sql-database-using-adonet"></a>Connessione al database SQL con ADO.NET

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

                    Submit_Tsql_NonQuery(connection, "2 - Create-Tables", Build_2_Tsql_CreateTables());

                    Submit_Tsql_NonQuery(connection, "3 - Inserts", Build_3_Tsql_Inserts());

                    Submit_Tsql_NonQuery(connection, "4 - Update-Join", Build_4_Tsql_UpdateJoin(),
                        "@csharpParmDepartmentName", "Accounting");

                    Submit_Tsql_NonQuery(connection, "5 - Delete-Join", Build_5_Tsql_DeleteJoin(),
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

<a name="cs_2_return"/>

### <a name="methods-that-return-t-sql-statements"></a>Metodi che restituiscono istruzioni T-SQL

```csharp
static string Build_2_Tsql_CreateTables()
{
    return @"
        DROP TABLE IF EXISTS tabEmployee;
        DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.

        CREATE TABLE tabDepartment
        (
            DepartmentCode  nchar(4)          not null    PRIMARY KEY,
            DepartmentName  nvarchar(128)     not null
        );

        CREATE TABLE tabEmployee
        (
            EmployeeGuid    uniqueIdentifier  not null  default NewId()    PRIMARY KEY,
            EmployeeName    nvarchar(128)     not null,
            EmployeeLevel   int               not null,
            DepartmentCode  nchar(4)              null
            REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
        );
    ";
}

static string Build_3_Tsql_Inserts()
{
    return @"
        -- The company has these departments.
        INSERT INTO tabDepartment (DepartmentCode, DepartmentName)
        VALUES
            ('acct', 'Accounting'),
            ('hres', 'Human Resources'),
            ('legl', 'Legal');

        -- The company has these employees, each in one department.
        INSERT INTO tabEmployee (EmployeeName, EmployeeLevel, DepartmentCode)
        VALUES
            ('Alison'  , 19, 'acct'),
            ('Barbara' , 17, 'hres'),
            ('Carol'   , 21, 'acct'),
            ('Deborah' , 24, 'legl'),
            ('Elle'    , 15, null);
    ";
}

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

<a name="cs_3_submit"/>

### <a name="submit-t-sql-to-the-database"></a>Inviare T-SQL al database

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
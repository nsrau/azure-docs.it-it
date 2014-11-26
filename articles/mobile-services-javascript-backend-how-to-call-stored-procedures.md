<properties pageTitle="Calling SQL stored procedures with a JavaScript back end" metaKeywords="stored procedures, SQL, mobile devices, Azure" description="explains how to use SQL stored procedures in a mobile services JavaScript backed." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Calling SQL stored procedures with a JavaScript back end" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Chiamata di stored procedure SQL con un back-end JavaScript

Motivi per cui potrebbe essere utile chiamare stored procedure SQL Server:

-   il servizio mobile usa stored procedure esistenti a cui è necessario accedere
-   l'architettura dell'applicazione prevede l'uso di stored procedure per l'accesso al database
-   preferenza personale

Questo argomento descrive come eseguire le operazioni seguenti:

-   [Chiamare una stored procedure semplice][Chiamare una stored procedure semplice]
-   [Chiamare una stored procedure con parametri][Chiamare una stored procedure con parametri]
-   [Altre informazioni][Altre informazioni]

Questo articolo descrive le stored procedure di un servizio mobile con un back-end JavaScript.

È però possibile creare servizi mobili con un back-end .Net, la cui architettura è completamente diversa e si basa su Entity Framework. Per altre informazioni, vedere [come funziona il back-end .Net di Servizi mobili di Azure][come funziona il back-end .Net di Servizi mobili di Azure].

## Chiamare una stored procedure semplice

**"Se un database usa le stored procedure, come è possibile chiamarle da un servizio mobile?"**

Questa operazione viene eseguita con l'[oggetto mssql][oggetto mssql], che consente di eseguire istruzioni *Transact-SQL* ("T-SQL") che chiamano la stored procedure.

### Scrittura e test del codice Transact SQL

Si supponga che la stored procedure si chiami *GetAll* e il servizio mobile **todolist**.

1.  Selezionare l'icona **Database** nella barra laterale del portale di Azure, quindi selezionare il database, il cui nome corrisponde a quello del servizio mobile, con il suffisso "\*\*\_db\*\*".

2.  In fondo alla pagina di avvio rapido del database scegliere il collegamento **Gestisci**.

3.  Accedere al database.

4.  Scegliere **Nuova query** nella barra superiore e aggiungere il codice seguente:

            EXEC todolist.GetAll

5.  Fare clic su **Esegui** e verificare i risultati.

Si noti come il nome della stored procedure sia preceduto dal nome dello schema del database, che per impostazione predefinita corrisponde al nome del servizio mobile.

### Dove si inserisce la chiamata all'oggetto mssql?

Se occorre chiamare la stored procedure direttamente, la soluzione più flessibile è scrivere e chiamare un'[API personalizzata][API personalizzata].

1.  Nel dashboard del servizio mobile fare clic su **API**, quindi su **CREA**, specificare il nome ***getall*** per lo script e aggiungere il codice seguente per chiamare la stored procedure:

        exports.get = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "EXEC todolist.GetAll";
            mssql.query(sql, {
                success: function(results) {                          
                        response.send(200, results); 
                }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

2.  Testare il funzionamento in un browser inserendo l'URL seguente nella riga dell'indirizzo:

        https://todolist.azure-mobile.net/api/getall

In alternativa, è possibile inserire codice simile in uno script del server standard di *lettura*, *inserimento*, *aggiornamento* o *eliminazione* di una tabella per fare in modo che il codice ignori il comportamento predefinito e chiami la stored procedure. Codice simile può essere inserito in uno script dell'*Utilità di pianificazione*. È però più flessibile creare un'API personalizzata.

### Chiamata del codice dal client

Modificare il codice del client in modo che chiami il metodo **invokeApi** sull'oggetto **MobileServiceClient**. La sintassi esatta del codice è specifica del dispositivo client ed è descritta negli argomenti seguenti:

-   [Windows Store C#][Windows Store C#]
-   [Windows Store JavaScript][Windows Store JavaScript]
-   [Windows Phone][Windows Phone]
-   [iOS][iOS]
-   [Android][Android]
-   [HTML][HTML]

## <a name="parameters"></a>Chiamare una stored procedure con parametri

Si supponga che la stored procedure si chiami *ItemsByStatus*, che contenga un solo parametro denominato *Status* e che la si voglia chiamare da un'API personalizzata.

1.  Seguire la procedura descritta nella sezione precedente per scrivere e testare il codice T-SQL tramite il portale del database di Azure, ma usare questo codice T-SQL:

        EXEc todolist.ItemsByStatus @Status = 1

2.  Fare clic su **Esegui** e verificare i risultati.

3.  Come nel passaggio precedente, creare un nuova API personalizzata denominata **completeall** con il codice seguente che chiama la stored procedure. Si noti che nel codice seguente il valore effettivo del parametro <**@Status*>\* usato per il test viene sostituito con **"?"**, compilato in fase di esecuzione con il parametro fornito.

        exports.get = function(request, response) { 
            var mssql = request.service.mssql;
            var param1 = parseInt(request.query.status);
            var sql = "EXEC todolist.ItemsByStatus @Status = ?";
            mssql.query(sql, [param1], {
                success: function(results) { 
                         response.send(200, results[0]); 
                    }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

4.  Testare l'API con questo URL in un browser:

        https://todolist.azure-mobile.net/api/completeall?status=1

5.  Chiamare l'API dal client come descritto nella sezione precedente.

### Firme di parametro più complesse

La stored procedure della sezione precedente ha un solo parametro. Di seguito è riportata la sintassi da usare per una firma più lunga:

        function read(query, user, request) {
            var sql = 'EXEC MySProc @Param1 = ?, @Param2 = ?, @Param3 = ?';
            var param1 = request.parameters.first;
            var param2 = request.parameters.second;
            var param3 = request.parameters.third;
            mssql.query(sql, [param1, param2, param3], {
                success: function(results) {
                    request.respond(200, results);
                }
            });
        }

Si noti che nell'istruzione **EXEC** i parametri vengono chiamati per nome invece che per posizione. Questa è la sintassi consigliata, in quanto isola il codice da piccole modifiche della stored procedure, ad esempio l'ordine dei parametri o l'aggiunta di parametri facoltativi.

## <a name="more"></a>Altre informazioni

Questo argomento è molto generale.

Per informazioni dettagliate sul codice usato, vedere gli argomenti seguenti:

-   -   

Ecco altri scenari che è possibile incontrare:

-   Per informazioni su come eseguire la lettura condizionale di una tabella o chiamare in alternativa una stored procedure, vedere [come eseguire più operazioni di lettura tramite script][come eseguire più operazioni di lettura tramite script]. Il blogger Carlos Figueira scrive spesso post su Servizi mobili di Azure e database.

-   Nel blog relativo all'[accesso a una stored procedure da uno schema diverso][accesso a una stored procedure da uno schema diverso] sono descritte le soluzioni per i problemi che è possibile incontrare durante l'accesso alle stored procedure che si trovano in schemi diversi dello stesso database di servizi mobili.

È anche possibile usare il portale di Azure per gestire e creare nuove stored procedure.




  [Chiamare una stored procedure semplice]: #simple
  [Chiamare una stored procedure con parametri]: #parameters
  [Altre informazioni]: #more
  [come funziona il back-end .Net di Servizi mobili di Azure]: http://curah.microsoft.com/64518/how-the-azure-mobile-services-net-backend-works
  [oggetto mssql]: http://msdn.microsoft.com/it-it/library/windowsazure/jj554212.aspx
  [API personalizzata]: http://msdn.microsoft.com/it-it/library/windowsazure/dn280974.aspx
  [Windows Store C#]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Windows Store JavaScript]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-windows-store-javascript-call-custom-api/
  [Windows Phone]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-windows-phone-call-custom-api/
  [iOS]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-ios-call-custom-api/
  [Android]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-android-call-custom-api/
  [HTML]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-html-call-custom-api/
  [come eseguire più operazioni di lettura tramite script]: http://social.msdn.microsoft.com/Forums/windowsazure/it-it/fccf4ae7-f43c-4c2d-8518-32e2df84a824/how-do-i-do-more-than-1-read-operation-by-scripting?forum=azuremobile
  [accesso a una stored procedure da uno schema diverso]: http://blogs.msdn.com/b/jpsanders/archive/2013/05/02/windows-azure-mobile-services-accessing-a-stored-procedure-from-a-different-schema.aspx

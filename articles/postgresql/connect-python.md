---
title: Lettura Python dal Database di Azure per PostgreSQL | Microsoft Docs
description: Supponendo di non avere alcuna conoscenza precedente, in questo articolo viene illustrato come eseguire un esempio di codice Python per scrivere e leggere i dati da una tabella in un Database di Azure per PostgreSQL.
services: postgresql
author: MightyPen
ms.author: genemi
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f5e8191e52d1e2a4502a961158b6fec1c061d1
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="python-reading-from-azure-database-for-postgresql"></a>Lettura Python dal Database di Azure per PostgreSQL


L'articolo indica un breve programma Python che usa un'istruzione SQL SELECT per leggere una riga da una tabella. L'articolo spiega anche come trovare e installare tutti i prerequisiti necessari per eseguire il programma Python.

Il programma Python di esempio e gli strumenti correlati a Python descritti, vengono tutti applicati ugualmente bene a diverse piattaforme, tra cui Windows, Mac e Linux. 


## <a name="install-the-python-interpreter"></a>Installare l'interprete Python


L'esempio di codice Python in questo articolo è stato scritto per la versione 2.7 dell'interprete Python e non può essere eseguito dalla versione 3. x.

Scaricare e installare la versione 2.7 dell'interprete Python da:

- [Scaricare l'interprete Python da python.org](https://www.python.org/downloads/)

Dopo l'installazione, tentare di trovare ed eseguire l'interprete nella riga di comando. Usare un comando, ad esempio:

`python.exe -?`


## <a name="install-pipexe-the-python-module-installer"></a>Installare pip.exe, il programma di installazione del modulo Python


L'installazione dell'interprete Python potrebbe avere anche installato pip.exe, magari in una directory secondaria denominata *Scripts/*. PIP.exe installa i moduli Python specializzati. Tentare di trovare ed eseguire pip.exe.

`pip.exe`

Se non è possibile eseguire pip.exe, vedere se si dispone del file relativo al programma di utilità Python denominato **get-pip.py**. Se si dispone del file get-pip.py, è possibile eseguirlo in modo da ottenere il file pip.exe:

`python.exe get-pip.py`


## <a name="install-psycopg-the-connection-module"></a>Installare psycopg, il modulo di connessione


Il programma Python richiede un modulo che sa come connettere il programma al Database di Azure per server PostgreSQL. Il nome del modulo di connessione è **psycopg2**. Per maggiori dettagli, vedere:

- [sito Web psycopg2](http://initd.org/psycopg/)

Installare psycopg2 usando il comando di installazione pip.exe seguente:

`pip.exe install psycopg2`


## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL


Se non si dispone già dell'accesso a un Database di Azure per il server PostgreSQL, ecco la documentazione che spiega i modi in cui è possibile creare un server:

- [Creare un Database di Azure per PostgreSQL usando il portale di Azure](quickstart-create-server-database-portal.md)
- [Creare un Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure](quickstart-create-server-database-azure-cli.md)


## <a name="obtain-the-connection-string-values"></a>Ottenere i valori della stringa di connessione


Dal portale di Azure è possibile ottenere i valori della stringa di connessione per il Database di Azure per il server PostgreSQL. Sono necessari i valori dei parametri come descritto nella tabella seguente.

- La colonna *Nome* &ndash; mostra gli identificatori dei parametri richiesti da psycopg2.
- La colonna *-Symbol* &ndash; indica gli identificatori dei parametri richiesti dal programma Python di esempio, ossia *PythonDriver.py*.


| Nome | -Symbol | Valore di esempio |
| :--  | :--     | :--           |
| host | -h | myazurepostgresql.database.windows.net |
| user | -U | myalias@myazurepostgresql |
| dbname | -d | postgres<br />*(Tutti i server PostgreSQL hanno un database denominato **postgres**.)* |
| port | -p | 5432 *(probabilmente questo valore specifico di 5432.)* |
| password | -P | MySecretPassword |
||||


## <a name="the-python-sample-program"></a>Il programma di esempio Python


Questa sezione indica il codice sorgente per il programma Python di esempio. Il programma viene eseguito più avanti nell'articolo.

```python
# PythonDriver.py
# Python version 2.7

import psycopg2
import sys
import getopt

def main():

    host = ""
    user = ""
    dbname = ""
    port = ""
    password = ""

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:U:d:p:P:", [])
    except getopt.GetoptError as exc:
        print str(exc)
        usage()
        exit(2)

    for o, a in opts:
        if o == "-h": host = str(a)
        if o == "-U": user = str(a)
        if o == "-d": dbname = str(a)
        if o == "-p": port = str(a)
        if o == "-P": password = str(a)

    conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)

    try:
        # Create a connection.  Raise an exception if cannot connect.
        conn = psycopg2.connect(conn_string) 
        cursor = conn.cursor()

        #cursor.execute("DROP TABLE testpy1;")
        #conn.commit()

        cursor.execute("CREATE TABLE testpy1 (id serial PRIMARY KEY, num integer, data varchar);")

        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (100, "First'row"))
        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (110, "Second_row"))
        conn.commit()

        cursor.execute("SELECT id, num, data FROM testpy1;")
        result = cursor.fetchone()

        # Optionally, you can comment these clean-up lines to leave
        # the testpy1 table available for your inspection by pgAdmin. 
        cursor.execute("DROP TABLE testpy1;")
        conn.commit()

    except Exception as exc:
        print "FAILED ", exc
        exit(1)

    print "SUCCESS: ", result

if __name__ == "__main__":
    main()
```


## <a name="command-lines-to-run-the-sample-program"></a>Righe di comando per eseguire il programma di esempio


Questa sezione mostra le righe di comando usate per testare il programma Python di esempio, con l'output effettivo di conferma.

Per i parametri descritti in precedenza, è necessario sostituire i valori di esempio indicati qui con i valori effettivi della stringa di connessione.

L'esatta sintassi per l'esecuzione del programma Python di esempio precedente può variare leggermente. L'esatta sintassi dipende dal sistema operativo e dal tipo di console in uso.


#### <a name="windows-cmdexe-console"></a>Console cmd.exe di Windows


Il blocco di codice seguente mostra l'effettiva esecuzione di un test del programma Python di esempio. Viene usata una riga di comando cmd.exe semplice. Dopo ogni carattere di continuazione di riga "^" è stato immesso:

1. È stato premuto il tasto Invio.
2. È apparsa la frase **Altri?**.
3. È stata immessa un'altra parte dell'intera riga, e così via.

Questa tecnica di continuazione di riga è stata usata per impedire all'esempio di diventare talmente esteso da inficiarne la visualizzazione qui o sulla carta stampata.

```cmd
set Prompt=[$P\]$_$+$G$G$S

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>> .\python.exe ".\_myalias\PythonDriver.py" ^
More?  -h myazurepostgresql.database.windows.net ^
More?  -p 5432 ^
More?  -d postgres ^
More?  -U myalias@myazurepostgresql ^
More?  -P mySecretPassword
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>>
```

È possibile visualizzare la riga **SUCCESS** come conferma che il programma è stato eseguito.

Come tecnica alternativa, l'intera riga di comando può essere inserita in un file con estensione .bat. Tale file può essere eseguito dalla riga di comando cmd.exe.


#### <a name="powershell-file"></a>File PowerShell


Nella console di PowerShell, la riga di comando non supporta il carattere di continuazione di riga. Pertanto, in questa sezione PowerShell i comandi sono stati inseriti in un file PowerShell, eseguito dalla riga di comando di PowerShell.

Copiare il codice seguente in un file denominato *PythonDriverRun.ps1*.

```powershell
# PythonDriverRun.ps1
cd C:\Users\myalias\AppData\Local\Programs\Python\Python27\

.\python.exe `
 .\_myalias\PythonDriver.py `
 -h myazurepostgresql.database.windows.net `
 -p 5432 `
 -d postgres `
 -U myalias@myazurepostgresql `
 -P mySecretPassword
```

Eseguire il file PythonDriverRun.ps1 come indicato di seguito. È possibile visualizzare la riga **SUCCESS** stampata dal programma come conferma.

```cmd
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> .\_myalias\PythonDriverRun.ps1
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >>
```

Se si preferisce eseguire PythonDriverRun.ps1 direttamente nella riga di comando PowerShell, è necessario anteporre un carattere "&" e uno spazio. Senza il prefisso "&", il messaggio di conferma scomparirà troppo rapidamente per poter essere visualizzato.

```
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> & .\python.exe ".\_myalias\PythonDriver.py" -h myazurepostgresql.database.windows.net -p 5432 -d postgres -U myalias@myazurepostgresql -P mySecretPassword
SUCCESS:  (1, 100, "First'row")
```


## <a name="install-pgadmin-to-inspect-your-server"></a>Installare pgAdmin, per eseguire l'ispezione del server


Quando il programma PythonDriverRun.ps1 termina, si ripulisce autonomamente eliminando la tabella testpy1 che ha creato. È possibile scegliere di utilizzare "#" per commentare la riga del codice sorgente che emette l'istruzione **DROP TABLE**. Questa opzione conserva la tabella così da poterla esaminare in un secondo momento.

Lo strumento pgAdmin consente di controllare qualsiasi server PostgreSQL e gli oggetti al suo interno. Gli utenti di Microsoft SQL Server o del Database SQL di Azure noteranno una somiglianza tra SQL Server Management Studio (SSMS) e pgAdmin.

Se lo si desidera, è possibile installare **pgAdmin** per ispezionare il server e la tabella **testpy1**.


#### <a name="1-install-pgadmin"></a>1. Install pgAdmin


Le istruzioni di installazione per pgAdmin sono disponibili all'indirizzo:

- [http://www.pgadmin.org/](http://www.pgadmin.org/)

Il nome del file eseguibile potrebbe essere **pgAdmin4.exe**, anziché pgAdmin.exe.

Per eseguire pgAdmin4.exe in un computer Windows, immettere nella riga di comando qualcosa che assomigli al comando seguente:

`"C:\Program Files (x86)\pgAdmin 4\v1\runtime\pgAdmin4.exe"`


#### <a name="2-connect-pgadmin-to-your-server"></a>2. Connettere pgAdmin al server

 
Dopo aver visualizzato pgAdmin, individuare il riquadro**Browser**. Dopodiché, fare clic con il pulsante destro su **Server** > **Crea** > **Server**. Qui il termine *Crea* si riferisce alla capacità di creare una *connessione* in un qualsiasi server PostgreSQL esistente, compreso un qualunque Database di Azure per server PostgreSQL.

Una volta stabilita la connessione, nel riquadro **Browser** viene visualizzata una struttura ad albero degli oggetti presenti.


#### <a name="3-navigate-in-the-pgadmin-tree-to-your-table"></a>3. Spostarsi nella struttura a d albero di pgAdmin per andare alla tabella


Per visualizzare la tabella, espandere gli elementi della struttura ad albero come indicato di seguito:

- **Server** &gt; *[YourServerHere]* &gt; **Database** &gt; postgres &gt; **Schemi** &gt; tabelle &gt; **pubbliche** &gt; testpy1

![pgAdmin mostra la tabella testpy1 nella struttura ad albero](./media/connect-python/pgAdmin-postgresql-table-testpy1.jpg)


#### <a name="4-drop-the-testpy1-table"></a>4. Eliminare la tabella testpy1


Per la pulizia finale, fare clic con il pulsante destro sul nodo testpy1 e selezionare **Delete/Drop** (Elimina).


## <a name="next-steps"></a>Passaggi successivi

- [Connessione Python al database SQL di Azure](../sql-database/sql-database-connect-query-python.md)
- [Raccolte connessioni per il Database di Azure per PostgreSQL](concepts-connection-libraries.md)


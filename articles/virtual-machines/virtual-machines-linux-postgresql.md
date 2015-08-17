<properties
	pageTitle="Installare e configurare PostgreSQL in una macchina virtuale di Microsoft Azure che esegue Linux"
	description="Informazioni su come installare e configurare PostgreSQL in una macchina virtuale (VM) Ubuntu o CentOS in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
  tags=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="linux"
	ms.workload="infrastructure-services"
	ms.date="06/04/2015"
	ms.author="mingzhan"/>


#Installare e configurare PostgreSQL in Microsoft Azure

PostgreSQL è un database open source avanzato simile a Oracle e DB2. Questo database include funzionalità aziendali quali la conformità ACID completa, l'elaborazione transazionale affidabile e il controllo della concorrenza per più versioni. Supporta anche standard come ANSI SQL e SQL/MED (compresi wrapper di dati esterni per Oracle, MySQL, MongoDB e molti altri). È inoltre altamente estendibile, supportando oltre 12 linguaggi procedurali, gli indici GIN e GIST, i dati spaziali e più funzionalità di tipo NoSQL per le applicazioni basate su chiave-valore o JSON.

Questo articolo illustrerà come installare e configurare PostgreSQL in una macchina virtuale di Azure che esegue Linux.

> [Azure.NOTE]Per poter completare questa esercitazione, è necessario disporre già di una macchina virtuale di Microsoft Azure che esegue Linux. Prima di procedere, vedere l'[esercitazione relativa alle macchine virtuali Linux di Azure](virtual-machines-linux-tutorial.md) per creare e impostare una macchina virtuale Linux.

[In questo caso, usare la porta 1999 come porta di PostgreSQL].

## Installare PostgreSQL

Connettersi tramite putty alla macchina virtuale Linux creata. Se è la prima volta che si usa una macchina virtuale Linux di Azure, vedere come usare putty per connettersi a una macchina virtuale Linux facendo clic [qui](virtual-machines-linux-use-ssh-key.md).

1. Eseguire il comando seguente per passare alla directory radice (admin):

		$ sudo su -

2. Alcune distribuzioni dispongono di dipendenze che è necessario installare prima di eseguire l'installazione di PostgreSQL. Nell'elenco individuare la distribuzione in uso ed eseguire il comando appropriato:

	- Redhat:

			# yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

	- Debian:

 			# apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

	- Suse:

			# zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Scaricare PostgreSQL nella directory radice e quindi decomprimere il pacchetto:

		# wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

		# tar jxvf  postgresql-9.3.5.tar.bz2

	Quello precedente è un esempio. Per l'indirizzo di download più dettagliato, fare clic [qui](https://ftp.postgresql.org/pub/source/).

4. Per avviare la compilazione, eseguire questi comandi:

		# cd postgresql-9.3.5

		# ./configure --prefix=/opt/postgresql-9.3.5

5. Se si intende compilare tutti gli elementi compilabili, inclusi la documentazione (pagine HTML e man) e i moduli aggiuntivi (contrib), eseguire invece il comando seguente:

		# gmake install-world

	Dovrebbe venire visualizzato il messaggio di conferma seguente:

		PostgreSQL, contrib, and documentation successfully made. Ready to install.

## Configurare PostgreSQL

1. (Facoltativo) Creare un collegamento simbolico per abbreviare il riferimento a PostgreSQL in modo da non includere il numero di versione:

		# ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Creare una directory per il database:

		# mkdir -p /opt/pgsql_data

3. Creare un utente non ROOT e modificare il relativo profilo. Passare quindi a tale nuovo utente (denominato *postgres* nell'esempio):

		# useradd postgres

		# chown -R postgres.postgres /opt/pgsql_data

		# su - postgres

    >[Azure.NOTE]Per motivi di sicurezza, PostgreSQL usa un utente non ROOT per inizializzare, avviare o arrestare il database.


4. Modificare il file *bash\_profile* immettendo i comandi seguenti. Queste righe verranno aggiunte alla fine del file *bash\_profile*:

		cat >> ~/.bash_profile <<EOF
		export PGPORT=1999
		export PGDATA=/opt/pgsql_data
		export LANG=en_US.utf8
		export PGHOME=/opt/pgsql
		export PATH=\$PATH:\$PGHOME/bin
		export MANPATH=\$MANPATH:\$PGHOME/share/man
		export DATA=`date +"%Y%m%d%H%M"`
		export PGUSER=postgres
		alias rm='rm -i'
		alias ll='ls -lh'
		EOF

5. Eseguire il file *bash\_profile*:

		$ source .bash_profile

6. Verificare l'installazione con il comando seguente:

		$ which psql

	Se l'installazione ha avuto esito positivo, verrà visualizzata la risposta seguente:

		/opt/pgsql/bin/psql

7. È anche possibile verificare la versione di PostgreSQL:

		$ psql -V

8. Inizializzare il database:

		$ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

	Dovrebbero venire visualizzato l'output seguente:

![immagine](./media/virtual-machines-linux-postgresql/no1.png)

## Impostare PostgreSQL

<!--	[postgres@ test ~]$ exit -->

Eseguire i comandi seguenti:

	# cd /root/postgresql-9.3.5/contrib/start-scripts

	# cp linux /etc/init.d/postgresql

Modificare due variabili nel file /etc/init.d/postgresql. Il prefisso (prefix) è impostato sul percorso di installazione di PostgreSQL: **/opt/pgsql**. PGDATA è impostato sul percorso di archiviazione dati di PostgreSQL: **/opt/pgsql\_data**.

	# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

	# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![immagine](./media/virtual-machines-linux-postgresql/no2.png)

Modificare il file per renderlo eseguibile:

	# chmod +x /etc/init.d/postgresql

Avviare PostgreSQL:

	# /etc/init.d/postgresql start

Controllare se l'endpoint di PostgreSQL è attivo:

	# netstat -tunlp|grep 1999

Dovrebbe venire visualizzato l'output seguente.

![immagine](./media/virtual-machines-linux-postgresql/no3.png)

## Connessione al database Postgres

Proseguire e passare di nuovo all'utente postgres:

	# su - postgres

Creare un database Postgres:

	$ createdb events

Connettersi al database events appena creato:

	$ psql -d events

## Come creare ed eliminare una tabella Postgres

Ora che ci si è connessi al database, è possibile crearvi le tabelle.

Ad esempio, creare una nuova tabella Postgres di esempio con il comando seguente:

	CREATE TABLE potluck (name VARCHAR(20),	food VARCHAR(30),	confirmed CHAR(1), signup_date DATE);

È stata così impostata una tabella di quattro colonne con i nomi e le restrizioni seguenti:

1. La colonna "name" è stata limitata dal comando VARCHAR a una lunghezza inferiore a 20 caratteri.
2. La colonna "food" indica la pietanza che verrà portata da ogni persona. VARCHAR limita questo testo a una lunghezza inferiore a 30 caratteri.
3. La colonna "confirmed" registra se la persona ha dato conferma della propria partecipazione. I valori consentiti sono "Y" e "N".
4. La colonna "date" indicherà quando la persona si è iscritta per l'evento. Postgres richiede che le date vengano immesse nel formato aaaa-mm-gg.

Se la tabella è stata creata correttamente, dovrebbe venire visualizzato quanto segue:

![immagine](./media/virtual-machines-linux-postgresql/no4.png)

È anche possibile verificare la struttura della tabella con il comando seguente:

![immagine](./media/virtual-machines-linux-postgresql/no5.png)

### Aggiungere dati a una tabella

Inserire innanzitutto le informazioni in una riga:

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Dovrebbe venire visualizzato questo output:

![immagine](./media/virtual-machines-linux-postgresql/no6.png)

È anche possibile aggiungere altre persone alla tabella. Ecco alcuni esempi oppure è possibile inserire i dati desiderati:

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### Mostrare le tabelle

Per mostrare una tabella, usare il comando seguente:

	select * from potluck;

L'output è:

![immagine](./media/virtual-machines-linux-postgresql/no7.png)

### Eliminare dati in una tabella

Per eliminare dati in una tabella, usare il comando seguente:

	delete from potluck where name=’John’;

In questo modo verranno eliminate tutte le informazioni presenti nella riga relativa a "John". L'output è:

![immagine](./media/virtual-machines-linux-postgresql/no8.png)

### Aggiornare dati in una tabella

Per aggiornare dati in una tabella, usare il comando seguente: In questo caso, Sandy ha confermato la sua partecipazione, pertanto lo stato di conferma verrà modificato da "N" a "Y":

 	UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##Altre informazioni su PostgreSQL
L'installazione di PostgreSQL in una macchina virtuale Linux di Microsoft Azure è stata così completata ed è ora possibile iniziare a farne uso in Microsoft Azure. Per altre informazioni su PostgreSQL, fare clic [qui](http://www.postgresql.org/).

<!---HONumber=August15_HO6-->
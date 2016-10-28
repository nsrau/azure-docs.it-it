<properties 
   pageTitle="Usare Apache Phoenix e SQuirrel in HDInsight | Microsoft Azure" 
   description="Informazioni su come usare Apache Phoenix in HDInsight e su come installare e configurare SQuirreL sulla workstation per la connessione a un cluster HBase in HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# Usare Apache Phoenix e SQuirreL con i cluster HBase basati su Windows in HDinsight  

Informazioni su come usare [Apache Phoenix](http://phoenix.apache.org/) in HDInsight e su come installare e configurare SQuirrel sulla workstation per la connessione a un cluster HBase in HDInsight. Per altre informazioni su Phoenix, vedere la [breve panoramica su Phoenix](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Per la grammatica Phoenix, vedere [Grammatica Phoenix](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Per informazioni sulla versione di Phoenix in HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].
>
> Le informazioni contenute in questo documento sono specifiche per i cluster HDInsight basati su Windows. Per informazioni sull'uso di Phoenix su HDInsight basato su Linux, vedere [Usare Apache Phoenix con cluster HBase basati su Linux in HDinsight](hdinsight-hbase-phoenix-squirrel-linux.md).

##Usare SQLLine
[SQLLine](http://sqlline.sourceforge.net/) è un'utilità della riga di comando per eseguire SQL.

###Prerequisiti
Per usare SQLLine sono necessari:

- **Un cluster HBase in HDInsight**. Per informazioni sul provisioning di un cluster HBase, vedere l'[introduzione all'uso di Apache HBase in HDInsight][hdinsight-hbase-get-started].
- **Connessione al cluster HBase tramite il file RDP (Remote Desktop Protocol)**. Per istruzioni, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure classico][hdinsight-manage-portal].

**Per individuare il nome host**

1. Aprire la **riga di comando di Hadoop** dal desktop.
2. Eseguire il comando seguente per richiamare il suffisso DNS:

		ipconfig

	Annotare il **suffisso DNS specifico della connessione**. Ad esempio, *myhbasecluster.f5.internal.cloudapp.net*. Quando ci si connette a un cluster HBase, sarà necessario connettersi a uno dei Zookeeper usando il nome di dominio completo. Ogni cluster HDInsight ha tre Zookeeper: *zookeeper0*, *zookeeper1* e *zookeeper2*. Il nome di dominio completo sarà simile a *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Per usare SQLLine**

1. Aprire la **riga di comando di Hadoop** dal desktop.
2. Eseguire i comandi seguenti per aprire SQLLine:

		cd %phoenix_home%\bin
		sqlline.py [The FQDN of one of the Zookeepers]

	![hdinsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

	I comandi usati nell'esempio:

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
		
		!tables
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;

Per altre informazioni, vedere il [manuale di SQLLine](http://sqlline.sourceforge.net/#manual) e [Grammatica Phoenix](http://phoenix.apache.org/language/index.html).


















##Usare SQuirreL

[SQuirreL SQL Client](http://squirrel-sql.sourceforge.net/) è un programma Java grafico che consente di visualizzare la struttura di un database conforme con JDBC, esplorare i dati nelle tabelle, inviare comandi SQL ecc. Può essere usato per connettersi a Apache Phoenix in HDInsight.

Questa sezione illustra come installare e configurare SQuirreL sulla workstation per la connessione a un cluster HBase in HDInsight tramite VPN.

###Prerequisiti

Prima di iniziare le procedure sono necessari:

- Un cluster HBase distribuito in una rete virtuale di Azure con una macchina virtuale DNS. Per istruzioni, vedere [Provisioning di cluster HBase in Rete virtuale di Azure][hdinsight-hbase-provision-vnet].

	>[AZURE.IMPORTANT] È necessario installare un server DNS nella rete virtuale. Per istruzioni, vedere [Configurare DNS tra due reti virtuali di Azure](hdinsight-hbase-geo-replication-configure-DNS.md)

- Ottenere il suffisso DNS specifico della connessione per il cluster HBase. Per ottenerlo, effettuare una connessione RDP al cluster e quindi eseguire IPConfig. Il suffisso DNS è simile a:

		myhbase.b7.internal.cloudapp.net
- Scaricare e installare [Microsoft Visual Studio Express 2013 per Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) sulla workstation. Sarà necessario eseguire lo strumento MakeCert dal pacchetto per creare il certificato.
- Scaricare e installare [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) sulla workstation. SQuirreL SQL Client versione 3.0 e versioni successive richiede la versione JRE 1.6 o versioni successive.


###Configurare una connessione VPN Point-to-Site alla rete virtuale di Azure

La configurazione di una connessione VPN Point-To-Site prevede tre passaggi:

1. [Configurare una rete virtuale e un gateway di routing dinamico](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Creare i certificati](#Create-your-certificates)
3. [Configurare il client VPN](#Configure-your-VPN-client)

Per altre informazioni, vedere [Configurare una connessione VPN Point-to-Site alla rete virtuale di Azure](../vpn-gateway/vpn-gateway-point-to-site-create.md).

#### Configurare una rete virtuale e un gateway di routing dinamico

Assicurarsi di aver eseguito il provisioning di un cluster HBase in una rete virtuale di Azure (vedere i prerequisiti per questa sezione). Il passaggio successivo consiste nel configurare una connessione Point-to-Site.

**Per configurare la connettività Point-to-Site**

1. Accedere al [portale di Azure classico][azure-portal].
2. A sinistra, fare clic su **RETI**.
3. Fare clic sulla rete virtuale creata (vedere [Provisioning di cluster HBase in Rete virtuale di Azure][hdinsight-hbase-provision-vnet]).
4. Fare clic su **CONFIGURA** nella parte superiore.
5. Nella sezione **Connettività Point-to-Site** selezionare **Configura connettività Point-to-Site**.
6. Configurare **IP INIZIALE** e **CIDR** per specificare l'intervallo indirizzi IP dal quale i client VPN riceveranno un indirizzo IP durante la connessione. L'intervallo non può sovrapporsi con nessuno degli intervalli all'interno della rete locale e la rete virtuale di Azure alla quale si effettuerà la connessione. Ad esempio, se si seleziona 10.0.0.0/20 per la rete virtuale, è possibile selezionare 10.1.0.0/24 per lo spazio degli indirizzi del client. Per altre informazioni, vedere la pagina [Configurare la connettività Point-to-Site][vnet-point-to-site-connectivity].
7. Nella sezione relativa agli spazi di indirizzi della rete virtuale, fare clic su **Aggiungi subnet gateway**.
7. Fare clic su **SALVA** nella parte inferiore della pagina.
8. Fare clic **SÌ** per confermare la modifica. Attendere che il sistema abbia terminato di apportare la modifica prima di passare alla procedura successiva.


**Per creare un gateway di routing dinamico**

1. Dal portale di Azure classico, fare clic su **DASHBOARD** nella parte superiore della pagina.
2. Fare clic su **CREA GATEWAY** nella parte inferiore della pagina.
3. Fare clic su **SÌ** per confermare. Attendere la creazione del gateway.
4. Fare clic su **DASHBOARD** nella parte superiore. Verrà visualizzato un grafico visuale della rete virtuale:

	![Diagramma virtuale Point-to-Site della rete virtuale di Azure][img-vnet-diagram]

	Il diagramma mostra 0 connessioni client. Dopo aver eseguito una connessione alla rete virtuale, il numero verrà aggiornato a uno.

#### Creare i certificati

Un modo per creare un certificato X.509 consiste nell'usare lo strumento di creazione certificati (makecert.exe) fornito con [Microsoft Visual Studio Express 2013 per Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).


**Per creare un certificato radice autofirmato**

1. Dalla workstation, aprire una finestra del prompt dei comandi.
2. Passare alla cartella Strumenti di Visual Studio.
3. Il comando nell'esempio seguente consente di creare e installare un certificato radice nell'archivio certificati personale nella workstation e creare anche un file corrispondente con estensione cer da caricare in seguito nel portale di Azure classico.

		makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

	Passare alla directory in cui si vuole inserire il file con estensione cer, dove HBaseVnetVPNRootCertificate è il nome che si vuole usare per il certificato.

	Non chiudere il prompt dei comandi. Sarà necessario nella procedura successiva.

	>[AZURE.NOTE] Poiché è stato creato un certificato radice da cui verranno generati i certificati client, è possibile esportarlo, insieme alla relativa chiave privata e salvarlo in una posizione sicura dove può essere recuperato.

**Per creare un certificato client**

- Dallo stesso prompt dei comandi (deve essere sullo stesso computer in cui è stato creato il certificato radice. Il certificato client deve essere generato dal certificato principale), eseguire il comando seguente:

  		makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

	HBaseVnetVPNRootCertificate è il nome del certificato radice. Deve corrispondere al nome del certificato radice.

	Il certificato radice e il certificato client vengono archiviati nell'archivio certificati personali del computer. Usare certmgr.msc per verificare.

	![Certificato vpn Point-to-Site della rete virtuale di Azure][img-certificate]

	Un certificato client deve essere installato in ogni computer che si connette alla rete virtuale. Si consiglia di creare client univoci certificati per ogni computer che si desidera connettersi alla rete virtuale. Per esportare i certificati client, usare certmgr.msc.

**Per caricare il certificato radice nel portale di Azure classico**

1. Dal portale di Azure classico, fare clic su **RETE** a sinistra.
2. Fare clic sulla rete virtuale in cui è stato distribuito il cluster HBase.
3. Fare clic su **CERTIFICATI** nella parte superiore.
4. Fare clic **CARICA** dalla parte inferiore e specificare il file del certificato radice creato nella penultima procedura. Attendere che il certificato sia stato importato.
5. Fare clic su **DASHBOARD** nella parte superiore. Lo stato viene mostrato nel diagramma virtuale.


#### Configurare il client VPN



**Per scaricare e installare il pacchetto VPN del client**

1. Dalla pagina DASHBOARD della rete virtuale, nella sezione riepilogo rapido, fare clic su **Scarica pacchetto VPN client a 64 bit** o su **Scarica pacchetto VPN client a 32 bit** in base alla versione del sistema operativo della workstation.
2. Fare clic su **Esegui** per installare il pacchetto.
3. Quando verrà visualizzato l'avviso di protezione, fare clic su **Altre informazioni**, quindi su **Esegui comunque**.
4. Fare clic due volte su **Sì**.

**Per connettersi alla rete VPN**

1. Sul desktop della workstation, fare clic sull'icona Reti sulla barra delle applicazioni. Verrà visualizzata una connessione VPN con il nome della propria rete virtuale.
2. Fare clic sul nome della connessione VPN.
3. Fare clic su **Connect**.

**Per testare la risoluzione del nome dominio e la connessione VPN**

- Dalla workstation, aprire un prompt dei comandi ed eseguire il ping di uno dei seguenti nomi dato che il suffisso DNS del cluster HBase è myhbase.b7.internal.cloudapp.net:

		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		headnode0.myhbase.b7.internal.cloudapp.net
		headnode1.myhbase.b7.internal.cloudapp.net
		workernode0.myhbase.b7.internal.cloudapp.net

###Installare e configurare SQuirreL sulla workstation

**Per installare SQuirreL**

1. Scaricare il file jar di SQL SQuirreL Client da [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Aprire/eseguire il file jar. È richiesto [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Fare clic su **Next** due volte.
4. Specificare un percorso per cui si dispone dell'autorizzazione di scrittura e fare clic su **Next**.
	>[AZURE.NOTE] La cartella di installazione predefinita è nella cartella c:\\Programmi\\Microsoft Files\\squirrel-sql-3.6. Per scrivere in questo percorso, al programma di installazione devono essere concessi i privilegi di amministratore. È possibile aprire un prompt dei comandi come amministratore, passare alla cartella bin di Java e quindi eseguire
	>
	>     java.exe -jar [the path of the SQuirreL jar file] 
5. Fare clic su **OK** per confermare la creazione della directory di destinazione.
6. L'impostazione predefinita consiste nell'installare i pacchetti Base e Standard. Fare clic su **Next**.
7. Fare clic su **Next** due volte e quindi su **Done**.


**Per installare il driver Phoenix**

Il file jar del driver Phoenix si trova nel cluster HBase. Il percorso è simile al seguente in base alle versioni:

	C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
È necessario copiarlo nella workstation nel percorso [cartella di installazione SQuirreL]/lib. Il modo più semplice consiste nell'effettuare una connessione RDP al cluster e quindi copiare e incollare il file (CTRL + C e CTRL + V) nella workstation.

**Per aggiungere un driver di Phoenix a SQuirreL**

1. Aprire SQuirreL SQL Client dalla workstation.
2. Fare clic sulla scheda **Driver** a sinistra.
2. Scegliere **New Driver** dal menu **Driver**.
3. Immettere le seguenti informazioni:

	- **Name**: Phoenix
	- **Example URL**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
	- **Class Name**: org.apache.phoenix.jdbc.PhoenixDriver

	>[AZURE.WARNING] Usare solo lettere minuscole nell'URL di esempio. È possibile usare il quorum zookeeper completo nel caso in cui uno di essi sia inattivo. I nomi host sono zookeeper0, zookeeper1 e zookeeper2.

	![Driver di HDInsight HBase Phoenix SQuirreL][img-squirrel-driver]
4. Fare clic su **OK**.

**Per creare un alias per il cluster HBase**

1. Da SQuirreL, fare clic sulla scheda **Alias** a sinistra.
2. Scegliere **New Alias** dal menu **Aliases**.
3. Immettere le seguenti informazioni:

	- **Name**: il nome del cluster HBase o qualsiasi altro nome.
	- **Driver**: Phoenix. Deve corrispondere al nome del driver creato nella procedura precedente.
	- **URL**: l'URL viene copiato dalla configurazione del driver. Assicurarsi di usare solo lettere minuscole.
	- **Nome utente**: può essere qualsiasi testo. Poiché qui viene usata la connettività VPN, il nome utente non viene utilizzato affatto.
	- **Password**: può essere qualsiasi testo.

	![Driver di HDInsight HBase Phoenix SQuirreL][img-squirrel-alias]
4. Fare clic su **Test**.
5. Fare clic su **Connect**. Quando viene effettuata la connessione, SQuirreL ha l'aspetto seguente:

	![HBase Phoenix SQuirreL][img-squirrel]

**Per eseguire un test**

1. Fare clic sulla scheda **SQL** accanto alla scheda **Objects**.
2. Copiare e incollare il codice seguente:

		CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Fare clic sul pulsante di esecuzione.

	![HBase Phoenix SQuirreL][img-squirrel-sql]
4. Tornare alla scheda **Objects**.
5. Espandere il nome alias, quindi espandere **TABLE**. La nuova tabella verrà elencata nell'area sottostante.
 
##Passaggi successivi
In questo articolo si è appreso come usare Apache Phoenix in HDInsight. Per altre informazioni, vedere

- [Panoramica di HDInsight HBase][hdinsight-hbase-overview]\: HBase è un database NoSQL open source Apache basato su Hadoop che fornisce un accesso casuale e coerenza assoluta a grandi quantità di dati non strutturati e semistrutturati.
- [Provisioning di cluster HBase in Rete virtuale di Azure][hdinsight-hbase-provision-vnet]\: con l'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti alle stessa rete virtuale delle applicazioni in modo che queste ultime possano comunicare direttamente con HBase.
- [Configurare la replica HBase in HDInsight](hdinsight-hbase-geo-replication.md): informazioni su come configurare la replica HBase in due data center di Azure.
- [Analisi dei sentimenti Twitter con HBase in HDInsight][hbase-twitter-sentiment]\: informazioni su come eseguire [l'analisi dei sentimenti](http://en.wikipedia.org/wiki/Sentiment_analysis) dei Big Data in tempo reale usando HBase in un cluster Hadoop in HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=AcomDC_0914_2016-->
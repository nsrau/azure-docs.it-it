<properties
	pageTitle="Creare un'applicazione HBase tramite Maven e Java, poi distribuirla in HDInsight basato su Linux | Microsoft Azure"
	description="Informazioni su come usare Apache Maven per compilare un'applicazione Apache HBase basata su Java e quindi distribuirla in HDInsight basato su Linux nel cloud Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor=""/>  

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="larryfr"/>  

#Usare Maven per compilare applicazioni Java che usano HBase con HDInsight basato su Linux (Hadoop)

In questo articolo si apprenderà come creare e compilare un'applicazione [Apache HBase](http://hbase.apache.org/) in Java usando Apache Maven, Utilizzare quindi l'applicazione con un cluster HDInsight basato su Linux.

[Maven](http://maven.apache.org/) è un progetto di gestione software e uno strumento di esplorazione che consente di compilare software, documentazione e report per i progetti Java. In questo articolo si apprenderà come usarlo per creare un'applicazione Java di base che crea, interroga ed elimina una tabella HBase su un cluster HDInsight basato su Linux.

> [AZURE.NOTE] Le procedure in questo documento presuppongono l'uso di un cluster HDInsight basato su Linux. Per informazioni sull'uso di un cluster HDInsight basato su Windows, vedere [Usare Maven per compilare applicazioni Java che usano HBase con HDInsight basato su Windows (Hadoop)](hdinsight-hbase-build-java-maven.md)

##Requisiti

* [Piattaforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o versioni successive

* [Maven](http://maven.apache.org/)

* [Un cluster Azure HDInsight basato su Linux con HBase](../hdinsight-hbase-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] Le procedure descritte in questo documento sono state testate con le versioni del cluster HDInsight 3.2, 3.3 e 3.4. I valori predefiniti specificati negli esempi sono per un cluster HDInsight 3.4.

* **Familiarità con SSH e SCP**. Per altre informazioni sull'uso di SSH e SCP con HDInsight, vedere gli articoli seguenti:

    * **Client Linux, Unix o OS X**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Client Windows**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##Creare il progetto

1. Dalla riga di comando nell'ambiente di sviluppo, passare alla directory in cui si vuole creare il progetto, ad esempio `cd code/hdinsight`.

2. Usare il comando __mvn__, che viene installato con Maven, per generare lo scaffolding per il progetto.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Viene creata una nuova directory nella directory corrente, con il nome specificato dal parametro __artifactID__ (in questo esempio, **hbaseapp**). che conterrà gli elementi seguenti:

	* __pom.xml__: il modello a oggetti dei progetti ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html), Project Object Model) contiene le informazioni e i dettagli di configurazione usati per compilare il progetto.

	* __src__: la directory che contiene la directory __main/java/com/microsoft/examples__, in cui verrà creata l’applicazione.

3. Eliminare il file __src/test/java/com/microsoft/examples/apptest.java__, perché non verrà usato in questo esempio.

##Aggiornare il modello a oggetti dei progetti

1. Modificare il file __pom.xml__ aggiungere il codice seguente nella sezione `<dependencies>`.

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

	Ciò indica a Maven che il progetto richiedere la versione __1.1.2__ di __hbase-client__. In fase di compilazione, quest'ultimo verrà scaricato dal repository Maven predefinito. È possibile usare la [ricerca nel repository centrale Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) per ottenere altre informazioni su questa dipendenza.

    > [AZURE.IMPORTANT] Il numero di versione deve corrispondere alla versione di HBase fornita con il cluster HDInsight. Usare la tabella seguente per trovare il numero di versione corretto.

    | Versione del cluster HDInsight | Versione di HBase da usare |
    | ----- | ----- |
    | 3\.2 | 0\.98.4-hadoop2 |
    | 3\.3 e 3.4 | 1\.1.2 |

    Per altre informazioni sulle versioni e sui componenti di HDInsight, vedere [Quali sono i diversi componenti di Hadoop disponibili in HDInsight?](hdinsight-component-versioning.md).

2. Se si usa un cluster HDInsight 3.3 o 3.4, è necessario aggiungere anche il codice seguente alla sezione `<dependencies>`:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Verranno caricati i componenti phoenix-core richiesti per Hbase versione 1.1.x.

2. Aggiungere il codice seguente al file __pom.xml__. Il codice deve essere incluso tra tag `<project>...</project>` nel file, ad esempio tra `</dependencies>` e `</project>`.

		<build>
		  <sourceDirectory>src</sourceDirectory>
		  <resources>
	        <resource>
	          <directory>${basedir}/conf</directory>
	          <filtering>false</filtering>
	          <includes>
	            <include>hbase-site.xml</include>
	          </includes>
	        </resource>
	      </resources>
		  <plugins>
		    <plugin>
        	  <groupId>org.apache.maven.plugins</groupId>
        	  <artifactId>maven-compiler-plugin</artifactId>
						<version>3.3</version>
        	  <configuration>
          	    <source>1.7</source>
          	    <target>1.7</target>
        	  </configuration>
      		</plugin>
		    <plugin>
		      <groupId>org.apache.maven.plugins</groupId>
		      <artifactId>maven-shade-plugin</artifactId>
		      <version>2.3</version>
		      <configuration>
		        <transformers>
		          <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
	              </transformer>
	            </transformers>
		      </configuration>
		      <executions>
		        <execution>
		          <phase>package</phase>
		          <goals>
		            <goal>shade</goal>
		          </goals>
		        </execution>
		      </executions>
		    </plugin>
		  </plugins>
		</build>

	In tal modo verrà configurata una risorsa (__conf/hbase-site.xml__,) che contiene informazioni di configurazione per HBase.

	> [AZURE.NOTE] È anche possibile impostare i valori di configurazione tramite codice. Per scoprire come procedere, leggere i commenti nell'esempio __CreateTable__ seguente.

	Verranno inoltre configurati i [plug-in compiler per Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) e [i plug-in shade per Maven](http://maven.apache.org/plugins/maven-shade-plugin/). Il plug-in compiler viene usato per compilare la topologia, mentre il plug-in shade viene usato per impedire la duplicazione della licenza nel pacchetto JAR compilato da Maven. Il motivo per cui viene usato questo plug-in è che i file di licenza duplicati causano un errore in fase di esecuzione sul cluster HDInsight. L'uso di maven-shade-plugin con l'implementazione di `ApacheLicenseResourceTransformer` previene il verificarsi di questo errore.

	Il plug-in maven-shade-plugin produce anche un file uberjar (o fatjar), che contiene tutte le dipendenze richieste dall'applicazione.

3. Salvare il file __pom.xml__.

4. Creare una nuova directory denominata __conf__ nella directory __hbaseapp__. Questo verrà utilizzato per contenere le informazioni di configurazione per la connessione a HBase.

5. Utilizzare il comando seguente per copiare la configurazione di HBase dal server HDInsight nella directory __conf__. Sostituire **USERNAME** con il nome di accesso a SSH. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight:

		scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

	> [AZURE.NOTE] Se è stata usata una password per l'account SSH, verrà richiesto di immetterla. Se si utilizza una chiave SSH con l'account, è necessario utilizzare il parametro `-i` per specificare il percorso del file di chiave. Nell'esempio seguente la chiave privata verrà caricata da `~/.ssh/id_rsa`:
	>
	> `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##Creazione dell'applicazione

1. Passare alla directory __hbaseapp/src/main/java/com/microsoft/examples__ e rinominare il file app.java in __CreateTable.java__.

2. Aprire il file __CreateTable.java__ e sostituire il contenuto esistente con quello seguente:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

	Si tratta della classe __CreateTable__, che consente di creare una tabella denominata __people__ e di popolarla con alcuni utenti predefiniti.

3. Salvare il file __CreateTable.java__.

4. Nella directory __hbaseapp/src/main/java/com/microsoft/examples__ creare un nuovo file denominato __SearchByEmail.java__. Usare il codice seguente come contenuto di questo file:

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Scan;
		import org.apache.hadoop.hbase.client.ResultScanner;
		import org.apache.hadoop.hbase.client.Result;
		import org.apache.hadoop.hbase.filter.RegexStringComparator;
		import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
		import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
		import org.apache.hadoop.hbase.util.Bytes;
		import org.apache.hadoop.util.GenericOptionsParser;

		public class SearchByEmail {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Use GenericOptionsParser to get only the parameters to the class
		    // and not all the parameters passed (when using WebHCat for example)
		    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
		    if (otherArgs.length != 1) {
		      System.out.println("usage: [regular expression]");
		      System.exit(-1);
		    }

			// Open the table
		    HTable table = new HTable(config, "people");

			// Define the family and qualifiers to be used
		    byte[] contactFamily = Bytes.toBytes("contactinfo");
		    byte[] emailQualifier = Bytes.toBytes("email");
		    byte[] nameFamily = Bytes.toBytes("name");
		    byte[] firstNameQualifier = Bytes.toBytes("first");
		    byte[] lastNameQualifier = Bytes.toBytes("last");

			// Create a new regex filter
		    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
			// Attach the regex filter to a filter
			//   for the email column
		    SingleColumnValueFilter filter = new SingleColumnValueFilter(
		      contactFamily,
		      emailQualifier,
		      CompareOp.EQUAL,
		      emailFilter
		    );

			// Create a scan and set the filter
		    Scan scan = new Scan();
		    scan.setFilter(filter);

			// Get the results
		    ResultScanner results = table.getScanner(scan);
			// Iterate over results and print  values
		    for (Result result : results ) {
		      String id = new String(result.getRow());
		      byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
		      String firstName = new String(firstNameObj);
		      byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
		      String lastName = new String(lastNameObj);
		      System.out.println(firstName + " " + lastName + " - ID: " + id);
			  byte[] emailObj = result.getValue(contactFamily, emailQualifier);
		      String email = new String(emailObj);
			  System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
		    }
		    results.close();
			table.close();
		  }
		}

	È possibile usare la classe __SearchByEmail__ per eseguire query sulle righe in base all'indirizzo e-mail. Poiché usa un filtro di espressione regolare, è possibile fornire una stringa o un'espressione regolare quando si usa la classe.

5. Salvare il file __SearchByEmail.java__.

6. Nella directory __hbaseapp/src/main/hava/com/microsoft/examples__ creare un nuovo file denominato __DeleteTable.java__. Usare il codice seguente come contenuto di questo file:

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;

		public class DeleteTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);

		    // Disable, and then delete the table
		    admin.disableTable("people");
		    admin.deleteTable("people");
		  }
		}

	Questa classe consente di pulire i dati di questo esempio procedendo a disabilitare e a eliminare la tabella creata dalla classe __CreateTable__.

7. Salvare il file __DeleteTable.java__.

##Compilare e creare il pacchetto dell'applicazione

2. Dalla directory __hbaseapp__ usare il comando seguente per compilare un file JAR contenente l'applicazione:

		mvn clean package

	In tal modo, vengono eliminati eventuali artefatti di compilazione precedenti, scaricate le dipendenze non ancora installate e compilato e creato il pacchetto dell'applicazione.

3. Quando il comando viene completato, la directory __hbaseapp/target__ conterrà un file denominato __hbaseapp-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE] Il file __hbaseapp-1.0-SNAPSHOT.jar__ è un file uberjar (talvolta chiamato fatjar), che contiene tutte le dipendenze richieste per eseguire l'applicazione.

##Caricare il file JAR ed eseguire i processi

1. Utilizzare quanto indicato di seguito per caricare il file jar nel cluster HDInsight. Sostituire **USERNAME** con il nome di accesso a SSH. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight:

		scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

	Questo caricherà il file nella directory principale per l'account utente SSH.

	> [AZURE.NOTE] Se è stata usata una password per l'account SSH, verrà richiesto di immetterla. Se si utilizza una chiave SSH con l'account, è necessario utilizzare il parametro `-i` per specificare il percorso del file di chiave. Nell'esempio seguente la chiave privata verrà caricata da `~/.ssh/id_rsa`:
	>
	> `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Connettersi al cluster HDInsight usando SSH. Sostituire **USERNAME** con il nome di accesso a SSH. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	> [AZURE.NOTE] Se è stata usata una password per l'account SSH, verrà richiesto di immetterla. Se si utilizza una chiave SSH con l'account, è necessario utilizzare il parametro `-i` per specificare il percorso del file di chiave. Nell'esempio seguente la chiave privata verrà caricata da `~/.ssh/id_rsa`:
	>
	> `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Per creare una nuova tabella HBase tramite l'applicazione Java, una volta connessi, utilizzare quanto indicato di seguito:

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

	Si creerà una nuova tabella HBase denominata __people__, che verrà popolata con i dati.

4. Successivamente, utilizzare le operazioni seguenti per la ricerca di indirizzi di posta elettronica memorizzati nella tabella:

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

	Dovrebbero essere visualizzati i risultati seguenti:

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

##Eliminare la tabella

Dopo aver finito con l'esempio, usare il comando seguente dalla sessione di Azure PowerShell per eliminare la tabella __people__ usata in questo esempio.

	hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

<!---HONumber=AcomDC_0914_2016-->
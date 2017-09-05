---
title: Come usare Spring Boot Starter con un'API DocumentDB per Azure Cosmos DB
description: Informazioni su come configurare un'applicazione creata con Spring Boot Initializer con l'API DocumentDB per Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: Spring, Spring Boot Starter, Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/08/2017
ms.author: robmcm;yungez;kevinzha
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 273cc750857c5e466882060a38ac0f3475811e98
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---

# <a name="how-to-use-the-spring-boot-starter-with-azure-cosmos-db-documentdb-api"></a>Come usare Spring Boot Starter con l'API DocumentDB per Azure Cosmos DB

## <a name="overview"></a>Panoramica

**[Spring Framework]** è una soluzione open source che consente agli sviluppatori Java di creare applicazioni di livello enterprise. Uno dei progetti più comuni che si basa su questa piattaforma è [Spring Boot], che fornisce un approccio semplificato per la creazione di applicazioni Java autonome. Per semplificare le operazioni iniziali con Spring Boot per gli sviluppatori, alcuni pacchetti Spring Boot di esempio sono disponibili all'indirizzo <https://github.com/spring-guides/>. Oltre a consentire di scegliere dall'elenco di progetti Spring Boot di base, **[Spring Initializr]** semplifica le operazioni iniziali degli sviluppatori per la creazione di applicazioni Spring Boot personalizzate.

Azure Cosmos DB è un servizio di database distribuito a livello globale che consente agli sviluppatori di usare i dati usando un'ampia gamma di API standard, come DocumentDB, MongoDB, Graph e Table. Microsoft Spring Boot Starter consente agli sviluppatori di usare applicazioni Spring Boot che si integrano facilmente con Azure Cosmos DB usando le API DocumentDB.

Questo articolo descrive la creazione di un Azure Cosmos DB usando il portale di Azure, l'uso di **Spring Initializr** per creare un'applicazione Java personalizzata e quindi l'aggiunta delle funzionalità di Spring Boot Starter all'applicazione personalizzata per l'archiviazione e il recupero di dati da Azure Cosmos DB usando l'API DocumentDB.

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti sono necessari per seguire le procedure disponibili in questo articolo:

* Sottoscrizione di Azure; se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN] oppure iscriversi per ottenere un [account Azure gratuito].

* [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/) versione 1.7 o successiva.

* [Apache Maven](http://maven.apache.org/) versione 3.0 o successiva.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Creare un Azure Cosmos DB usando il portale di Azure

1. Passare al portale di Azure all'indirizzo <https://portal.azure.com/> e fare clic su **+Nuovo**.

   ![Portale di Azure][AZ01]

1. Fare clic su **Database** e quindi su **Azure Cosmos DB**.

   ![Portale di Azure][AZ02]

1. Nella pagina **Azure Cosmos DB** immettere le informazioni seguenti:

   * Immettere un **ID** univoco, che verrà usato come URI per il database, ad esempio *wingtiptoysdata.documents.azure.com*.
   * Scegliere **SQL (Document DB)** per l'API.
   * Selezionare la **sottoscrizione** da usare per il database.
   * Specificare se creare un nuovo **gruppo di risorse** per il database o sceglierne uno esistente.
   * Specificare il **percorso** per il database.
   
   Dopo avere specificato queste opzioni, fare clic su **Crea** per creare il database.

   ![Portale di Azure][AZ03]

1. Al termine della creazione, il database viene elencato nel **Dashboard** di Azure e nelle pagine **Tutte le risorse** e **Azure Cosmos DB**. È possibile fare clic sul database in una di queste posizioni per aprire la pagina delle proprietà per la cache.

   ![Portale di Azure][AZ04]

1. Quando viene visualizzata la pagina delle proprietà per il database, fare clic su **Chiavi di accesso** e copiare le chiavi di accesso e l'URI per il database. Questi valori verranno usati all'interno dell'applicazione Spring Boot.

   ![Portale di Azure][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Creare un'applicazione Spring Boot semplice con Spring Initializr

1. Passare a <https://start.spring.io/>.

1. Specificare che si vuole generare un progetto **Maven** con **Java**, immettere i nomi in **Group** (Gruppo) e **Artifact** (Elemento) per l'applicazione e quindi fare clic sul pulsante **Generate Project** (Genera progetto).

   ![Opzioni di base di Spring Initializr][SI01]

   > [!NOTE]
   >
   > Spring Initializr usa i valori di **Group** (Gruppo) e **Artifact** (Elemento) per creare il nome del pacchetto, ad esempio *com.example.wintiptoys*.
   >

1. Quando richiesto, scaricare il progetto in un percorso nel computer locale.

   ![Scaricare il progetto Spring Boot personalizzato][SI02]

1. Dopo l'estrazione dei file nel sistema locale, la semplice applicazione Spring Boot sarà pronta per la modifica.

   ![File del progetto Spring Boot personalizzato][SI03]

## <a name="configure-your-spring-boot-app-to-use-the-azure-spring-boot-starter"></a>Configurare l'app Spring Boot per usare Azure Spring Boot Starter

1. Individuare il file *pom.xml* nella directory dell'app, ad esempio:

   `C:\SpringBoot\wingtiptoys\pom.xml`

   -oppure-

   `/users/example/home/wingtiptoys/pom.xml`

   ![Individuare il file pom.xml][PM01]

1. Aprire il file *pom.xml* in un editor di testo e aggiungere le righe seguenti all'elenco di `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-documentdb-spring-boot-starter</artifactId>
      <version>0.1.4</version>
   </dependency>
   ```

   ![Modifica del file pom.xml][PM02]

1. Salvare e chiudere il file *pom.xml*.

## <a name="configure-your-spring-boot-app-to-use-your-azure-cosmos-db"></a>Configurare l'app Spring Boot per usare Azure Cosmos DB

1. Individuare il file *application.properties* nella directory *resources* dell'app, ad esempio:

   `C:\SpringBoot\wingtiptoys\src\main\resources\application.properties`

   -oppure-

   `/users/example/home/wingtiptoys/src/main/resources/application.properties`

   ![Individuare il file application.properties][RE01]

1. Aprire il file *application.properties* in un editor di testo, quindi aggiungere le righe seguenti al file e sostituire i valori di esempio con le proprietà appropriate per il database:

   ```yaml
   # Specify the DNS URI of your Azure Cosmos DB.
   azure.documentdb.uri=https://wingtiptoys.documents.azure.com:443/

   # Specify the access key for your database.
   azure.documentdb.key=57686f6120447564652c20426f6220526f636b73==

   # Specify the name of your database.
   azure.documentdb.database=wingtiptoysdata
   ```

   ![Modifica del file application.properties][RE02]

1. Salvare e chiudere il file *application.properties*.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Aggiungere il codice di esempio per implementare le funzionalità di base del database

In questa sezione si creano due classi Java per l'archiviazione dei dati utente e si modifica la classe principale dell'applicazione per creare un'istanza della classe utente e salvarla nel database.

### <a name="define-a-basic-class-for-storing-user-data"></a>Definire una classe di base per l'archiviazione dei dati utente

1. Creare un nuovo file denominato *User.java* nella stessa directory del file dell'applicazione Java main.

1. Aprire il file *User.java* in un editor di testo e aggiungere le righe seguenti al file per definire una classe User generica che consenta di archiviare e recuperare i valori nel database:

   ```java
   package com.example.wingtiptoys;

   public class User {
      private String id;
      private String firstName;
      private String lastName;
 
      public User(String id, String firstName, String lastName) {
         this.id = id;
         this.firstName = firstName;
         this.lastName = lastName;
      }
   
      public String getId() {
         return this.id;
      }

      public void setId(String id) {
         this.id = id;
      }

      public String getFirstName() {
         return firstName;
      }

      public void setFirstName(String firstName) {
         this.firstName = firstName;
      }

      public String getLastName() {
         return lastName;
      }

      public void setLastName(String lastName) {
         this.lastName = lastName;
      }

      @Override
      public String toString() {
         return String.format("User: %s %s", firstName, lastName);
      }
   }
   ```

1. Salvare e chiudere il file *User.java*.

### <a name="define-a-data-repository-interface"></a>Definire un'interfaccia del repository di dati

1. Creare un nuovo file denominato *UserRepository.java* nella stessa directory del file dell'applicazione Java main.

1. Aprire il file *UserRepository.java* in un editor di testo e aggiungere le righe seguenti al file per definire l'interfaccia utente del repository che estende l'interfaccia del repository predefinita di DocumentDB:

   ```java
   package com.example.wingtiptoys;

   import com.microsoft.azure.spring.data.documentdb.repository.DocumentDbRepository;
   import org.springframework.stereotype.Repository;

   @Repository
   public interface UserRepository extends DocumentDbRepository<User, String> {}   
   ```

1. Salvare e chiudere il file *UserRepository.java*.

### <a name="modify-the-main-application-class"></a>Modificare la classe dell'applicazione main

1. Individuare il file Java dell'applicazione main nella directory del pacchetto dell'app, ad esempio:

   `C:\SpringBoot\wingtiptoys\src\main\java\com\example\wingtiptoys\WingtiptoysApplication.java`

   -oppure-

   `/users/example/home/wingtiptoys/src/main/java/com/example/wingtiptoys/WingtiptoysApplication.java`

   ![Individuare il file Java dell'applicazione][JV01]

1. Aprire il file Java dell'applicazione main in un editor di testo e aggiungere le righe seguenti al file:

   ```java
   package com.example.wingtiptoys;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.CommandLineRunner;

   @SpringBootApplication
   public class WingtiptoysApplication implements CommandLineRunner {

      @Autowired
      private UserRepository repository;
    
      public static void main(String[] args) {
         SpringApplication.run(WingtiptoysApplication.class, args);
      }

      public void run(String... var1) throws Exception {
         final User testUser = new User("testId", "testFirstName", "testLastName");

         repository.deleteAll();
         repository.save(testUser);

         final User result = repository.findOne(testUser.getId());

         System.out.printf("\n\n%s\n\n",result.toString());
      }
   }
   ```

1. Salvare e chiudere il file Java dell'applicazione main.

## <a name="build-and-test-your-app"></a>Compilare e testare l'app

1. Aprire un prompt dei comandi e cambiare la directory passando alla cartella in cui si trova il file *pom.xml*, ad esempio:

   `cd C:\SpringBoot\wingtiptoys`

   -oppure-

   `cd /users/example/home/wingtiptoys`

1. Compilare l'applicazione Spring Boot con Maven ed eseguirla, ad esempio:

   ```shell
   mvn package
   java -jar target/wingtiptoys-0.0.1-SNAPSHOT.jar
   ```

1. Verranno visualizzati diversi messaggi di runtime e verrà mostrato il messaggio `User: testFirstName testLastName` per indicare che i valori archiviati e recuperati correttamente dal database.

   ![Output positivo dall'applicazione][JV02]

1. FACOLTATIVO: è possibile usare il portale di Azure per visualizzare il contenuto di Azure Cosmos DB nella pagina delle proprietà del database facendo clic su **Esplora documenti** e quindi selezionando l'elemento nell'elenco per visualizzare il contenuto.

   ![Utilizzo di Esplora documenti per visualizzare i dati][JV03]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'utilizzo di Azure Cosmos DB e Java, vedere gli articoli seguenti:

* [Documentazione di Azure Cosmos DB].

* [Azure Cosmos DB: Creare un'app per le API DocumentDB con Java e il portale di Azure][Build a DocumentDB API app with Java]

Per altre informazioni sull'uso delle applicazioni Spring Boot in Azure, vedere gli articoli seguenti:

* [Spring Boot DocumenDB Starter for Azure](https://github.com/Microsoft/azure-spring-boot-starters/tree/master/azure-documentdb-spring-boot-starter-sample)

* [Distribuire un'applicazione Spring Boot nel servizio app di Azure](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Eseguire un'applicazione Spring Boot in un cluster Kubernetes nel servizio contenitore di Azure](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure] e gli [strumenti Java per Visual Studio Team Services].

<!-- URL List -->

[Documentazione di Azure Cosmos DB]: /azure/cosmos-db/
[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Build a DocumentDB API app with Java]: https://docs.microsoft.com/azure/cosmos-db/create-documentdb-java
[account Azure gratuito]: https://azure.microsoft.com/pricing/free-trial/
[strumenti Java per Visual Studio Team Services]: https://java.visualstudio.com/
[vantaggi per i sottoscrittori di MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[AZ01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ01.png
[AZ02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ02.png
[AZ03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ03.png
[AZ04]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ04.png
[AZ05]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ05.png

[SI01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI01.png
[SI02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI02.png
[SI03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI03.png

[RE01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE01.png
[RE02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE02.png

[PM01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM01.png
[PM02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM02.png

[JV01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV01.png
[JV02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV02.png
[JV03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV03.png


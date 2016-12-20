---
title: "Novità di Azure Toolkit for Eclipse"
description: Informazioni su come usare Azure Toolkit for Eclipse.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 16b066ea-aae7-4c30-9a12-fa0c3711b93e
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm;asirveda;martinsawicki
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2b572ac3416936b1511a9d267c30b56af714eaf3


---
# <a name="whats-new-in-the-azure-toolkit-for-eclipse"></a>Novità di Azure Toolkit for Eclipse
## <a name="azure-toolkit-for-eclipse-releases"></a>Versioni di Azure Toolkit for Eclipse
Questo articolo include informazioni sulle diverse versioni e sugli aggiornamenti più recenti di Azure Toolkit for Eclipse.

> [!NOTE]
> Esiste anche un Toolkit di Azure per l’IDE di IntelliJ. Per ulteriori informazioni, vedere [Azure Toolkit per IntelliJ].
> 
> 

### <a name="august-26-2016"></a>26 agosto 2016
La versione di Azure Toolkit per Eclipse rilasciata ad agosto 2016 include i miglioramenti seguenti:

* **Distribuzioni di JDK personalizzate**. Azure Toolkit per Eclipse supporta ora la specifica e la distribuzione di una versione JDK arbitraria nel contenitore di app Web di Azure:
  * Oltre alla versione JDK fornita da Azure, è possibile scegliere tra un'ampia gamma di versioni di Zulu OpenJDK rese disponibili in Azure da Azul Systems.
  * È anche possibile specificare una distribuzione di JDK personalizzata se viene caricata come file ZIP nell'account di archiviazione.
* **Miglioramenti alla visualizzazione di Azure Explorer**:
  * Supporto per la gestione di Macchine virtuali usando il nuovo modello di Azure Resource Manager: è possibile elencare, creare ed eliminare le macchine virtuali basate su Resource Manager senza uscire dall'ambiente IDE.
  * Supporto per la gestione di BLOB di account di archiviazione tramite Azure Resource Manager, che integra le funzionalità esistenti per la gestione di account di archiviazione "classici".
* **Microsoft JDBC Driver 6.0 per SQL Server**. Questo aggiornamento include il driver JDBC più aggiornato per Microsoft SQL Server (versione 6.0), incluso ora come libreria che è possibile aggiungere facilmente ai progetti Java, sostituendo in tal modo la versione precedente.

### <a name="june-29-2016"></a>29 giugno 2016
La versione del Toolkit di Azure per Eclipse rilasciata a giugno 2016 include i miglioramenti seguenti:

* **Requisito Java 8**. Per il Toolkit Azure per Eclipse è ora necessario Java 8. Sebbene questo requisito sia fondamentale per il toolkit, le applicazioni possono continuare a usare versioni di Java supportate da Azure.
* **Supporto per i JDK di Java più recenti**. Le versioni più recenti dei JDK di Java sono ora supportate dal Toolkit di Azure per Eclipse.
* **Supporto per Azure SDK versione 2.9.1**. La versione più recente di Azure SDK è ora il prerequisito minimo per Azure Toolkit per Eclipse.
* **Campioni integrati**. Il Toolkit di Azure per Eclipse presenta adesso numerose applicazioni campione per fornire supporto agli sviluppatori nella fase iniziale.
* **Integrazione dello strumento HDInsight**. Gli strumenti HDInsight di Azure sono adesso contenuti nel Toolkit di Azure per Eclipse. Per altre informazioni, vedere il [plug-in degli strumenti HDInsight per Eclipse].
* **Debug remoto delle app Web Java**. Il Toolkit di Azure per Eclipse supporta adesso il debug remoto delle App Web Java nel servizio app di Azure.
* **Supporto per la versione di Eclipse Luna.**  La nuova versione minima richiesta dell'IDE di Eclipse è Luna.

### <a name="april-12-2016"></a>12 aprile 2016
La versione di Azure Toolkit per Eclipse rilasciata ad aprile 2016 include i miglioramenti seguenti:

* **Supporto per Azure SDK versione 2.9.0**. La versione più recente di Azure SDK è ora il prerequisito minimo per Azure Toolkit per Eclipse.
* **Vari miglioramenti di usabilità, velocità di risposta e prestazioni relativamente al supporto di app Web di Azure**. Prestazioni ottimizzate relativamente alla comunicazione tra il Toolkit e il risultato di Azure in un'interfaccia utente più reattiva.
* **Possibilità di eliminare un contenitore di applicazioni Web in Azure da Eclipse**. Azure Toolkit per Eclipse consente ora di eliminare un contenitore Web di Azure esistente senza uscire da Eclipse.

### <a name="march-7-2016"></a>7 marzo 2016
La versione di Azure Toolkit for Eclipse rilasciata a marzo 2016 include i miglioramenti seguenti:

* **Supporto per la distribuzione rapida di applicazioni Java leggere**. Azure Toolkit for Eclipse ora supporta la distribuzione rapida delle applicazioni Java leggere in contenitori di app Web di Azure, consentendo di distribuire le applicazioni Java in alcuni secondi anziché in minuti.
* **Supporto per la gestione di app Web con la visualizzazione Azure Explorer**. La visualizzazione di Azure Explorer nel toolkit consente di elencare, avviare e arrestare le App Web di Azure.
* **Distribuzioni aggiornate di Tomcat, Jetty e Zulu OpenJDK**. Il toolkit di Azure per Eclipse offre il supporto delle versioni aggiornate di Tomcat, Jetty e Zulu OpenJDK per le distribuzioni di Java nei servizi cloud di Azure.

### <a name="january-4-2016"></a>4 gennaio 2016
La versione di Azure Toolkit per Eclipse rilasciata a gennaio 2016 include i miglioramenti seguenti:

* **Supporto per gli aggiornamenti di Zulu OpenJDK**. Per altre informazioni, vedere la [pagina Web di Azul Systems per Zulu OpenJDK].
* **Distribuzioni aggiornate di Tomcat e Jetty**. Le distribuzioni di Tomcat e Jetty disponibili in Microsoft Azure per l'uso con Azure Toolkit for Eclipse sono state aggiornate.
* **Parità di funzionalità tra i toolkit Eclipse e IntelliJ per Azure**. Azure Toolkit for Eclipse e [Azure Toolkit per IntelliJ] supportano ora lo stesso set di funzionalità.

### <a name="september-1-2015"></a>1° settembre 2015
La versione di Azure Toolkit for Eclipse rilasciata a settembre 2015 include i miglioramenti seguenti:

* **Supporto per gli aggiornamenti di Zulu OpenJDK**. Per altre informazioni, vedere la [pagina Web di Azul Systems per Zulu OpenJDK].
* **Distribuzioni aggiornate di Tomcat e Jetty**. Le distribuzioni di Tomcat e Jetty disponibili in Microsoft Azure per l'uso con Azure Toolkit for Eclipse sono state aggiornate. Queste distribuzioni consentono agli sviluppatori di creare rapidamente progetti di sviluppo e test con Azure Toolkit for Eclipse.
* **Supporto per i riferimenti di Tomcat e Jetty aggiornati automaticamente**. Oltre alle specifiche versioni di Tomcat e Jetty disponibili in Azure, gli sviluppatori ora possono fare riferimento a una distribuzione detta "Più recente (aggiornata automaticamente)", che si aggiorna automaticamente alla distribuzione più recente di ogni versione principale di Tomcat o Jetty al riciclo successivo delle istanze del ruolo. Il riciclo viene eseguito automaticamente, ma gli sviluppatori possono attivarlo manualmente tramite il portale di Azure. Con questa nuova funzionalità gli sviluppatori non devono ridistribuire l'applicazione per mantenere aggiornato il software del server. (
* Questa funzionalità è attualmente concepita solo a scopo di sviluppo e test e per applicazioni non cruciali e non è consigliabile per l'ambiente di produzione.
* **Visualizzazione di esplorazione delle risorse di Azure per BLOB, code e tabelle nell'archiviazione di Azure**. Ciò consente agli sviluppatori di eseguire un set di attività comuni con i relativi elementi di archiviazione direttamente dall'IDE di Eclipse. Ad esempio, eliminazione, caricamento o download di BLOB.

### <a name="august-1-2015"></a>1° agosto 2015
La versione di Azure Toolkit for Eclipse rilasciata ad agosto 2015 include i miglioramenti seguenti:

* **Gestione della chiave di strumentazione di Application Insights**. Questo aggiornamento consente di acquisire, creare e gestire le chiavi di strumentazione di Application Insights direttamente dall'IDE di Eclipse.
* **Microsoft JDBC Driver 4.1 per SQL Server**. Questo aggiornamento include il supporto per il driver JDBC per Microsoft SQL Server.
* **Versione 2.7 di Azure SDK**. Questo aggiornamento più recente di Azure SDK è il nuovo prerequisito per il toolkit quando viene installato in Windows. Non è necessario nei sistemi operativi non Windows.
* **Supporto per l'aggiornamento di Zulu OpenJDK v7**. Per altre informazioni, vedere la [pagina Web di Azul Systems per Zulu OpenJDK].

### <a name="may-1-2015"></a>1° maggio 2015
La versione di Azure Toolkit for Eclipse rilasciata a maggio 2015 include i miglioramenti seguenti:

* **Interfaccia utente migliorata per la selezione del server**. Questa versione semplifica l'uso del toolkit nei sistemi operativi non Windows.
* **Supporto per i progetti Maven**. Questa versione supporta i progetti Maven come applicazioni che il toolkit può distribuire in Azure e configurare in Application Insights.
* **Versione 2.6 di Azure SDK**. Questo aggiornamento più recente di Azure SDK è il nuovo prerequisito per il toolkit quando viene installato in Windows. Non è necessario nei sistemi operativi non Windows.
* **Aggiornamento della distribuzione invece della ripubblicazione**. Se si ripubblica un progetto di distribuzione quando è già attiva la versione precedente, il toolkit ora usa la funzionalità di aggiornamento della distribuzione di Azure invece di arrestare la distribuzione precedente e ripubblicarla da zero, come accadeva in passato. Questo abilita l'esecuzione del servizio cloud senza interruzioni, quando possibile, consentendo di ottenere una disponibilità elevata anche durante un aggiornamento e di velocizzare il processo di ripubblicazione.
* **Supporto per la versione più recente di Zulu OpenJDK v8, aggiornamento 40**. Per altre informazioni, vedere la [pagina Web di Azul Systems per Zulu OpenJDK].

### <a name="march-9-2015"></a>9 marzo 2015
La versione di Azure Toolkit for Eclipse rilasciata a marzo 2015 include i miglioramenti seguenti:

* **Supporto per Mac, Ubuntu e altre versioni di Linux**. Questa versione di Azure Toolkit for Eclipse aggiunge il supporto per Mac OS e diverse piattaforme Unix. Gli sviluppatori possono quindi installare il toolkit per creare, configurare e pubblicare progetti Java in Servizi cloud di Azure (PaaS) da Eclipse in esecuzione in sistemi operativi diversi da Windows.

> [!NOTE]
> Questa funzionalità è disponibile in anteprima e non è consigliabile usarla in ambienti di produzione. Non è disponibile un contratto di servizio per l'assistenza clienti, ma l'invio di qualsiasi tipo di commenti e suggerimenti è consigliabile e apprezzato.
> 
> 

* **Nuovo plug-in di Application Insights**. Gli sviluppatori ora possono configurare la telemetria server automatica con Application Insights in Azure.
* **Automazione della distribuzione da riga di comando basata su Ant**. Con questa funzionalità gli sviluppatori possono automatizzare la pubblicazione delle versioni più recenti delle proprie distribuzioni usando Ant all'esterno di Eclipse. Uno script pregenerato viene configurato automaticamente per un progetto dopo la prima distribuzione da Eclipse. Lo script potrà essere usato nelle distribuzioni successive per automatizzarle completamente usando solo la riga di comando.
* **Disponibilità di Tomcat e Jetty in Azure per una distribuzione più semplice e veloce**. Ora gli sviluppatori possono fare riferimento direttamente a diverse versioni di Tomcat e Jetty disponibili in Azure, anziché dover caricare un server Java nei propri account (o tramite il toolkit). Non è quindi necessario caricare un server Java per scenari introduttivi veloci.
* **Metodo di collegamento per la pubblicazione di app Web Java nei servizi cloud di Azure**. Per ridurre la curva di apprendimento per gli scenari di sviluppo e test semplici, ora gli sviluppatori possono pubblicare le applicazioni Java più direttamente in Azure. Invece di dover eseguire il processo di creazione e configurazione di un progetto di distribuzione di Azure, le applicazioni verranno distribuite con un'istanza predefinita di Tomcat v8 e Zulu JVM (OpenJDK).

### <a name="january-30-2015"></a>30 gennaio 2015
La versione di Azure Toolkit for Eclipse rilasciata a gennaio 2015 include i miglioramenti seguenti:

* **Supporto per IBM® WebSphere® Application Server Liberty Core**. Questa versione aggiunge IBM WebSphere Application Server Liberty Core all'elenco di server applicazioni supportati da cui il toolkit è in grado di eseguire la distribuzione in Azure. Quest'ultima aggiunta estende l'attuale elenco di server applicazioni supportati &quot;direttamente&quot; dal toolkit, che già include diverse versioni di Tomcat, Jetty, JBoss e GlassFish.
* **Inclusione di Application Insights SDK**. Questa nuova libreria di API client (v0.9.0) fa parte del Package for Azure Libraries for Java.
* **Aggiornamento del Package for Azure Libraries for Java**. Questo aggiornamento include Azure Libraries for Java v0.7.0 e Storage Client API v2.0.0, nonché il nuovo Application Insights SDK v0.9.0.

### <a name="november-12-2014"></a>12 novembre 2014
La versione di Azure Toolkit for Eclipse rilasciata a novembre 2014 include i miglioramenti seguenti:

* **Supporto per Azure SDK 2.5**. Il più recente aggiornamento di Azure SDK è il nuovo prerequisito per il toolkit.
* **Supporto per la versione aggiornata dei pacchetti Zulu OpenJDK v1.8, v1.7 e v1.6**. Per altre informazioni, vedere la [pagina Web di Azul Systems per Zulu OpenJDK].
* **Supporto per le nuove dimensioni D Standard per i servizi cloud**, che offre il miglioramento delle prestazioni e risorse di memoria aggiuntive. Per altre informazioni, vedere la pagina [Dimensioni delle macchine virtuali e dei servizi cloud per Azure].

### <a name="october-17-2014"></a>17 ottobre 2014
La versione di Azure Toolkit for Eclipse rilasciata a ottobre 2014 include i miglioramenti seguenti:

* **Miglioramenti delle prestazioni negli scenari di pubblicazione nel cloud**. Il caricamento delle informazioni di sottoscrizione è più veloce nel caso di utenti con più sottoscrizioni e account di archiviazione.
* **Supporto per la versione aggiornata del pacchetto Zulu OpenJDK v1.8**. Per altre informazioni, vedere la [pagina Web di Azul Systems per Zulu OpenJDK].
* **Supporto per deprecare le versioni precedenti dei JDK di terze parti**. I pacchetti JDK deprecati non vengono più visualizzati nel menu a discesa dei nuovi progetti di distribuzione. I progetti esistenti che fanno riferimento a pacchetti JDK deprecati continueranno a poterlo fare per il momento, ma è consigliabile aggiornare questi progetti in modo che facciano riferimento alle versioni più recenti.
* **Versione aggiornata della libreria di API client di Package for Azure Libraries for Java**. Per altre informazioni, vedere l'articolo relativo all' [API client di Microsoft Azure].
* **Correzioni di bug.**  Questa versione contiene numerose correzioni di bug varie basate su report degli utenti e testing.

### <a name="august-5-2014"></a>5 agosto 2014
La versione di Azure Toolkit for Eclipse rilasciata ad agosto 2014 include i miglioramenti seguenti:

* **Supporto per Azure SDK 2.4.**  Le versioni precedenti di Eclipse Toolkit non funzioneranno con questo nuovo SDK.
* **Versioni aggiornate dei pacchetti Zulu OpenJDK v1.6, 1.7 e v1.8.** Per altre informazioni, vedere la [pagina Web di Azul Systems per Zulu OpenJDK].
* **Versione aggiornata della libreria di API client del Package for Azure Libraries for Java.** Per altre informazioni, vedere l'articolo relativo all' [API client di Microsoft Azure].
* **Supporto per il più recente formato di file delle impostazioni di pubblicazione.**  È stato aggiunto il supporto per la versione 2.0 del formato di file delle impostazioni di pubblicazione.
* **Modifiche dell'architettura alla base della funzionalità di pubblicazione nel cloud.**  Per supportare la pubblicazione nel cloud, il toolkit usa l'API client di Microsoft Azure per Java.
* **Correzioni di bug.**  Questa versione include numerose correzioni di bug richieste dagli utenti.

### <a name="june-12-2014"></a>12 giugno 2014
La versione di Azure Toolkit for Eclipse rilasciata a giugno 2014 è un aggiornamento di manutenzione secondario che offre i miglioramenti seguenti:

* **Supporto per il pacchetto Zulu OpenJDK v1.8.** Per altre informazioni, vedere la [pagina Web di Azul Systems per Zulu OpenJDK].
* **Versioni aggiornate dei pacchetti Zulu OpenJDK v1.6 e 1.7.** Per altre informazioni, vedere la [pagina Web di Azul Systems per Zulu OpenJDK].
* **Versione aggiornata della libreria di API client del Package for Azure Libraries for Java.** Per altre informazioni, vedere l'articolo relativo all' [API client di Microsoft Azure].
* **Correzioni di bug.**  Questa versione include numerose correzioni di bug richieste dagli utenti.

### <a name="april-4-2014"></a>4 aprile 2014
È stata rilasciata la versione di Azure Plugin for Eclipse di aprile 2014. Questo aggiornamento accompagna la versione di Azure SDK 2.3, che costituisce un prerequisito e verrà scaricata automaticamente quando si installa il plug-in. L'aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti a livello di usabilità basati su commenti rispetto alla versione di anteprima di febbraio 2014:

* **Supporto per la versione di Azure SDK 2.3.**  La versione di Azure Plugin for Eclipse rilasciata ad aprile 2014 richiede Azure SDK 2.3. Quando si usa il nuovo plug-in, se Azure SDK 2.3 non è già disponibile, verrà richiesto di consentirne l'installazione. Non usare Azure SDK 2.3 con le versioni precedenti del plug-in.
* **Aggiornamento di applicazioni senza la distribuzione del pacchetto completo.**  Quando si distribuiscono applicazioni Java che fanno parte del progetto, il plug-in le carica automaticamente nell'account di archiviazione selezionato, per poterle aggiornare e per riciclare le istanze del ruolo per distribuire il codice delle applicazioni più recente, senza dover ricompilare e ridistribuire l'intero pacchetto.
* **Tomcat 8 ora è un server applicazioni riconosciuto.** Se si seleziona una directory di installazione di Tomcat 8 nel computer locale tramite la scheda **Server** della finestra di dialogo **Azure Deployment Project**, il plug-in la rileverà automaticamente e potrà distribuire Tomcat 8 in modo automatico, analogamente alle versioni precedenti di Tomcat già nell'elenco.
* **Aggiornamenti pacchetto Azul Zulu OpenJDK: v1.7 aggiornamento 51 e v1.6 aggiornamento 47.**  A partire da questa versione è disponibile l'aggiornamento 51 del pacchetto Zulu Open JDK v7 di Azul System. Iniziano anche a essere disponibili i pacchetti Zulu Open JDK v6 a partire dall'aggiornamento 47. Questi aggiornamenti sono in aggiunta agli aggiornamenti 45, 40 e 25 del pacchetto Zulu Open JDK v7 disponibili in precedenza.
* **Supporto per le dimensioni A8 e A9 delle macchine virtuali di Microsoft Azure.**  È ora possibile distribuire un servizio cloud nelle dimensioni delle macchine virtuali A8 e A9 a memoria elevata. Per altre informazioni su queste dimensioni delle macchine virtuali, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure].
* **Reindirizzamento automatico da HTTP a HTTPS per i ruoli abilitati per SSL.** Quando il servizio cloud include solo ruoli HTTPS, se la richiesta dell'utente specifica HTTP, viene eseguito il reindirizzamento automatico a HTTPS Non è necessario creare un ruolo separato per gestire le richieste HTTP.
* **Express Emulator usato per l'emulazione locale.**  Express Emulator di Azure viene ora usato come emulatore durante il debug di applicazioni in locale.
* **Re-branding di Azure in Microsoft Azure.**  Le schermate dell'interfaccia utente riflettono il re-branding di Azure che non è più denominato Azure.

### <a name="february-6-2014"></a>6 febbraio 2014
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di febbraio 2014 L'aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti a livello di usabilità basati su commenti rispetto alla versione di anteprima di ottobre 2013:

* **Supporto per l'offload SSL.**  Con l'offload SSL (Secure Sockets Layer) aggiunto come funzionalità, è possibile attivare facilmente il supporto HTTPS (Hypertext Transfer Protocol Secure) nella distribuzione Java in Azure, senza dover configurare SSL nel server applicazioni Java. Ciò è particolarmente rilevante nell'affinità di sessione e/o negli scenari di comunicazione autenticati. Ad esempio, quando si usa il filtro Servizio di controllo di accesso (ACS), già supportato dal toolkit. Per altre informazioni, vedere l'articolo relativo all'[offload SSL] e l'articolo su [come usare l'offload SSL].
* **GlassFish 4 ora è un server applicazioni riconosciuto.** Se si seleziona una directory di installazione di GlassFish 4 nel computer locale tramite la scheda **Server** della finestra di dialogo **Azure Deployment Project**, il plug-in la rileverà automaticamente e potrà distribuire GlassFish OSE 4 in modo automatico, analogamente alla versione GlassFish OSE 3 già nell'elenco.
* **Aggiornamento 45 del pacchetto Azul Zulu OpenJDK.**  A partire da questa versione, è disponibile l'aggiornamento 45 per Zulu (pacchetto Open JDK v7) di Azul System, in aggiunta agli aggiornamenti 40 e 25 disponibili in precedenza.
* **Supporto dell'impostazione automatica per le porte di endpoint private.**  È possibile impostare automaticamente una porta privata per gli endpoint di input e interni per consentire ad Azure di assegnare automaticamente una porta a tale endpoint. In precedenza era possibile assegnare solo un numero di porta specifico.
* **Supporto per la personalizzazione del nome certificato (CN) nell'interfaccia utente per la creazione di certificati autofirmati.** In precedenza veniva usato lo stesso nome hardcoded per tutti i nuovi certificati. Ora è possibile specificare un nome certificato personalizzato per distinguere facilmente più certificati usati per scopi diversi nel portale di Azure.
* **Barra degli strumenti di azure:** la barra degli strumenti di Azure è stata aggiornata con le modifiche seguenti. 
  * ![][ic710876] È stata aggiunta questa icona per il **Nuovo progetto di distribuzione Azure**.
  * ![][ic710877] Questa icona è stato aggiunta come collegamento alla finestra di dialogo per la creazione di un certificato autofirmato.
* **Supporto per le dimensioni A5 delle macchine virtuali di Azure.**  Ora è possibile distribuire un servizio cloud nelle dimensioni delle macchine virtuali A5 a memoria elevata. Per altre informazioni sulle dimensioni delle macchine virtuali, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure].
* **Supporto per Microsoft Windows Server 2012 R2.**  È possibile selezionare Windows Server 2012 R2 come sistema operativo cloud.

### <a name="october-22-2013"></a>22 ottobre 2013
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di ottobre 2013. L'aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti a livello di usabilità basati sui commenti rispetto alla versione di anteprima di settembre 2013:

* **Supporto per Azure SDK versione 2.2.**  La versione di anteprima di Azure Plugin for Eclipse rilasciata a ottobre 2013 supporta Azure SDK 2.2. Il plug-in funziona comunque con Azure SDK 2.1 e installerà automaticamente Azure SDK 2.2 se non è già installato almeno Azure SDK 2.1.
* **Aggiornamento 40 del pacchetto Azul Zulu OpenJDK.**  Come annunciato per la versione di anteprima di settembre 2013, il plug-in consente l'uso di un JDK fornito da terze parti direttamente in Azure, senza richiedere il caricamento di un JDK personalizzato. Nella versione di ottobre 2013 è disponibile l'aggiornamento 40 per Zulu (pacchetto Open JDK v7) di Azul System, in aggiunta all'aggiornamento 25 pubblicato in origine.
* **Collegamento alla distribuzione cloud nel log attività.** Nel log attività di Azure, quando la distribuzione ha lo stato **Published**, è possibile fare clic su **Published** perché corrisponde a un collegamento alla distribuzione, che verrà quindi aperta nel browser. L'etichetta per lo stato **Published** in precedenza era **Running**.
* **Selezione del sistema operativo disponibile in fase di pubblicazione.** La finestra di dialogo **Publish to Azure** include un nuovo campo, **Target OS**, che fornisce un modo più facilmente individuabile per l'impostazione del sistema operativo di destinazione.
* **Sovrascrittura automatica della distribuzione precedente.** La finestra di dialogo **Publish to Azure** include una nuova casella di controllo **Overwrite previous deployment**. Se questa opzione è selezionata, quando si pubblica la nuova distribuzione quella precedente verrà sovrascritta automaticamente. Non si verificheranno errori di tipo &quot;conflitto 409&quot; quando si pubblica nello stesso percorso senza prima annullare la pubblicazione della distribuzione precedente.
* **Jetty 9 ora è un server applicazioni riconosciuto.** Se si seleziona una directory di installazione di Jetty 9 nel computer locale tramite la scheda **Server** della finestra di dialogo **Azure Deployment Project**, il plug-in la rileverà automaticamente e sarà in grado di distribuire Jetty 9 in modo automatico, analogamente alle versioni precedenti di Jetty già nell'elenco.
* **Aggiunta di un ruolo al menu di scelta rapida del progetto.** Il menu di scelta rapida del progetto **Azure** include una nuova voce di menu, **Aggiungi ruolo**, che fornisce un modo più rapido e facilmente individuabile di aggiungere un nuovo ruolo al progetto di Azure.
* **Aggiornamento della libreria Package for the Azure Libraries for Java.** Si basa sulla versione 0.4.6 dell' [API client di Microsoft Azure].

### <a name="september-25-2013"></a>25 settembre 2013
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di settembre 2013. L'aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti a livello di usabilità basati su commenti rispetto alla versione di anteprima di agosto 2013:

* **Possibilità di distribuire il pacchetto Azul Zulu OpenJDK disponibile in Azure.**  È stata aggiunta una nuova opzione quando si specifica il JDK da usare con la distribuzione di Azure. Con questa opzione è possibile distribuire un pacchetto JDK di terze parti direttamente nel cloud di Azure, senza doverne caricare uno personalizzato. Azul Systems fornisce il primo di questi pacchetti denominati Zulu, basati su OpenJDK, che ora possono essere distribuiti tramite questa opzione.
* **Aggiornamento della libreria Package for the Azure Libraries for Java.** Si basa sulla versione 0.4.5 dell' [API client di Microsoft Azure].

### <a name="august-1-2013"></a>1° agosto 2013
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di agosto 2013. Questo aggiornamento accompagna la versione di Azure SDK 2.1, che costituisce un prerequisito e verrà scaricata automaticamente quando si installa il plug-in. L'aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti a livello di usabilità basati su commenti rispetto alla versione di anteprima di luglio 2013:

* **Rimozione delle opzioni per includere il JDK locale e il server applicazioni locale come parte del pacchetto di distribuzione.**  Il download del JDK e del server applicazioni dall'archiviazione cloud durante la distribuzione è preferibile all'incorporamento di questi componenti nel pacchetto, perché il download degli elementi permette di ottenere dimensioni del pacchetto di distribuzione inferiori, tempi di distribuzione più rapidi e maggiore facilità di manutenzione. Di conseguenza, l'opzione per includere il JDK e il server applicazioni nel pacchetto di distribuzione è stata rimossa. I progetti esistenti configurati per includere il JDK locale e il server applicazioni locale come parte del pacchetto di distribuzione saranno convertiti automaticamente per il caricamento automatico del JDK e del server applicazioni nell'archiviazione cloud.
* **Supporto per Azure SDK versione 2.1.**  La versione di anteprima di Azure Plugin for Eclipse rilasciata ad agosto 2013 richiede Azure SDK 2.1. Non usare la versione di anteprima di agosto 2013 con le versioni precedenti di Azure SDK e non usare Azure SDK 2.1 con le versioni precedenti di Azure Plugin for Eclipse.
* **Supporto per la versione di Eclipse Kepler.**  In relazione a ciò, la nuova versione minima richiesta dell'IDE di Eclipse è Indigo. Azure Plugin for Eclipse non viene più ufficialmente testato in Helios.

### <a name="july-3-2013"></a>3 luglio 2013
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di luglio 2013. L'aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti a livello di usabilità basati su commenti rispetto alla versione di anteprima di maggio 2013:

* **Possibilità di creare un nuovo account di archiviazione.** È stato aggiunto un pulsante **New** alla finestra di dialogo **Add Storage Account**. In questo modo è possibile creare un account di archiviazione nel plug-in per Eclipse, senza che sia necessario accedere al portale di gestione di Azure. Per usare questa funzionalità, è necessario avere già una sottoscrizione di Azure. Per altre informazioni sulla creazione di un nuovo account di archiviazione, vedere [Per creare un nuovo account di archiviazione].
* **Nuova opzione &quot;(auto)&quot; per l'account di archiviazione usato per la distribuzione automatica di JDK e server e per la memorizzazione nella cache.** Quando si usa l'opzione **Automatically upload** per il JDK e il server applicazioni, è possibile specificare **(auto)** per l'URL e l'account di archiviazione da usare quando si carica il JDK e il server applicazioni oppure quando si usa il Servizio di memorizzazione nella cache di Azure. Queste funzionalità useranno quindi automaticamente lo stesso account di archiviazione selezionato nella finestra di dialogo **Publish to Azure** . L'esercitazione [Creazione di un'applicazione Hello World per Azure in Eclipse] è stata aggiornata per l'uso della nuova opzione **(auto)** .
* **Possibilità di impostare gli endpoint di servizio di Azure.**  Specificare gli endpoint di servizio che determinano se l'applicazione viene distribuita e gestita tramite la piattaforma Azure globale, Azure gestita da 21Vianet in Cina o una piattaforma Azure privata. Per altre informazioni, vedere [Endpoint del servizio di Azure].
* **Possibilità di specificare una risorsa di archiviazione locale per distribuzioni di grandi dimensioni.**  Se la distribuzione è troppo grande per essere contenuta nella cartella approot predefinita, è possibile specificare una risorsa di archiviazione locale come destinazione di distribuzione per il JDK e il server applicazioni. Per altre informazioni, vedere [Distribuzione di distribuzioni di grandi dimensioni].
* **Supporto per le dimensioni A6 e A7 delle macchine virtuali di Azure.**  È possibile distribuire un servizio cloud nelle dimensioni delle macchine virtuali A6 e A7 a memoria elevata. Per altre informazioni su queste dimensioni, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure].
* **Aggiornamento della libreria Package for the Azure Libraries for Java.** Si basa sulla versione 0.4.4 dell' [API client di Microsoft Azure].

### <a name="may-1-2013"></a>1° maggio 2013
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di maggio 2013. Questo aggiornamento principale accompagna la versione di Azure SDK 2.0, che costituisce un prerequisito e verrà scaricata automaticamente quando si installa il plug-in. Questa versione include nuove funzionalità, correzioni di bug e alcuni miglioramenti a livello di usabilità basati su feedback rispetto alla versione di anteprima di febbraio 2013:

* **Caricamento automatico del JDK e del server applicazioni nell'archiviazione di Azure e distribuzione da tale area.**  Nuova opzione che consente di caricare automaticamente il JDK e il server applicazioni selezionati, quando necessario, in un account di archiviazione di Azure specificato e di distribuire questi componenti da tale account, invece di incorporarli nel pacchetto di distribuzione o richiedere all'utente di caricarli manualmente. Questa funzionalità comunemente richiesta può migliorare notevolmente la facilità di distribuzione dei componenti JDK e server, specialmente per gli utenti meno esperti. Per una procedura dettagliata sull'uso di queste opzioni, vedere [Creazione di un'applicazione Hello World per Azure in Eclipse].
* **Verifica dell'account di archiviazione centralizzato e possibilità di fare riferimento più facilmente agli account di archiviazione, tramite un elenco a discesa.**  Si applica a più funzionalità basate sull'archiviazione, ad esempio distribuzione dei componenti JDK e server e memorizzazione nella cache. Per altre informazioni, vedere [Elenco di account di archiviazione di Azure].
* **Configurazione dell'accesso remoto più semplice nella pubblicazione guidata nel cloud.**  È sufficiente digitare un nome utente e una password per abilitare l'accesso remoto oppure lasciare vuoti i campi per mantenerlo disabilitato.
* **Aggiornamento della libreria Package for the Azure Libraries for Java.** Si basa sulla versione 0.4.2 dell' [API client di Microsoft Azure].
* **Supporto per le sessioni permanenti in Windows Server 2012.**  In precedenza le sessioni permanenti funzionavano solo in Windows Server 2008 R2, mentre ora entrambe le destinazioni dei sistemi operativi cloud supportano l'affinità di sessione.
* **Miglioramenti delle prestazioni di caricamento del pacchetto.**  Anche quando il JDK e il server applicazioni sono incorporati nel pacchetto di distribuzione, la parte relativa al caricamento del processo di distribuzione può essere circa due volte più veloce rispetto alle versioni precedenti.

### <a name="february-8-2013"></a>8 febbraio 2013
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di febbraio 2013 Questo aggiornamento secondario include correzioni di bug, miglioramenti a livello di usabilità basati su feedback e alcune nuove funzionalità rispetto alla versione di anteprima di novembre 2012:

* Supporto per la distribuzione di JDK, server applicazioni e altri componenti arbitrari scaricati dall'archivio BLOB di Azure pubblico o privato, anziché includerli nel pacchetto di distribuzione quando si distribuisce nel cloud.
* Possibilità di modificare l'ordine in cui vengono elaborati i componenti di un ruolo definiti dall'utente, grazie all'aggiunta dei pulsanti **Move Up** e **Move Down** nella sezione **Components** di **Azure Role Properties**.
* Aggiornamento della libreria **Package for Azure Libraries for Java** basato sulla versione 0.4.0 dell' [API client di Microsoft Azure].

### <a name="november-5-2012"></a>5 Novembre 2012
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di novembre 2012. Questo aggiornamento principale include numerose nuove funzionalità, oltre a correzioni di bug aggiuntive e miglioramenti a livello di usabilità basati su feedback rispetto alla versione di anteprima di settembre 2012:

* Supporto per Microsoft Windows Server 2012 come sistema operativo cloud.
* Supporto per la memorizzazione nella cache di Azure con risorse condivise per i client che supportano Memcache.
* Inclusione delle librerie client di Apache Qpid JMS per sfruttare la messaggistica basata su Azure AMQP.
* Procedura guidata **New Project** migliorata con una nuova pagina alla fine che consente agli utenti di abilitare rapidamente diverse funzionalità principali comuni nel progetto: sessioni permanenti, memorizzazione nella cache e debug remoto.
* Riduzione automatica a 1 delle istanze del ruolo eseguite nell'emulatore di calcolo, per evitare conflitti di binding tra le istanze del server.

### <a name="september-28-2012"></a>28 settembre 2012
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di settembre 2012. L'aggiornamento del servizio include numerose correzioni di bug aggiuntive rispetto alla versione di anteprima di agosto 2012, oltre ad alcuni miglioramenti a livello di usabilità basati su feedback nelle funzionalità esistenti:

* Supporto per Microsoft Windows 8 e Microsoft Windows Server 2012 come sistema operativo di sviluppo, che risolve i problemi che in precedenza impedivano al plug-in di funzionare correttamente in questi sistemi operativi.
* Supporto migliorato per specificare gli intervalli di porte degli endpoint.
* Correzioni di bug relativi a percorsi di file contenenti spazi.
* Miglioramenti del menu di scelta rapida del ruolo per un accesso più rapido alle impostazioni di configurazione specifiche del ruolo.
* Perfezionamenti secondari nella procedura guidata **Publish to cloud** e alcune correzioni di bug aggiuntive.

### <a name="august-28-2012"></a>28 agosto 2012
È stata rilasciata la versione di anteprima di Azure Plugin for Eclipse di agosto 2012. L'aggiornamento del servizio include correzioni di bug aggiuntive rispetto alla versione di anteprima di luglio 2012, oltre a diversi miglioramenti a livello di usabilità basati su commenti per le funzionalità esistenti:

* Nella finestra di dialogo Azure Access Control Services Filter:
  * **Opzione per incorporare il certificato di firma** nel file WAR dell'applicazione per semplificare la distribuzione cloud.
  * **Opzione per creare un certificato autofirmato** nell'interfaccia utente del filtro ACS. Per altre informazioni sul filtro Servizio di controllo di accesso di Azure, vedere [Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse].
* Nella procedura guidata Azure Deployment Project applicabile anche alla pagina delle proprietà di configurazione server del ruolo:
  * **Individuazione automatica del percorso del JDK** nel computer, che è possibile ignorare.
  * **Rilevamento automatico del tipo di server** quando si seleziona la directory di installazione del server applicazioni.

### <a name="july-15-2012"></a>15 luglio 2012
La versione di anteprima di Azure Plugin for Eclipse rilasciata a luglio 2012 risolve diversi bug della massima priorità trovati e/o segnalati dagli utenti dopo il rilascio della versione di giugno 2012. Questo è solo un aggiornamento del servizio, non include nuove funzionalità.

### <a name="june-7-2012"></a>7 giugno 2012
È stata rilasciata la versione CTP di Azure Plugin for Eclipse di giugno 2012. Le nuove funzionalità includono:

* **Nuova procedura guidata Azure Deployment Project:** consente di selezionare il JDK, il server applicazioni Java e le applicazioni Java direttamente nell'interfaccia utente migliorata della procedura guidata. Nelle configurazioni del server predefinite che è possibile selezionare sono inclusi Tomcat 6, Tomcat 7, GlassFish OSE 3, Jetty 7, Jetty 8, JBoss 6 e JBoss 7 (versione autonoma). L'elenco di configurazioni del server può anche essere personalizzato. Questo miglioramento dell'interfaccia utente è un'alternativa al trascinamento della selezione di file compressi e alla copia di script di avvio, che in precedenza costituivano l'approccio principale. Quel metodo funziona ancora correttamente, ma verrà probabilmente usato per scenari più avanzati.
* **Pagina delle proprietà del ruolo di configurazione del server:** consente di cambiare facilmente i JDK, i server applicazioni e le applicazioni Java associati alla distribuzione dopo la creazione del progetto. Per altre informazioni, vedere [Proprietà di configurazione del server].
* **&quot;Procedura guidata &quot;Publish to cloud:** offre un modo semplice per distribuire il progetto in Azure direttamente da Eclipse, automatizzando il precedente processo impegnativo di recuperare le credenziali, accedere al portale di gestione di Azure, caricare il pacchetti e così via. Per un esempio di distribuzione diretta del progetto in Azure, vedere [Creazione di un'applicazione Hello World per Azure in Eclipse].
* **Barra degli strumenti di Azure:** in Eclipse è disponibile una barra degli strumenti di Azure che contiene i pulsanti per richiamare le funzionalità seguenti:
  * ![][ic710879] **Run in Azure Emulator**: esegue il progetto nell'emulatore.
  * ![][ic710880] **Reset Azure Emulator**: reimposta l'emulatore.
  * ![][ic710881] **Build Cloud Package for Azure**: compila il pacchetto per la distribuzione.
  * ![][ic710876] **New Azure Deployment Project**: crea un nuovo progetto di distribuzione di Azure.
  * ![][ic710882] **Publish to Azure Cloud**: pubblica il progetto in Azure.
  * ![][ic710883] **Unpublish**: elimina la distribuzione.
  * Molti di questi pulsanti della barra degli strumenti di Azure vengono usati in [Creazione di un'applicazione Hello World per Azure in Eclipse].
* **Azure Libraries for Java:** disponibile come parte della singola libreria Package for Azure Libraries for Java in Eclipse, accompagna l'installazione del plug-in e include anche tutte le dipendenze necessarie. Basta aggiungere un riferimento alla libreria nel progetto Java per evitare di scaricare altri elementi separatamente. Per altre informazioni, vedere [Installare il Toolkit di Azure per Eclipse.].
* **Microsoft JDBC Driver 4.0 per SQL Server disponibile durante l'installazione del plug-in:** durante l'installazione del nuovo plug-in è possibile installare la versione più recente di Microsoft JDBC Driver per SQL Server.
* **Azure Access Control Service Filter disponibile durante l'installazione del plug-in:** questo nuovo componente, incluso nella libreria di Eclipse all'interno del toolkit, consente alle applicazioni Web Java di usare direttamente l'autenticazione tramite il Servizio di controllo di accesso di Microsoft Azure (ACS) usando diversi provider di identità, come Google, Live.com e Yahoo!. Non è necessario scrivere logica di autenticazione personalizzata, ma è sufficiente configurare alcune opzioni per fare in modo che l'abilitazione degli utenti all'accesso tramite ACS venga eseguita dal filtro. È possibile concentrarsi solo sulla scrittura del codice che consente agli utenti di accedere alle risorse in base alla loro identità, restituita all'applicazione dal filtro all'interno dell'oggetto Request. Per un'esercitazione sull'uso del filtro ACS, vedere [Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse].
* **Rilevamento automatico del prerequisito di Azure SDK 1.7:** quando si crea un nuovo progetto di distribuzione di Azure, Azure SDK 1.7 viene scaricato automaticamente se non è già installato.
* **Endpoint di istanza:** consente l'accesso diretto agli endpoint delle porte per la comunicazione con le istanze del ruolo con carico bilanciato. Gli endpoint di istanza possono essere aggiunti tramite l'interfaccia utente degli endpoint, disponibile nella pagina [Endpoints properties] . Questo approccio consente di abilitare il debug remoto e la diagnostica JMX per istanze di calcolo specifiche eseguite nel cloud in scenari con distribuzioni a istanze multiple. 
* **Interfaccia utente dei componenti:** rende più semplice per gli utenti avanzati configurare le dipendenze del progetto tra singoli ruoli di Azure presenti nel progetto e altre risorse esterne, come i progetti di applicazioni Java. Facilita anche la descrizione della logica di distribuzione. Per altre informazioni, vedere [Proprietà dei componenti].
* **Aggiornamento automatico delle versioni precedenti del progetto:** quando si apre un'area di lavoro che include un progetto di Azure creato con una versione precedente del plug-in, i progetti precedenti saranno visualizzati in Eclipse come chiusi, perché le versioni di tali progetti non sono compatibili con la nuova versione del plug-in. Se si prova ad aprire uno di questi progetti precedenti, viene avviata una procedura di aggiornamento guidato. Se si accetta l'aggiornamento, viene creato un nuovo progetto, al cui nome viene aggiunto **_Upgraded**, che sarà aggiornato automaticamente per funzionare con la nuova versione del plug-in. Il nuovo progetto può essere rinominato secondo le esigenze. Il progetto originale non sarà modificato come parte dell'aggiornamento e rimarrà chiuso.

### <a name="december-10-2011"></a>10 dicembre 2011
È stata rilasciata la versione CTP di Azure Plugin for Eclipse di dicembre 2011. Le nuove funzionalità includono:

* **Supporto per affinità di sessione (&quot;sessioni permanenti&quot;):** consente di abilitare applicazioni Java con stato in cluster tramite una semplice casella di controllo. Per altre informazioni, vedere [Affinità di sessione].
* **Esempi di script di avvio predefiniti:** sono disponibili per i server Java più comuni (Tomcat, Jetty, JBoss, GlassFish) e possono essere semplicemente copiati e incollati dalle directory degli esempi del progetto nello script di avvio personalizzato.
* **Output di avvio dell'emulatore in tempo reale:** è possibile osservare l'esecuzione di tutti i passaggi dello script di avvio in una finestra della console dedicata, che mostra l'avanzamento e gli errori dello script mentre viene eseguito in Azure.
* **Monitoraggio automatico leggero di java.exe:** forza un riciclo dei ruoli quando si interrompe l'esecuzione di java.exe usando uno script leggero predefinito incluso nella distribuzione.
* **Interfaccia utente di configurazione del debug remoto di app Java:** consente di abilitare facilmente il debugger remoto di Eclipse per accedere all'app Java in esecuzione nell'emulatore o nel cloud di Azure, per poter esaminare il codice Java ed eseguire il debug in tempo reale. Per altre informazioni, vedere [Debug delle applicazione Azure in Eclipse].
* **Interfaccia utente di configurazione delle risorse di archiviazione locali:** non è più necessario configurare le risorse locali manipolando direttamente il codice XML. Questa funzionalità consente anche di accedere al percorso di file effettivo della risorsa locale dopo la distribuzione tramite una variabile di ambiente a cui è possibile fare riferimento direttamente dallo script di avvio. Per altre informazioni, vedere [Proprietà dell'archiviazione in locale].
* **Interfaccia utente di configurazione delle variabili di ambiente: **non è più necessario impostare le variabili di ambiente modificando manualmente il codice XML di configurazione. Per altre informazioni, vedere [Proprietà delle variabili di ambiente].
* **Driver JDBC per SQL Azure:** viene installato tramite il plug-in come libreria di Eclipse integrata direttamente, facilitando la programmazione per SQL Azure. 
* **Accesso rapido all'interfaccia utente di configurazione del ruolo tramite il menu di scelta rapida**: basta fare clic con il pulsante destro del mouse sulla cartella del ruolo e scegliere **Properties**.
* **Icone personalizzate del progetto di Azure e della cartella del ruolo:** per una migliore visibilità e un'esplorazione più facile dell'area di lavoro e del progetto.

## <a name="see-also"></a>Vedere anche
Per ulteriori informazioni sui Toolkit di Azure per gli IDE di Java, consultare i seguenti collegamenti:

* [Toolkit di Azure per Eclipse]
  * [Installare il Toolkit di Azure per Eclipse.]
  * [Creare un'app Web Hello World per Azure in Eclipse]
  * *Novità del Toolkit di Azure per Eclipse (questo articolo)*
* [Azure Toolkit per IntelliJ]
  * [Installazione del Toolkit di Azure per IntelliJ]
  * [Creare un'app Web Hello World per Azure in IntelliJ]
  * [Novità del Toolkit di Azure per IntelliJ]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'app Web Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclipse.]: ./azure-toolkit-for-eclipse-installation.md
[Installazione del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novità di Azure Toolkit per Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novità del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[pagina Web di Azul Systems per Zulu OpenJDK]: http://go.microsoft.com/fwlink/?LinkId=402457
[Endpoint del servizio di Azure]: http://go.microsoft.com/fwlink/?LinkID=699526
[Elenco di account di archiviazione di Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Proprietà dei componenti]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Creazione di un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Debug delle applicazione Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Distribuzione di distribuzioni di grandi dimensioni]: http://go.microsoft.com/fwlink/?LinkID=699536
[Endpoints properties]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[Proprietà delle variabili di ambiente]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[plug-in degli strumenti HDInsight per Eclipse]: ./hdinsight/hdinsight-apache-spark-eclipse-tool-plugin.md
[Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse]: http://go.microsoft.com/fwlink/?LinkID=264703
[come usare l'offload SSL]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installare il Toolkit di Azure per Eclipse.]: http://go.microsoft.com/fwlink/?LinkId=699546
[Proprietà dell'archiviazione in locale]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[API client di Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=280397
[Proprietà di configurazione del server]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Affinità di sessione]: http://go.microsoft.com/fwlink/?LinkID=699548
[offload SSL]: http://go.microsoft.com/fwlink/?LinkID=699549
[Per creare un nuovo account di archiviazione]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Dimensioni delle macchine virtuali e dei servizi cloud per Azure]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->



<!--HONumber=Nov16_HO3-->



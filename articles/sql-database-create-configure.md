<properties linkid="manage-services-how-to-configure-a-sqldb" urlDisplayName="How to configure" pageTitle="How to configure a SQL Database - Azure" metaKeywords="Azure creating SQL Server, Azure configuring SQL Server" description="Learn how to create and configure a logical server using SQL Server in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure SQL Database" authors="Lori Clark," solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Lori Clark,"></tags>

# <span id="configLogical"></span></a>Come creare e configurare un database SQL

In questo argomento verranno illustrate le procedure per la creazione e la configurazione di un server logico. Nel nuovo portale di gestione di Azure (anteprima) i flussi di lavoro revisionati consentono di creare prima un database e quindi un server.

Tuttavia, in questo argomento verrà creato prima il server. Questo approccio può essere preferibile se si dispone già di alcuni database di SQL Server da caricare.

## Sommario

-   [Procedura: Creare un server logico][Procedura: Creare un server logico]
-   [Procedura: Configurare il firewall per il server logico][Procedura: Configurare il firewall per il server logico]

## <span id="createLogical"></span></a>Procedura: Creare un server logico

1.  Accedere al [portale di gestione][portale di gestione].

2.  Fare clic su **SQL Database** e quindi, nella pagina iniziale SQL Database, su **SERVERS**.

3.  Fare clic su **Add** nella parte inferiore della pagina.

4.  In Server Settings immettere un nome di amministratore composto da una sola parola senza spazi.

    Nel database SQL viene utilizzata l'autenticazione SQL tramite una connessione crittografata. Con il nome specificato verrà creato un nuovo account di accesso con autenticazione di SQL Server assegnato al ruolo predefinito del server sysadmin.

    L'account di accesso non può essere un indirizzo di posta elettronica, un account utente Windows o un Windows Live ID. In un database SQL non sono supportate né l'autenticazione basata sulle attestazioni né l'autenticazione di Windows.

5.  Impostare una password complessa composta da più di otto caratteri, usando una combinazione di caratteri in minuscolo e maiuscolo e da un numero o un simbolo.

6.  Scegliere un'area, che determina la posizione geografica del server. Poiché le aree non possono essere cambiate facilmente, sceglierne una significativa per questo server. Selezionare la posizione più vicina. Per ridurre i costi relativi alla larghezza di banda in uscita e la latenza dei dati, è consigliabile mantenere il database e l'applicazione Azure nella stessa area.

7.  Assicurarsi che la casella di controllo **Allow Services** sia selezionata in modo da consentire la connessione al database tramite il portale di gestione per il database SQL, i servizi di archiviazione e altri servizi di Azure.

8.  Al termine, fare clic sul segno di spunta nella parte inferiore della pagina.

Si noti che non è stato specificato un nome di server. Il database SQL genera automaticamente il nome del server per garantire che non vi siano voci DNS duplicate. Il nome del server è una stringa alfanumerica di dieci caratteri. Il nome del server di database SQL non è modificabile.

Nel passaggio successivo si configurerà il firewall in modo da consentire l'accesso alle connessioni da applicazioni in esecuzione nella rete.

## <span id="configFWLogical"></span></a>Procedura: Configurare il firewall per il server logico

1.  Nel [portale di gestione][portale di gestione] fare clic su **SQL Databases**, su **Servers** e quindi sul server appena creato.

2.  Fare clic su **Configure**.

3.  Copiare l'indirizzo IP del client corrente, ovvero l'indirizzo IP su cui è in ascolto il router o il server proxy, se ci si connette da una rete. Il database SQL rileva l'indirizzo IP utilizzato dalla connessione corrente, pertanto è possibile creare una regola firewall per accettare le richieste di connessione da questo dispositivo.

4.  Incollare l'indirizzo IP nell'intervallo di inizio e di fine. In seguito, se si verificano errori di connessione in cui si informa che l'intervallo è troppo ristretto, è possibile modificare la regola per ampliarlo.

    Se i computer client utilizzano indirizzi IP assegnati dinamicamente, è necessario specificare un intervallo sufficientemente ampio da includere gli indirizzi IP assegnati ai computer della rete. Iniziare con un intervallo ristretto, quindi ampliarlo solo se è necessario.

5.  Immettere un nome per la regola firewall, ad esempio il nome del computer o della società.

6.  Fare clic sul segno di spunta per salvare la regola.

7.  Per completare il passaggio, fare clic su **Save** nella parte inferiore della pagina. Se il pulsante **Save** non è visualizzato, aggiornare la pagina del browser.

A questo punto sono stati configurati un server logico, una regola del firewall che consente le connessioni in ingresso dall'indirizzo IP e un account di accesso dell'amministratore. Nel passaggio successivo verranno completate le rimanenti procedure di configurazione nel computer locale.

**Nota:** il server logico appena creato è temporaneo e verrà ospitato dinamicamente nei server fisici di un data center. Se si elimina il server, tenere presente che si tratta di un'azione irreversibile. Assicurarsi di eseguire il backup di tutti i database caricati in seguito nel server.

  [Procedura: Creare un server logico]: #createLogical
  [Procedura: Configurare il firewall per il server logico]: #configFWLogical
  [portale di gestione]: http://manage.windowsazure.com

<properties urlDisplayName="Tomcat on Virtual Machine" pageTitle="Tomcat in una macchina virtuale - Esercitazione di Azure" metaKeywords="Azure vm, creating vm Tomcat, configuring vm Tomcat" description="Informazioni su come creare una macchina virtuale Windows e configurarla per eseguire un server applicazioni Apache Tomcat." metaCanonical="" services="virtual-machines" documentationCenter="Java" title="Come eseguire un server applicazioni Java su una macchina virtuale" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Come eseguire un server applicazioni Java su una macchina virtuale

Con Azure è possibile usare una macchina virtuale per fornire funzionalità di server. Si può ad esempio configurare una macchina virtuale in esecuzione in Azure per ospitare un server applicazioni Java, come Apache Tomcat. Una volta completata la lettura di questa guida, si disporrà di tutte le informazioni necessarie per creare una macchina virtuale in esecuzione in Azure e configurarla per eseguire un server applicazioni Java.

Si apprenderà come:

-   Creare una macchina virtuale in cui è già installato un JDK.
-   Accedere in remoto alla macchina virtuale.
-   Installare un server applicazioni Java sulla macchina virtuale.
-   Creare un endpoint per la macchina virtuale.
-   Aprire una porta nel firewall per il server applicazioni.

Ai fini di questa esercitazione, in una macchina virtuale verrà installato un server applicazioni Apache Tomcat. Dopo il completamento dell'installazione si otterrà un'installazione di Tomcat simile alla seguente.

![Macchina virtuale che esegue Apache Tomcat][Macchina virtuale che esegue Apache Tomcat]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Per creare una macchina virtuale

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **New**, quindi **Compute**, su **Virtual machine** e infine su **From Gallery**.
3.  Nella finestra di dialogo per la selezione della macchina virtuale selezionare **JDK 7 Windows Server 2012**.
    Si noti che è disponibile anche **JDK 6 Windows Server 2012** nel caso in cui siano presenti applicazioni legacy non ancora predisposte per l'esecuzione in JDK 7.
4.  Fare clic su **Avanti**.
5.  Nella finestra di dialogo **Virtual machine configuration**:

    1.  Specificare un nome per la macchina virtuale.
    2.  Specificare la dimensione da usare per la macchina virtuale.
    3.  Immettere un nome per l'amministratore nel campo **Nome utente**. Prendere nota di questo nome e della password che verrà immessa successivamente perché verranno usati per l'accesso in remoto alla macchina virtuale.
    4.  Immettere una password nel campo **Nuova password** e reimmetterla nel campo **Conferma**. Si tratta della password dell'account dell'amministratore.
    5.  Fare clic su **Avanti**.

6.  Nella finestra di dialogo **Configurazione macchina virtuale** successiva:

    1.  Per **Servizio cloud** usare l'impostazione predefinita di **Crea un nuovo servizio cloud**.
    2.  Il valore di **Nome DNS del servizio cloud** deve essere univoco in cloudapp.net. Se necessario, modificarlo in modo che in Azure sia indicato che è univoco.
    3.  Specificare un'area, un gruppo di affinità o una rete virtuale. Ai fini di questa esercitazione, specificare come area **Stati Uniti occidentali**.
    4.  Nella casella **Account di archiviazione** selezionare **Usa un account di archiviazione generato automaticamente**.
    5.  Nella casella **Set di disponibilità** selezionare **(Nessuno)**.
    6.  Fare clic su **Avanti**.

7.  Nella finestra di dialogo **Configurazione macchina virtuale** finale:

    1.  Accettare le voci di endpoint predefinite.
    2.  Fare clic su **Operazione completata**.

## Per accedere in remoto alla macchina virtuale

1.  Accedere al [portale di gestione][portale di gestione di Azure].
2.  Fare clic su **Macchine virtuali**.
3.  Fare clic sul nome della macchina virtuale a cui si desidera accedere.
4.  Una volta avviata la macchina virtuale, un menu a comparsa nella parte inferiore della pagina consente le connessioni.
5.  Fare clic su **Connetti**.
6.  Rispondere ai prompt visualizzati per connettersi alla macchina virtuale. Ciò implica salvare o aprire il file RDP che contiene i dettagli della connessione. Potrebbe essere necessario copiare url:port nell'ultima parte della prima riga del file RDP e incollarlo in un'applicazione di accesso remoto.

## Per installare un server applicazioni Java sulla macchina virtuale

È possibile copiare un server applicazioni Java nella macchina virtuale oppure installarlo tramite un apposito programma di installazione.

Ai fini di questa esercitazione, verrà installato Tomcat.

1.  Mentre si è connessi alla macchina virtuale, aprire una sessione del browser alla pagina <http://tomcat.apache.org/download-70.cgi>.
2.  Fare doppio clic sul collegamento per **32-bit/64-bit Windows Service Installer**. Questa tecnica consente di installare Tomcat come servizio Windows.
3.  Quando richiesto, scegliere di eseguire il programma di installazione.
4.  Nella procedura guidata **Apache Tomcat Setup** attenersi ai prompt per installare Tomcat. Ai fini di questa esercitazione, si possono accettare le impostazioni predefinite. Quando si arriva alla finestra di dialogo **Completing the Apache Tomcat Setup Wizard**, se si desidera, è possibile selezionare **Run Apache Tomcat** per avviare subito Tomcat. Fare clic su **Finish** per completare il processo di installazione di Tomcat.

## Per avviare Tomcat

Se si è scelto di non eseguire Tomcat nella finestra di dialogo **Completing the Apache Tomcat Setup Wizard**, per avviarlo aprire un prompt dei comandi nella macchina virtuale ed eseguire **net start Tomcat7**.

Tomcat dovrebbe risultare in esecuzione se si apre la pagina <http://localhost:8080> nel browser della macchina virtuale.

Per vedere Tomcat in esecuzione da macchine esterne, sarà necessario creare un endpoint e aprire una porta.

## Per creare un endpoint per la macchina virtuale

1.  Accedere al [portale di gestione][portale di gestione di Azure].
2.  Fare clic su **Macchine virtuali**.
3.  Fare clic sul nome della macchina virtuale che esegue il server applicazioni Java.
4.  Fare clic su **Endpoint**.
5.  Fare clic su **Aggiungi**.
6.  Nella finestra di dialogo **Add endpoint** assicurarsi che l'opzione **Add standalone endpoint** sia selezionata, quindi fare clic su **Next**.
7.  Nella finestra di dialogo **New endpoint details**:

    1.  Specificare un nome per l'endpoint, ad esempio **HttpIn**.
    2.  Specificare **TCP** per il protocollo.
    3.  Specificare **80** per la porta pubblica.
    4.  Specificare **8080** per la porta privata.
    5.  Fare clic sul pulsante **Complete** per chiudere la finestra di dialogo. A questo punto verrà creato l'endpoint.

## Per aprire nel firewall una porta per la macchina virtuale

1.  Accedere alla macchina virtuale.
2.  Fare clic sul pulsante **Start** di Windows.
3.  Fare clic su **Pannello di controllo**.
4.  Fare clic su **Sistema e sicurezza**, quindi su **Windows Firewall** e infine su **Impostazioni avanzate**.
5.  Fare clic su **Regole connessioni in entrata** e quindi su **Nuova regola**.

![Nuova regola connessioni in entrata][Nuova regola connessioni in entrata]

1.  Per la nuova regola selezionare **Porta** per il **tipo di regola** e quindi fare clic su **Avanti**.

![Porta della nuova regola connessioni in entrata][Porta della nuova regola connessioni in entrata]

1.  Selezionare **TCP** per il protocollo e **8080** per la porta, quindi fare clic su **Avanti**.

![Nuova regola connessioni in entrata][1]

1.  Scegliere **Consenti la connessione** e quindi fare clic su **Avanti**.

![Operazione per nuova regola connessioni in entrata][Operazione per nuova regola connessioni in entrata]

1.  Assicurarsi che le opzioni **Dominio**, **Privato** e **Pubblico** siano selezionate per il profilo e fare clic su **Avanti**.

![Profilo per nuova regola connessioni in entrata][Profilo per nuova regola connessioni in entrata]

1.  Specificare un nome per la regola, ad esempio **HttpIn** (non è tuttavia necessario che il nome della regola coincida con quello dell'endpoint), quindi fare clic su **Fine**.

![Nome della nuova regola connessioni in entrata][Nome della nuova regola connessioni in entrata]

A questo punto il sito Web Tomcat dovrebbe essere visibile da un browser esterno, usando un URL nel formato **http://*nome\_DNS*.cloudapp.net**, dove ***nome\_DNS*** è il nome DNS specificato durante la creazione della macchina virtuale.

## Considerazioni sul ciclo di vita delle applicazioni

-   È possibile creare il proprio archivio Web di applicazioni (WAR) e aggiungerlo alla cartella **webapps**. Ad esempio, creare un progetto Web dinamico JSP (Java Service Page) di base ed esportarlo come file WAR, quindi copiare il file WAR nella cartella di **webapps** Apache Tomcat sulla macchina virtuale ed eseguirlo in un browser.
-   Per impostazione predefinita, quando viene installato, il servizio Tomcat è impostato per l'avvio manuale. È possibile impostarlo per l'avvio automatico con lo snap-in Servizi. Per avviare tale snap-in, fare clic sul pulsante **Start** di Windows, quindi nel Pannello di controllo fare clic su **Strumenti di amministrazione** e infine **Servizi**. Per impostare Tomcat per l'avvio automatico, fare doppio clic sul servizio **Apache Tomcat** nello snap-in Servizi e impostare **Tipo di avvio** su **Automatico**, come illustrato nella figura seguente.

    ![Impostazione di un servizio per l'avvio automatico][Impostazione di un servizio per l'avvio automatico]

    L'impostazione di Tomcat per l'avvio automatico può risultare utile in quanto in caso di riavvio della macchina virtuale, ad esempio dopo l'installazione di aggiornamenti software che richiedono un riavvio, il servizio verrà riavviato automaticamente.

## Passaggi successivi

-   Per informazioni su altri servizi, ad esempio Archiviazione, Service Bus, Database SQL di Azure e altri ancora che può essere utile includere nelle applicazioni Java, fare riferimento alle informazioni disponibili all'indirizzo [http://www.windowsazure.com/it-it/develop/java/][http://www.windowsazure.com/it-it/develop/java/].

  [Macchina virtuale che esegue Apache Tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Nuova regola connessioni in entrata]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
  [Porta della nuova regola connessioni in entrata]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
  [1]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
  [Operazione per nuova regola connessioni in entrata]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
  [Profilo per nuova regola connessioni in entrata]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png
  [Nome della nuova regola connessioni in entrata]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
  [Impostazione di un servizio per l'avvio automatico]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png
  [http://www.windowsazure.com/it-it/develop/java/]: http://www.windowsazure.com/it-it/develop/java/

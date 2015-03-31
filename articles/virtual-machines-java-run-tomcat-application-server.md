<properties 
	pageTitle="Tomcat in una macchina virtuale - Esercitazione di Azure" 
	description="Informazioni su come creare una macchina virtuale Windows e configurarla per eseguire un server applicazioni Apache Tomcat." 
	services="virtual-machines" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="robmcm"/>

# Come eseguire un server applicazioni Java su una macchina virtuale

Con Azure è possibile usare una macchina virtuale per fornire funzionalità di server. Si può ad esempio configurare una macchina virtuale in esecuzione in Azure per ospitare un server applicazioni Java, come Apache Tomcat. Una volta completata la lettura di questa guida, si disporrà di tutte le informazioni necessarie per creare una macchina virtuale in esecuzione in Azure e configurarla per eseguire un server applicazioni Java.

Si apprenderà:

* Come creare una macchina virtuale in cui è già installato un JDK.
* Come accedere in remoto alla macchina virtuale.
* Come installare un server applicazioni Java sulla macchina virtuale.
* Come creare un endpoint per la macchina virtuale.
* Come aprire una porta nel firewall per il server applicazioni.

Ai fini di questa esercitazione, in una macchina virtuale verrà installato un server applicazioni Apache Tomcat. Dopo il completamento dell'installazione si otterrà un'installazione di Tomcat simile alla seguente.

![Virtual machine running Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Per creare una macchina virtuale

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Nuovo**, **Calcolo**, **Macchina virtuale** e quindi fare clic su **Da raccolta**.
3. Nella finestra di dialogo **Selezione immagine macchina virtuale** selezionare **JDK 7 Windows Server 2012**.
Si noti che è disponibile anche **JDK 6 Windows Server 2012** nel caso in cui siano presenti applicazioni non ancora predisposte per l'esecuzione in JDK 7.
4. Fare clic su **Avanti**.
5. Nella finestra di dialogo <strong>Configurazione macchina virtuale</strong> :
    1. Specificare un nome per la macchina virtuale.
    2. Specificare la dimensione da usare per la macchina virtuale.
    3. Immettere un nome per l'amministratore nel campo **Nome utente**. Prendere nota di questo nome e della password che verrà immessa successivamente perché verranno usati per l'accesso in remoto alla macchina virtuale.
    4. Immettere una password nel campo **Nuova password** e reimmetterla nel campo **Conferma**. Si tratta della password dell'account dell'amministratore.
    5. Fare clic su **Avanti**.
6. Nella finestra dialogo successiva <strong>Configurazione macchina virtuale</strong> :
    1. Per **Servizio cloud** usare l'impostazione predefinita di **Crea un nuovo servizio cloud**.
    2. Il valore di **Nome DNS del servizio cloud** deve essere univoco in cloudapp.net. Se necessario, modificarlo in modo che sia indicato come univoco in Azure.
    2. Specificare un'area, un gruppo di affinità o una rete virtuale. Ai fini di questa esercitazione, specificare come area **Stati Uniti occidentali**.
    2. Nella casella **Account di archiviazione** selezionare **Usa un account di archiviazione generato automaticamente**.
    3. Nella casella **Set di disponibilità** selezionare **(Nessuno)**.
    4. Fare clic su **Avanti**.
7. Nell'ultima finestra di dialogo <strong>Configurazione macchina virtuale</strong> :
    1. Accettare le voci di endpoint predefinite.
    2. Fare clic su **Operazione completata**.

## Per accedere in remoto alla macchina virtuale

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Macchine virtuali**.
3. Fare clic sul nome della macchina virtuale a cui si desidera accedere.
4. Una volta avviata la macchina virtuale, un menu a comparsa nella parte inferiore della pagina consente le connessioni.
5. Fare clic su **Connetti**.
6. Rispondere ai prompt visualizzati per connettersi alla macchina virtuale. Ciò implica salvare o aprire il file RDP che contiene i dettagli della connessione. Potrebbe essere necessario copiare url:port nell'ultima parte della prima riga del file RDP e incollarlo in un'applicazione di accesso remoto.

## Per installare un server applicazioni Java sulla macchina virtuale

È possibile copiare un server applicazioni Java nella macchina virtuale oppure installarlo tramite un apposito programma di installazione. 

Ai fini di questa esercitazione, verrà installato Tomcat.

1. Mentre si è connessi alla macchina virtuale, aprire una sessione del browser alla pagina <http://tomcat.apache.org/download-70.cgi>.
2. Fare doppio clic sul collegamento per **32-bit/64-bit Windows Service Installer**. Questa tecnica consente di installare Tomcat come servizio Windows.
3. Quando richiesto, scegliere di eseguire il programma di installazione.
4. Nella procedura guidata **Apache Tomcat Setup** attenersi ai prompt per installare Tomcat. Ai fini di questa esercitazione, si possono accettare le impostazioni predefinite. Quando viene visualizzata la finestra di dialogo **Completing the Apache Tomcat Setup Wizard**, è possibile selezionare **Run Apache Tomcat** per avviare subito Tomcat. Fare clic su **Finish** per completare il processo di installazione di Tomcat.

## Per avviare Tomcat
Se si è scelto di non eseguire Tomcat nella finestra di dialogo **Completing the Apache Tomcat Setup Wizard**, per avviarlo aprire un prompt dei comandi nella macchina virtuale ed eseguire **net start Tomcat7**.

Tomcat dovrebbe risultare in esecuzione se si apre la pagina <http://localhost:8080> nel browser della macchina virtuale.

Per vedere Tomcat in esecuzione da macchine esterne, sarà necessario creare un endpoint e aprire una porta.

## Per creare un endpoint per la macchina virtuale
1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Macchine virtuali**.
3. Fare clic sul nome della macchina virtuale che esegue il server applicazioni Java.
4. Fare clic su **Endpoint**.
5. Fare clic su **Aggiungi**.
6. Nella finestra di dialogo **Aggiungi endpoint** assicurarsi che l'opzione **Aggiungi endpoint autonomo** sia selezionata, quindi fare clic su **Avanti**.
7. Nella finestra di dialogo <strong>Dettagli nuovo endpoint</strong> :
    1. Specificare un nome per l'endpoint, ad esempio **HttpIn**.
    2. Specificare **TCP** per il protocollo.
    3. Specificare **80** per la porta pubblica.
    4. Specificare **8080** per la porta privata.
    5. Fare clic sul pulsante **Completa** per chiudere la finestra di dialogo. A questo punto verrà creato l'endpoint.

## Per aprire nel firewall una porta per la macchina virtuale
1. Accedere alla macchina virtuale.
2. Fare clic sul pulsante **Start di Windows**.
3. Fare clic su **Pannello di controllo**.
4. Fare clic su **Sistema e sicurezza**, quindi su **Windows Firewall** e infine su **Impostazioni avanzate**.
5. Fare clic su **Regole connessioni in entrata** e quindi su **Nuova regola**.

 ![New inbound rule][NewIBRule]

6. Per la nuova regola selezionare **Porta** per **Tipo di regola** e quindi fare clic su **Avanti**.

 ![New inbound rule port][NewRulePort]

7. Selezionare **TCP** per il protocollo e specificare **8080** per la porta, quindi fare clic su **Avanti**.

 ![New inbound rule ][NewRuleProtocol]

8. Scegliere **Consenti la connessione** e quindi fare clic su **Avanti**.

 ![New inbound rule action][NewRuleAction]

9. Assicurarsi che le opzioni **Dominio**, **Privato** e **Pubblico** siano selezionate per il profilo e fare clic su **Avanti**.

 ![New inbound rule profile][NewRuleProfile]

10. Specificare un nome per la regola, ad esempio **HttpIn** (non è tuttavia necessario che il nome della regola coincida con quello dell'endpoint), quindi fare clic su **Fine**.  

 ![New inbound rule name][NewRuleName]

A questo punto, il sito Web Tomcat dovrebbe essere visibile da un browser esterno, usando un URL nel formato **http://*nome\_DNS\*.cloudapp.net**, dove ***nome\_DNS\*** è il nome DNS specificato durante la creazione della macchina virtuale.

## Considerazioni sul ciclo di vita delle applicazioni
* È possibile creare il proprio archivio Web di applicazioni (WAR) e aggiungerlo alla cartella **webapps**. Ad esempio, creare un progetto Web dinamico JSP (Java Service Page) di base ed esportarlo come file WAR, quindi copiare il file WAR nella cartella di **webapps** Apache Tomcat sulla macchina virtuale ed eseguirlo in un browser.
* Per impostazione predefinita, quando viene installato, il servizio Tomcat è impostato per l'avvio manuale. È possibile impostarlo per l'avvio automatico con lo snap-in Servizi. Per avviare tale snap-in, fare clic sul pulsante **Start di Windows**, quindi nel Pannello di controllo fare clic su **Strumenti di amministrazione** e infine su **Servizi**. Per impostare Tomcat per l'avvio automatico, fare doppio clic sul servizio **Apache Tomcat** nello snap-in Servizi e impostare **Tipo di avvio** su **Automatico**, come illustrato nella figura seguente.

    ![Setting a service to start automatically][service_automatic_startup]

    L'impostazione di Tomcat per l'avvio automatico può risultare utile in quanto in caso di riavvio della macchina virtuale, ad esempio dopo l'installazione di aggiornamenti software che richiedono un riavvio, il servizio verrà riavviato automaticamente.

## Passaggi successivi
* Per informazioni su altri servizi, ad esempio Archiviazione, Service Bus, Database SQL di Azure e altri ancora che può essere utile includere nelle applicazioni Java, fare riferimento alle informazioni disponibili all'indirizzo <http://www.windowsazure.com/develop/java/>.

[virtual_machine_tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png

<!--HONumber=47-->

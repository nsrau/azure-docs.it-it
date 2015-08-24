<properties
	pageTitle="Tomcat in una macchina virtuale | Microsoft Azure"
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
	ms.date="06/03/2015"
	ms.author="robmcm"/>

# Come eseguire un server applicazioni Java su una macchina virtuale

Con Azure è possibile utilizzare una macchina virtuale per fornire funzionalità di server. Si può ad esempio configurare una macchina virtuale in esecuzione in Azure per ospitare un server applicazioni Java, come Apache Tomcat. Dopo aver completato la lettura di questa guida, si disporrà di tutte le informazioni necessarie per creare una macchina virtuale in esecuzione su Azure e configurarla per eseguire un server applicazioni Java.

Si acquisiranno le nozioni seguenti:

* Come creare una macchina virtuale che dispone di un Java Development Kit (JDK) già installato.
* Come accedere in remoto alla macchina virtuale.
* Installare un server applicazioni Java sulla macchina virtuale.
* Creare un endpoint per la macchina virtuale.
* Aprire una porta nel firewall per il server applicazioni.

Ai fini di questa esercitazione, verrà installato un server applicazioni Apache Tomcat su una macchina virtuale. Dopo il completamento dell'installazione si otterrà un'installazione di Tomcat simile alla seguente.

![Macchina virtuale che esegue Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Per creare una macchina virtuale

1. Accedere al [portale di Azure](https://manage.windowsazure.com).
2. Fare clic su **New**, quindi **Compute**, su **Virtual machine** e infine su **From Gallery**.
3. Nella finestra di dialogo **Seleziona immagine macchina virtuale** selezionare **JDK 7 Windows Server 2012**. Se si dispone di applicazioni legacy non ancora predisposte per l'esecuzione in JDK 7, è disponibile anche **JDK 6 Windows Server 2012**.
4. Fare clic su **Next**.
5. Nella finestra di dialogo **Configurazione macchina virtuale**:
    1. Specificare un nome per la macchina virtuale.
    2. Specificare la dimensione da utilizzare per la macchina virtuale.
    3. Immettere un nome per l'amministratore nel campo **User Name**. Prendere nota del nome e della password immesse successivamente, perché verranno utilizzate per l'accesso in remoto alla macchina virtuale.
    4. Immettere una password nel campo **New password** e reimmetterlo nel campo **Confirm**. Si tratta della password dell'account dell'amministratore.
    5. Fare clic su **Avanti**.
6. Nella finestra di dialogo **Configurazione macchina virtuale** successiva:
    1. Per **Cloud service** utilizzare l'impostazione predefinita di **Create a new cloud service**.
    2. Il valore di **Nome DNS del servizio cloud** deve essere univoco in cloudapp.net. Se necessario, modificarlo in modo che sia indicato come univoco in Azure.
    2. Specificare un'area, un gruppo di affinità o una rete virtuale. Ai fini di questa esercitazione, specificare un’area come **Stati Uniti Occidentali**.
    2. Nella casella **Storage Account** selezionare **Use an automatically generated storage account**.
    3. Nella casella **Availability Set** selezionare **(None)**.
    4. Fare clic su **Avanti**.
7. Nella finestra di dialogo **Configurazione macchina virtuale** finale:
    1. Accettare le voci di endpoint predefinite.
    2. Fare clic su **Complete**.

## Per accedere in remoto alla macchina virtuale

1. Accedere al [Portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Virtual machines**.
3. Fare clic sul nome della macchina virtuale a cui si desidera accedere.
4. Dopo aver avviato la macchina virtuale, è possibile eseguire le connessioni tramite un menu a comparsa nella parte inferiore della pagina.
5. Fare clic su **Connect**.
6. Rispondere ai prompt visualizzati per connettersi alla macchina virtuale. Ciò implica salvare o aprire il file RDP che contiene i dettagli della connessione. Potrebbe essere necessario copiare url:port nell'ultima parte della prima riga del file .rdp e incollarlo in un'applicazione di accesso remoto.

## Per installare un server applicazioni Java sulla macchina virtuale

È possibile copiare un server applicazioni Java sulla macchina virtuale oppure installarlo tramite un programma di installazione.

Ai fini di questa esercitazione, verrà installato Tomcat.

1. Una volta eseguito l’accesso alla macchina virtuale, aprire una sessione del browser alla pagina [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Fare doppio clic sul collegamento per **32-bit/64-bit Windows Service Installer**. Questa tecnica consentirà di installare Tomcat come servizio di Windows.
3. Quando richiesto, scegliere di eseguire il programma di installazione.
4. Nella procedura guidata **Apache Tomcat Setup** attenersi ai prompt per installare Tomcat. Ai fini di questa esercitazione, è possibile accettare le impostazioni predefinite. Quando si arriva alla finestra di dialogo **Completamento della configurazione guidata di Apache Tomcat**, è possibile selezionare **Esegui Apache Tomcat** per avviare subito Tomcat. Fare clic su **Finish** per completare il processo di installazione di Tomcat.

## Per avviare Tomcat
Se si è scelto di non eseguire Tomcat nella finestra di dialogo **Completamento della configurazione guidata di Apache Tomcat**, per avviarlo aprire un prompt dei comandi sulla macchina virtuale ed eseguire **net start Tomcat7**.

Tomcat dovrebbe risultare in esecuzione se si apre la pagina <http://localhost:8080> nel browser della macchina virtuale.

Per vedere Tomcat in esecuzione da macchine esterne, è necessario creare un endpoint e aprire una porta.

## Per creare un endpoint per la macchina virtuale
1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Virtual machines**.
3. Fare clic sul nome della macchina virtuale che esegue il server applicazioni Java.
4. Fare clic su **Endpoints**.
5. Fare clic su **Aggiungi**.
6. Nella finestra di dialogo **Aggiungi endpoint**, assicurarsi che l'opzione **Aggiungi endpoint autonomo** sia selezionata, quindi fare clic su **Avanti**.
7. Nella finestra di dialogo **Dettagli nuovo endpoint**:
    1. Specificare un nome per l'endpoint, ad esempio **HttpIn**.
    2. Specificare **TCP** per il protocollo.
    3. Specificare **80** per la porta pubblica.
    4. Specificare **8080** per la porta privata.
    5. Fare clic sul pulsante **Operazione completata** per chiudere la finestra di dialogo. A questo punto verrà creato l'endpoint.

## Per aprire nel firewall una porta per la macchina virtuale
1. Accedere alla macchina virtuale.
2. Fare clic sul pulsante **Start** di Windows.
3. Fare clic su **Pannello di controllo**.
4. Fare clic su **Sistema e sicurezza**, quindi su **Windows Firewall** e infine su **Impostazioni avanzate**.
5. Fare clic su **Regole connessioni in entrata**, quindi su **Nuova regola**. ![Nuova regola connessioni in entrata][NewIBRule]
6. Per **Tipo regola**, selezionare **Porta**, quindi fare clic su **Avanti**. ![Porta della nuova regola connessioni in entrata][NewRulePort]
7. Nella schermata **Protocollo e porte**, selezionare **TCP**, specificare **8080** come **Porta locale specifica**, quindi fare clic su **Avanti**. ![Nuova regola connessioni in entrata][NewRuleProtocol]
8. Nella schermata **Azione**, selezionare **Consenti la connessione**, quindi fare clic su **Avanti**. ![Operazione per nuova regola connessioni in entrata][NewRuleAction]
9. Nella schermata **Profilo**, verificare che le opzioni **Dominio**, **Privato** e **Pubblico** siano selezionate, quindi fare clic su **Avanti**. ![Profilo per nuova regola connessioni in entrata][NewRuleProfile]
10. Nella schermata **Nome**, specificare un nome per la regola, come **HttpIn** (non è tuttavia necessario che il nome della regola coincida con quello dell'endpoint), quindi fare clic su **Fine**. ![Nome della nuova regola connessioni in entrata][NewRuleName]

A questo punto, il sito Web Tomcat dovrebbe essere visibile da un browser esterno utilizzando un URL nel formato ****http://*your\_DNS\_name*.cloudapp.net**, dove ***your\_DNS\_name*** è il nome DNS specificato durante la creazione della macchina virtuale.

## Considerazioni sul ciclo di vita delle applicazioni
* È possibile creare il proprio archivio di applicazioni Web (WAR) e aggiungerlo alla cartella **webapps**. Ad esempio, creare un progetto Web dinamico JSP (Java Service Page) di base ed esportarlo come file WAR, quindi copiare il file WAR nella cartella di **webapps** Apache Tomcat sulla macchina virtuale ed eseguirlo in un browser.
* Per impostazione predefinita, quando viene installato, il servizio Tomcat è impostato per l'avvio manuale. È possibile passare a esso per avviare automaticamente tramite lo snap-in Servizi. Avviare lo snap-in Servizi facendo clic su **Start di Windows**, **Strumenti di amministrazione**, **Servizi**. Fare doppio clic sul servizio **Apache Tomcat** e impostare **Tipo di avvio** su **Automatico**.

    ![Impostazione di un servizio per l'avvio automatico][service_automatic_startup]

    L'impostazione di Tomcat per l'avvio automatico può risultare utile in quanto in caso di riavvio della macchina virtuale, ad esempio dopo l'installazione di aggiornamenti software che richiedono un riavvio, il servizio verrà riavviato automaticamente.

## Passaggi successivi
Scoprire di più su altri servizi, come Archiviazione, Bus di servizio e Database SQL di Azure, che si desidera includere nelle applicazioni Java consultando le informazioni disponibili nel [Centro per sviluppatori Java](http://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png

<!---HONumber=August15_HO7-->
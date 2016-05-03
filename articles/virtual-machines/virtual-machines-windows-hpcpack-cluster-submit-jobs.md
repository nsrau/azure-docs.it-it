<properties
 pageTitle="Inviare processi a un cluster HPC Pack in Azure | Microsoft Azure"
 description="Informazioni su come configurare un computer locale per l'invio di processi a un cluster HPC Pack in Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="04/14/2016"
 ms.author="danlep"/>

# Inviare i processi HPC da un computer locale a un cluster HPC Pack distribuito in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configurare un computer client locale che esegue Windows per l'esecuzione di strumenti di invio di processi di HPC Pack che comunicano con un cluster HPC Pack in Azure tramite HTTPS. Per molti utenti cluster questa procedura rappresenta un modo semplice e flessibile per inviare processi a un cluster HPC Pack basato sul cloud senza la necessità di connettersi direttamente alla VM del nodo head o di accedere a una sottoscrizione di Azure per eseguire strumenti di invio di processi.

![Invio di un processo a un cluster in Azure][jobsubmit]

## Prerequisiti

* **Nodo head HPC Pack distribuito in una VM di Azure**: si consiglia l'uso di strumenti automatizzati, ad esempio un [modello di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) o uno [script di Azure PowerShell](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) per distribuire il nodo head e il cluster. Per completare la procedura descritta in questo articolo, sono necessari il nome DNS del nodo head e le credenziali di un amministratore del cluster.

* **Supporto di installazione di HPC Pack**: il pacchetto di installazione gratuito della versione più recente di HPC Pack (HPC Pack 2012 R2) è disponibile per il download nell'[Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Verificare di avere scaricato la stessa versione di HPC Pack installata nella VM del nodo head.

* **Computer client**: è necessario un computer client Windows o Windows server in grado di eseguire le utilità client di HPC Pack (vedere i [requisiti di sistema](https://technet.microsoft.com/library/dn535781.aspx)). Se si prevede di inviare i processi solo tramite il portale Web di HPC Pack o l'API REST, è possibile usare un computer client qualsiasi.


## Passaggio 1: installare e configurare i componenti Web nel nodo head

Per consentire a un'interfaccia REST di inviare processi al cluster tramite HTTPS, è necessario installare e configurare i componenti Web di HPC Pack nel nodo head HPC Pack. Installare innanzitutto i componenti Web eseguendo il file di installazione HpcWebComponents.msi. Configurare quindi i componenti eseguendo lo script di HPC PowerShell **Set-HPCWebComponents.ps1**.

Per le procedure dettagliate, vedere [Installare i componenti Web di Microsoft HPC Pack](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Alcuni modelli di avvio rapido di Azure per HPC Pack installano e configurano i componenti Web automaticamente. Se si usa lo [script di distribuzione IaaS di HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) per creare il cluster, è possibile scegliere di installare e configurare i componenti Web durante la distribuzione.

**Per installare i componenti Web**

1. Connettersi alla macchina virtuale del nodo head usando le credenziali di amministratore del cluster.

2. Dalla cartella di installazione di HPC Pack, eseguire HpcWebComponents.msi nel nodo head.

3. Seguire i passaggi della procedura guidata per installare i componenti Web

**Per configurare i componenti Web**

1. Nel nodo head avviare HPC PowerShell come amministratore.

2. Per spostarsi sul percorso dello script di configurazione, digitare il comando seguente:

    ```
    cd $env:CCP_HOME\bin
    ```
3. Per configurare l'interfaccia REST e avviare il servizio Web HPC, digitare il comando seguente:

    ```
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Quando viene richiesto di selezionare un certificato, scegliere il certificato corrispondente al nome DNS pubblico del nodo head. Ad esempio, se si usa lo script di distribuzione di HPC Pack IaaS per creare il cluster, il nome del certificato è nel formato CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net. Se si usa un modello di avvio rapido di Azure, il nome del certificato è nel formato CN=&lt;*HeadNodeDnsName*&gt;.&lt;*region*&gt;.cloudapp.azure.

    >[AZURE.NOTE] È necessario selezionare questo certificato per inviare processi al nodo head da un computer locale in un momento successivo. Non selezionare né configurare un certificato corrispondente al nome computer del nodo head nel dominio Active Directory, ad esempio CN=*MyHPCHeadNode.HpcAzure.local*.

5. Per configurare il portale Web per l'invio di processi, digitare il comando seguente:

    ```
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Al termine dello script, arrestare e riavviare il Servizio utilità di pianificazione processi HPC digitando i comandi seguenti:

    ```
    net stop hpcscheduler
    net start hpcscheduler
    ```

## Passaggio 2: installare le utilità client di HPC Pack in un computer locale

Se si vogliono scaricare le utilità client di HPC Pack (installazione completa) dall'[Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024) nel computer client. All'inizio della procedura di installazione, scegliere l'opzione di installazione per le utilità client di HPC Pack.

Per usare gli strumenti client di HPC Pack per inviare processi alla macchina virtuale del nodo head, è necessario esportare un certificato dal nodo head e installarlo nel computer client. Il certificato deve essere in formato CER.

**Per esportare il certificato dal nodo head**

1. Nel nodo head aggiungere lo snap-in Certificati a Microsoft Management Console per l'account computer locale. Per la procedura che illustra come aggiungere lo snap-in, vedere [Aggiungere lo snap-in Certificati a MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. Nell'albero della console espandere **Certificati - Computer locale** > **Personale** e quindi fare clic su **Certificati**.

3. Individuare il certificato configurato per i componenti Web di HPC Pack in [Passaggio 1: Installare e configurare i componenti Web nel nodo head](#step-1:-install-and-configure-the-web-components-on-the-head-node), CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net.

4. Fare clic con il pulsante destro del mouse sul certificato, scegliere **Tutte le attività** e quindi fare clic su **Esporta**.

5. In Esportazione guidata certificati fare clic su **Avanti**, quindi assicurarsi che l'opzione **No, non esportare la chiave privata** sia selezionata.

6. Seguire i passaggi rimanenti della procedura guidata per esportare il certificato nel formato binario codificato DER X.509 (.CER).


**Per importare il certificato nel computer client**


1. Copiare il certificato esportato dal nodo head in una cartella nel computer client.

2. Nel computer client eseguire certmgr.msc.

3. In Gestione certificati espandere **Certificati - Utente corrente** > **Autorità di certificazione radice attendibili**, fare clic con il pulsante destro del mouse su **Certificati**, scegliere **Tutte le attività** e quindi fare clic su **Importa**.

4. Nell'Importazione guidata certificati fare clic su **Avanti** e seguire i passaggi per importare il certificato esportato dal nodo head nell'archivio Autorità di certificazione radice disponibile nell'elenco locale.



>[AZURE.SECURITY] Potrebbe venire visualizzato un avviso di sicurezza perché l'autorità di certificazione nel nodo head non verrà riconosciuta dal computer client. A scopo di test è possibile ignorare questo avviso e completare l'importazione del certificato.

## Passaggio 3: eseguire processi di prova sul cluster

Per verificare la configurazione, provare a eseguire processi nel cluster in Azure usando il computer locale che esegue le utilità client di HPC Pack. È ad esempio possibile usare gli strumenti dell'interfaccia utente grafica o gli strumenti della riga di comando di HPC Pack per inviare i processi al cluster oppure un portale basato sul Web.


**Per eseguire comandi di invio processi nel computer client**


1. Nel computer client, avviare un prompt dei comandi.

2. Digitare un comando di esempio. Ad esempio, per elencare tutti i processi nel cluster, digitare un comando simile a uno dei seguenti in base al nome DNS completo del nodo head:

    ```
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all

    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Nell'URL dell'Utilità di pianificazione usare il nome DNS completo del nodo head e non l'indirizzo IP. Se si specifica l'indirizzo IP, viene restituito un errore che indica che "è necessario che il certificato del server includa una catena di certificati valida o deve essere posizionato nell'archivio radice attendibile".

3. Quando richiesto, digitare il nome utente (nel formato &lt;DomainName&gt;\\&lt;UserName&gt;) e la password dell'amministratore del cluster HPC o di un altro utente cluster configurato. È possibile scegliere di archiviare le credenziali in locale per ulteriori operazioni di processo.

    Verrà visualizzato un elenco di processi.


**Per usare HPC Job Manager nel computer client**

1. Se in precedenza le credenziali di dominio per un utente cluster non sono state archiviate nel computer client quando è stato inviato il processo, è possibile aggiungerle in Gestione credenziali.

    a. Nel Pannello di controllo del computer client avviare Gestione credenziali.

    b. Fare clic su **Credenziali Windows** e quindi su **Aggiungi credenziali generiche**.

    c. Specificare l'indirizzo Internet, ad esempio https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler o https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com/HpcScheduler), quindi specificare il nome utente, nel formato &lt;NomeDominio&gt;\\&lt;NomeUtente&gt;, e la password dell'amministratore del cluster HPC o di un altro utente del cluster configurato.

2. Nel computer client avviare Gestione processi HPC.

3. Nella finestra di dialogo **Seleziona nodo head** digitare l'URL del nodo head in Azure, ad esempio https://&lt;HeadNodeDnsName&gt;.cloudapp.net o https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com).

    Verrà visualizzato il gestore dei processi HPC con un elenco dei processi sul nodo head.

**Per usare il portale Web in esecuzione nel nodo head**

1. Avviare un Web browser nel computer client e digitare uno dei seguenti indirizzi in base al nome DNS completo del nodo head:

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal

    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Nella finestra di dialogo di sicurezza che viene visualizzata digitare le credenziali di dominio dell'amministrazione cluster HPC. È anche possibile aggiungere altri utenti cluster in ruoli diversi. Vedere [Gestione degli utenti del cluster](https://technet.microsoft.com/library/ff919335.aspx).

    Verrà visualizzato l'elenco dei processi.

3. Per inviare un processo di esempio che restituisca la stringa "Hello World" dal cluster, fare clic su **Nuovo processo** nel riquadro di spostamento a sinistra.

4. Nella sezione **Dalle pagine di invio** della pagina **Nuovo processo** fare clic su **HelloWorld**. Verrà visualizzata la pagina di invio processi.

5. Fare clic su **Submit**. Se richiesto, specificare le credenziali di dominio dell'amministratore cluster HPC. Il processo viene inviato e, nella pagina **Processi**, viene visualizzato l'ID del processo.

6. Per visualizzare i risultati del processo inviato, fare clic sull'ID del processo e scegliere **Visualizza attività** per visualizzare l'output del comando (in **Output**).

## Passaggi successivi

* Per inviare processi al cluster Azure, è anche possibile usare l'[API REST HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).

* Per inviare processi cluster da un client Linux, vedere l'esempio Python in [HPC Pack 2012 R2 SDK e codice di esempio](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png

<!---HONumber=AcomDC_0420_2016-->
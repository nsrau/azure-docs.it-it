---
title: Inviare processi a un cluster HPC Pack in Azure | Microsoft Docs
description: Informazioni su come configurare un computer locale per l'invio di processi a un cluster HPC Pack in Azure
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: d5953f1e1dd2deb4d871bd67352a6a5b2ae13dbf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Inviare i processi HPC da un computer locale a un cluster HPC Pack distribuito in Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Configurare un computer client locale per l'invio di processi a un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) in Azure. In questo articolo viene illustrato come configurare un computer locale con gli strumenti client per inviare processi tramite HTTPS al cluster in Azure. In questo modo, più utenti di cluster possono inviare processi a un cluster HPC Pack basato sul cloud, ma senza connettersi direttamente alla VM del nodo head o accedere a una sottoscrizione di Azure.

![Invio di un processo a un cluster in Azure][jobsubmit]

## <a name="prerequisites"></a>Prerequisiti
* **Nodo head HPC Pack distribuito in una VM di Azure**: si consiglia l'uso di strumenti automatizzati, ad esempio un [modello di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) o uno [script di Azure PowerShell](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) per distribuire il nodo head e il cluster. Per completare la procedura descritta in questo articolo, sono necessari il nome DNS del nodo head e le credenziali di un amministratore del cluster.
* **Computer client** : è necessario un computer client Windows o Windows server in grado di eseguire le utilità client di HPC Pack (vedere i [requisiti di sistema](https://technet.microsoft.com/library/dn535781.aspx)). Se si prevede di inviare i processi solo tramite il portale Web di HPC Pack o l'API REST, è possibile usare un computer client qualsiasi.
* **Supporto di installazione di HPC Pack** : per installare il client di HPC Pack, è disponibile gratuitamente il pacchetto di installazione dell'ultima versione di HPC Pack (HPC Pack 2012 R2) è disponibile per il download nell' [Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Verificare di avere scaricato la stessa versione di HPC Pack installata nella VM del nodo head.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Passaggio 1: installare e configurare i componenti Web nel nodo head
Per consentire a un'interfaccia REST di inviare processi al cluster tramite HTTPS, assicurarsi che i componenti Web di HPC Pack siano configurati nel nodo head HPC Pack. Se non sono ancora installati, per prima cosa procedere all'installazione dei componenti Web eseguendo il file di installazione HpcWebComponents.msi. Configurare quindi i componenti eseguendo lo script di HPC PowerShell **Set-HPCWebComponents.ps1**.

Per le procedure dettagliate, vedere [Installare i componenti Web di Microsoft HPC Pack](http://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Alcuni modelli di avvio rapido di Azure per HPC Pack installano e configurano i componenti Web automaticamente. Se si usa lo [script di distribuzione IaaS di HPC Pack](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) per creare il cluster, è possibile scegliere di installare e configurare i componenti Web durante la distribuzione.
> 
> 

**Per installare i componenti Web**

1. Connettersi alla macchina virtuale del nodo head usando le credenziali di amministratore del cluster.
2. Dalla cartella di installazione di HPC Pack, eseguire HpcWebComponents.msi nel nodo head.
3. Seguire i passaggi della procedura guidata per installare i componenti Web

**Per configurare i componenti Web**

1. Nel nodo head avviare HPC PowerShell come amministratore.
2. Per spostarsi sul percorso dello script di configurazione, digitare il comando seguente:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Per configurare l'interfaccia REST e avviare il servizio Web HPC, digitare il comando seguente:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. Quando viene richiesto di selezionare un certificato, scegliere il certificato corrispondente al nome DNS pubblico del nodo head. Ad esempio, se si distribuisce la VM del nodo head usando il modello di distribuzione classica, il nome del certificato ha un aspetto simile a CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net. Se si usa un modello di distribuzione di Resource Manager, il nome del certificato ha un aspetto simile a CN=&lt;*HeadNodeDnsName*&gt;.&lt;*region*&gt;.cloudapp.azure.com.
   
   > [!NOTE]
   > È necessario selezionare questo certificato per inviare processi al nodo head da un computer locale in un momento successivo. Non selezionare né configurare un certificato corrispondente al nome computer del nodo head nel dominio di Active Directory, ad esempio CN=*MyHPCHeadNode.HpcAzure.local*.
   > 
   > 
5. Per configurare il portale Web per l'invio di processi, digitare il comando seguente:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Al termine dello script, arrestare e riavviare il servizio Utilità di pianificazione dei processi HPC digitando i comandi seguenti:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Passaggio 2: installare le utilità client di HPC Pack in un computer locale
Se si desidera installare le utilità client di HPC Pack nel computer, scaricare i file di installazione (installazione completa) dall' [Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). All'inizio della procedura di installazione, scegliere l'opzione di installazione per le **utilità client di HPC Pack**.

Per usare gli strumenti client di HPC Pack per inviare processi alla VM del nodo head, è necessario anche esportare un certificato dal nodo head e installarlo nel computer client. Il certificato deve essere in formato .CER.

**Per esportare il certificato dal nodo head**

1. Nel nodo head aggiungere lo snap-in Certificati a Microsoft Management Console per l'account computer locale. Per la procedura che illustra come aggiungere lo snap-in, vedere [Aggiungere lo snap-in Certificati a MMC](https://technet.microsoft.com/library/cc754431.aspx).
2. Nell'albero della console espandere **Certificati – Computer locale** > **Personale**, quindi fare clic su **Certificati**.
3. Individuare il certificato configurato per i componenti Web di HPC Pack in [Passaggio 1: installare e configurare i componenti Web nel nodo head](#step-1:-install-and-configure-the-web-components-on-the-head-node) (ad esempio, CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net).
4. Fare clic con il pulsante destro del mouse sul nome del certificato e scegliere **All Tasks** (Tutte le attività) > **Esporta**.
5. In Esportazione guidata certificati fare clic su **Avanti**, quindi assicurarsi che l'opzione **Non esportare la chiave privata** sia selezionata.
6. Seguire i passaggi rimanenti della procedura guidata per esportare il certificato nel formato binario codificato DER X.509 (.CER).

**Per importare il certificato nel computer client**

1. Copiare il certificato esportato dal nodo head in una cartella nel computer client.
2. Nel computer client eseguire certmgr.msc.
3. In Gestione certificati espandere **Certificati - Utente corrente** > **Autorità di certificazione principale attendibili**, fare clic con il pulsante destro del mouse su **Certificati**, quindi fare clic su **All Tasks** (Tutte le attività) > **Importa**.
4. Nell'Importazione guidata certificati fare clic su **Avanti** e seguire i passaggi per importare il certificato esportato dal nodo head nell'archivio Autorità di certificazione radice disponibile nell'elenco locale.

> [!TIP]
> Potrebbe venire visualizzato un avviso di sicurezza perché l'autorità di certificazione nel nodo head non viene riconosciuta dal computer client. A scopo di test è possibile ignorare questo avviso e completare l'importazione del certificato.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Passaggio 3: eseguire processi di prova sul cluster
Per verificare la configurazione, provare a eseguire processi nel cluster in Azure usando il computer locale. È ad esempio possibile usare gli strumenti dell'interfaccia utente grafica o gli strumenti della riga di comando di HPC Pack per inviare i processi al cluster oppure un portale basato sul Web.

**Per eseguire comandi di invio processi nel computer client**

1. In un computer client con installate le utilità client HPC Pack avviare un prompt dei comandi.
2. Digitare un comando di esempio. Ad esempio, per elencare tutti i processi nel cluster, digitare un comando simile a uno dei seguenti in base al nome DNS completo del nodo head:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    oppure
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Nell'URL dell'Utilità di pianificazione usare il nome DNS completo del nodo head e non l'indirizzo IP. Se si specifica l'indirizzo IP, viene visualizzato un errore che indica che è necessario che il certificato del server includa una catena di certificati valida o sia posizionato nell'archivio radice attendibile.
   > 
   > 
3. Quando richiesto, digitare il nome utente (nel formato &lt;DomainName&gt;\\&lt;UserName&gt;) e la password dell'amministratore del cluster HPC o di un altro utente cluster configurato. È possibile scegliere di archiviare le credenziali in locale per ulteriori operazioni di processo.
   
    Verrà visualizzato un elenco di processi.

**Per usare HPC Job Manager nel computer client**

1. Se in precedenza le credenziali di dominio per un utente cluster non sono state archiviate quando è stato inviato il processo, è possibile aggiungerle in Gestione credenziali.
   
    a. Nel Pannello di controllo del computer client avviare Gestione credenziali.
   
    b. Fare clic su **Credenziali Windows** > **Aggiungi credenziali generiche**.
   
    c. Specificare l'indirizzo Internet (ad esempio https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler o https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com/HpcScheduler) e il nome utente (&lt;DomainName&gt;\\&lt;UserName&gt;) e la password dell'amministratore del cluster o un altro utente cluster configurato.
2. Nel computer client avviare Gestione processi HPC.
3. Nella finestra di dialogo **Select Head Node** (Seleziona nodo head) digitare l'URL del nodo head in Azure (ad esempio https://&lt;HeadNodeDnsName&gt;.cloudapp.net o https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com).
   
    Verrà visualizzato il gestore dei processi HPC con un elenco dei processi sul nodo head.

**Per usare il portale Web in esecuzione nel nodo head**

1. Avviare un Web browser nel computer client e immettere uno dei seguenti indirizzi in base al nome DNS completo del nodo head:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    oppure
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Nella finestra di dialogo di sicurezza che viene visualizzata digitare le credenziali di dominio dell'amministrazione cluster HPC. È anche possibile aggiungere altri utenti cluster in ruoli diversi. Vedere [Gestione degli utenti del cluster](https://technet.microsoft.com/library/ff919335.aspx).
   
    Verrà visualizzato l'elenco dei processi.
3. Per inviare un processo di esempio che restituisca la stringa "Hello World" dal cluster, fare clic su **Nuovo processo** nel riquadro di spostamento a sinistra.
4. Nella sezione **From submission pages** (Dalle pagine di invio) della pagina **Nuovo processo** fare clic su **HelloWorld**. Verrà visualizzata la pagina di invio processi.
5. Fare clic su **Submit**. Se richiesto, specificare le credenziali di dominio dell'amministratore cluster HPC. Il processo viene inviato e, nella pagina **My Jobs** (Processi), viene visualizzato l'ID del processo.
6. Per visualizzare i risultati del processo inviato, fare clic sull'ID del processo e scegliere **Visualizza attività** per visualizzare l'output del comando (in **Output**).

## <a name="next-steps"></a>Passaggi successivi
* Per inviare processi al cluster Azure, è anche possibile usare l' [API REST HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Per inviare processi cluster da un client Linux, vedere l'esempio Python in [HPC Pack 2012 R2 SDK e codice di esempio](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png

---
title: Eseguire server applicazioni Java in una macchina virtuale di Azure classica | Documentazione Microsoft
description: Questa esercitazione usa le risorse create con il modello di distribuzione classica e illustra come creare una macchina virtuale di Windows e configurarla per eseguire il server dell'applicazione Apache Tomcat.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 03/16/2017
ms.author: robmcm
ms.openlocfilehash: 768c94fe0d2d6cfafc1f8b57256012e01de0f7a9
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Come eseguire un server di applicazione di Java in una macchina virtuale creata con il modello di distribuzione classica.
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per un modello di Resource Manager con cui distribuire un'app Web con Java 8 e Tomcat, vedere [qui](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Con Azure è possibile utilizzare una macchina virtuale per fornire funzionalità di server. Si può ad esempio configurare una macchina virtuale in esecuzione in Azure per ospitare un server applicazioni Java, come Apache Tomcat.

Una volta completata la lettura di questa guida, si disporrà di tutte le informazioni necessarie per creare una macchina virtuale in esecuzione in Azure e configurarla per eseguire un server applicazioni Java. Verranno illustrate ed eseguite le attività seguenti:

* Come creare una macchina virtuale che dispone di un Java Development Kit (JDK) già installato.
* Come accedere in remoto alla macchina virtuale.
* Come installare un server applicazioni Java, Apache Tomcat, sulla macchina virtuale.
* Creare un endpoint per la macchina virtuale.
* Aprire una porta nel firewall per il server applicazioni.

Il completamento dell'installazione comporta l'esecuzione di Tomcat su una macchina virtuale.

![Macchina virtuale che esegue Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Per creare una macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com).  
2. Fare clic su **Nuovo**, **Calcola**, quindi fare clic su **See all** (Vedi tutto) in **App in primo piano**.
3. Fare clic su **JDK**, quindi su **JDK 8** nel riquadro **JDK**.  
   Se si dispone di applicazioni legacy non ancora predisposte per l'esecuzione in JDK 8, sono disponibili macchine virtuali che supportano **JDK 6** e **JDK 7**.
4. Nel riquadro di JDK 8, selezionare **Classico**, quindi fare clic su **Crea**.
5. Nel pannello **Informazioni di base**:
   1. Specificare un nome per la macchina virtuale.
   2. Immettere un nome per l'amministratore nel campo **User Name** . Ricordare questo nome e la password associata che segue nel campo successivo. Saranno necessarie per l'accesso da remoto alla macchina virtuale.
   3. Immettere una password nel campo **Nuova password** e reimmetterla nel campo **Conferma password**. Si tratta della password dell'account dell'amministratore.
   4. Scegliere la **Sottoscrizione** appropriata.
   5. Per **Gruppo di risorse** fare clic su **Crea nuovo** e immettere il nome del nuovo gruppo di risorse. In alternativa fare clic su **Use existing** (Usa esistente) e selezionare uno dei gruppi di risorse disponibili.
   6. Selezionare la località in cui risiede la macchina virtuale, ad esempio **Stati Uniti centro-meridionali**.
6. Fare clic su **Avanti**.
7. Nel pannello **Virtual machine image size** (Dimensioni dell'immagine della macchina virtuale), selezionare **A1 Standard** (A1 Standard) o un'altra immagine appropriata.
8. Fare clic su **Seleziona**.

9. Nel pannello **Impostazioni** fare clic su **OK**. È possibile usare i valori predefiniti generati da Azure.  
10. Nel pannello **Riepilogo** fare clic su **OK**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Per accedere in remoto alla macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Macchine virtuali (classico)**. Se necessario, fare clic su **Altri servizi** nell'angolo in basso a sinistra nelle categorie di servizi. La voce **Macchine virtuali (classico)** è elencata nel gruppo **Calcola**.
3. Fare clic sul nome della macchina virtuale a cui si desidera accedere.
4. Dopo aver avviato la macchina virtuale, è possibile eseguire le connessioni tramite un menu nella parte superiore del riquadro.
5. Fare clic su **Connect**.
6. Rispondere ai prompt visualizzati per connettersi alla macchina virtuale. In generale l'utente deve salvare o aprire il file con estensione RDP che contiene i dettagli della connessione. Potrebbe essere necessario copiare url:port nell'ultima parte della prima riga del file .rdp e incollarlo in un'applicazione di accesso remoto.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Per installare un server applicazioni Java sulla macchina virtuale
È possibile copiare un server applicazioni Java sulla macchina virtuale oppure installarlo tramite un programma di installazione.

In questa esercitazione Tomcat viene usato come server per le applicazioni Java da installare.

1. Una volta eseguito l’accesso alla macchina virtuale, aprire una sessione del browser alla pagina [Apache Tomcat](http://tomcat.apache.org/download-80.cgi).
2. Fare doppio clic sul collegamento per **32-bit/64-bit Windows Service Installer**. Questa tecnica consente di installare Tomcat come servizio di Windows.
3. Quando richiesto, scegliere di eseguire il programma di installazione.
4. Nella procedura guidata **Apache Tomcat Setup** attenersi ai prompt per installare Tomcat. Ai fini di questa esercitazione, è possibile accettare le impostazioni predefinite. Quando si arriva alla finestra di dialogo **Completing the Apache Tomcat Setup Wizard** (Completamento della configurazione guidata di Apache Tomcat), è possibile selezionare **Run Apache Tomcat** (Esegui Apache Tomcat) per avviare subito Tomcat. Fare clic su **Finish** per completare il processo di installazione di Tomcat.

## <a name="to-start-tomcat"></a>Per avviare Tomcat

È possibile avviare manualmente Tomcat aprendo un prompt dei comandi nella macchina virtuale ed eseguendo il comando **net&nbsp;start&nbsp;Tomcat8**.

Quando Tomcat è in esecuzione, è possibile accedere a Tomcat immettendo l'URL <http://localhost:8080> nel browser della macchina virtuale.

Per vedere Tomcat in esecuzione da macchine esterne, è necessario creare un endpoint e aprire una porta.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Per creare un endpoint per la macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Macchine virtuali (classico)**.
3. Fare clic sul nome della macchina virtuale che esegue il server applicazioni Java.
4. Fare clic su **Endpoints**.
5. Fare clic su **Aggiungi**.
6. Nella finestra di dialogo **Aggiungi endpoint**:
   1. Specificare un nome per l'endpoint, ad esempio **HttpIn**.
   2. Selezionare **TCP** per il protocollo.
   3. Specificare **80** per la porta pubblica.
   4. Specificare **8080** per la porta privata.
   5. Selezionare **Disattivato** per l'indirizzo IP mobile.
   6. Lasciare l'elenco di controllo di accesso com'è.
   7. Fare clic sul pulsante **OK** per chiudere la finestra di dialogo e creare l'endpoint.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Per aprire nel firewall una porta per la macchina virtuale
1. Accedere alla macchina virtuale.
2. Fare clic sul pulsante **Start**di Windows.
3. Fare clic su **Pannello di controllo**.
4. Fare clic su **Sistema e sicurezza**, quindi su **Windows Firewall** e infine su **Impostazioni avanzate**.
5. Fare clic su **Regole connessioni in entrata** quindi su **Nuova regola**.  
   ![Nuova regola connessioni in entrata][NewIBRule]
6. Per **Tipo regola**, selezionare **Porta**, quindi fare clic su **Avanti**.  
   ![Porta della nuova regola connessioni in entrata][NewRulePort]
7. Nella schermata **Protocollo e porte** selezionare **TCP**, specificare **8080** come **Specific local port** (Porta locale specifica), quindi fare clic su **Avanti**.  
  ![Nuova regola connessioni in entrata][NewRuleProtocol]
8. Nella schermata **Azione** selezionare **Consenti la connessione**, quindi fare clic su **Avanti**.
   ![Operazione per nuova regola connessioni in entrata][NewRuleAction]
9. Nella schermata **Profilo** verificare che le opzioni **Dominio**, **Privato** e **Pubblico** siano selezionate, quindi fare clic su **Avanti**.
   ![Profilo per nuova regola connessioni in entrata][NewRuleProfile]
10. Nella schermata **Nome** specificare un nome per la regola, come **HttpIn** (non è tuttavia necessario che il nome della regola coincida con quello dell'endpoint), quindi fare clic su **Fine**.  
    ![Nome della nuova regola connessioni in entrata][NewRuleName]

A questo punto, il sito Web Tomcat dovrebbe essere visibile da un browser esterno. Nella finestra dell'indirizzo del browser digitare un URL nel formato **http://*nome\_DNS\_in uso*.cloudapp.net**, dove ***il nome\_DNS\_***in uso è il nome DNS specificato durante la creazione della macchina virtuale.

## <a name="application-lifecycle-considerations"></a>Considerazioni sul ciclo di vita delle applicazioni
* È possibile creare il proprio archivio di applicazioni Web (WAR) e aggiungerlo alla cartella **webapps** . Ad esempio, creare un progetto Web dinamico di una pagina JSP (Java Service Page) di base ed esportarlo come file con estensione WAR. Successivamente, copiare il file con estensione WAR nella cartella **webapps** di Apache Tomcat nella macchina virtuale e quindi eseguirlo in un browser.
* Per impostazione predefinita, quando viene installato, il servizio Tomcat è impostato per l'avvio manuale. È possibile passare a esso per avviare automaticamente tramite lo snap-in Servizi. Avviare lo snap-in Servizi facendo clic su **Start di Windows**, **Strumenti di amministrazione**, **Servizi**. Fare doppio clic sul servizio **Apache Tomcat** e impostare **Tipo di avvio** su **Automatico**.

    ![Impostazione di un servizio per l'avvio automatico][service_automatic_startup]

    L'impostazione di Tomcat per l'avvio automatico può risultare utile in quanto in caso di riavvio della macchina virtuale, ad esempio dopo l'installazione di aggiornamenti software che richiedono un riavvio, il servizio verrà riavviato automaticamente.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su altri servizi, ad esempio Archiviazione, Service Bus e Database SQL di Azure che può essere utile includere nelle applicazioni Java, fare riferimento alle informazioni disponibili all'indirizzo in [Centro per sviluppatori Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->

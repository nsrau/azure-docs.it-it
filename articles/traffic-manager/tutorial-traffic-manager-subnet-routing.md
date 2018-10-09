---
title: Configurare il metodo di routing del traffico subnet usando Gestione traffico | Microsoft Docs
description: Questo articolo descrive come configurare Gestione traffico per instradare il traffico dalle subnet utente agli endpoint specifici.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: 20c34b820eb326a18be1c4298b0850a58599be64
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956235"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Indirizzare il traffico a endpoint specifici basati sulla subnet di utente usando Gestione traffico

Questo articolo descrive come configurare il metodo di routing del traffico della subnet. Il metodo di routing del traffico**Subnet** consente di eseguire il mapping di un set di intervalli di indirizzi IP a endpoint specifici e quando si riceve una richiesta da Gestione traffico, controlla l'indirizzo IP di origine della richiesta e restituisce l'endpoint associato. 

In questa esercitazione, usando il routing di subnet, a seconda dell'indirizzo IP della query dell'utente, il traffico viene indirizzato a un sito Web interno o a un sito Web di produzione.


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare due macchine virtuali che eseguono un sito Web di base in IIS
> * Creare due macchine virtuali di test per visualizzare Gestione traffico in azione
> * Configurare il nome DNS per le macchine virtuali che eseguono IIS
> * Creare un profilo Gestione traffico per il routing del traffico basato sulla subnet dell'utente
> * Aggiungere endpoint di macchine virtuali al profilo di Gestione traffico
> * Visualizzare Gestione traffico in azione


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Per visualizzare Gestione traffico in azione, è necessario implementare quanto segue:
- due siti Web di base in esecuzione in diverse aree di Azure - **Stati Uniti orientali** (usato come sito Web interno) e **Europa occidentale** (usato come sito Web di produzione).
- Due macchine virtuali (VM) per il test di Gestione traffico: una in **Stati Uniti orientali** e la seconda in **Europa occidentale**. 

Le macchine virtuali per il test vengono usate per illustrare come Gestione traffico indirizza il traffico utente al sito Web interno o al sito Web di produzione, a seconda della subnet da cui ha origine la query dell'utente.

### <a name="sign-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

### <a name="create-websites"></a>Creare i siti Web

In questa sezione si creano due istanze di sito Web che forniscono due endpoint di servizio per il profilo di Gestione traffico in due aree di Azure. La procedura di creazione dei due siti Web include i passaggi seguenti:
1. Creare due VM per l'esecuzione di un sito Web di base, una in **Stati Uniti orientali** e l'altra in **Europa occidentale**.
2. Installare un server IIS in ogni VM e aggiornare la pagina predefinita del sito Web che descrive il nome della VM a cui viene connesso l'utente quando visita il sito Web.

#### <a name="create-vms-for-running-websites"></a>Creare VM per l'esecuzione di siti Web
In questa sezione si creano due macchine virtuali *InternalWebsite* e *ProdWebsite* nelle aree di Azure **Stati Uniti orientali** e **Europa occidentale**.

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **Crea una risorsa** > **Calcolo** > **Windows Server 2016 VM**.
2. Immettere o selezionare le informazioni seguenti in **Basics** (Generale), accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:

    |Impostazione|Valore|
    |---|---|
    |NOME|InternalWebsite|
    |Nome utente| Immettere un nome utente a scelta.|
    |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Gruppo di risorse| Selezionare **Nuovo** e quindi digitare *myResourceGroupTM1*.|
    |Località| Selezionare **Stati Uniti orientali**.|
    |||
4. Selezionare le dimensioni della macchina virtuale in **Scegli una dimensione**.
5. Selezionare i valori seguenti in **Impostazioni**, quindi scegliere **OK**:
    
    |Impostazione|Valore|
    |---|---|
    |Rete virtuale| Selezionare **Rete virtuale** in **Crea rete virtuale**, immettere **myVNet1** per *Nome* e *mySubnet* per la subnet.|
    |Gruppo di sicurezza di rete|Selezionare **Base** e dall'elenco a discesa **Selezionare le porte in ingresso pubbliche** selezionare **HTTP** e **RDP**. |
    |Diagnostica di avvio|Selezionare **Disabilitata**.|
    |||
6. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della macchina virtuale.

7. Ripetere i passaggi da 1 a 6, con le modifiche seguenti:

    |Impostazione|Valore|
    |---|---|
    |Gruppo di risorse | Selezionare **Nuovo** e quindi digitare *myResourceGroupTM2*|
    |Località|Europa occidentale|
    |Nome macchina virtuale | ProdWebsite|
    |Rete virtuale | Selezionare **Rete virtuale** in **Crea rete virtuale**, immettere **myVNet2** per *Nome* e *mySubnet* per la subnet.|
    |||
8. La creazione delle macchine virtuali può richiedere alcuni minuti. Non procedere con i passaggi rimanenti finché non sono state create entrambe le macchine virtuali.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installare IIS e personalizzare la pagina Web predefinita

In questa sezione si installa il server IIS nelle due VM, *myIISVMEastUS*   &  *myIISVMWEurope*, e quindi si aggiorna la pagina predefinita del sito Web. La pagina del sito Web personalizzata mostra il nome della VM a cui viene connesso l'utente quando visita il sito Web da un Web browser.

1. Selezionare **Tutte le risorse** nel menu a sinistra e quindi nell'elenco delle risorse fare clic su *myIISVMEastUS*, che si trova nel gruppo di risorse *myResourceGroupTM1*.
2. Nella pagina **Panoramica** fare clic su **Connetti** e quindi selezionare **Scarica file RDP** in **Connetti a macchina virtuale**. 
3. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM. Potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un altro account** per specificare le credenziali immesse al momento della creazione della VM. 
4. Selezionare **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione.
6. Nel desktop del server passare a **Strumenti di amministrazione Windows**>**Server Manager**.
7. Avviare Windows PowerShell sulla macchina virtuale -*InternalWebsite* e usare i comandi seguenti per installare il server IIS e aggiornare il file htm predefinito.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```
8. Chiudere la connessione RDP con la VM *InternalWebsite*.
9. Ripetere i passaggi da 1 a 6 creando una connessione RDP alla VM *ProdWebsite* all'interno del gruppo di risorse *myResourceGroupTM2* per installare IIS e personalizzare la pagina Web predefinita.
10. Avviare Windows PowerShell sulla VM *ProdWebsite*e usare i comandi seguenti per installare il server IIS e aggiornare il file htm predefinito.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurare i nomi DNS per le VM che eseguono IIS

Gestione traffico instrada il traffico degli utenti in base al nome DNS degli endpoint di servizio. In questa sezione si configurano i nomi DNS per i server IIS: *InternalWebsite* e *ProdWebsite*.

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi nell'elenco delle risorse selezionare *InternalWebsite*, che si trova nel gruppo di risorse *myResourceGroupTM1*.
2. Nella pagina **Panoramica**, in **Nome DNS**, selezionare **Configura**.
3. Nella pagina **Configurazione**, sotto l'etichetta del nome DNS, aggiungere un nome univoco e quindi selezionare **Salva**.
4. Ripetere i passaggi da 1 a 3 per la VM denominata *myIISVMWEurope* che si trova nel gruppo di risorse *myResourceGroupTM2*.

### <a name="create-test-vms"></a>Creare le VM di test

In questa sezione si crea una macchina virtuale (*UserVMUS* e *UserVMEurope*) in ciascuna area di Azure (**Stati Uniti orientali** e **Europa occidentale**). Queste VM verranno usate per verificare come Gestione traffico instrada il traffico verso il server IIS più vicino quando si visita il sito Web.

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **Creare una risorsa** > **Calcolo** > **Windows Server 2016 Data center**.
2. Immettere o selezionare le informazioni seguenti in **Basics** (Generale), accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:

    |Impostazione|Valore|
    |---|---|
    |NOME|*UserVMUS*|
    |Nome utente| Immettere un nome utente a scelta.|
    |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Gruppo di risorse| Selezionare **Esistente** e quindi *myResourceGroupTM1*.|
    |||

4. Selezionare le dimensioni della macchina virtuale in **Scegli una dimensione**.
5. Selezionare i valori seguenti in **Impostazioni**, quindi scegliere **OK**:
    |Impostazione|Valore|
    |---|---|
    |Rete virtuale| Selezionare **Rete virtuale**, nella sezione **Creare rete virtuale**, per il **Nome** immettere *myVNet3*, per la subnet immettere *mySubnet3*.|
    |Gruppo di sicurezza di rete|Selezionare **Base** e dall'elenco a discesa **Selezionare le porte in ingresso pubbliche** selezionare **HTTP** e **RDP**. |
    |Diagnostica di avvio|Selezionare **Disabilitata**.|
    |||

6. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della macchina virtuale.

7. Ripetere i passaggi da 1 a 5, con le modifiche seguenti:

    |Impostazione|Valore|
    |---|---|
    |Nome macchina virtuale | *UserVMEurope*|
    |Gruppo di risorse | Selezionare **Esistente** e quindi digitare *myResourceGroupTM2*.|
    |Rete virtuale | Selezionare **Rete virtuale**, nella sezione **Creare rete virtuale**, per il **Nome** immettere *myVNet4*, per la subnet immettere *mySubnet4*.|
    |||

8. La creazione delle macchine virtuali può richiedere alcuni minuti. Non procedere con i passaggi rimanenti finché non sono state create entrambe le macchine virtuali.

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico
Creare un profilo di Gestione traffico che consente di restituire specifici endpoint in base all'indirizzo IP di origine della richiesta.

1. In alto a sinistra nello schermo selezionare **Crea una risorsa** > **Rete** > **Profilo di Gestione traffico** > **Crea**.
2. In **Crea profilo di Gestione traffico** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le impostazioni rimanenti e quindi selezionare **Crea**:
    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | NOME                   | Questo nome deve essere univoco all'interno della zona trafficmanager.net e determina il nome DNS, trafficmanager.net, che viene usato per accedere al profilo di Gestione traffico.                                   |
    | Metodo di routing          | Selezionare il metodo di routing **Subnet**.                                       |
    | Sottoscrizione            | Selezionare la propria sottoscrizione.                          |
    | Gruppo di risorse          | Selezionare **Esistente** e immettere *myResourceGroupTM1*. |
    | |                              |
    |
  
    ![Creare un profilo di Gestione traffico](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico

Aggiungere le due macchine virtuali in esecuzione sui server IIS - *InternalWebsite*  & *ProdWebsite* per instradare il traffico utente in base alla subnet della query dell'utente.

1. Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico creato nella sezione precedente e selezionarlo nei risultati visualizzati.
2. In **Profilo di Gestione traffico**, nella sezione **Impostazioni**, fare clic su **Endpoint** e quindi su **Aggiungi**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi scegliere **OK**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | type                    | Endpoint di Azure                                   |
    | NOME           | myInternalWebSiteEndpoint                                        |
    | Tipo di risorsa di destinazione           | Indirizzo IP pubblico                          |
    | Risorsa di destinazione          | **Scegliere un indirizzo IP pubblico** per visualizzare l'elenco delle risorse con gli indirizzi IP pubblici inclusi nella stessa sottoscrizione. In **Risorsa** selezionare l'indirizzo IP pubblico denominato *InternalWebsite-ip*. Questo è l'indirizzo IP pubblico della VM del server IIS nell'area Stati Uniti orientali.|
    |  Impostazioni del routing della subnet    |   Aggiungere l'indirizzo IP della macchina virtuale di test *UserVMUS*. Tutte le query dell'utente provenienti da questa macchina virtuale vengono indirizzate a *InternalWebSiteEndpoint*.    |

4. Ripetere i passaggi 2 e 3 per aggiungere un altro endpoint denominato *ProdWebsiteEndpoint* per l'indirizzo IP pubblico *ProdWebsite-ip* associato alla VM del server IIS denominata *ProdWebsite*. Per le **Impostazioni di routing della subnet**, aggiungere l'indirizzo IP della macchina virtuale di test: *UserVMEurope*. Qualsiasi query dell'utente di questo test verrà instradata all'endpoint - *myProdWebsiteEndpoint*.
5.  Una volta completata l'aggiunta di entrambi gli endpoint, essi vengono visualizzati in **Profilo di Gestione traffico** insieme al relativo stato di monitoraggio **Online**.

 
## <a name="test-traffic-manager-profile"></a>Testare il profilo di Gestione traffico
In questa sezione viene testato il modo in cui Gestione traffico instrada il traffico utente da una determinata subnet a un endpoint specifico. Per visualizzare Gestione traffico in azione, completare i passaggi seguenti:
1. Determinare il nome DNS del profilo di Gestione traffico.
2. Visualizzare Gestione traffico in azione nel modo seguente:
    - Dalla VM di test (*UserVMUS*) che si trova nell'area **Stati Uniti orientali**, a un Web browser, passare al nome DNS del profilo di Gestione traffico.
    - Dalla VM di test (*UserVMEurope*) che si trova nell'area **Europa occidentale**, a un Web browser, passare al nome DNS del profilo di Gestione traffico.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinare il nome DNS del profilo di Gestione traffico
In questa esercitazione, per semplicità, si usa il nome DNS del profilo di Gestione traffico per visitare i siti Web. 

È possibile determinare il nome DNS del profilo di Gestione traffico nel modo seguente:

1.  Nella barra di ricerca del portale cercare il nome del **Profilo di Gestione traffico** creato nella sezione precedente. Fare clic sul profilo di Gestione traffico nei risultati visualizzati.
1. Fare clic su **Panoramica**.
2. Il **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato. Nelle distribuzioni di produzione si configura un nome di dominio personalizzato in modo che punti al nome di dominio di Gestione traffico usando un record CNAME DNS.

### <a name="view-traffic-manager-in-action"></a>Visualizzare Gestione traffico in azione
In questa sezione è possibile visualizzare Gestione traffico in azione. 

1. Selezionare **Tutte le risorse** nel menu a sinistra, quindi nell'elenco delle risorse fare clic su *myUserVMUS*, che si trova nel gruppo di risorse *myResourceGroupTM1*.
2. Nella pagina **Panoramica** fare clic su **Connetti** e quindi selezionare **Scarica file RDP** in **Connetti a macchina virtuale**. 
3. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM. Potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un altro account** per specificare le credenziali immesse al momento della creazione della VM. 
4. Selezionare **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione. 
1. In un browser Web nella VM *UserVMUS*, digitare il nome DNS del profilo di Gestione traffico per visualizzare il sito Web. Poiché l'indirizzo IP della macchina virtuale *UserVMUS* è associato all'endpoint *myInternalWebsiteEndpoint*, il browser Web avvia il server del sito Web Test:*InternalWebsite*.

2. A questo punto, connettersi alla VM *UserVMEurope* che si trova in **Europa occidentale** eseguendo i passaggi da 1 a 5 e passare al nome di dominio del profilo di Gestione traffico da questa VM. Poiché l'indirizzo IP della macchina virtuale *UserVMEurope* è associato all'endpoint *myProductionWebsiteEndpoint*, il web browser avvia il server del sito Web Test:*ProdWebsite*. 
  
## <a name="delete-the-traffic-manager-profile"></a>Eliminare il profilo di Gestione traffico
Quando non sono più necessari, eliminare i gruppi di risorse (**ResourceGroupTM1** e **ResourceGroupTM2**). A tale scopo, selezionare il gruppo di risorse (**ResourceGroupTM1** o **ResourceGroupTM2**) e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [metodo di routing del traffico Ponderato](traffic-manager-configure-weighted-routing-method.md).
- Informazioni sul [metodo di routing Priorità](traffic-manager-configure-priority-routing-method.md).
- Informazioni sul [metodo di routing Geografico](traffic-manager-configure-geographic-routing-method.md).



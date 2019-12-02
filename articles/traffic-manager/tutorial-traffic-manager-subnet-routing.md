---
title: 'Esercitazione: Configurare il routing del traffico delle subnet con Gestione traffico di Azure'
description: Questo articolo descrive come configurare Gestione traffico per instradare il traffico dalle subnet utente ad endpoint specifici.
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: allensu
ms.openlocfilehash: 00bc453ebb0e467f48bd886fc7c6b6c422693864
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420264"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Esercitazione: Indirizzare il traffico a endpoint specifici basati sulla subnet dell'utente usando Gestione traffico

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

- due siti Web di base in esecuzione in diverse aree di Azure: **Stati Uniti orientali** (usato come sito Web interno) e **Europa occidentale** (usato come sito Web di produzione).
- Due macchine virtuali (VM) per il test di Gestione traffico: una in **Stati Uniti orientali** e la seconda in **Europa occidentale**.

Le macchine virtuali per il test vengono usate per illustrare come Gestione traffico indirizza il traffico utente al sito Web interno o al sito Web di produzione, a seconda della subnet da cui ha origine la query dell'utente.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

### <a name="create-websites"></a>Creare i siti Web

In questa sezione si creano due istanze di sito Web che forniscono due endpoint di servizio per il profilo di Gestione traffico in due aree di Azure. La procedura di creazione dei due siti Web include i passaggi seguenti:

1. Creare due VM per l'esecuzione di un sito Web di base, una in **Stati Uniti orientali** e l'altra in **Europa occidentale**.
2. Installare un server IIS in ogni VM e aggiornare la pagina predefinita del sito Web che descrive il nome della VM a cui viene connesso l'utente quando visita il sito Web.

#### <a name="create-vms-for-running-websites"></a>Creare VM per l'esecuzione di siti Web

In questa sezione vengono create due macchine virtuali *myIISVMEastUS* e *myIISVMWestEurope* nelle aree **Stati Uniti orientali** ed **Europa occidentale** di Azure.

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **Creare una risorsa** > **Calcolo** > **Windows Server 2019 Data center**.
2. In **Crea macchina virtuale** digitare o selezionare i valori seguenti nella scheda **Nozioni di base**:

   - **Sottoscrizione** > **Gruppo di risorse**: Selezionare **Crea nuovo** e quindi digitare **myResourceGroupTM1**.
   - **Dettagli istanza** > **Nome macchina virtuale**: Digitare *myIISVMEastUS*.
   - **Dettagli istanza** > **Area**:  Selezionare **Stati Uniti orientali**.
   - **Account amministratore** > **Nome utente**:  Immettere un nome utente a scelta.
   - **Account amministratore** > **Password**:  Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regole porta in ingresso** > **Porte in ingresso pubbliche**: Selezionare **Consenti porte selezionate**.
   - **Regole porta in ingresso** > **Selezionare le porte in ingresso**: selezionare **RDP** e **HTTP** nella casella a discesa.

3. Selezionare la scheda **Gestione** oppure **Avanti: Dischi**, quindi **Avanti: Rete**, quindi **Avanti: Gestione**. In **Monitoraggio** impostare **Diagnostica di avvio** su **Off**.
4. Selezionare **Rivedi e crea**.
5. Rivedere le impostazioni e fare clic su **Crea**.  
6. Seguire i passaggi per creare una seconda macchina virtuale denominata *myIISVMWestEurope*, assegnando al **Gruppo di risorse** il nome *myResourceGroupTM2*, impostando la **località** su *Europa occidentale* e scegliendo per tutte le altre impostazioni gli stessi valori di *myIISVMEastUS*.
7. La creazione delle macchine virtuali può richiedere alcuni minuti. Non procedere con i passaggi rimanenti finché non sono state create entrambe le macchine virtuali.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installare IIS e personalizzare la pagina Web predefinita

In questa sezione si installa il server IIS nelle due macchine virtuali, *myIISVMEastUS* e *myIISVMWestEurope*, e quindi si aggiorna la pagina predefinita del sito Web. La pagina del sito Web personalizzata mostra il nome della VM a cui viene connesso l'utente quando visita il sito Web da un Web browser.

1. Selezionare **Tutte le risorse** nel menu a sinistra e quindi nell'elenco delle risorse fare clic su *myIISVMEastUS*, che si trova nel gruppo di risorse *myResourceGroupTM1*.
2. Nella pagina **Panoramica** fare clic su **Connetti** e quindi selezionare **Scarica file RDP** in **Connetti a macchina virtuale**.
3. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM. Potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un altro account** per specificare le credenziali immesse al momento della creazione della VM.
4. Selezionare **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione.
6. Nel desktop del server passare a **Strumenti di amministrazione Windows**>**Server Manager**.
7. Avviare Windows PowerShell nella macchina virtuale *myIISVMEastUS* e usare i comandi seguenti per installare il server IIS e aggiornare il file HTM predefinito.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Chiudere la connessione RDP con la macchina virtuale *myIISVMEastUS*.
9. Ripetere i passaggi da 1 a 6 creando una connessione RDP alla macchina virtuale *myIISVMWestEurope* all'interno del gruppo di risorse *myResourceGroupTM2* per installare IIS e personalizzare la pagina Web predefinita.
10. Avviare Windows PowerShell nella macchina virtuale *myIISVMWestEurope* e usare i comandi seguenti per installare il server IIS e aggiornare il file HTM predefinito.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurare i nomi DNS per le VM che eseguono IIS

Gestione traffico instrada il traffico degli utenti in base al nome DNS degli endpoint di servizio. In questa sezione si configurano i nomi DNS per i server IIS, *myIISVMEastUS* e *myIISVMWestEurope*.

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi nell'elenco delle risorse selezionare *myIISVMEastUS*, che si trova nel gruppo di risorse *myResourceGroupTM1*.
2. Nella pagina **Panoramica**, in **Nome DNS**, selezionare **Configura**.
3. Nella pagina **Configurazione**, sotto l'etichetta del nome DNS, aggiungere un nome univoco e quindi selezionare **Salva**.
4. Ripetere i passaggi da 1 a 3 per la macchina virtuale denominata *myIISVMWestEurope* che si trova nel gruppo di risorse *myResourceGroupTM2*.

### <a name="create-test-vms"></a>Creare le VM di test

In questa sezione si crea una macchina virtuale (*myVMEastUS* e *myVMWestEurope*) in ogni area di Azure (**Stati Uniti orientali** ed **Europa occidentale**). Si useranno queste macchine virtuali per testare come Gestione traffico instrada il traffico utente in base alla subnet della query dell'utente.

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **Creare una risorsa** > **Calcolo** > **Windows Server 2019 Data center**.
2. In **Crea macchina virtuale** digitare o selezionare i valori seguenti nella scheda **Nozioni di base**:

   - **Sottoscrizione** > **Gruppo di risorse**: Selezionare **myResourceGroupTM1**.
   - **Dettagli istanza** > **Nome macchina virtuale**: Digitare *myVMEastUS*.
   - **Dettagli istanza** > **Area**:  Selezionare **Stati Uniti orientali**.
   - **Account amministratore** > **Nome utente**:  Immettere un nome utente a scelta.
   - **Account amministratore** > **Password**:  Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regole porta in ingresso** > **Porte in ingresso pubbliche**: Selezionare **Consenti porte selezionate**.
   - **Regole porta in ingresso** > **Selezionare le porte in ingresso**: selezionare **RDP** nella casella a discesa.

3. Selezionare la scheda **Gestione** oppure **Avanti: Dischi**, quindi **Avanti: Rete**, quindi **Avanti: Gestione**. In **Monitoraggio** impostare **Diagnostica di avvio** su **Off**.
4. Selezionare **Rivedi e crea**.
5. Rivedere le impostazioni e fare clic su **Crea**.  
6. Seguire i passaggi per creare una seconda macchina virtuale denominata *myVMWestEurope*, assegnando al **Gruppo di risorse** il nome *myResourceGroupTM2*, impostando la **località** su *Europa occidentale* e scegliendo per tutte le altre impostazioni gli stessi valori di *myVMEastUS*.
7. La creazione delle macchine virtuali può richiedere alcuni minuti. Non procedere con i passaggi rimanenti finché non sono state create entrambe le macchine virtuali.

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

Creare un profilo di Gestione traffico che consente di restituire specifici endpoint in base all'indirizzo IP di origine della richiesta.

1. In alto a sinistra nello schermo selezionare **Crea una risorsa** > **Rete** > **Profilo di Gestione traffico** > **Crea**.
2. In **Crea profilo di Gestione traffico** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le impostazioni rimanenti e quindi selezionare **Crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | NOME                   | Questo nome deve essere univoco all'interno della zona trafficmanager.net e determina il nome DNS, trafficmanager.net, che viene usato per accedere al profilo di Gestione traffico.                                   |
    | Metodo di routing          | Selezionare il metodo di routing **Subnet**.                                       |
    | Subscription            | Selezionare la propria sottoscrizione.                          |
    | Resource group          | Selezionare **Esistente** e immettere *myResourceGroupTM1*. |
    | |                              |
    |

    ![Creare un profilo di Gestione traffico](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico

Aggiungere le due macchine virtuali in esecuzione sui server IIS, *myIISVMEastUS* & *myIISVMWestEurope*, per instradare il traffico utente in base alla subnet della query dell'utente.

1. Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico creato nella sezione precedente e selezionarlo nei risultati visualizzati.
2. In **Profilo di Gestione traffico**, nella sezione **Impostazioni**, fare clic su **Endpoint** e quindi su **Aggiungi**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi scegliere **OK**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Type                    | Endpoint di Azure                                   |
    | NOME           | myInternalWebSiteEndpoint                                        |
    | Tipo di risorsa di destinazione           | Indirizzo IP pubblico                          |
    | Risorsa di destinazione          | **Scegliere un indirizzo IP pubblico** per visualizzare l'elenco delle risorse con gli indirizzi IP pubblici inclusi nella stessa sottoscrizione. In **Risorsa** selezionare l'indirizzo IP pubblico denominato *myIISVMEastUS-ip*. Questo è l'indirizzo IP pubblico della VM del server IIS nell'area Stati Uniti orientali.|
    |  Impostazioni del routing della subnet    |   Aggiungere l'indirizzo IP della VM di test *myVMEastUS*. Tutte le query dell'utente provenienti da questa macchina virtuale vengono indirizzate a *myInternalWebSiteEndpoint*.    |

4. Ripetere i passaggi 2 e 3 per aggiungere un altro endpoint denominato *myProdWebsiteEndpoint* per l'indirizzo IP pubblico *myIISVMWestEurope-ip* associato alla macchina virtuale del server IIS denominata *myIISVMWestEurope*. Per le **Impostazioni di routing della subnet** aggiungere l'indirizzo IP della VM di test: *myVMWestEurope*. Qualsiasi query dell'utente di questo test verrà instradata all'endpoint - *myProdWebsiteEndpoint*.
5. Una volta completata l'aggiunta di entrambi gli endpoint, essi vengono visualizzati in **Profilo di Gestione traffico** insieme al relativo stato di monitoraggio **Online**.

## <a name="test-traffic-manager-profile"></a>Testare il profilo di Gestione traffico

In questa sezione viene testato il modo in cui Gestione traffico instrada il traffico utente da una determinata subnet a un endpoint specifico. Per visualizzare Gestione traffico in azione, completare i passaggi seguenti:

1. Determinare il nome DNS del profilo di Gestione traffico.
2. Visualizzare Gestione traffico in azione nel modo seguente:
    - Dalla VM di test (*myVMEastUS*) che si trova nell'area **Stati Uniti orientali**, in un Web browser, passare al nome DNS del profilo di Gestione traffico.
    - Dalla macchina virtuale di test (*myVMWestEurope*) che si trova nell'area **Europa occidentale**, in un Web browser passare al nome DNS del profilo di Gestione traffico.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinare il nome DNS del profilo di Gestione traffico

In questa esercitazione, per semplicità, si usa il nome DNS del profilo di Gestione traffico per visitare i siti Web.

È possibile determinare il nome DNS del profilo di Gestione traffico nel modo seguente:

1. Nella barra di ricerca del portale cercare il nome del **Profilo di Gestione traffico** creato nella sezione precedente. Fare clic sul profilo di Gestione traffico nei risultati visualizzati.
2. Fare clic su **Panoramica**.
3. Il **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato. Nelle distribuzioni di produzione si configura un nome di dominio personalizzato in modo che punti al nome di dominio di Gestione traffico usando un record CNAME DNS.

### <a name="view-traffic-manager-in-action"></a>Visualizzare Gestione traffico in azione

In questa sezione è possibile visualizzare Gestione traffico in azione.

1. Selezionare **Tutte le risorse** nel menu a sinistra e quindi nell'elenco delle risorse fare clic su *myVMEastUS*, che si trova nel gruppo di risorse *myResourceGroupTM1*.
2. Nella pagina **Panoramica** fare clic su **Connetti** e quindi selezionare **Scarica file RDP** in **Connetti a macchina virtuale**.
3. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM. Potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un altro account** per specificare le credenziali immesse al momento della creazione della VM.
4. Selezionare **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione.
6. In un Web browser, nella VM *myVMEastUS*, digitare il nome DNS del profilo di Gestione traffico per visualizzare il sito Web. Poiché l'indirizzo IP della macchina virtuale *myVMEastUS* è associato all'endpoint *myInternalWebsiteEndpoint*, il Web browser avvia il server del sito Web di test, *myIISVMEastUS*.

7. A questo punto, connettersi alla VM *myVMWestEurope* che si trova in **Europa occidentale** eseguendo i passaggi da 1 a 5 e passare al nome di dominio del profilo di Gestione traffico da questa VM. Poiché l'indirizzo IP della macchina virtuale *myVMWestEurope* è associato all'endpoint *myProductionWebsiteEndpoint*, il Web browser avvia il server del sito Web di test, *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Eliminare il profilo di Gestione traffico

Quando non sono più necessari, eliminare i gruppi di risorse (**ResourceGroupTM1** e **ResourceGroupTM2**). A tale scopo, selezionare il gruppo di risorse (**ResourceGroupTM1** o **ResourceGroupTM2**) e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [metodo di routing del traffico Ponderato](traffic-manager-configure-weighted-routing-method.md).
- Informazioni sul [metodo di routing Priorità](traffic-manager-configure-priority-routing-method.md).
- Informazioni sul [metodo di routing Geografico](traffic-manager-configure-geographic-routing-method.md).

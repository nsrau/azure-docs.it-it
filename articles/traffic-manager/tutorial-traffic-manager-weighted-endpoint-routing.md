---
title: 'Esercitazione: Instradare il traffico agli endpoint ponderati - Gestione traffico di Azure'
description: Questa esercitazione spiega come instradare il traffico agli endpoint ponderati con Gestione traffico.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: allensu
ms.openlocfilehash: f9e2b6f6a45279c52e19a63509c57fb34e739330
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258380"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Esercitazione: Controllare il routing del traffico agli endpoint ponderati con Gestione traffico

Questa esercitazione spiega come usare Gestione traffico per controllare il routing del traffico utente tra gli endpoint usando il metodo di routing ponderato. In questo metodo di routing viene assegnato un peso a ogni endpoint come parte della configurazione del profilo di Gestione traffico. Il traffico utente viene quindi instradato in base al peso assegnato a ogni endpoint. Il peso è un numero intero compreso tra 1 e 1.000. Maggiore sarà il valore del peso assegnato a un endpoint, più elevata sarà la relativa priorità.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare due macchine virtuali che eseguono un sito Web di base in IIS.
> * Creare due macchine virtuali di test per vedere in azione Gestione traffico.
> * Configurare un nome DNS per le macchine virtuali che eseguono IIS.
> * Creare un profilo di Gestione traffico.
> * Aggiungere endpoint di macchine virtuali al profilo di Gestione traffico.
> * Visualizzare Gestione traffico in azione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per vedere in azione Gestione traffico, distribuire quanto segue per questa esercitazione:

- Due istanze di siti Web di base in esecuzione in aree diverse di Azure, ovvero Stati Uniti orientali ed Europa occidentale.
- Due macchine virtuali (VM) per il test di Gestione traffico: una in Stati Uniti orientali e l'altra in Europa occidentale. Le VM di test vengono usate per illustrare come Gestione traffico instrada il traffico degli utenti verso un sito Web con peso maggiore assegnato al relativo endpoint.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

### <a name="create-websites"></a>Creare i siti Web

In questa sezione si creano due istanze di sito Web che forniscono due endpoint di servizio per il profilo di Gestione traffico in due aree di Azure. Per creare i due siti Web, completare la procedura seguente:

1. Creare due macchine virtuali per l'esecuzione di un sito Web di base, una in Stati Uniti orientali e l'altra in Europa occidentale.
2. Installare un server IIS in ogni macchina virtuale. Aggiornare la pagina Web predefinita che descrive il nome della macchina virtuale a cui viene connesso l'utente quando visita il sito Web.

#### <a name="create-vms-for-running-websites"></a>Creare VM per l'esecuzione di siti Web

In questa sezione vengono create due macchine virtuali *myIISVMEastUS* e *myIISVMWestEurope* nelle aree Stati Uniti orientali ed Europa occidentale di Azure.

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

![Creare una macchina virtuale](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>Installare IIS e personalizzare la pagina Web predefinita

In questa sezione si installa il server IIS nelle due macchine virtuali, myIISVMEastUS e myIISVMWestEurope, e quindi si aggiorna la pagina Web predefinita. La pagina Web personalizzata mostra il nome della macchina virtuale a cui viene connesso l'utente quando visita il sito Web da un Web browser.

1. Nel menu a sinistra selezionare **Tutte le risorse**. Nell'elenco delle risorse selezionare**myIISVMEastUS** nel gruppo di risorse**myResourceGroupTM1**.
2. Nella pagina **Panoramica** selezionare **Connetti**. In **Connetti alla macchina virtuale in corso** selezionare **Scarica file RDP**.
3. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati quando è stata creata la macchina virtuale. Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un account diverso** per specificare le credenziali immesse al momento della creazione della macchina virtuale.
4. Selezionare **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione.
6. Nel desktop del server passare a **Strumenti di amministrazione Windows** > **Server Manager**.
7. Aprire Windows PowerShell nella macchina virtuale 1. Usare i comandi seguenti per installare il server IIS e aggiornare il file .htm predefinito.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![Installare IIS e personalizzare la pagina Web](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Chiudere la connessione RDP con **myIISVMEastUS**.
9. Ripetere i passaggi da 1 a 8. Creare una connessione RDP alla VM **myIISVMWestEurope** all'interno del gruppo di risorse **myResourceGroupTM2** per installare IIS e personalizzare la pagina Web predefinita.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurare i nomi DNS per le VM che eseguono IIS

Gestione traffico instrada il traffico degli utenti in base al nome DNS degli endpoint di servizio. In questa sezione si configurano i nomi DNS per i server IIS, myIISVMEastUS e myIISVMWestEurope.

1. Nel menu a sinistra selezionare **Tutte le risorse**. Nell'elenco delle risorse selezionare**myIISVMEastUS** nel gruppo di risorse**myResourceGroupTM1**.
2. Nella pagina **Panoramica**, in **Nome DNS**, selezionare **Configura**.
3. Nella pagina **Configurazione** aggiungere un nome univoco sotto l'etichetta del nome DNS. Selezionare quindi **Salva**.
4. Ripetere i passaggi da 1 a 3 per la macchina virtuale denominata **myIISVMWestEurope** nel gruppo di risorse **myResourceGroupTM2**.

### <a name="create-a-test-vm"></a>Creare una macchina virtuale di test

In questa sezione si crea una macchina virtuale (*myVMEastUS* e *myVMWestEurope*) in ogni area di Azure (**Stati Uniti orientali** ed **Europa occidentale**). Queste VM verranno usate per verificare come Gestione traffico instrada il traffico verso l'endpoint del sito Web con il valore di peso maggiore.

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

Creare un profilo di Gestione traffico basato sul metodo di routing **Ponderato**.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Profilo di Gestione traffico** > **Crea**.
2. In **Crea profilo di Gestione traffico** immettere o selezionare le informazioni seguenti. Accettare i valori predefiniti delle altre impostazioni e quindi selezionare **Crea**.

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | NOME                   | Immettere un nome univoco all'interno della zona trafficmanager.net. Tale nome determinerà il nome DNS trafficmanager.net, che viene usato per accedere al profilo di Gestione traffico.                                   |
    | Metodo di routing          | Selezionare il metodo di routing **Ponderato**.                                       |
    | Sottoscrizione            | Selezionare la propria sottoscrizione.                          |
    | Gruppo di risorse          | Selezionare **Usa esistente** e quindi **myResourceGroupTM1**. |
    |        |   |

    ![Creare un profilo di Gestione traffico](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico

Aggiungere le due macchine virtuali che eseguono i server IIS, ovvero myIISVMEastUS e myIISVMWestEurope, per instradare il traffico degli utenti verso di esse.

1. Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico creato nella sezione precedente. Selezionare il profilo nei risultati visualizzati.
2. In **Profilo di Gestione traffico** selezionare **Endpoint** > **Aggiungi** nella sezione **Impostazioni**.
3. Immettere o selezionare le informazioni seguenti. Accettare i valori predefiniti delle altre impostazioni e quindi fare clic su **OK**.

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Type                    | Immettere l'endpoint di Azure.                                   |
    | NOME           | Immettere **myEastUSEndpoint**.                                        |
    | Tipo di risorsa di destinazione           | Selezionare **Indirizzo IP pubblico**.                          |
    | Risorsa di destinazione          | Scegliere un indirizzo IP pubblico per visualizzare l'elenco delle risorse con gli indirizzi IP pubblici inclusi nella stessa sottoscrizione. In **Risorsa** selezionare l'indirizzo IP pubblico denominato **myIISVMEastUS-ip**. Questo è l'indirizzo IP pubblico della VM del server IIS nell'area Stati Uniti orientali.|
    |  Peso      | Immettere **100**.        |
    |        |           |

4. Ripetere i passaggi 2 e 3 per aggiungere un altro endpoint denominato **myWestEuropeEndpoint** per l'indirizzo IP pubblico **myIISVMWestEurope-ip**. Questo indirizzo è associato alla macchina virtuale del server IIS denominata myIISVMWestEurope. In **Peso** immettere **25**.
5. Dopo aver aggiunto entrambi gli endpoint, questi vengono visualizzati nel profilo di Gestione traffico unitamente allo stato di monitoraggio **Online**.

## <a name="test-the-traffic-manager-profile"></a>Testare il profilo di Gestione traffico

Per vedere in azione Gestione traffico, completare i passaggi seguenti:

1. Determinare il nome DNS del profilo di Gestione traffico.
2. Visualizzare Gestione traffico in azione.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinare il nome DNS del profilo di Gestione traffico

In questa esercitazione, per semplicità, si usa il nome DNS del profilo di Gestione traffico per visitare i siti Web.

È possibile determinare il nome DNS del profilo di Gestione traffico nel modo seguente:

1. Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico creato nella sezione precedente. Selezionare il profilo di Gestione traffico nei risultati visualizzati.
2. Selezionare **Panoramica**.
3. Il profilo di Gestione traffico visualizza il relativo nome DNS. Nelle distribuzioni di produzione si configura un nome di dominio personalizzato in modo che punti al nome di dominio di Gestione traffico usando un record CNAME DNS.

   ![Nome DNS di Gestione traffico](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Visualizzare Gestione traffico in azione

In questa sezione è possibile vedere in azione Gestione traffico.

1. Nel menu a sinistra selezionare **Tutte le risorse**. Nell'elenco delle risorse selezionare**myVMEastUS** nel gruppo di risorse**myResourceGroupTM1**.
2. Nella pagina **Panoramica** selezionare **Connetti**. In **Connetti alla macchina virtuale in corso** selezionare **Scarica file RDP**.
3. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati quando è stata creata la macchina virtuale. Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un account diverso** per specificare le credenziali immesse al momento della creazione della macchina virtuale.
4. Selezionare **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione.
6. In un Web browser, nella macchina virtuale myVMEastUS, immettere il nome DNS del profilo di Gestione traffico per visualizzare il sito Web. Si viene instradati al sito Web ospitato nel server IIS myIISVMEastUS perché ad esso è stato assegnato un peso maggiore di **100**. Al server IIS myIISVMWestEurope è stato assegnato un valore di peso endpoint inferiore, corrispondente a **25**.

   ![Testare il profilo di Gestione traffico](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Ripetere i passaggi da 1 a 6 per la macchina virtuale myVMWestEurope per visualizzare la risposta ponderata del sito Web.

## <a name="delete-the-traffic-manager-profile"></a>Eliminare il profilo di Gestione traffico

Quando i gruppi di risorse creati in questa esercitazione non sono più necessari, è possibile eliminarli. A tale scopo, selezionare il gruppo di risorse (**ResourceGroupTM1** o **ResourceGroupTM2**) e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare il metodo di routing del traffico Geografico tramite Gestione traffico](traffic-manager-configure-geographic-routing-method.md)

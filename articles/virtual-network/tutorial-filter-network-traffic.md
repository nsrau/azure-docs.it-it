---
title: Filtrare il traffico di rete - Esercitazione - Portale di Azure
titlesuffix: Azure Virtual Network
description: Questa esercitazione illustra come filtrare il traffico di rete a una subnet, con un gruppo di sicurezza di rete, usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: ad34c6a876ca21bc7ef32cce638240e0d23b3177
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723916"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Esercitazione: Filtrare il traffico di rete con un gruppo di sicurezza di rete usando il portale di Azure

È possibile filtrare il traffico di rete in ingresso e in uscita da una subnet di rete virtuale con un gruppo di sicurezza di rete. I gruppi di sicurezza di rete contengono regole di sicurezza per filtrare il traffico di rete in base a indirizzo IP, porta e protocollo. Le regole di sicurezza vengono applicate alle risorse distribuite in una subnet. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gruppo di sicurezza di rete e le regole di sicurezza
> * Creare una rete virtuale e associare un gruppo di sicurezza di rete a una subnet
> * Distribuire le macchine virtuali in una subnet
> * Testare i filtri del traffico

Se si preferisce, è possibile completare questa esercitazione usando l'[interfaccia della riga di comando di Azure](tutorial-filter-network-traffic-cli.md) oppure [PowerShell](tutorial-filter-network-traffic-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Rete virtuale**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Nome                    | myVirtualNetwork                                   |
    | Spazio degli indirizzi           | 10.0.0.0/16                                        |
    | Sottoscrizione            | Selezionare la propria sottoscrizione.                          |
    | Gruppo di risorse          | Selezionare **Crea nuovo** e immettere *myResourceGroup*. |
    | Località                | Selezionare **Stati Uniti orientali**.                                |
    | Subnet - Nome            | mySubnet                                           |
    | Subnet - Intervallo di indirizzi  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Creare gruppi di sicurezza dell'applicazione

Un gruppo di sicurezza delle applicazioni consente di raggruppare i server con funzioni simili, ad esempio i server Web.

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Nella casella **Cerca nel Marketplace** immettere *Gruppo di sicurezza delle applicazioni*. Quando **Gruppo di sicurezza delle applicazioni** viene visualizzato nei risultati della ricerca, selezionarlo, selezionare ancora **Gruppo di sicurezza delle applicazioni** in **Tutto** e quindi selezionare **Crea**.
3. Immettere o selezionare le informazioni seguenti e quindi selezionare **Crea**:

    | Impostazione        | Valore                                                         |
    | ---            | ---                                                           |
    | NOME           | myAsgWebServers                                               |
    | Sottoscrizione   | Selezionare la propria sottoscrizione.                                     |
    | Gruppo di risorse | Selezionare **Usa esistente** e quindi **myResourceGroup**. |
    | Località       | Stati Uniti orientali                                                       |

4. Completare di nuovo il passaggio 3, specificando i valori seguenti:

    | Impostazione        | Valore                                                         |
    | ---            | ---                                                           |
    | NOME           | myAsgMgmtServers                                              |
    | Sottoscrizione   | Selezionare la propria sottoscrizione.                                     |
    | Gruppo di risorse | Selezionare **Usa esistente** e quindi **myResourceGroup**. |
    | Località       | Stati Uniti orientali                                                       |

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi selezionare **Gruppo di sicurezza di rete**.
3. Immettere o selezionare le informazioni seguenti e quindi selezionare **Crea**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myNsg|
    |Sottoscrizione| Selezionare la propria sottoscrizione.|
    |Gruppo di risorse | Selezionare **Usa esistente** e quindi *myResourceGroup*.|
    |Località|Stati Uniti orientali|

## <a name="associate-network-security-group-to-subnet"></a>Associare il gruppo di sicurezza di rete alla subnet

1. Nella casella *Cerca risorse, servizi e documentazione* nella parte superiore del portale iniziare a digitare *myNsg*. Selezionare **myNsg** quando viene visualizzato nei risultati della ricerca.
2. In **IMPOSTAZIONI** selezionare **Subnet** e quindi selezionare **+ Associa**, come illustrato nell'immagine seguente:

    ![Associare il gruppo di sicurezza di rete alla subnet](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. In **Associa subnet** selezionare **Rete virtuale** e quindi selezionare **myVirtualNetwork**. Selezionare **Subnet**, selezionare **mySubnet** e quindi selezionare **OK**.

## <a name="create-security-rules"></a>Creare regole di sicurezza

1. In **IMPOSTAZIONI**  selezionare **Regole di sicurezza in ingresso**, quindi **+ Aggiungi**, come illustrato nell'immagine seguente:

    ![Aggiungere una regola di sicurezza in ingresso](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. Creare una regola di sicurezza che consenta le porte 80 e 443 per il gruppo di sicurezza delle applicazioni **myAsgWebServers**. In **Aggiungi regola di sicurezza in ingresso** immettere o selezionare i valori seguenti, accettare le impostazioni predefinite restanti e quindi selezionare **Aggiungi**:

    | Impostazione                 | Valore                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Destination             | Selezionare **Gruppo di sicurezza delle applicazioni** e quindi selezionare **myAsgWebServers** per **Gruppo di sicurezza delle applicazioni**.  |
    | Intervalli di porte di destinazione | Immettere 80,443                                                                                                    |
    | Protocollo                | Selezionare TCP                                                                                                      |
    | NOME                    | Allow-Web-All                                                                                                   |

3. Completare di nuovo il passaggio 2, usando i valori seguenti:

    | Impostazione                 | Valore                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Destination             | Selezionare **Gruppo di sicurezza delle applicazioni** e quindi selezionare **myAsgMgmtServers** per **Gruppo di sicurezza delle applicazioni**. |
    | Intervalli di porte di destinazione | Immettere 3389                                                                                                      |
    | Protocollo                | Selezionare TCP                                                                                                      |
    | Priorità                | Immettere 110                                                                                                       |
    | NOME                    | Allow-RDP-All                                                                                                   |

    In questa esercitazione RDP (porta 3389) è esposto a Internet per la VM assegnata al gruppo di sicurezza delle applicazioni *myAsgMgmtServers*. Per gli ambienti di produzione, anziché esporre la porta 3389 a Internet, è consigliabile connettersi alle risorse di Azure da gestire tramite una connessione di rete VPN o privata.

Dopo aver completato i passaggi da 1 a 3, esaminare le regole create. L'elenco si presenterà come quello nell'immagine seguente:

![Regole di sicurezza](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale.

### <a name="create-the-first-vm"></a>Creare la prima VM

1. Selezionare **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi scegliere **OK**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myVmWeb|
    |Nome utente| Immettere un nome utente a scelta.|
    |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Sottoscrizione| Selezionare la propria sottoscrizione.|
    |Gruppo di risorse| Selezionare **Usa esistente** e selezionare **myResourceGroup**.|
    |Località| Selezionare **Stati Uniti orientali**.|

4. Selezionare una dimensione per la VM e quindi selezionare **Seleziona**.
5. In **Impostazioni** selezionare i valori seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **OK**:

    |Impostazione|Valore|
    |---|---|
    |Rete virtuale |Selezionare **myVirtualNetwork**|
    |Gruppo di sicurezza di rete | Selezionare **Advanced** (Avanzate).|
    |Gruppo di sicurezza di rete (firewall)| Selezionare **(nuovo) myVmWeb-nsg** e quindi in **Scegli un gruppo di sicurezza di rete** selezionare **Nessuno**. |

6. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della VM.

### <a name="create-the-second-vm"></a>Creare la seconda VM

Completare nuovamente i passaggi 1-6, ma nel passaggio 3 assegnare alla VM il nome *myVmMgmt*. La distribuzione della VM richiede alcuni minuti. Continuare con il passaggio successivo solo dopo che la VM è stata distribuita.

## <a name="associate-network-interfaces-to-an-asg"></a>Associare le interfacce di rete a un gruppo di sicurezza delle applicazioni

Quando il portale ha creato le VM, ha creato un'interfaccia di rete per ogni VM e ha associato l'interfaccia di rete alla VM. Aggiungere l'interfaccia di rete per ogni VM a uno dei gruppi di sicurezza delle applicazioni creati in precedenza:

1. Nella casella *Cerca risorse, servizi e documentazione* nella parte superiore del portale iniziare a digitare *myVmWeb*. Selezionare la macchina virtuale **myVmWeb** quando viene visualizzata nei risultati della ricerca.
2. In **IMPOSTAZIONI** selezionare **Rete**.  Selezionare **Configura i gruppi di sicurezza delle applicazioni**, selezionare **myAsgWebServers** per **Gruppi di sicurezza delle applicazioni** e quindi selezionare **Salva**, come illustrato nella figura seguente:

    ![Associare al gruppo di sicurezza delle applicazioni](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. Completare di nuovo i passaggi 1 e 2, cercando la VM **myVmMgmt** e selezionando il gruppo di sicurezza delle applicazioni **myAsgMgmtServers**.

## <a name="test-traffic-filters"></a>Testare i filtri del traffico

1. Connettersi alla VM *myVmMgmt*. Immettere *myVmMgmt* nella casella di ricerca nella parte superiore del portale. Selezionare **myVmMgmt** quando viene visualizzato nei risultati della ricerca. Scegliere il pulsante **Connetti**.
2. Selezionare **Scarica file RDP**.
3. Aprire il file con estensione rdp scaricato e selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM. Potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un altro account** per specificare le credenziali immesse al momento della creazione della VM.
4. Selezionare **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione.

    La connessione ha esito positivo, perché nella porta 3389 è consentito il traffico in ingresso proveniente da Internet al gruppo di sicurezza dell'applicazione *myAsgMgmtServers* in cui si trova l'interfaccia di rete collegata alla VM *myVmMgmt*.

6. Connettersi alla VM *myVmWeb* dalla VM *myVmMgmt* immettendo il comando seguente in una sessione di PowerShell:

    ``` 
    mstsc /v:myVmWeb
    ```

    È possibile connettersi alla VM myVmWeb dalla VM myVmMgmt perché le VM nella stessa rete virtuale possono comunicare tra di esse tra qualsiasi porta, per impostazione predefinita. Non è tuttavia possibile creare una connessione Desktop remoto alla VM *myVmWeb* da Internet perché la regola di sicurezza per il gruppo *myAsgWebServers* non consente il traffico in ingresso da Internet sulla porta 3389 e il traffico in ingresso da Internet è negato a tutte le risorse, per impostazione predefinita.

7. Per installare Microsoft IIS nella VM *myVmWeb*, immettere il comando seguente da una sessione di PowerShell nella VM *myVmWeb*:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Al termine dell'installazione di IIS, disconnettersi dalla VM *myVmWeb*, rimanendo nella connessione Desktop remoto della VM *myVmMgmt*.
9. Disconnettersi dalla VM *myVmMgmt*.
10. Nella casella *Cerca risorse, servizi e documentazione* nella parte superiore del portale di Azure iniziare a digitare *myVmWeb* dal computer. Selezionare **myVmWeb** quando viene visualizzato nei risultati della ricerca. Si noti l'**indirizzo IP pubblico** della macchina virtuale. L'indirizzo visualizzato nella figura seguente è 137.135.84.74, ma l'indirizzo dell'utente è diverso:

    ![Indirizzo IP pubblico](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. Per verificare che sia possibile accedere al server Web *myVmWeb* da Internet, aprire un browser Internet nel computer e passare a `http://<public-ip-address-from-previous-step>`. Viene visualizzata la schermata iniziale di IIS, perché sulla porta 80 è consentito il traffico in ingresso proveniente da Internet al gruppo di sicurezza delle applicazioni *myAsgWebServers* in cui si trova l'interfaccia di rete collegata alla VM *myVmWeb*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un gruppo di sicurezza di rete, che è stato associato a una subnet di rete virtuale. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md) e [Gestire un gruppo di sicurezza di rete](manage-network-security-group.md).

Per impostazione predefinita, Azure instrada il traffico tra subnet. È anche possibile ad esempio scegliere di instradare il traffico tra subnet tramite una VM che funge da firewall. Per informazioni su come creare una tabella di route, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Creare una tabella di route](./tutorial-create-route-table-portal.md)
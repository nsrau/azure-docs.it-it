---
title: Limitare l'accesso di rete alle risorse PaaS - Esercitazione - Portale di Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come limitare l'accesso di rete alle risorse di Azure, ad esempio Archiviazione di Azure e il database SQL di Azure, con gli endpoint di servizio della rete virtuale usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/14/2018
ms.author: jdial
ms.openlocfilehash: 2efbd6e0fc3f90909553bc839a8b61ff3ed681ad
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267391"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Esercitazione: Limitare l'accesso di rete alle risorse PaaS con gli endpoint di servizio della rete virtuale usando il portale di Azure

Gli endpoint di servizio della rete virtuale consentono di limitare l'accesso di rete ad alcune risorse dei servizi di Azure a una subnet della rete virtuale. È anche possibile rimuovere l'accesso Internet alle risorse. Gli endpoint di servizio forniscono la connessione diretta dalla rete virtuale ai servizi di Azure supportati, consentendo di usare lo spazio indirizzi privato della rete virtuale per accedere ai servizi di Azure. Il traffico destinato alle risorse di Azure tramite gli endpoint di servizio rimane sempre nella rete backbone di Microsoft Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete virtuale con una subnet
> * Aggiungere una subnet e abilitare un endpoint di servizio
> * Creare una risorsa di Azure e consentire l'accesso di rete alla risorsa da una sola subnet
> * Distribuire una macchina virtuale (VM) in ogni subnet
> * Verificare che venga consentito l'accesso a una risorsa da una subnet
> * Verificare che venga rifiutato l'accesso a una risorsa da una subnet e da Internet

Se si preferisce, è possibile completare questa esercitazione usando l'[interfaccia della riga di comando di Azure](tutorial-restrict-network-access-to-resources-cli.md) oppure [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Rete virtuale**.
3. Immettere o selezionare le informazioni seguenti e quindi selezionare **Crea**:

    |Impostazione|Valore|
    |----|----|
    |NOME| myVirtualNetwork |
    |Spazio degli indirizzi| 10.0.0.0/16|
    |Sottoscrizione| Selezionare la propria sottoscrizione|
    |Gruppo di risorse | Selezionare **Crea nuovo** e immettere *myResourceGroup*.|
    |Località| Selezionare **Stati Uniti orientali**. |
    |Nome della subnet| Pubblico|
    |Intervallo di indirizzi subnet| 10.0.0.0/24|
    |Endpoint di servizio| Disabled|

    ![Immettere le informazioni di base sulla rete virtuale](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)


## <a name="enable-a-service-endpoint"></a>Abilitare un endpoint di servizio

Gli endpoint di servizio sono abilitati per servizio e per subnet. Creare una subnet e abilitare un endpoint di servizio per la subnet.

1. Nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale immettere *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
2. Aggiungere una subnet alla rete virtuale. In **IMPOSTAZIONI** selezionare **Subnet** e quindi selezionare **+ Subnet**, come illustrato nell'immagine seguente:

    ![Aggiungi subnet](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. In **Aggiungi subnet** selezionare o immettere le informazioni seguenti e quindi fare clic su **OK**:

    |Impostazione|Valore|
    |----|----|
    |NOME| Privato |
    |Intervallo di indirizzi| 10.0.1.0/24|
    |Endpoint di servizio| Selezionare **Microsoft.Storage** in **Servizi**|

> [!CAUTION]
> Prima di abilitare un endpoint di servizio per una subnet esistente che contiene risorse, vedere [Cambiare le impostazioni della subnet](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Limitare l'accesso di rete per una subnet

Per impostazione predefinita, tutte le macchine virtuali in una subnet possono comunicare con tutte le risorse. È possibile limitare le comunicazioni verso e da tutte le risorse in una subnet creando un gruppo di sicurezza di rete e associandolo alla subnet.

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi selezionare **Gruppo di sicurezza di rete**.
3. In **Create a network security group** (Crea un gruppo di sicurezza di rete) immettere o selezionare le informazioni seguenti e quindi selezionare **Crea**:

    |Impostazione|Valore|
    |----|----|
    |NOME| myNsgPrivate |
    |Sottoscrizione| Selezionare la propria sottoscrizione|
    |Gruppo di risorse | Selezionare **Usa esistente** e selezionare *myResourceGroup*.|
    |Località| Selezionare **Stati Uniti orientali**. |

4. Dopo che il gruppo di sicurezza di rete è stato creato, immettere *myNsgPrivate* nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale. Selezionare **myNsgPrivate** quando viene visualizzato nei risultati della ricerca.
5. In **IMPOSTAZIONI** selezionare **Regole di sicurezza in uscita**.
6. Selezionare **+ Aggiungi**.
7. Creare una regola che consenta le comunicazioni in uscita al servizio Archiviazione di Azure. Immettere o selezionare le informazioni seguenti e quindi fare clic su **OK**:

    |Impostazione|Valore|
    |----|----|
    |Sorgente| Selezionare **VirtualNetwork** |
    |Intervalli di porte di origine| * |
    |Destination | Selezionare **Service Tag** (Tag del servizio)|
    |Tag del servizio di destinazione | Selezionare **Archiviazione**|
    |Intervalli di porte di destinazione| * |
    |Protocollo|Qualsiasi|
    |Azione|CONSENTI|
    |Priorità|100|
    |NOME|Allow-Storage-All|
    
8. Creare una regola che neghi le comunicazioni in uscita verso Internet. Questa regola esegue l'override di una regola predefinita in tutti i gruppi di sicurezza di rete che consente le comunicazioni Internet in uscita. Completare di nuovo i passaggi 6 e 7, usando i valori seguenti:

    |Impostazione|Valore|
    |----|----|
    |Sorgente| Selezionare **VirtualNetwork** |
    |Intervalli di porte di origine| * |
    |Destination | Selezionare **Service Tag** (Tag del servizio)|
    |Tag del servizio di destinazione| Selezionare **Internet**|
    |Intervalli di porte di destinazione| * |
    |Protocollo|Qualsiasi|
    |Azione|Nega|
    |Priorità|110|
    |NOME|Deny-Internet-All|

9. In **IMPOSTAZIONI** selezionare **Regole di sicurezza in ingresso**.
10. Selezionare **+ Aggiungi**.
11. Creare una regola che consenta il traffico Remote Desktop Protocol (RDP) in ingresso verso la subnet da qualsiasi posizione. La regola esegue l'override di una regola di sicurezza predefinita che rifiuta tutto il traffico in ingresso da Internet. Sono consentite le connessioni Desktop remoto alla subnet per poter testare la connettività in un passaggio successivo. Completare di nuovo i passaggi 6 e 7, usando i valori seguenti:

    |Impostazione|Valore|
    |----|----|
    |Sorgente| Qualsiasi |
    |Intervalli di porte di origine| * |
    |Destination | Selezionare **Service Tag** (Tag del servizio)|
    |Tag del servizio di destinazione| Selezionare **VirtualNetwork**|
    |Intervalli di porte di destinazione| 3389 |
    |Protocollo|Qualsiasi|
    |Azione|CONSENTI|
    |Priorità|120|
    |NOME|Allow-RDP-All|

12. In **Impostazioni** selezionare **Subnet**.
13. Selezionare **+ Associa**
14. In **Associa subnet** selezionare **Rete virtuale** e quindi selezionare **myVirtualNetwork** in **Scegliere una rete virtuale**.
15. In **Scegli subnet** selezionare **Privata** e quindi fare clic su **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Limitare l'accesso di rete a una risorsa

I passaggi necessari per limitare l'accesso di rete alle risorse create tramite i servizi di Azure abilitati per gli endpoint di servizio variano a seconda dei servizi. Vedere la documentazione relativa ai singoli servizi per i passaggi specifici. La parte rimanente di questa esercitazione include, a titolo di esempio, i passaggi da eseguire per limitare l'accesso di rete per un account di archiviazione di Azure.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Archiviazione** e quindi **Account di archiviazione: BLOB, File, Tabelle, Code**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:

    |Impostazione|Valore|
    |----|----|
    |NOME| Immettere un nome univoco per tutte le località di Azure, di lunghezza compresa tra 3 e 24 caratteri e costituito solo da numeri e lettere minuscole.|
    |Tipo di account|Archiviazione v2 (utilizzo generico v2)|
    |Replica| Archiviazione con ridondanza locale|
    |Sottoscrizione| Selezionare la propria sottoscrizione|
    |Gruppo di risorse | Selezionare **Usa esistente** e selezionare *myResourceGroup*.|
    |Località| Selezionare **Stati Uniti orientali**. |

### <a name="create-a-file-share-in-the-storage-account"></a>Creare una condivisione file nell'account di archiviazione

1. Dopo aver creato l'account di archiviazione, immettere il nome dell'account di archiviazione nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale. Quando il nome dell'account di archiviazione viene visualizzato nei risultati della ricerca, selezionarlo.
2. Selezionare **File** come illustrato nell'immagine seguente:

    ![Account di archiviazione](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 
3. Selezionare **+ Condivisione file** in **Servizio file**.
4. Immettere *my-file-share* in **Nome** e quindi fare clic su **OK**.
5. Chiudere la finestra di dialogo **Servizio file**.

### <a name="restrict-network-access-to-a-subnet"></a>Limitare l'accesso di rete a una subnet

Per impostazione predefinita, gli account di archiviazione accettano connessioni di rete dai client in qualsiasi rete, inclusa la rete Internet. Negare l'accesso di rete da Internet e da tutte le altre subnet in tutte le reti virtuali, tranne per la subnet *privata* nella rete virtuale *myVirtualNetwork*.

1. In **IMPOSTAZIONI** per l'account di archiviazione selezionare **Firewall e reti virtuali**.
2. In **Reti virtuali** selezionare **Reti selezionate**.
3. Selezionare **Aggiungi rete virtuale esistente**.
4. In **Aggiungi reti** selezionare i valori seguenti e quindi selezionare **Aggiungi**:

    |Impostazione|Valore|
    |----|----|
    |Sottoscrizione| Selezionare la propria sottoscrizione.|
    |Reti virtuali|Selezionare **myVirtualNetwork** in **Reti virtuali**|
    |Subnet| Selezionare **Privata** in **Subnet**|

    ![Firewall e reti virtuali](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png) 

5. Selezionare **Salva**.
6. Chiudere la finestra di dialogo **Firewall e reti virtuali**.
7. In **IMPOSTAZIONI** per l'account di archiviazione selezionare **Chiavi di accesso**, come illustrato nell'immagine seguente:

      ![Firewall e reti virtuali](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Prendere nota del valore di **Chiave**, perché sarà necessario immetterlo manualmente in un passaggio successivo quando si eseguirà il mapping della condivisione file a una lettera di unità in una macchina virtuale.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Per testare l'accesso di rete a un account di archiviazione, distribuire una VM in ogni subnet.

### <a name="create-the-first-virtual-machine"></a>Creare la prima macchina virtuale

1. Selezionare **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**.
3. Immettere o selezionare le informazioni seguenti e quindi fare clic su **OK**:

    |Impostazione|Valore|
    |----|----|
    |NOME| myVmPublic|
    |Nome utente|Immettere un nome utente a scelta.|
    |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Sottoscrizione| Selezionare la propria sottoscrizione.|
    |Gruppo di risorse| Selezionare **Usa esistente** e selezionare **myResourceGroup**.|
    |Località| Selezionare **Stati Uniti orientali**.|

    ![Immettere le informazioni di base relative a una macchina virtuale](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Selezionare una dimensione per la macchina virtuale e quindi scegliere **Seleziona**.
5. In **Impostazioni** selezionare **Rete** e quindi selezionare **myVirtualNetwork**. Selezionare quindi **Subnet** e selezionare **Pubblica**, come illustrato nell'immagine seguente:

    ![Selezionare una rete virtuale](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
6. Nella pagina **Riepilogo** selezionare **Crea** per avviare la distribuzione della macchina virtuale. La distribuzione della VM richiede alcuni minuti, ma è possibile continuare con il passaggio successivo durante la creazione della VM.

### <a name="create-the-second-virtual-machine"></a>Creare la seconda macchina virtuale

Completare di nuovo i passaggi da 1 a 6, ma nel passaggio 3 assegnare alla macchina virtuale il nome *myVmPrivate* e nel passaggio 5 selezionare la subnet **Privata**.

La distribuzione della VM richiede alcuni minuti. Non continuare con il passaggio successivo fino al termine della creazione e all'apertura delle impostazioni nel portale.

## <a name="confirm-access-to-storage-account"></a>Verificare che venga consentito l'accesso a un account di archiviazione

1. Al termine della creazione della VM *myVmPrivate*, Azure apre le relative impostazioni. Connettersi alla VM selezionando il pulsante **Connetti**, come illustrato nell'immagine seguente:

    ![Connettersi a una macchina virtuale](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Dopo aver selezionato il pulsante **Connetti**, viene creato e scaricato nel computer un file Remote Desktop Protocol con estensione rdp.  
3. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM. Potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un altro account** per specificare le credenziali immesse al momento della creazione della VM. 
4. Selezionare **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione.
6. Nella VM *myVmPrivate* eseguire il mapping della condivisione file di Azure all'unità Z usando PowerShell. Prima di eseguire i comandi seguenti, sostituire `<storage-account-key>` e `<storage-account-name>` con i valori specificati e recuperati in [Creare un account di archiviazione](#create-a-storage-account).

    ```powershell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
    ```
    
    L'output restituito da PowerShell è simile all'output di esempio seguente:

    ```powershell
    Name           Used (GB)     Free (GB) Provider      Root
    ----           ---------     --------- --------      ----
    Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
    ```

    Il mapping della condivisione file di Azure all'unità Z è stato eseguito correttamente.

7. Verificare che la VM non abbia connettività Internet in uscita da un prompt dei comandi:

    ```
    ping bing.com
    ```
    
    Non si ricevono risposte perché il gruppo di sicurezza di rete associato alla subnet *privata* non consente l'accesso in uscita a Internet.

8. Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Verificare che venga rifiutato l'accesso a un account di archiviazione

1. Immettere *myVmPublic* nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale.
2. Selezionare **myVmPublic** quando viene visualizzato nei risultati della ricerca.
3. Completare i passaggi da 1 a 6 di [Verificare che venga consentito l'accesso a un account di archiviazione](#confirm-access-to-storage-account) per la VM *myVmPublic*.

    L'accesso viene rifiutato e si riceve un errore `New-PSDrive : Access is denied`. L'accesso viene rifiutato perché la VM *myVmPublic* è distribuita nella subnet *Public*. La subnet *pubblica* non ha un endpoint di servizio abilitato per Archiviazione di Azure. L'account di archiviazione consente l'accesso di rete solo dalla subnet *privata* e non dalla subnet *pubblica*.

4. Chiudere la sessione Desktop remoto alla VM *myVmPublic*.

5. Dal computer passare al [portale](https://portal.azure.com) di Azure.
6. Immettere il nome dell'account di archiviazione creato nella casella **Cerca risorse, servizi e documentazione**. Quando il nome dell'account di archiviazione viene visualizzato nei risultati della ricerca, selezionarlo.
7. Selezionare **File**.
8. Viene visualizzato l'errore illustrato nell'immagine seguente:

    ![Errore Accesso negato](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

    L'accesso viene rifiutato perché il computer non è nella subnet *Privata* della rete virtuale *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato abilitato un endpoint di servizio per una subnet della rete virtuale. È stato illustrato che è possibile abilitare gli endpoint di servizio per le risorse distribuite da più servizi di Azure. È stato creato un account di archiviazione di Azure ed è stato limitato l'accesso di rete all'account di archiviazione alle sole risorse in una subnet della rete virtuale. Per altre informazioni sugli endpoint servizio, vedere [Panoramica degli endpoint servizio](virtual-network-service-endpoints-overview.md) e [Gestire le subnet](virtual-network-manage-subnet.md).

Se nell'account sono presenti più reti virtuali, può essere necessario connettere due reti virtuali per consentire alle risorse di ogni rete virtuale di comunicare tra loro. Passare all'esercitazione successiva per informazioni su come connettere le reti virtuali.

> [!div class="nextstepaction"]
> [Connettere reti virtuali](./tutorial-connect-virtual-networks-portal.md)

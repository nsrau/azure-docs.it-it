---
title: Come abilitare la virtualizzazione annidata in macchine virtuali di Azure | Microsoft Docs
description: Come abilitare la virtualizzazione annidata in macchine virtuali di Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: philmea
manager: timlt
ms.author: philmea
ms.date: 10/09/2017
ms.topic: howto
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: c2f511cd024accc099423f2ed5bbb15d2dd23414
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Come abilitare la virtualizzazione annidata in una macchina virtuale di Azure

La virtualizzazione annidata è supportata nelle macchine virtuali di Azure delle serie Dv3 ed Ev3. Questa funzionalità offre una notevole flessibilità e supporta vari scenari, tra cui ambienti di sviluppo, test, formazione e dimostrazione. 

Questo articolo illustra come abilitare la virtualizzazione annidata in una macchina virtuale di Azure e configurare la connettività Internet per tale macchina virtuale guest.

## <a name="create-a-dv3-or-ev3-series-azure-vm"></a>Creare una macchina virtuale di Azure della serie Dv3 o Ev3

Creare una nuova macchina virtuale di Azure di Windows Server 2016 e scegliere le dimensioni nella serie Dv3 o Ev3. Assicurarsi di scegliere dimensioni sufficienti a supportare le richieste di una macchina virtuale guest. In questo esempio viene usata una macchina virtuale di Azure di dimensioni D3_v3. 

Per informazioni sulla disponibilità delle macchine virtuali della serie Dv3 o Ev3 nelle diverse aree geografiche, vedere [qui](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Per istruzioni dettagliate sulla creazione di una nuova macchina virtuale, vedere [Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Connettersi alla macchina virtuale di Azure

Creare una connessione Desktop remoto alla macchina virtuale.

1. Fare clic sul pulsante **Connetti** nelle proprietà della macchina virtuale. Verrà creato e scaricato un file Remote Desktop Protocol, con estensione rdp.

2. Per connettersi alla VM, aprire il file RDP scaricato. Se richiesto, fare clic su **Connetti**. In Mac, è necessario un client RDP come questo [client Desktop remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponibile nel Mac App Store.

3. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale e quindi fare clic su **OK**.

4. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Abilitare la funzionalità Hyper-V nella macchina virtuale di Azure
È possibile configurare queste impostazioni manualmente oppure usare lo script di PowerShell fornito da Microsoft per automatizzare la configurazione.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opzione 1: Usare uno script di PowerShell per configurare la virtualizzazione annidata
Lo script di PowerShell per abilitare la virtualizzazione annidata in un host Windows Server 2016 è disponibile in [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Lo script verifica i prerequisiti e quindi configura la virtualizzazione annidata nella macchina virtuale di Azure. Per completare la configurazione, è necessario riavviare la macchina virtuale di Azure. Questo script potrebbe funzionare anche in altri ambienti, ma non vengono fornite garanzie in tal senso. Per una dimostrazione video sulla virtualizzazione annidata in esecuzione in Azure, vedere il post di blog di Azure https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opzione 2: Configurare manualmente la virtualizzazione annidata

1. Nella macchina virtuale di Azure aprire PowerShell come amministratore. 

2. Abilitare la funzionalità Hyper-V e gli strumenti di gestione.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Questo comando riavvia la macchina virtuale di Azure. Durante il processo di riavvio la connessione RDP viene interrotta.
    
3. Dopo il riavvio della macchina virtuale di Azure riconnettersi alla macchina virtuale tramite RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Configurare la connettività Internet per la macchina virtuale guest
Creare una nuova scheda di rete virtuale per la macchina virtuale guest e configurare un gateway NAT per abilitare la connettività Internet.

### <a name="create-a-nat-virtual-network-switch"></a>Creare un commutatore di rete virtuale NAT

1. Nella macchina virtuale di Azure aprire PowerShell come amministratore.
   
2. Creare un commutatore interno.

    ```powershell
    New-VMSwitch -SwitchName "InternalNATSwitch" -SwitchType Internal
    ```

3. Visualizzare le proprietà del commutatore e prendere nota del valore di ifIndex della nuova scheda.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Prendere nota del valore di "ifIndex" per il commutatore virtuale appena creato.
    
4. Creare un indirizzo IP per il gateway NAT.
    
Per poter configurare il gateway, sono necessarie alcune informazioni sulla rete:    
  * IPAddress: l'IP del gateway NAT specifica l'indirizzo IPv4 o IPv6 da usare come indirizzo predefinito del gateway per la subnet di rete virtuale. Il formato generico è a.b.c.1, ad esempio "192.168.0.1". Anche se la posizione finale non deve essere .1, in genere lo è (sulla base della lunghezza del prefisso). In genere è consigliabile usare uno spazio degli indirizzi di rete privata conforme al documento RFC 1918. 
  * PrefixLength: la lunghezza del prefisso della subnet definisce le dimensioni della subnet locale (subnet mask). La lunghezza del prefisso della subnet è un valore intero compreso tra 0 e 32. Con 0 viene eseguito il mapping all'intera rete Internet, mentre con 32 si consente il mapping di un solo IP. I valori comuni sono compresi tra 24 e 12 a seconda del numero di IP che è necessario collegare al NAT. Un valore comune di PrefixLength è 24, che corrisponde a una subnet mask 255.255.255.0.
  * InterfaceIndex: il valore **ifIndex** corrisponde all'indice dell'interfaccia del commutatore virtuale creato nel passaggio precedente. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Creare la rete NAT

Per poter configurare il gateway, è necessario specificare le informazioni sulla rete e sul gateway NAT:
  * Name: corrisponde al nome della rete NAT. 
  * InternalIPInterfaceAddressPrefix: il prefisso della subnet NAT descrive sia il prefisso IP del gateway NAT che la lunghezza del prefisso della subnet definiti in precedenza. Il formato generico è a.b.c.0/lunghezza del prefisso della subnet NAT. 

In PowerShell creare una nuova rete NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Creare la macchina virtuale guest

1. Aprire la console di gestione di Hyper-V e creare una nuova macchina virtuale. Configurare la macchina virtuale per l'uso della nuova rete interna creata.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Installare un sistema operativo nella macchina virtuale guest.
    
    >[!NOTE] 
    >
    >Per installare un sistema operativo nella macchina virtuale, è necessario il relativo supporto di installazione. In questo caso verrà usato Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Assegnare un indirizzo IP alla macchina virtuale guest

Per assegnare un indirizzo IP alla macchina virtuale guest, è possibile impostare manualmente un indirizzo IP statico nella macchina virtuale guest oppure configurare DHCP nella macchina virtuale di Azure in modo che l'indirizzo IP venga assegnato dinamicamente.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opzione 1: Configurare DHCP per l'assegnazione dinamica di un indirizzo IP alla macchina virtuale guest
Seguire la procedura seguente per configurare DHCP nella macchina virtuale host per l'assegnazione dinamica degli indirizzi.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Installare il server DHCP nella macchina virtuale di Azure

1. Aprire Server Manager. Nel dashboard fare clic su **Aggiungi ruoli e funzionalità**. Viene visualizzata la procedura guidata Aggiungi ruoli e funzionalità.
  
2. Nella procedura guidata fare clic su **Avanti** finché non viene visualizzata la pagina Ruoli server.
  
3. Selezionare la casella di controllo **Server DHCP**, fare clic su **Aggiungi funzionalità** e quindi fare clic su **Avanti** fino a completare la procedura guidata.
  
4. Fare clic su **Installa**.

#### <a name="configure-a-new-dhcp-scope"></a>Configurare un nuovo ambito DHCP

1. Aprire Gestore DHCP.
  
2. Nel riquadro di spostamento, espandere il nome del server, fare clic con il pulsante destro del mouse su **IPv4** e scegliere **Nuovo ambito**. Viene visualizzata la Creazione guidata ambito. Fare clic su **Avanti**.
  
3. Immettere un nome e una descrizione per l'ambito e quindi fare clic su **Avanti**.
  
4. Definire un intervallo IP per il server DHCP, ad esempio da 192.168.0.100 a 192.168.0.200.
  
5. Fare clic su **Avanti** finché non viene visualizzata la pagina Gateway predefinito. Come gateway predefinito immettere l'indirizzo IP creato in precedenza, ad esempio 192.168.0.1.
  
6. Fare clic su **Avanti** fino al completamento della procedura guidata senza modificare i valori predefiniti e quindi fare clic su **Fine**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opzione 2: Impostare manualmente un indirizzo IP statico nella macchina virtuale guest
Se non è stato configurato DHCP per l'assegnazione dinamica di un indirizzo IP alla macchina virtuale guest, seguire questa procedura per impostare un indirizzo IP statico.

1. Nella macchina virtuale di Azure aprire PowerShell come amministratore.

2. Fare clic con il pulsante destro del mouse sulla macchina virtuale guest e quindi su Connetti.

3. Accedere alla macchina virtuale guest.

4. Nella macchina virtuale guest aprire Centro connessioni di rete e condivisione.

5. Configurare la scheda di rete per un indirizzo compreso nell'intervallo della rete NAT creato nella sezione precedente.

In questo esempio verrà usato un indirizzo compreso nell'intervallo 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testare la connettività in una macchina virtuale guest

Nella macchina virtuale guest aprire il browser e passare a una pagina Web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

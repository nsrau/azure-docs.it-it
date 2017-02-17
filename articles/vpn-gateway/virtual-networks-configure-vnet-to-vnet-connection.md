---
title: 'Connettere una rete virtuale di Azure a un&quot;altra rete virtuale: versione classica | Documentazione Microsoft'
description: Come connettere tra loro diverse reti virtuali di Azure tramite PowerShell e il portale di Azure classico.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 7413827f-233d-4c7c-a133-9c99cf031833
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eadb1f29da69e7f6fcc2c7c19ba67f4e3072c346
ms.openlocfilehash: 863b308125a7a9ae2b9d9104d150bd2474c064b3


---
# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>Configurare una connessione da rete virtuale a rete virtuale per il modello di distribuzione classico
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Classica - Portale classico](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

Questo articolo illustra la procedura per creare e connettere reti virtuali mediante il modello di distribuzione classica (noto anche come Service Management). Nella procedura seguente viene usato il portale di Azure classico per creare gateway e reti virtuali, mentre PowerShell viene usato per configurare la connessione da rete virtuale a rete virtuale. Non è possibile configurare una connessione nel portale.

![Diagramma di connettività tra reti virtuali](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Metodi e modelli di distribuzione per connessioni da rete virtuale a rete virtuale
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Nella tabella seguente vengono descritti i metodi e i modelli di distribuzione attualmente disponibili per le configurazioni da rete virtuale a rete virtuale. Quando è disponibile un articolo contenente la procedura di configurazione, nella tabella è presente un collegamento diretto.

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Informazioni sulla connessione da rete virtuale a rete virtuale
La connessione di una rete virtuale a un'altra rete virtuale è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE. 

Le reti virtuali possono trovarsi in diverse sottoscrizioni e aree geografiche diverse. È possibile combinare una comunicazione da rete virtuale a rete virtuale con configurazioni multisito. Questo permette di definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali.

### <a name="why-connect-virtual-networks"></a>Perché connettere reti virtuali?
È possibile connettere reti virtuali per i seguenti motivi:

* **Presenza e ridondanza in più aree geografiche**
  
  * È possibile configurare la sincronizzazione o la replica geografica con connettività sicura senza passare da endpoint con connessione Internet.
  * Con Azure Load Balancer e una tecnologia di clustering Microsoft o di terze parti, è possibile configurare il carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Un esempio importante è rappresentato dalla configurazione SQL AlwaysOn con gruppi di disponibilità distribuiti in più aree di Azure.
* **Applicazioni multi livello in singole aree geografiche con alto grado di isolamento**
  
  * Nella stessa area, è possibile configurare applicazioni multilivello con più reti virtuali collegate tra loro tramite forte isolamento e comunicazione tra livelli sicura.
* **Comunicazione tra sottoscrizioni e organizzazioni in Azure**
  
  * Se si dispone di più sottoscrizioni di Azure, si possono connettere tra loro i carichi di lavoro di sottoscrizioni diverse in modo sicuro tra reti virtuali.
  * Per le aziende o i provider di servizi, è ora possibile abilitare la comunicazione tra organizzazioni con tecnologia VPN sicura in Azure.

### <a name="vnet-to-vnet-considerations-for-classic-vnets"></a>Considerazioni sulle connessioni tra reti virtuali per le reti virtuali classiche
* Le reti virtuali possono trovarsi in sottoscrizioni uguali o diverse.
* Le reti virtuali possono essere nelle sottoscrizioni uguale o diverse.
* Un servizio cloud o un endpoint di bilanciamento del carico non può estendersi tra reti virtuali, anche se sono connesse tra loro.
* Per il collegamento di più reti virtuali non sono necessari dispositivi VPN.
* VNet to VNet supporta la connessione di reti virtuali di Azure. Non supporta invece la connessione di macchine virtuali o servizi cloud non distribuiti in una rete virtuale.
* Per la connessione da rete virtuale a rete virtuale sono necessari gateway con routing dinamico. I gateway con routing statico di Azure non sono supportate.
* La connettività di rete virtuale può essere usata contemporaneamente con VPN multisito, con un massimo di 10 tunnel VPN per gateway VPN di rete virtuale con connessione ad altre reti virtuali o a siti locali.
* Gli spazi degli indirizzi delle reti virtuali e dei siti di rete locali non devono sovrapporsi. Spazi degli indirizzi sovrapposti causerà la creazione di reti virtuali o caricare i file di configurazione netcfg esito negativo.
* Non sono supportati tunnel ridondanti tra una coppia di reti virtuali.
* Tutti i tunnel VPN della rete virtuale, tra cui reti VPN da punto a sito, condividono la larghezza di banda disponibile sul gateway VPN e il contratto di servizio relativo al tempo di inattività del gateway VPN in Azure.
* Il traffico da rete virtuale a rete virtuale scorre attraverso il backbone di Azure.

## <a name="a-namestep1astep-1---plan-your-ip-address-ranges"></a><a name="step1"></a>Passaggio 1: Pianificare gli intervalli di indirizzi IP
È importante stabilire gli intervalli che verranno usati per configurare le reti virtuali. Per questa configurazione, controllare che nessun intervallo di rete virtuale si sovrapponga a un altro o a una delle reti locali alle quali si connette.

La tabella seguente mostra un esempio di come definire le reti virtuali. Usare gli intervalli solo come linee guida. Annotare gli intervalli per le reti virtuali. Queste informazioni sono necessarie per i passaggi successivi.

**Impostazioni di esempio**

| Rete virtuale | Spazio di indirizzi | Region | Si connette al sito della rete locale |
|:--- |:--- |:--- |:--- |
| VNet1 |VNet1 (10.1.0.0/16) |Stati Uniti occidentali |VNet2Local (10.2.0.0/16) |
| VNet2 |VNet2 (10.2.0.0/16) |Giappone orientale |VNet1Local (10.1.0.0/16) |

## <a name="step-2---create-vnet1"></a>Passaggio 2: Creare la rete virtuale VNet1
In questo passaggio viene creata la rete virtuale VNet1. Quando si utilizza un esempio, sostituire i valori con quelli personalizzati. Se la rete virtuale esiste già, non è necessario eseguire questo passaggio. È tuttavia necessario verificare che gli intervalli di indirizzi IP non si sovrappongano agli intervalli della seconda rete virtuale né alle altre reti virtuali alle quali si desidera connettersi.

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com). In questo articolo, si usa il portale classico perché alcune delle impostazioni di configurazione necessarie non sono ancora disponibili nel portale di Azure.
2. In basso a sinistra nella schermata fare clic su **Nuovo** > **Servizi di rete** > **Rete virtuale** > **Creazione personalizzata** per avviare la configurazione guidata. Nel corso della procedura guidata aggiungere per ogni pagina i valori specificati.

### <a name="virtual-network-details"></a>Dettagli della rete virtuale
Nella pagina Dettagli della rete virtuale immettere le seguenti informazioni:

  ![Dettagli della rete virtuale](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

* **Name -** assegnare un nome alla rete virtuale. Ad esempio, VNet1:
* **Indirizzo** : quando si crea una rete virtuale viene associata a una località di Azure (area). Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.

### <a name="dns-servers-and-vpn-connectivity"></a>Server DNS e connettività VPN
Nella pagina DNS Servers and VPN Connectivity immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra.

  ![Server DNS e connettività VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

* **Server DNS** : immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dall'elenco a discesa. Questa impostazione non comporta la creazione di un server DNS. Consente di specificare i server DNS da usare per la risoluzione dei nomi per la rete virtuale. Se si desidera disporre di risoluzione dei nomi tra le reti virtuali, è necessario configurare il server DNS, anziché usare la risoluzione dei nomi fornita da Azure.
* Non selezionare nessuna delle caselle di controllo relative alla connettività S2S o P2S. Fare clic sulla freccia in basso a destra per passare alla schermata successiva.

### <a name="virtual-network-address-spaces"></a>Spazi di indirizzi della rete virtuale
Nella pagina Spazi di indirizzi della rete virtuale è possibile immettere lo spazio di indirizzi che si desidera utilizzare per la rete virtuale. Questi sono gli indirizzi IP dinamici (DIP) che verranno assegnati alle macchine virtuali e alle istanze di altri ruoli distribuiti nella rete virtuale. 

Se si sta creando una rete virtuale che si connetterà anche alla rete locale, è particolarmente importante selezionare un intervallo che non vada a sovrapporsi agli intervalli usati per la rete locale. In questo caso è necessario coordinarsi con l'amministratore di rete. L'amministratore di rete dovrà selezionare un intervallo di indirizzi IP dallo spazio di indirizzi della rete locale da usare per la rete virtuale.

  ![Spazi di indirizzi della rete virtuale](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

* **Spazio degli indirizzi** inclusi IP iniziale e conteggio indirizzi. Verificare che gli spazi degli indirizzi specificati non si sovrappongano agli spazi degli indirizzi presenti nella rete locale. In questo esempio usiamo 10.1.0.0/16 per VNet1.
* **Aggiungi subnet** inclusi IP iniziale e conteggio indirizzi. Non sono necessarie altre subnet, ma è possibile creare una subnet separata per le macchine virtuali che avranno DIP statici. In alternativa, è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.

**Fare clic sul segno di spunta** in basso a destra nella pagina per iniziare a creare la rete virtuale. Dopo aver creato la rete virtuale, lo Stato visualizzato nella pagina Reti sarà Creato.

## <a name="step-3---create-vnet2"></a>Passaggio 3: Creare la rete virtuale VNet2
Successivamente, ripetere i passaggi precedenti per creare un'altra rete virtuale. Nei passaggi successivi verranno connesse le due reti virtuali. Consultare le [impostazioni di esempio](#step1) al Passaggio 1. Se la rete virtuale esiste già, non è necessario eseguire questo passaggio. È tuttavia necessario verificare che gli intervalli di indirizzi IP non si sovrappongano agli intervalli delle altre rete virtuali o locali alle quali si desidera connettersi.

## <a name="step-4---add-the-local-network-sites"></a>Passaggio 4: Aggiungere i siti di rete locale
Quando si crea una configurazione da rete virtuale a rete virtuale, è necessario configurare i siti di rete locale, visualizzati sulla pagina **Reti locali** del portale. Azure utilizza le impostazioni specificate in ogni sito di rete locale per stabilire come instradare il traffico tra le reti virtuali. Il nome da assegnare a ogni sito di rete locale viene stabilito dall'utente. Dal momento che il valore verrà selezionato da un elenco a discesa nei passaggi successivi, è consigliabile usare un nome descrittivo.

Ad esempio, VNet1 si connette al sito di rete locale creato dall'utente con il nome "VNet2Local". Le impostazioni di VNet2Local contengono i prefissi dell'indirizzo per VNet2, nonché un indirizzo IP pubblico per il gateway VNet2. VNet2 si connette a un sito di rete locale creato dall'utente con il nome "VNet1Local", il quale contiene i prefissi dell'indirizzo per VNet1 e l'indirizzo IP pubblico per il gateway VNet1.

### <a name="a-namelocalnetaadd-the-local-network-site-vnet1local"></a><a name="localnet"></a>Aggiungere il sito di rete locale VNet1Local
1. Nell'angolo in basso a sinistra della schermata fare clic su **Nuovo** > **Servizi di rete** > **Rete virtuale** > **Aggiungi rete locale**.
2. Nella pagina **Specificare i dettagli della rete locale** immettere nel campo **Nome** il nome da assegnare alla rete alla quale si desidera connettersi. In questo esempio viene utilizzato il nome "VNet1Local" per fare riferimento agli intervalli di indirizzi IP e al gateway della rete VNet1.
3. In **Indirizzo IP dispositivo VPN (facoltativo)**specificare un indirizzo IP pubblico valido. In genere, si utilizzerà l'indirizzo IP esterno effettivo di un dispositivo VPN. Per le configurazioni da rete virtuale a rete virtuale, si usa l'indirizzo IP pubblico assegnato al gateway della rete virtuale. Tuttavia, dato che il gateway non è ancora stato creato, è possibile specificare come segnaposto un qualsiasi indirizzo IP pubblico valido. Non lasciare vuoto questo campo, poiché per questa configurazione non è un valore facoltativo. In un passaggio successivo si tornerà a queste impostazioni e si configureranno con i corrispondenti indirizzi IP gateway generati da Azure. Fare clic sulla freccia per passare alla schermata successiva.
4. In **Specificare la pagina dell'indirizzo**, inserire l'intervallo di indirizzi IP e il numero degli indirizzi per VNet1. Tale intervallo dovrà corrispondere esattamente a quello configurato per VNet1. Azure usa l'intervallo di indirizzi IP specificato per instradare il traffico destinato a VNet1. Fare clic sul segno di spunta per creare la rete locale.

### <a name="add-the-local-network-site-vnet2local"></a>Aggiungere il sito di rete locale VNet2Local
Seguire i passaggi precedenti per creare il sito di rete locale "VNet2Local". Se necessario, consultare i valori delle [impostazioni di esempio](#step1) al Passaggio 1.

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>Configurare ciascuna rete virtuale in modo che punti a una rete locale.
Ogni rete virtuale deve puntare alla rete locale alla quale si desidera indirizzare il traffico. 

**Per VNet1**

1. Accedere alla pagina **Configura** della rete virtuale **VNet1**. 
2. Nell'area dedicata alla connettività da sito a sito selezionare "Connetti alla rete locale", quindi scegliere **VNet2Local** come rete locale nell'elenco a discesa. 
3. Salvare le impostazioni.

**Per VNet2**

1. Accedere alla pagina **Configura** della rete virtuale **VNet2**. 
2. Nell'area dedicata alla connettività da sito a sito selezionare "Connetti alla rete locale", quindi scegliere **VNet1Local** come rete locale nell'elenco a discesa. 
3. Salvare le impostazioni.

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>Passaggio 5: Configurare un gateway per ogni rete virtuale
Configurare un gateway di routing dinamico per ogni rete virtuale. Questa configurazione non supporta i gateway con routing statico. Se si utilizzano reti virtuali configurate in precedenza e che dispongono già di gateway con routing dinamico, non è necessario eseguire questo passaggio. Se i gateway sono statici, è necessario eliminarli e ricrearli come gateway con routing dinamico. Se si elimina un gateway, l'indirizzo IP pubblico assegnato a quest'ultimo viene rilasciato ed è necessario tornare indietro e riconfigurare le reti locali e i dispositivi VPN con il nuovo indirizzo IP pubblico del nuovo gateway.

1. Nella pagina **Reti** verificare che la colonna di stato per la rete virtuale indichi **Creata**.
2. Nella colonna **Nome** fare clic sul nome della rete virtuale. In questo esempio viene usato il nome "VNet1".
3. Nel **Dashboard** pagina, si noti che la rete virtuale non è ancora un gateway configurato. Verrà visualizzato lo stato mentre si eseguono i passaggi necessari per configurare il gateway.
4. Nella parte inferiore della pagina fare clic su **Crea gateway** e **Routing dinamico**. Quando viene richiesto di confermare che si desidera procedere con la creazione del gateway, fare clic su Sì.
   
      ![Tipo gateway](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  
5. Durante la creazione del gateway la relativa rappresentazione grafica nella pagina diventa gialla e viene visualizzato il testo "Creazione del gateway". La creazione del gateway richiede in genere circa 30 minuti.
6. Ripetere gli stessi passaggi per VNet2. Non è necessario che il gateway di rete virtuale primo di completare prima di iniziare a creare il secondo.
7. Quando lo stato del gateway diventa "Connessione in corso", nel dashboard è visibile l'indirizzo IP pubblico di ogni gateway. Annotare l'indirizzo IP corrispondente a ciascuna rete virtuale, avendo cura di non confonderli. Questi indirizzi IP vengono usati durante la modifica degli indirizzi IP segnaposto per il dispositivo VPN di ogni rete locale.

## <a name="step-6---edit-the-local-network"></a>Passaggio 6 - Modificare la rete locale
1. Nella pagina **Reti locali** pagina fare clic sul nome della rete locale che si desidera modificare, quindi fare clic su **Modifica** nella parte inferiore della pagina. Per **indirizzo IP dispositivo VPN**, immettere l'indirizzo IP del gateway corrispondente per la rete virtuale. Ad esempio, per VNet1Local, immettere l'indirizzo IP del gateway assegnato a VNet1. Fare clic sulla freccia nella parte inferiore della pagina.
2. Nel **specificare lo spazio degli indirizzi** pagina fare clic sul segno di spunta in basso a destra senza apportare alcuna modifica.

## <a name="step-7---create-the-vpn-connection"></a>Passaggio 7: Creare la connessione VPN
Quando tutti i passaggi precedenti sono stati completati, impostare le chiavi già condivise IPsec/IKE e creare la connessione. Questa serie di passaggi usa PowerShell e non può essere configurata nel portale. Per altre informazioni sull'installazione dei cmdlet di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Assicurarsi di scaricare l'ultima versione dei cmdlet di gestione del servizio. 

1. Aprire Windows PowerShell ed effettuare l'accesso.
   
        Add-AzureAccount
2. Selezionare la sottoscrizione in cui si trovano le reti virtuali.
   
        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"
3. Creare le connessioni. Negli esempi notare che la chiave condivisa è esattamente la stessa. La chiave condivisa deve sempre corrispondere.

    Connessione da VNet1 a VNet2

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    Connessione da VNet2 a VNet1

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Attendere l'inizializzazione delle connessioni. Dopo l'inizializzazione, l'aspetto del gateway è quello mostrato nella figura seguente.
   
    ![Stato del gateway - connesso](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  
   
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>Passaggi successivi
È possibile aggiungere macchine virtuali alla rete virtuale. Per altre informazioni, vedere [Macchine virtuali - Documentazione](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) .




<!--HONumber=Jan17_HO4-->



---
title: 'Creare una connessione tra reti virtuali: versione classica: portale di Azure | Microsoft Docs'
description: Come connettere tra loro diverse reti virtuali di Azure tramite PowerShell e il portale di Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2017
ms.author: cherylmc
ms.openlocfilehash: 1e7a7af26fbfb728aa5a6b8a0d63b71f678256bf
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configurare una connessione da rete virtuale a rete virtuale (versione classica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Questo articolo descrive come creare una connessione gateway VPN tra reti virtuali. Le reti virtuali possono trovarsi in aree geografiche uguali o diverse e in sottoscrizioni uguali o diverse. I passaggi di questo articolo sono applicabili al modello di distribuzione classico e al portale di Azure. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Connettersi tra modelli di distribuzione diversi - Portale di Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Connettersi tra modelli di distribuzione diversi - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagramma di connettività tra reti virtuali](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Informazioni sulla connessione da rete virtuale a rete virtuale

La connessione di una rete virtuale a un'altra rete virtuale nel modello di distribuzione classico che usa un gateway VPN è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE.

Le reti virtuali possono trovarsi in diverse sottoscrizioni e aree geografiche diverse. È possibile combinare una comunicazione da rete virtuale a rete virtuale con configurazioni multisito. Questo permette di definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali.

![Connessioni da rete virtuale a rete virtuale](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Perché connettere reti virtuali?

È possibile connettere reti virtuali per i seguenti motivi:

* **Presenza e ridondanza in più aree geografiche**

  * È possibile configurare la sincronizzazione o la replica geografica con connettività sicura senza passare da endpoint con connessione Internet.
  * Con Azure Load Balancer e una tecnologia di clustering Microsoft o di terze parti, è possibile configurare il carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Un esempio importante è rappresentato dalla configurazione SQL AlwaysOn con gruppi di disponibilità distribuiti in più aree di Azure.
* **Applicazioni multi livello in singole aree geografiche con alto grado di isolamento**

  * Nella stessa area, è possibile configurare applicazioni multilivello con più reti virtuali collegate tra loro tramite forte isolamento e comunicazione tra livelli sicura.
* **Comunicazione tra sottoscrizioni e organizzazioni in Azure**

  * Se si dispone di più sottoscrizioni di Azure, si possono connettere tra loro i carichi di lavoro di sottoscrizioni diverse in modo sicuro tra reti virtuali.
  * Per le aziende o i provider di servizi, è ora possibile abilitare la comunicazione tra organizzazioni con tecnologia VPN sicura in Azure.

Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Considerazioni sulle connessioni da rete virtuale a rete virtuale](#faq) alla fine di questo articolo.

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare questo esercizio, scaricare e installare la versione più recente dei cmdlet di PowerShell per gestione del servizio (SM, Service Management) di Azure. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Il portale viene utilizzato per la maggior parte dei passaggi, ma per creare connessioni tra VNet, occorre utilizzare PoweShell. Non è possibile creare connessioni dal portale di Azure.

## <a name="plan"></a>Passaggio 1: Pianificare gli intervalli di indirizzi IP

È importante stabilire gli intervalli che verranno usati per configurare le reti virtuali. Per questa configurazione, controllare che nessun intervallo di rete virtuale si sovrapponga a un altro o a una delle reti locali alle quali si connette.

La tabella seguente mostra un esempio di come definire le reti virtuali. Usare gli intervalli solo come linee guida. Annotare gli intervalli per le reti virtuali. Queste informazioni sono necessarie per i passaggi successivi.

**Esempio**

| Rete virtuale | Spazio di indirizzi | Region | Si connette al sito della rete locale |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Stati Uniti orientali |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Stati Uniti occidentali |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Passaggio 2: Creare le reti virtuali

Creare due reti virtuali nel [portale di Azure](https://portal.azure.com). Per altre informazioni sulla procedura per creare reti virtuali classiche, vedere [Creare una rete virtuale classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Quando si usa il portale per creare una rete virtuale classica, è necessario passare alla pagina della rete virtuale tramite la procedura seguente; in caso contrario l'opzione per creare una rete virtuale classica non viene visualizzata:

1. Fare clic su "+" per aprire la pagina "Nuovo".
2. Nel campo "Cerca nel Marketplace" digitare "Rete virtuale". Se invece si seleziona Rete -> Rete virtuale, non sarà possibile ottenere l'opzione per creare una rete virtuale classica.
3. Cercare "Rete virtuale" nell'elenco restituito e fare clic per aprire la pagina Rete virtuale. 
4. Nella pagina della rete virtuale, selezionare "Classica" per creare una rete virtuale classica. 

Quando si segue questo articolo come esercizio, è possibile usare i valori di esempio seguenti:

**Valori per TestVNet1**

Nome: TestVNet1<br>
Spazio di indirizzi: 10.11.0.0/16, 10.12.0.0/16 (facoltativo)<br>
Subnet name: predefinito<br>
Intervallo di indirizzi subnet: 10.11.0.1/24<br>
Gruppo di risorse: ClassicRG<br>
Location: Stati Uniti orientali<br>
GatewaySubnet: 10.11.1.0/27

**Valori per TestVNet4**

Nome: TestVNet4<br>
Spazio di indirizzi: 10.41.0.0/16, 10.42.0.0/16 (facoltativo)<br>
Subnet name: predefinito<br>
Intervallo di indirizzi subnet: 10.41.0.1/24<br>
Gruppo di risorse: ClassicRG<br>
Località: Stati Uniti occidentali<br>
GatewaySubnet: 10.41.1.0/27

**Quando si creano le reti virtuali, tenere presenti le seguenti impostazioni:**

* **Spazi di indirizzi della rete virtuale**: nella pagina Spazi di indirizzi della rete virtuale specificare l'intervallo di indirizzi che si desidera usare per la rete virtuale. Questi sono gli indirizzi IP dinamici che verranno assegnati alle macchine virtuali e alle istanze di altri ruoli distribuiti nella rete virtuale.<br>Gli spazi di indirizzi selezionati non possono sovrapporsi agli spazi di indirizzi delle altre reti virtuali o delle posizioni in locale a cui si connetterà la rete virtuale.

* **Indirizzo** : quando si crea una rete virtuale viene associata a una località di Azure (area). Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.

**Dopo aver creato le reti virtuali, è possibile aggiungere le seguenti impostazioni:**

* **Spazio indirizzi**: per questa configurazione non sono necessari altri spazi degli indirizzi, ma è possibile aggiungere altri spazi degli indirizzi dopo aver creato la rete virtuale.

* **Subnet** : per questa configurazione non sono necessarie altre subnet, ma è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.

* **Server DNS**: per immettere il nome del server DNS e l'indirizzo IP. Questa impostazione non comporta la creazione di un server DNS. Consente di specificare i server DNS da usare per la risoluzione dei nomi per la rete virtuale.

In questa sezione, configurare il tipo di connessione, il sito locale e creare il gateway.

## <a name="localsite"></a>Passaggio 3: configurare il sito locale

Azure utilizza le impostazioni specificate in ogni sito di rete locale per stabilire come instradare il traffico tra le reti virtuali. Ogni rete virtuale deve puntare alla rete locale alla quale si desidera indirizzare il traffico. Il nome da assegnare a ogni sito di rete locale viene stabilito dall'utente. È consigliabile usare un nome descrittivo.

Ad esempio, TestVNet1 si connette al sito di rete locale creato dall'utente con il nome "VNet4Local". Le impostazioni per VNet4Local contengono i prefissi di indirizzo per TestVNet4.

Il sito locale per ogni rete virtuale è l'altra rete virtuale. Per la configurazione vengono usati i valori nell'esempio seguente:

| Rete virtuale | Spazio di indirizzi | Region | Si connette al sito della rete locale |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Stati Uniti orientali |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Stati Uniti occidentali |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Individuare TestVNet1 nel portale di Azure. Nella sezione **Connessioni VPN** della pagina fare clic su **Gateway**.

    ![Nessun gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Nella pagina **Nuova connessione VPN** selezionare **Da sito a sito**.
3. Fare clic su **Sito locale** per aprire la pagina Sito locale e configurare le impostazioni.
4. Nella pagina **Sito locale**, assegnare il nome al sito locale. Nel nostro esempio, il sito locale viene denominato "VNet4Local".
5. Per **Indirizzo IP del gateway VPN** è possibile usare qualsiasi indirizzo IP desiderato, purché abbia un formato valido. In genere, si utilizzerà l'indirizzo IP esterno effettivo di un dispositivo VPN. Tuttavia per la configurazioni da rete virtuale a rete virtuale classica, si usa l'indirizzo IP pubblico assegnato al gateway di rete virtuale. Dato che il gateway di rete virtuale non è ancora stato creato, specificare come segnaposto un qualsiasi indirizzo IP pubblico valido.<br>Non lasciare vuoto questo campo, poiché per questa configurazione non è un valore facoltativo. In un passaggio successivo si tornerà a queste impostazioni per configurarle con i corrispondenti indirizzi IP del gateway di rete virtuale generati da Azure.
6. Per **Spazio indirizzi client** usare lo spazio degli indirizzi dell'altra rete virtuale. Vedere l'esempio di pianificazione. Fare clic su **OK** per salvare le impostazioni e tornare alla pagina **Nuova connessione VPN**.

    ![sito locale](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Passaggio 4: creare il gateway di rete virtuale

Ogni rete virtuale deve essere un gateway di rete virtuale. Il gateway di rete virtuale indirizza e crittografa il traffico.

1. Nella pagina **Nuova connessione VPN** selezionare la casella di controllo **Crea gateway immediatamente**.
2. Fare clic su **Subnet, dimensioni e tipo di routing**. Nella pagina **Configurazione gateway** fare clic su **Subnet**.
3. Il nome della subnet del gateway viene inserito automaticamente con il nome richiesto "GatewaySubnet". **Intervallo di indirizzi** contiene gli indirizzi IP allocati per i servizi del gateway VPN. Alcune configurazioni consentono una subnet del gateway pari a/29, ma è preferibile usare /28 o /27 a configurazioni future che potrebbero richiedere più indirizzi IP per i servizi del gateway. Nelle impostazioni di esempio viene usato 10.11.1.0/27. Modificare lo spazio degli indirizzi, quindi fare clic su **OK**.
4. Configurare le **dimensioni del gateway**. Questa impostazione fa riferimento allo [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configurare il **tipo di routing**. Il tipo di routing per questa configurazione deve essere **Dinamico**. Non è possibile modificare il tipo di routing in un secondo momento, a meno che non si chiuda il gateway per crearne uno nuovo.
6. Fare clic su **OK**.
7. Nella pagina **Nuova connessione VPN** fare clic su **OK** per iniziare a creare il gateway di rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato.

## <a name="vnet4settings"></a>Passaggio 5: Configurare le impostazioni di TestVNet4

Ripetere i passaggi per [creare un sito locale](#localsite) e [creare il gateway di rete virtuale](#gw) per configurare TestVNet4, sostituendo i valori se necessario. Se si esegue questo esercizio, usare i [valori di esempio](#vnetvalues).

## <a name="updatelocal"></a>Passaggio 6: Aggiornare i siti locali

Dopo aver creato il gateway di rete virtuale per entrambe le reti virtuali, è necessario modificare i valori di **Indirizzo IP del gateway VPN** per i siti locali.

|Nome della rete virtuale|Sito collegato|Indirizzo IP del gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Indirizzo IP del gateway VPN per TestVNet4|
|TestVNet4|VNet1Local|Indirizzo IP del gateway VPN per TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1: ottenere l'indirizzo IP pubblico del gateway di rete virtuale

1. Posizionare la rete virtuale nel portale di Azure.
2. Fare clic per aprire la pagina **Panoramica** della rete virtuale. Nella pagina, in **Connessioni VPN**, è possibile visualizzare l'indirizzo IP per il gateway di rete virtuale.

  ![IP pubblico](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Copiare l'indirizzo IP, sarà necessario nella sezione successiva.
4. Ripetere questi passaggi per TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Parte 2: modificare i siti locali

1. Posizionare la rete virtuale nel portale di Azure.
2. Nella pagina **Panoramica** della rete virtuale fare clic sul sito locale.

  ![Sito locale creato](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Nella pagina **Connessioni VPN da sito a sito** fare clic sul nome del sito locale che si intende modificare.

  ![Aprire il sito locale](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Fare clic sul **sito locale** che si desidera modificare.

  ![modifica del sito](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Aggiornare **Indirizzo IP del gateway VPN** e fare clic su **OK** per salvare le impostazioni.

  ![IP del gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Chiudere le altre pagine.
7. Ripetere questi passaggi per TestVNet4.

## <a name="getvalues"></a>Passaggio 7: Recuperare i valori del file di configurazione di rete

Quando si creano reti virtuali classiche nel portale di Azure, il nome da visualizzare non è il nome completo usato per PowerShell. Ad esempio, una rete virtuale che sembra avere il nome **TestVNet1** nel portale potrebbe avere un nome molto più lungo nel file di configurazione di rete. Il nome potrebbe essere simile a: **Gruppo ClassicRG TestVNet1**. Quando si creano le connessioni, è importante usare i valori visualizzati nel file di configurazione di rete.

Nelle procedure seguenti, ci si connetterà al proprio account Azure per scaricare e visualizzare il file di configurazione di rete per ottenere i valori necessari alle connessioni.

1. Scaricare e installare la versione più recente dei cmdlet di PowerShell per Gestione del servizio di Azure. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

2. Aprire la console di PowerShell con diritti elevati e connettersi all'account. Per eseguire la connessione, usare gli esempi che seguono:

  ```powershell
  Login-AzureRmAccount
  ```

  Controllare le sottoscrizioni per l'account.

  ```powershell
  Get-AzureRmSubscription
  ```

  Se sono disponibili più sottoscrizioni, selezionare la sottoscrizione da usare.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

  Successivamente, utilizzare il cmdlet seguente per aggiungere la sottoscrizione di Azure a PowerShell per il modello di distribuzione classico.

  ```powershell
  Add-AzureAccount
  ```
3. Esportare e visualizzare il file di configurazione di rete. Creare una directory nel computer ed esportarvi il file di configurazione di rete. In questo esempio il file di configurazione di rete viene esportato in **C:\AzureNet**.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
4. Aprire il file con un editor di testo e visualizzare il nome delle reti virtuali e dei siti. Questo sarà il nome da usare nella creazione delle le connessioni.<br>I nomi delle reti virtuali sono elencati come **Nome VirtualNetworkSite =**<br>I nomi dei siti sono elencati come **Nome LocalNetworkSiteRef =**

## <a name="createconnections"></a>Passaggio 8: Creare le connessioni al gateway VPN

Quando tutti i passaggi precedenti sono stati completati, è possibile impostare le chiavi già condivise IPsec/IKE e creare la connessione. Questa procedura usa PowerShell. Impossibile configurare le connessioni da rete virtuale a rete virtuale per il modello di distribuzione classico nel portale di Azure.

Negli esempi notare che la chiave condivisa è esattamente la stessa. La chiave condivisa deve sempre corrispondere. Assicurarsi di sostituire i valori in questi esempi con i nomi esatti delle reti virtuali e dei siti di rete locale.

1. Creare la connessione da TestVNet1 a TestVNet4.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
  -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
  ```
2. Creare la connessione da TestVNet4 a TestVNet1.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
  -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
  ```
3. Attendere l'inizializzazione delle connessioni. Dopo l'inizializzazione del gateway, lo stato è "Riuscito".

  ```
  Error          :
  HttpStatusCode : OK
  Id             :
  Status         : Successful
  RequestId      :
  StatusCode     : OK
  ```

## <a name="faq"></a>Considerazioni sulle connessioni tra reti virtuali per le reti virtuali classiche
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

## <a name="next-steps"></a>Passaggi successivi
Verificare le connessioni. Vedere [Verificare una connessione di Gateway VPN](vpn-gateway-verify-connection-resource-manager.md).

---
title: Usare la rete WAN virtuale di Azure per creare una connessione da sito a sito in Azure | Microsoft Docs
description: In questa esercitazione si vedrà come usare la rete WAN virtuale di Azure per creare una connessione VPN da sito a sito ad Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: e9be7ef5c4f37c66f7cbf2c6226936438b367108
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515159"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Esercitazione: Creare una connessione da sito a sito con la rete WAN virtuale di Azure

Questa esercitazione illustra come usare la rete WAN virtuale per connettersi alle risorse in Azure tramite una connessione VPN IPSec/IKE (IKEv1 e IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).

> [!NOTE]
> Se sono disponibili molti siti, in genere si ricorre ai servizi di un [partner di rete WAN virtuale](https://aka.ms/virtualwan) per creare questa configurazione. È comunque possibile creare questa configurazione in autonomia, se si ha familiarità con le tecnologie e funzionalità di rete e si è esperti della configurazione del dispositivo VPN.
>

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwan.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete WAN
> * Creare un sito
> * Creare un hub
> * Connettere un hub a un sito
> * Creare una rete virtuale compatibile (se non ne esiste già una)
> * Connettere una rete virtuale a un hub
> * Scaricare e applicare la configurazione del dispositivo VPN
> * Visualizzare la rete WAN virtuale
> * Visualizzare lo stato di integrità delle risorse
> * Monitorare una connessione

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. Creare una rete WAN virtuale

In un browser passare al [portale di Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e accedere con l'account Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="site"></a>2. Creare un sito

Creare il numero di siti necessari, corrispondenti alle località fisiche. Ad esempio, se esiste una succursale a New York, una a Londra e una a Milano, creare tre siti separati. Questi siti contengono gli endpoint di dispositivo VPN locali. A questo punto, è possibile specificare un solo spazio di indirizzi privato per il sito.

1. Fare clic sulla rete WAN creata. Nella pagina della rete WAN, in **WAN Architecture** (Architettura di rete WAN) fare clic su **Siti VPN** per aprire la pagina Siti VPN.
2. Nella pagina **Siti VPN** fare clic su **+Crea sito**.
3. Nella pagina **Crea sito** compilare i campi seguenti:

   * **Nome** - Nome che si vuole usare per fare riferimento al sito locale.
   * **Indirizzo IP pubblico** - Indirizzo IP pubblico del dispositivo VPN che risiede nel sito locale.
   * **Spazio indirizzi privato** - Spazio di indirizzi IP nel sito locale. Il traffico destinato a questo spazio di indirizzi viene indirizzato al sito locale.
   * **Sottoscrizione** - Verificare la sottoscrizione.
   * **Gruppo di risorse** - Selezionare il gruppo di risorse che si vuole usare.
   * **Posizione**
4. Fare clic su **Mostra impostazioni avanzate** per visualizzare le impostazioni aggiuntive. 

   È possibile selezionare **BGP** per abilitare il protocollo BGP, abilitando questa funzionalità su tutte le connessioni create per questo sito in Azure. La configurazione di BGP in una rete WAN virtuale equivale alla configurazione di BGP in un gateway VPN di Azure. L'indirizzo del peer BGP locale *non deve* essere uguale all'indirizzo IP pubblico del dispositivo VPN né allo spazio di indirizzi della rete virtuale del sito VPN. Usare un indirizzo IP diverso nel dispositivo VPN per il peer BGP. Può trattarsi di un indirizzo assegnato all'interfaccia di loopback nel dispositivo. Tuttavia, *non può* essere un indirizzo APIPA (169.254.*x*.*x*). Specificare questo indirizzo nel gateway di rete locale corrispondente che rappresenta la posizione. Per i prerequisiti di BGP, vedere [Informazioni su BGP con i gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

   È anche possibile immettere **informazioni sul dispositivo** (campi facoltativi). Queste informazioni possono consentire al team di Azure di comprendere meglio l'ambiente per aggiungere ulteriori possibilità di ottimizzazione in futuro o per offrire supporto per la risoluzione dei problemi.
   
5. Fare clic su **Conferma**.
6. Dopo aver fatto clic su **Conferma**, visualizzare lo stato della pagina Siti VPN. Il sito passerà da **Provisioning** a **Provisioning effettuato**.

## <a name="hub"></a>3. Creare un hub

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="associate"></a>4. Associare i siti all'hub

In genere, gli hub devono essere associati a siti che si trovano nella stessa area della rete virtuale.

1. Nella pagina **Siti VPN** selezionare uno o più siti da associare all'hub e quindi fare clic su **+Nuova associazione hub**.
2. Nella pagina **Associa i siti a un hub** selezionare un hub dall'elenco a discesa. È possibile associare un sito ad altri hub facendo clic su **+Aggiungi associazione**.
3. È anche possibile aggiungere una **PSK** specifica o usare quella predefinita.
4. Fare clic su **Conferma**.
5. È possibile visualizzare lo stato di connessione nella pagina **Siti VPN**.

## <a name="vnet"></a>5. Crea rete virtuale

Se non è già disponibile una rete virtuale, è possibile crearne rapidamente una tramite PowerShell o il portale di Azure. Se è già disponibile una rete virtuale, verificare che soddisfi i criteri richiesti e che non abbia un gateway di rete virtuale.

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="vnet"></a>6. Connettere la rete virtuale a un hub

In questo passaggio si crea la connessione di peering tra l'hub e una rete virtuale. Ripetere questi passaggi per ogni rete virtuale a cui ci si vuole connettere.

1. Nella pagina della rete WAN virtuale fare clic su **Connessioni rete virtuale**.
2. Nella pagina di connessione alla rete virtuale fare clic su **+ Aggiungi connessione**.
3. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

    * **Nome connessione** - Specificare un nome per la connessione.
    * **Hub** - Selezionare l'hub che si vuole associare a questa connessione.
    * **Sottoscrizione** - Verificare la sottoscrizione.
    * **Rete virtuale** - Selezionare la rete virtuale che si vuole connettere all'hub. La rete virtuale non può avere un gateway di rete virtuale già esistente.
4. Fare clic su **OK** per creare la connessione peering.

## <a name="device"></a>7. Scaricare la configurazione della VPN

Usare la configurazione del dispositivo VPN per configurare il dispositivo VPN locale.

1. Nella pagina della rete WAN virtuale fare clic su **Panoramica**.
2. Nella parte superiore della pagina Panoramica fare clic su **Scarica configurazione VPN**. Azure crea un account di archiviazione nel gruppo di risorse 'microsoft-network-[posizione]', dove posizione è la posizione della rete WAN. Dopo aver applicato la configurazione ai dispositivi VPN, è possibile eliminare questo account di archiviazione.
3. Al termine della creazione del file è possibile fare clic sul collegamento per scaricarlo.
4. Applicare la configurazione al dispositivo VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Informazioni sui file di configurazione del dispositivo VPN

Il file di configurazione del dispositivo contiene le impostazioni da usare quando si configura il dispositivo VPN locale. Quando si visualizza questo file, notare le informazioni seguenti:

* **vpnSiteConfiguration -** Questa sezione indica i dettagli del dispositivo configurato come un sito che si connette alla rete WAN virtuale. Include il nome e l'indirizzo IP pubblico del dispositivo della succursale.
* **vpnSiteConnections -** Questa sezione include informazioni sulle impostazioni seguenti:

    * **Spazio degli indirizzi** della rete virtuale degli hub virtuali<br>Esempio:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Spazio degli indirizzi** delle reti virtuali connesse all'hub<br>Esempio:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Indirizzi IP** del gateway VPN dell'hub virtuale. Dato che ogni connessione del gateway VPN comprende due tunnel nella configurazione attiva-attiva, nel file saranno elencati entrambi gli indirizzi IP. In questo esempio, sono indicati "Instance0" e "Instance1" per ogni sito.<br>Esempio:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Dettagli di configurazione della connessione al gateway VPN**, ad esempio BGP, chiave precondivisa e così via. PSK è la chiave precondivisa generata automaticamente. È sempre possibile modificare la connessione nella pagina Panoramica per una chiave precondivisa personalizzata.
  
### <a name="example-device-configuration-file"></a>Esempio di file di configurazione del dispositivo

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Configurazione del dispositivo VPN

>[!NOTE]
> Se si usa la soluzione di un partner di rete WAN virtuale, la configurazione del dispositivo VPN avviene automaticamente. Il controller del dispositivo ottiene il file di configurazione da Azure e lo applica al dispositivo per configurare la connessione ad Azure. Ciò significa che non è necessario sapere come configurare manualmente il dispositivo VPN.
>

Se sono necessarie istruzioni per configurare il dispositivo, è possibile usare le istruzioni riportate nella [pagina degli script di configurazione del dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) con le avvertenze seguenti:

* Le istruzioni nella pagina dei dispositivi VPN non vengono scritte per la rete WAN virtuale, ma è possibile usare i valori della rete WAN virtuale dal file di configurazione per configurare manualmente il dispositivo VPN. 
* Gli script di configurazione del dispositivo scaricabili per Gateway VPN non funzionano per la rete WAN virtuale, perché la configurazione è diversa.
* Una nuova rete WAN virtuale può supportare IKEv1 e IKEv2.
* La rete WAN virtuale può usare solo dispositivi VPN e istruzioni per il dispositivo basati su route.

## <a name="viewwan"></a>8. Visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.
2. Nella pagina Panoramica ogni punto sulla mappa rappresenta un hub. Passare il mouse su qualsiasi punto per visualizzare il riepilogo dell'integrità dell'hub.
3. Nella sezione degli hub e delle connessioni è possibile visualizzare lo stato dell'hub, il sito, l'area, lo stato della connessione VPN e i byte in entrata e in uscita.

## <a name="viewhealth"></a>9. Visualizzare l'integrità delle risorse

1. Passare alla rete WAN.
2. Nella pagina della rete WAN, nella sezione **Supporto e risoluzione dei problemi** fare clic su **Integrità** e visualizzare la risorsa.

## <a name="connectmon"></a>10. Monitorare una connessione

Creare una connessione per monitorare la comunicazione tra una macchina virtuale di Azure e un sito remoto. Per informazioni su come configurare un monitoraggio della connessione, vedere [Monitorare la comunicazione di rete](~/articles/network-watcher/connection-monitor.md). Il campo di origine è l'IP della macchina virtuale in Azure e l'IP di destinazione è l'indirizzo IP del sito.

## <a name="cleanup"></a>11. Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una rete WAN
> * Creare un sito
> * Creare un hub
> * Connettere un hub a un sito
> * Connettere una rete virtuale a un hub
> * Scaricare e applicare la configurazione del dispositivo VPN
> * Visualizzare la rete WAN virtuale
> * Visualizzare lo stato di integrità delle risorse
> * Monitorare una connessione

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).

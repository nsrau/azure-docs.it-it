---
title: 'Esercitazione: Usare la rete WAN virtuale di Azure per creare connessioni da sito a sito'
description: In questa esercitazione si vedrà come usare la rete WAN virtuale di Azure per creare una connessione VPN da sito a sito ad Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 7ba0f1b6f37da923e389964b99a02295dc3d6050
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359528"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Esercitazione: Creare una connessione da sito a sito con la rete WAN virtuale di Azure

Questa esercitazione illustra come usare la rete WAN virtuale per connettersi alle risorse in Azure tramite una connessione VPN IPSec/IKE (IKEv1 e IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete WAN virtuale
> * Creare un hub
> * Creare un sito
> * Connettere un sito a un hub
> * Connettere un sito VPN a un hub
> * Connettere una rete virtuale a un hub
> * Scaricare un file di configurazione
> * Configurare il gateway VPN

> [!NOTE]
> Se sono disponibili molti siti, in genere si ricorre ai servizi di un [partner di rete WAN virtuale](https://aka.ms/virtualwan) per creare questa configurazione. È comunque possibile creare questa configurazione in autonomia, se si ha familiarità con le tecnologie e funzionalità di rete e si è esperti della configurazione del dispositivo VPN.
>

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwan.png)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Creare una rete WAN virtuale

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Creare un hub

Un hub è una rete virtuale che può contenere gateway per funzionalità da sito a sito, ExpressRoute o da punto a sito. Dopo aver creato l'hub, verranno addebitati i relativi costi, anche se si non collega alcun sito. La creazione del gateway VPN da sito a sito nell'hub virtuale richiede circa 30 minuti.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Creare un sito

A questo punto è possibile creare i siti che corrispondono alle posizioni fisiche. Creare il numero di siti necessari, corrispondenti alle località fisiche. Ad esempio, se esiste una succursale a New York, una a Londra e una a Milano, creare tre siti separati. Questi siti contengono gli endpoint di dispositivo VPN locali. È possibile creare fino a 1000 siti per ciascun hub virtuale in una rete WAN virtuale. Se sono presenti più hub, è possibile crearne 1000 per ognuno di questi hub. Se è presente un dispositivo CPE partner di rete WAN virtuale (link all'interno), consultare le informazioni relative all'automazione in Azure. In genere l'automazione implica una semplice esperienza tramite clic per l'esportazione di informazioni sui rami su larga scala in Azure e per la configurazione della connettività dal CPE al gateway VPN della rete WAN virtuale di Azure. Per altre informazioni, vedere [Indicazioni sull'automazione da Azure ai partner CPE](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>Connettere il sito VPN all'hub

In questo passaggio si connette il sito VPN all'hub.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Connettere la rete virtuale all'hub

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>Scaricare la configurazione della VPN

Usare la configurazione del dispositivo VPN per configurare il dispositivo VPN locale.

1. Nella pagina della rete WAN virtuale fare clic su **Panoramica**.
2. Nella parte superiore della pagina **Hub -> sito VPN** fare clic su **Scarica configurazione VPN**. Azure crea un account di archiviazione nel gruppo di risorse 'microsoft-network-[posizione]', dove posizione è la posizione della rete WAN. Dopo aver applicato la configurazione ai dispositivi VPN, è possibile eliminare questo account di archiviazione.
3. Al termine della creazione del file è possibile fare clic sul collegamento per scaricarlo.
4. Applicare la configurazione al dispositivo VPN locale.

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
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Indirizzi IP** del gateway VPN dell'hub virtuale. Dato che ogni connessione del gateway VPN comprende due tunnel nella configurazione attiva-attiva, nel file saranno elencati entrambi gli indirizzi IP. In questo esempio, sono indicati "Instance0" e "Instance1" per ogni sito.<br>Esempio:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Dettagli di configurazione della connessione al gateway VPN** , ad esempio BGP, chiave precondivisa e così via. PSK è la chiave precondivisa generata automaticamente. È sempre possibile modificare la connessione nella pagina Panoramica per una chiave precondivisa personalizzata.
  
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
                  "10.3.0.0/16"
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
* La rete WAN virtuale può usare dispositivi VPN sia basati su criteri che basati su route e istruzioni per il dispositivo.

## <a name="configure-your-vpn-gateway"></a><a name="gateway-config"></a>Configurare il gateway VPN

È possibile visualizzare e configurare le impostazioni del gateway VPN in qualsiasi momento selezionando **Visualizza/Configura**.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="Screenshot che mostra la pagina 'VPN (da sito a sito)' con una freccia che punta all'azione 'Visualizza/Configura'n." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

Nella pagina **Modifica gateway VPN** è possibile visualizzare le impostazioni seguenti:

* Indirizzo IP pubblico del gateway VPN (assegnato da Azure)
* Indirizzo IP privato del gateway VPN (assegnato da Azure)
* Indirizzo IP BGP predefinito del gateway VPN (assegnato da Azure)
* Opzione di configurazione per l'indirizzo IP BGP personalizzato: Questo campo è riservato per APIPA (Automatic Private IP Addressing). Azure supporta gli indirizzi IP BGP compresi negli intervalli 169.254.21.* e 169.254.22.*. Azure accetta le connessioni BGP in questi intervalli, ma effettua la connessione con l'IP BGP predefinito.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="Visualizzare la configurazione" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere:

> [!div class="nextstepaction"]
> * [Domande frequenti sulla rete WAN virtuale](virtual-wan-faq.md)

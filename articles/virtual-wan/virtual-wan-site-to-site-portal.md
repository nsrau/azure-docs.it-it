---
title: Usare la rete WAN virtuale di Azure per creare una connessione da sito a sito in Azure | Microsoft Docs
description: In questa esercitazione si vedrà come usare la rete WAN virtuale di Azure per creare una connessione VPN da sito a sito ad Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 1b61c335dec2c641862c08fd6f752d78b2ee5866
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056665"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan-preview"></a>Esercitazione: Creare una connessione da sito a sito con la rete WAN virtuale di Azure (anteprima)

Questa esercitazione illustra come usare la rete WAN virtuale per connettersi alle risorse in Azure tramite una connessione VPN IPSec/IKE (IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).

> [!NOTE]
> Se sono disponibili molti siti, in genere si ricorre ai servizi di un [partner di rete WAN virtuale](https://aka.ms.virtualwan) per creare questa configurazione. È comunque possibile creare questa configurazione in autonomia, se si ha familiarità con le tecnologie e funzionalità di rete e si è esperti della configurazione del dispositivo VPN.
>

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwan.png)

In questa esercitazione si apprenderà come:

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

> [!IMPORTANT]
> La rete WAN virtuale di Azure è attualmente disponibile come anteprima pubblica gestita. Per usare la rete WAN virtuale, è necessario [registrarsi per l'anteprima](#enroll).
>
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Assicurarsi di avere un dispositivo VPN basato su route compatibile che supporta IKEv2 e che sia presente un utente in grado di configurarlo. Se si collabora con un partner di rete WAN virtuale, le impostazioni di configurazione vengono create automaticamente e non è necessario preoccuparsi di sapere come configurare il dispositivo manualmente.
* Verificare di avere un indirizzo IPv4 pubblico esterno per il dispositivo VPN. L'indirizzo IP non può trovarsi dietro un NAT.
* Se esiste già una rete virtuale a cui ci si vuole connettere, verificare che nessuna delle subnet della rete locale si sovrapponga alle reti virtuali a cui ci si vuole connettere. La rete virtuale non richiede una subnet del gateway e non possono essere presenti gateway di rete virtuale. Se non è disponibile una rete virtuale, crearne una seguendo la procedura descritta in questo articolo.
* Ottenere un intervallo di indirizzi IP per l'area dell'hub. L'hub è una rete virtuale e l'intervallo di indirizzi specificati per l'area dell'hub non può sovrapporsi ad alcuna delle reti virtuali esistenti a cui ci si connette. Inoltre non può sovrapporsi agli intervalli di indirizzi a cui ci si connette in locale. Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli.
* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="enroll"></a>1. Registrarsi all'anteprima

Prima di poter configurare la rete WAN virtuale, è necessario registrare la sottoscrizione nell'anteprima. In caso contrario, non sarà possibile utilizzare la rete WAN virtuale nel portale. Per eseguire la registrazione, inviare un messaggio di posta elettronica all'indirizzo **azurevirtualwan@microsoft.com** con l'ID della sottoscrizione. Si riceverà un messaggio di posta elettronica dopo la registrazione della sottoscrizione.

## <a name="vnet"></a>2. Creare una rete virtuale

Se non è già disponibile una rete virtuale, è possibile crearne rapidamente una tramite PowerShell. È anche possibile creare una rete virtuale tramite il portale di Azure.

* Assicurarsi di verificare che lo spazio degli indirizzi della rete virtuale creata non si sovrapponga a uno qualsiasi degli intervalli di indirizzi per le altre reti virtuali a cui ci si vuole connettere o allo spazio degli indirizzi della rete locale. 
* Se è già disponibile una rete virtuale, verificare che soddisfi i criteri richiesti e che non abbia un gateway di rete virtuale.

È possibile creare con facilità la rete virtuale facendo clic su "Prova" in questo articolo per aprire una console di PowerShell. Personalizzare i valori, quindi copiare e incollare i comandi nella finestra della console.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Modificare i comandi di PowerShell e quindi creare un gruppo di risorse.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Creare una rete virtuale

Modificare i comandi di PowerShell per creare una rete virtuale compatibile per l'ambiente specifico.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```

## <a name="wan"></a>3. Creare una rete WAN virtuale

1. In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.
2. A questo punto, è possibile trovare la rete WAN passando a **Tutti i servizi** e cercando "rete WAN virtuale". In alternativa, è possibile cercare "rete WAN virtuale" nella casella di ricerca nella parte superiore del portale di Azure. Fare clic su **Rete WAN virtuale** per aprire la pagina.
3. Fare clic su **Crea** per aprire la pagina **Crea rete WAN**. Se la pagina non è disponibile, significa che non è ancora stata ottenuta l'approvazione per questa anteprima.

  ![Crea rete WAN](./media/virtual-wan-site-to-site-portal/createwan.png)
4. Nella pagina Crea rete WAN compilare i campi seguenti.

  * **Nome** - Selezionare il nome che si vuole usare per la rete WAN.
  * **Sottoscrizione** - Selezionare la sottoscrizione che si vuole usare.
  * **Gruppo di risorse** - Creare un nuovo gruppo o usarne uno esistente.
  * **Posizione risorse** - Scegliere una posizione per le risorse nell'elenco a discesa. Una rete WAN è una risorsa globale e non si trova in un'area specifica. Tuttavia, è necessario selezionare un'area per poter gestire e individuare più facilmente la risorsa WAN creata.
5. Fare clic su **Crea** per creare la rete WAN.

## <a name="site"></a>4. Creare un sito

Creare il numero di siti necessari, corrispondenti alle località fisiche. Ad esempio, se esiste una succursale a New York, una a Londra e una a Milano, creare tre siti separati. Questi siti contengono gli endpoint di dispositivo VPN locali. A questo punto, è possibile specificare un solo spazio di indirizzi privato per il sito.

1. Passare a **Tutte le risorse**.
2. Fare clic sulla rete WAN virtuale creata.
3. Fare clic su **+ Crea sito** nella parte superiore della pagina per aprire la pagina **Crea sito**.

  ![nuovo sito](media/virtual-wan-site-to-site-portal/createsite.png)
4. Nella pagina **Crea sito** compilare i campi seguenti:

  *  **Nome** - Questo è il nome che si vuole usare per fare riferimento al sito locale.
  *  **Indirizzo IP pubblico** - Questo è l'indirizzo IP pubblico del dispositivo VPN che risiede nel sito locale.
  *  **Spazio indirizzi privato** - Spazio di indirizzi IP nel sito locale. Il traffico destinato a questo spazio di indirizzi viene indirizzato al sito locale.
  *  **Sottoscrizione** - Verificare la sottoscrizione.
  *  **Gruppo di risorse** - Selezionare il gruppo di risorse che si vuole usare.
5. Fare clic su **Mostra impostazioni avanzate** per visualizzare le impostazioni aggiuntive. È possibile **abilitare BGP** (campo facoltativo che consente di abilitare questa funzionalità su tutte le connessioni create per questo sito in Azure). È anche possibile immettere **informazioni sul dispositivo** (campo facoltativo). Queste informazioni possono consentire al team di Azure di comprendere meglio l'ambiente per aggiungere ulteriori possibilità di ottimizzazione in futuro o per offrire supporto per la risoluzione dei problemi.

  ![BGP](media/virtual-wan-site-to-site-portal/sitebgp.png)
6. Fare clic su **Conferma** per creare il sito.
7. Ripetere questi passaggi per ogni sito che si vuole creare.

## <a name="hub"></a>5. Creare un hub e connettere i siti

1. Nella pagina della rete WAN virtuale fare clic su **Siti**.
2. In **Siti non associati** viene visualizzato un elenco di siti che non sono ancora stati connessi a un hub.
3. Selezionare i siti che si vuole associare.
4. Nell'elenco a discesa selezionare l'area a cui verrà associato all'hub. È consigliabile associare l'hub all'area in cui risiedono le reti virtuali a cui ci si vuole connettere.
5. Fare clic su **Conferma**. Se non esiste ancora un hub in quest'area, verrà creato automaticamente una rete virtuale dell'hub virtuale. In questo caso viene visualizzata la pagina **Crea hub a livello di area**.
6. Nella pagina **Crea hub a livello di area** immettere l'intervallo di indirizzi della rete virtuale dell'hub. Si tratta della rete virtuale che conterrà i servizi dell'hub. L'intervallo immesso in questa posizione deve essere un intervallo di indirizzi IP privati e non può sovrapporsi ad alcuno spazio di indirizzi locale o spazio di indirizzi di rete virtuale. Verrà creato un endpoint VPN nella rete virtuale dell'hub. (La creazione automatica di hub e gateway è disponibile solo nel portale.)
7. Fare clic su **Crea**.

## <a name="vnet"></a>6. Connettere la rete virtuale a un hub

In questo passaggio si crea la connessione di peering tra l'hub e una rete virtuale. Ripetere questi passaggi per ogni rete virtuale a cui ci si vuole connettere.

1. Nella pagina della rete WAN virtuale fare clic su **Connessione rete virtuale**.
2. Nella pagina di connessione alla rete virtuale fare clic su **+ Aggiungi connessione**.
3. Nella pagina **Aggiungi connessione** compilare i campi seguenti:

    * **Nome connessione** - Specificare un nome per la connessione.
    * **Hub** - Selezionare l'hub che si vuole associare a questa connessione.
    * **Sottoscrizione** - Verificare la sottoscrizione.
    * **Rete virtuale** - Selezionare la rete virtuale che si vuole connettere all'hub. La rete virtuale non può avere un gateway di rete virtuale già esistente.

## <a name="device"></a>7. Scaricare la configurazione della VPN

Usare la configurazione del dispositivo VPN per configurare il dispositivo VPN locale.

1. Nella pagina della rete WAN virtuale fare clic su **Panoramica**.
2. Nella parte superiore della pagina Panoramica fare clic su **Scarica configurazione VPN**. Azure crea un account di archiviazione nel gruppo di risorse 'microsoft-network-[posizione]', dove posizione è la posizione della rete WAN. Dopo aver applicato la configurazione ai dispositivi VPN, è possibile eliminare questo account di archiviazione.
3. Al termine della creazione del file è possibile fare clic sul collegamento per scaricarlo.
4. Applicare la configurazione al dispositivo VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Informazioni sui file di configurazione del dispositivo VPN

Il file di configurazione del dispositivo contiene le impostazioni da usare quando si configura il dispositivo VPN locale. Quando si visualizza questo file, notare le informazioni seguenti:

* **vpnSiteConfiguration -** Questa sezione indica i dettagli del dispositivo configurato come un sito che si connette alla rete WAN virtuale. Include il nome e l'indirizzo IP pubblico del dispositivo della succursale.
* **vpnSiteConnections -** Questa sezione include informazioni su quanto segue:

    * **Spazio degli indirizzi** della rete virtuale degli hub virtuali<br>Esempio:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Spazio degli indirizzi** delle reti virtuali connesse all'hub<br>Esempio:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Indirizzi IP** del gateway VPN dell'hub virtuale. Dato che ogni connessione del gateway VPN comprende 2 tunnel nella configurazione attiva-attiva, nel file saranno elencati entrambi gli indirizzi IP. In questo esempio, sono indicati "Instance0" e "Instance1" per ogni sito.<br>Esempio:

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
> Se si utilizza la soluzione di un partner per la rete WAN virtuale, la configurazione del dispositivo VPN avviene automaticamente quando il controller del dispositivo ottiene il file di configurazione da Azure e lo applica al dispositivo per configurare la connessione ad Azure. Ciò significa che non è necessario sapere come configurare manualmente il dispositivo VPN.
>

Se sono necessarie istruzioni per configurare il dispositivo, è possibile usare le istruzioni riportate nella [pagina degli script di configurazione del dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) con le avvertenze seguenti:

* Le istruzioni nella pagina dei dispositivi VPN non vengono scritte per la rete WAN virtuale, ma è possibile usare i valori della rete WAN virtuale dal file di configurazione per configurare manualmente il dispositivo VPN. 
* Gli script di configurazione del dispositivo scaricabili per Gateway VPN non funzionano per la rete WAN virtuale, perché la configurazione è diversa.
* La rete WAN virtuale può usare solo IKEv2 e non IKEv1.
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

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="feedback"></a>Commenti e suggerimenti per l'anteprima

I commenti e i suggerimenti degli utenti sono molto apprezzati. Inviare un messaggio di posta elettronica all'indirizzo <azurevirtualwan@microsoft.com> per segnalare eventuali problemi o per fornire commenti e suggerimenti (positivi o negativi) sulla rete WAN virtuale. Includere il nome della propria società tra parentesi quadre "[ ]" nella riga dell'oggetto. Se si sta segnalando un problema, includere anche l'ID sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come:

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
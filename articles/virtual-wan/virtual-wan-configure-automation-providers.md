---
title: Partner di rete WAN virtuale di Azure | Microsoft Docs
description: Questo articolo illustra ai partner come configurare l'automazione della rete WAN virtuale di Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: cherylmc
ms.openlocfilehash: 2f847d8db983303d46b465f4f80bff65eeff632f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168500"
---
# <a name="virtual-wan-partners"></a>Partner di rete WAN virtuale

Questo articolo descrive come configurare l'ambiente di automazione per connettere e configurare un dispositivo di branch (un dispositivo VPN locale di un cliente o un CPE SDWAN) per la rete WAN virtuale di Azure. L'articolo è rivolto ai provider che forniscono dispositivi di branch in grado di supportare la connettività VPN tramite IPsec/IKEv2 o IPsec/IKEv1.

Un dispositivo di branch (un dispositivo VPN locale di un cliente o un CPE SDWAN) usa in genere un dashboard di controller/dispositivo per il provisioning. Gli amministratori delle soluzioni SD-WAN spesso possono usare una console di gestione per effettuare il pre-provisioning di un dispositivo prima che venga collegato alla rete. Questo dispositivo abilitato per VPN ottiene la logica del piano di controllo da un controller. Il dispositivo VPN o il controller SD-WAN possono usare le API di Azure per automatizzare la connettività alla rete WAN virtuale di Azure. Questo tipo di connessione richiede che il dispositivo locale disponga di un indirizzo IP pubblico per l'esterno.

## <a name ="before"></a>Prima di iniziare il processo di automazione

* Verificare che il dispositivo supporti IPsec IKEv1 o IKEv2. Vedere i [criteri predefiniti](#default).
* Visualizzare le [API REST](#additional) usate per automatizzare la connettività alla rete WAN virtuale di Azure.
* Testare l'esperienza nel portale della rete WAN virtuale di Azure.
* Decidere quindi quale parte della procedura della connettività si vuole automatizzare. È consigliabile automatizzare almeno i seguenti passaggi:

  * Controllo di accesso
  * Caricamento delle informazioni sul dispositivo di branch nella rete WAN virtuale di Azure
  * Download della configurazione di Azure e configurazione della connettività dal dispositivo di branch alla rete WAN virtuale di Azure

### <a name ="additional"></a>Informazioni aggiuntive

* [API REST](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs) per automatizzare la creazione dell'hub virtuale
* [API REST](https://docs.microsoft.com/rest/api/virtualwan/vpngateways) per automatizzare il gateway VPN di Azure per la rete WAN virtuale
* [API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections) per connettere una VPNSite a un hub VPN di Azure
* [Criteri IPsec predefiniti](#default)

## <a name ="ae"></a>Esperienza del cliente

Comprendere l'esperienza utente prevista in combinazione con la rete WAN virtuale di Azure.

  1. Un utente di rete WAN virtuale avvia in genere il processo creando una risorsa di rete WAN virtuale.
  2. L'utente configura un accesso al gruppo di risorse basate su entità servizio per il sistema locale (il controller di branch o il software di provisioning del dispositivo VPN) per scrivere informazioni sul branch nella rete WAN virtuale di Azure.
  3. L'utente può decidere in questa fase di accedere all'interfaccia utente e impostare le credenziali dell'entità servizio. Al termine il controller è in grado di caricare le informazioni sul branch con la procedura automatizza che verrà specificata. L'equivalente manuale di questa procedura sul lato Azure è "Crea sito".
  4. Una volta che le informazioni sul sito (dispositivo Branch) sono disponibili in Azure, l'utente collegherà il sito a un hub. Un hub virtuale è una rete virtuale gestita da Microsoft. L'hub contiene vari endpoint di servizio per abilitare la connettività dalla rete locale (vpnsite). L'hub è l'elemento centrale della rete in un'area. Può esistere un solo hub per ogni area di Azure e il relativo endpoint VPN (vpngateway) viene creato durante questo processo. Il gateway VPN è un gateway scalabile che si adatta in base alla larghezza di banda e ai requisiti di connessione. È possibile scegliere di automatizzare la creazione dell'hub virtuale e del gateway VPN dal dashboard del controller del dispositivo di branch.
  5. Dopo che l'hub virtuale è stato associato al sito, viene generato un file di configurazione che l'utente deve scaricare manualmente. È qui che entra in gioco l'automazione e rende l'esperienza utente facile e veloce. Invece di chiedere all'utente di scaricare e configurare manualmente il dispositivo di branch, è possibile impostare l'automazione e offrire un'esperienza basata su pochi clic nell'interfaccia utente. Si riducono così i problemi di connettività tipici, ad esempio la mancata corrispondenza delle chiavi condivise e dei parametri IPSec, la leggibilità del file di configurazione e così via.
  6. Alla fine di questo passaggio nella soluzione, l'utente si ritroverà una connessione da sito a sito tra il dispositivo di branch e l'hub virtuale. È possibile anche impostare connessioni aggiuntive in altri hub. Ogni connessione è un tunnel attivo-attivo. Il cliente può scegliere di usare un ISP diverso per ognuno dei collegamenti per il tunnel.
  7. Valutare la possibilità di fornire funzionalità di monitoraggio e risoluzione dei problemi nell'interfaccia di gestione CPE. Gli scenari tipici includono "il cliente non è in grado di accedere alle risorse di Azure a causa di un problema CPE", "Mostra i parametri IPsec sul lato CPE" e così via.

## <a name ="understand"></a>Dettagli automazione

###  <a name="access"></a>Controllo degli accessi

I clienti devono essere in grado di impostare il controllo degli accessi appropriato per la rete WAN virtuale nell'interfaccia utente del dispositivo. Questa operazione è consigliabile usando un'entità servizio di Azure. L'accesso basato su entità servizio fornisce al dispositivo un'autenticazione adeguata al controller per caricare le informazioni sul branch. Per altre informazioni, vedere [Create service principal](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) (Creare un'entità servizio). Anche se questa funzionalità non è inclusa nell'offerta della rete WAN virtuale di Azure, di seguito vengono elencati i passaggi tipici eseguiti per configurare l'accesso in Azure dopo il quale vengono inseriti i dettagli rilevanti nel dashboard di gestione del dispositivo.

* Creare un'applicazione di Azure Active Directory per il controller del dispositivo locale.
* Ottenere l'ID applicazione e la chiave di autenticazione
* Ottenere l'ID tenant
* Assegnare l'applicazione al ruolo "Collaboratore"

###  <a name="branch"></a>Caricare le informazioni sul dispositivo di branch

È consigliabile progettare l'esperienza utente per caricare informazioni sul ramo (sito locale) in Azure. È possibile usare le [API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) per VPNSite per creare le informazioni sul sito nella rete WAN virtuale. È possibile specificare tutti i dispositivi VPN/SDWAN di branch o selezionare le personalizzazioni di dispositivo di conseguenza.

### <a name="device"></a>Download della configurazione e connessione del dispositivo

Questo passaggio prevede il download della configurazione di Azure e la configurazione della connettività dal dispositivo di branch alla rete WAN virtuale di Azure. In questo passaggio, un cliente che non usa un provider scarica manualmente la configurazione di Azure e la applica al dispositivo SDWAN/VPN in locale. In qualità di provider, è consigliabile automatizzare questo passaggio. Per ulteriori informazioni, visualizzare le [API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) di download. Il controller del dispositivo può chiamare l'API REST ' GetVpnConfiguration ' per scaricare la configurazione di Azure.

**Note di configurazione**

  * Se le reti virtuali di Azure sono collegate all'hub virtuale, verranno visualizzate come ConnectedSubnets.
  * La connettività VPN usa la configurazione basata su Route e supporta sia i protocolli IKEv1 che IKEv2.

## <a name="devicefile"></a>File di configurazione del dispositivo

Il file di configurazione del dispositivo contiene le impostazioni da usare quando si configura il dispositivo VPN locale. Quando si visualizza questo file, notare le informazioni seguenti:

* **vpnSiteConfiguration -** Questa sezione indica i dettagli del dispositivo configurato come un sito che si connette alla rete WAN virtuale. Include il nome e l'indirizzo IP pubblico del dispositivo della succursale.
* **vpnSiteConnections:** in questa sezione vengono fornite informazioni su quanto segue:

    * **Spazio degli indirizzi** della rete virtuale degli hub.<br>Esempio:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Spazio degli indirizzi** delle reti virtuali che sono collegate all'hub.<br>Esempio:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Indirizzi IP** del gateway VPN dell'hub virtuale. Dato che ogni connessione del gateway VPN comprende 2 tunnel nella configurazione attiva-attiva, nel file saranno elencati entrambi gli indirizzi IP. In questo esempio, sono indicati "Instance0" e "Instance1" per ogni sito.<br>Esempio:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Dettagli di configurazione della connessione al gateway VPN**, ad esempio BGP, chiave precondivisa e così via. PSK è la chiave precondivisa generata automaticamente. È sempre possibile modificare la connessione nella pagina Panoramica per una chiave precondivisa personalizzata.
  
**File di configurazione del dispositivo di esempio**

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

## <a name="default"></a>Dettagli connettività

La configurazione del dispositivo VPN/SDWAN o SD-WAN locale deve contenere o corrispondere agli algoritmi e ai parametri seguenti specificati nei criteri IPsec/IKE di Azure.

* Algoritmo di crittografia IKE
* Algoritmo di integrità IKE
* Gruppo DH
* Algoritmo di crittografia IPsec
* Algoritmo di integrità IPsec
* Gruppo PFS

### <a name="default"></a>Criteri predefiniti per la connettività IPsec

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom"></a>Criteri personalizzati per la connettività IPsec

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere [Informazioni sulla rete WAN virtuale di Azure](virtual-wan-about.md) e [Domande frequenti sulla rete WAN virtuale di Azure](virtual-wan-faq.md).

Per informazioni aggiuntive, inviare un messaggio di posta elettronica a <azurevirtualwan@microsoft.com>. Includere il nome della propria società tra parentesi quadre "[ ]" nella riga dell'oggetto.
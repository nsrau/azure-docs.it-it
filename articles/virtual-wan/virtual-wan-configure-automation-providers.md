---
title: Configurare l'automazione della rete WAN virtuale di Azure - per i partner della rete WAN virtuale | Microsoft Docs
description: Questo articolo illustra ai partner per le soluzioni di connettività software-defined come configurare l'automazione della rete WAN virtuale di Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: bac728f286c90550107b27da76a070623577ed82
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918901"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Configurare l'automazione della rete WAN virtuale - per i partner della rete WAN virtuale(anteprima)

Questo articolo aiuta a comprendere come configurare l'ambiente di automazione per connettersi e configurare un dispositivo del branch (un dispositivo VPN locale di un cliente o SDWAN) per le rete WAN virtuale di Azure. Se l'utente è un provider che fornisce i dispositivi del branch in grado di supportare la connettività VPN tramite IPsec/IKEv2, questo è l'articolo giusto.

Le soluzioni di connettività software-defined usano in genere un controller o un centro di provisioning dei dispositivi per gestire i propri dispositivi del branch. Il controller può usare le API di Azure per automatizzare la connettività alla rete WAN virtuale di Azure. Questo tipo di connessione richiede un dispositivo VPN o SDWAN che si trova in locale con un indirizzo IP pubblico esterno ad esso assegnato.

##  <a name="access"></a>Controllo degli accessi

I clienti devono essere in grado di impostare il controllo degli accessi appropriato per la rete WAN virtuale nell'interfaccia utente del dispositivo. Questa operazione è consigliabile usando un'entità servizio di Azure. L'accesso basato su entità servizio fornisce al dispositivo un'autenticazione adeguata al controller per caricare le informazioni sul branch. Per altre informazioni vedere [Creare un'entità servizio](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).

##  <a name="site"></a>Caricare le informazioni sul branch

Progettare l'esperienza utente per caricare le informazioni (da sito locale) sul branch in Azure. [Le API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) per **VPNSite** possono essere usate per creare le informazioni sul sito nella rete WAN virtuale. È possibile specificare tutti i dispositivi VPN/SDWAN di branch o selezionare le personalizzazioni di dispositivo di conseguenza.

##  <a name="hub"></a>Hub e servizi

Dopo il caricamento del dispositivo branch in Azure, il cliente in genere seleziona l'area dell'hub e/o i servizi nel portale di Azure, che richiama/richiamano una serie di operazioni per creare la rete virtuale hub e l'endpoint VPN all'interno dell'hub. Il gateway VPN è un gateway scalabile che si adatta in base alla larghezza di banda e ai requisiti di connessione.

## <a name="device"></a>Configurazione del dispositivo

In questo passaggio, un cliente che non usa un provider scarica manualmente la configurazione di Azure e la applica al dispositivo SDWAN/VPN in locale. In qualità di provider, è consigliabile automatizzare questo passaggio. Il controller può chiamare l'API REST **GetVpnConfiguration** per scaricare la configurazione di Azure, che in genere avrà un aspetto simile al file seguente.

**Note di configurazione**

  * Se le reti virtuali di Azure sono collegate all'hub virtuale, verranno visualizzate come ConnectedSubnets.
  * La connettività VPN sfrutta una configurazione basata su route e IKEv2.

### <a name="understanding-the-device-configuration-file"></a>Informazioni sulla configurazione predefinita

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
  
### <a name="example-device-configuration-file"></a>File di configurazione del dispositivo di esempio

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

## <a name="default"></a>Criteri predefiniti

### <a name="initiator"></a>Iniziatore

Le sezioni seguenti elencano le combinazioni supportate dei criteri quando Azure è l'iniziatore del tunnel.

**Fase 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Fase 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE


### <a name="responder"></a>Risponditore

Le sezioni seguenti elencano le combinazioni supportate dei criteri quando Azure è il risponditore per il tunnel.

**Fase 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Fase 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* CBC_DES, SHA_1, PFS_NONE 
* AES_256, SHA_1, PFS_1
* AES_256, SHA_1, PFS_2
* AES_256, SHA_1, PFS_14
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* CBC_3DES, SHA_1, PFS_1
* CBC_3DES, SHA_1, PFS_2
* CBC_3DES, SHA_256, PFS_2
* AES_256, SHA_256, PFS_1
* AES_256, SHA_256, PFS_2
* AES_256, SHA_256, PFS_14
* AES_256, SHA_1, PFS_24
* AES_256, SHA_256, PFS_24
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* CBC_3DES, SHA_1, PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>È necessaria la corrispondenza di tutti gli elementi tra il vpngateway dell'hub virtuale e le configurazioni dei dispositivi VPN/SDWAN o SD-WAN locali?

La configurazione del dispositivo VPN/SDWAN o SD-WAN locale deve contenere o corrispondere agli algoritmi e ai parametri seguenti specificati nei criteri IPsec/IKE di Azure. La durata delle associazioni di sicurezza è una specifica locale. Non è necessaria la corrispondenza.

* Algoritmo di crittografia IKE
* Algoritmo di integrità IKE
* Gruppo DH
* Algoritmo di crittografia IPsec
* Algoritmo di integrità IPsec
* Gruppo PFS

## <a name="feedback"></a>Commenti e suggerimenti per l'anteprima

I commenti e i suggerimenti degli utenti sono molto apprezzati. Inviare un messaggio di posta elettronica all'indirizzo <azurevirtualwan@microsoft.com> per segnalare eventuali problemi o per fornire commenti e suggerimenti (positivi o negativi) sulla rete WAN virtuale. Includere il nome della propria società tra parentesi quadre "[ ]" nella riga dell'oggetto. Se si sta segnalando un problema, includere anche l'ID sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere [Informazioni sulla rete WAN virtuale di Azure](virtual-wan-about.md) e [Domande frequenti sulla rete WAN virtuale di Azure](virtual-wan-faq.md).

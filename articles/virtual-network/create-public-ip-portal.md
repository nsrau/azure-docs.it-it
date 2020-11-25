---
title: Creare un IP pubblico-portale di Azure
description: Informazioni su come creare un indirizzo IP pubblico nella portale di Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: add763b713b93604e089d7aec586876fecd2887c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95895639"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Guida introduttiva: creare un indirizzo IP pubblico usando il portale di Azure

Questo articolo illustra come creare una risorsa indirizzo IP pubblico usando il portale di Azure. Per altre informazioni sulle risorse a cui questo può essere associato, sulla differenza tra lo SKU Basic e standard e altre informazioni correlate, vedere [indirizzi IP pubblici](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  Per questo esempio, si concentrerà solo sugli indirizzi IPv4; per altre informazioni sugli indirizzi IPv6, vedere [IPv6 per Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

# <a name="standard-sku---using-zones"></a>[**SKU standard-uso di zone**](#tab/option-create-public-ip-standard-zones)

Usare la procedura seguente per creare un indirizzo IP pubblico con ridondanza della zona standard denominato **myStandardZRPublicIP**.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa**. 
3. Nella casella di ricerca digitare *indirizzo IP pubblico*.
4. Nei risultati della ricerca selezionare **Indirizzo IP pubblico**. Quindi, nella pagina **Indirizzo IP pubblico** selezionare **Crea**.
5. Nella pagina **Crea indirizzo IP pubblico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | valore                       |
    | ---                     | ---                         |
    | Versione indirizzo IP              | Selezionare IPv4                 |    
    | SKU                     | selezionare **Standard**         |
    | Nome                    | Immettere *myStandardZRPublicIP*          |
    | Assegnazione indirizzi IP   | Si noti che questa operazione verrà bloccata come "static"                                        |
    | Timeout di inattività (minuti)  | Lasciare il valore 4        |
    | Etichetta del nome DNS          | Lasciare vuoto il valore    |
    | Subscription            | Selezionare la propria sottoscrizione.   |
    | Resource group          | Selezionare **Crea nuovo** , immettere myResourceGroup, quindi fare clic su **OK** . |
    | Location                | Seleziona **Stati Uniti orientali 2**      |
    | Zona di disponibilità       | Selezionare la zona con **ridondanza della zona** o seleziona zona specifica (vedere la nota sotto) |

Si noti che queste sono solo selezioni valide nelle aree con [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).  È anche possibile selezionare una zona specifica in queste aree, anche se non sarà resiliente agli errori di zona.

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-create-public-ip-basic)

Usare la procedura seguente per creare un indirizzo IP pubblico statico di base denominato **myBasicPublicIP**.  Gli indirizzi IP pubblici di base non hanno il concetto di zone di disponibilità.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa**. 
3. Nella casella di ricerca digitare *indirizzo IP pubblico*.
4. Nei risultati della ricerca selezionare **Indirizzo IP pubblico**. Quindi, nella pagina **Indirizzo IP pubblico** selezionare **Crea**.
5. Nella pagina **Crea indirizzo IP pubblico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | valore                       |
    | ---                     | ---                         |
    | Versione indirizzo IP              | Selezionare IPv4                 |    
    | SKU                     | selezionare **Standard**         |
    | Nome                    | Immettere *myBasicPublicIP*          |
    | Assegnazione indirizzi IP   | Scegliere **statico** (vedere la nota sotto)                                     |
    | Timeout di inattività (minuti)  | Lasciare il valore 4        |
    | Etichetta del nome DNS          | Lasciare vuoto il valore    |
    | Subscription            | Selezionare la propria sottoscrizione.   |
    | Resource group          | Selezionare **Crea nuovo** , immettere myResourceGroup, quindi fare clic su **OK** . |
    | Location                | Seleziona **Stati Uniti orientali 2**      |

Se è accettabile che l'indirizzo IP cambi nel tempo, è possibile selezionare Assegnazione IP **dinamica** .

---

## <a name="additional-information"></a>Informazioni aggiuntive 

Per altri dettagli sui singoli campi elencati sopra, vedere [gestire gli indirizzi IP pubblici](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Passaggi successivi
- Associare un [indirizzo IP pubblico a una macchina virtuale](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).
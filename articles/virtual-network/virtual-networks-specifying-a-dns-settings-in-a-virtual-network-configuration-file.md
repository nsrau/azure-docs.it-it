---
title: Indicazione delle impostazioni DNS in un file di configurazione di rete virtuale | Documentazione Microsoft
description: Come modificare le impostazioni del server DNS in una rete virtuale usando un file di configurazione di rete virtuale nel modello di distribuzione classica
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: 36f7ed9b02b66718327c1a05a6cf29eedf39e7a5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248386"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Indicazione delle impostazioni DNS in un file di configurazione di rete virtuale
Un file di configurazione di rete dispone di due elementi che è possibile usare per specificare le impostazioni Domain Name System (DNS): **DnsServers** e **DnsServerRef**. È possibile aggiungere un elenco dei server DNS specificando gli indirizzi IP e nomi di riferimento all’elemento **DnsServers** . È quindi possibile utilizzare un elemento **DnsServerRef** per specificare le voci del server DNS che vengono utilizzate per siti di rete diversi all'interno della rete virtuale.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

In questo articolo viene illustrato il modello di distribuzione classica.

Il file di configurazione di rete può contenere i seguenti elementi. Il titolo di ogni elemento è collegato a una pagina che fornisce informazioni aggiuntive sulle impostazioni del valore dell'elemento.

> [!IMPORTANT]
> Per altre informazioni sull'utilizzo del file di configurazione di rete, vedere [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md). Per informazioni sulle impostazioni specifiche contenute in un file di configurazione di rete, vedere [Schema di configurazione di rete virtuale Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Elemento DNS](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> L'attributo **nome** dell'elemento **DnsServer** viene usato solo come riferimento per l'elemento **DnsServerRef**. Non rappresenta il nome host per il server DNS. Ogni valore dell’attributo **DnsServer** deve essere univoco nell'intera sottoscrizione Microsoft Azure
> 
> 

[Elemento siti di rete virtuale](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Per specificare questa impostazione per l'elemento siti di rete virtuale, esso deve essere definito in precedenza nell'elemento DNS. Il *nome*del DnsServerRef dell'elemento siti di rete virtuale deve fare riferimento a un valore nome specificato nell'elemento DNS per *nome* DnsServer.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sullo [Schema di configurazione delle reti virtuali di Azure](https://go.microsoft.com/fwlink/?LinkId=248093).
* Informazioni sullo [Schema di configurazione dei servizi di Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).


---
title: Indicazione delle impostazioni DNS in un file cscfg | Documentazione Microsoft
description: Specificare le impostazioni DNS personalizzate utilizzando il file di configurazione del servizio per la rete virtuale
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 009206f1e0ba848538ed2c666032a63051d062e4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790747"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Specifica le impostazioni DNS in un File di configurazione del servizio
## <a name="dns-elements"></a>Elemento DNS
Un file di configurazione del servizio può contenere un elemento DnsServers con un elenco di indirizzi IPv4 per i server Domain Name System (DNS) che verrà utilizzato dal servizio. Le impostazioni nel file di configurazione del servizio hanno la precedenza sulle impostazioni nel file di configurazione di rete. Per altre informazioni, vedere [Schema di configurazione dei servizi di Azure (con estensione .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> L'attributo **name** nell'elemento **DnsServer** viene usato solo come nome di riferimento. Non rappresenta il nome host per il server DNS. Ogni valore dell’attributo **DnsServer** deve essere univoco nell'intera sottoscrizione Microsoft Azure
> 
> 

## <a name="see-also"></a>Vedere anche
[Schema di configurazione dei servizi di Azure (file .cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Attività di configurazione di Rete virtuale di Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurare una rete virtuale usando un file di configurazione di rete](http://go.microsoft.com/fwlink/?LinkId=248094)

[Informazioni sulle impostazioni della rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?LinkId=248092)


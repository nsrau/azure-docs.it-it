---
title: Elenco di porte e URL da aggiungere all&quot;elenco elementi consentiti per Azure RemoteApp distribuito nella rete virtuale del cliente | Documentazione Microsoft
description: Informazioni sulle porte e gli URL da configurare per la comunicazione con RemoteApp di Azure.
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: 5a001ff7-14c9-47fa-9b39-78fd5a5f0250
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 114ed651f914060818f10efac849e7f85a3da981
ms.openlocfilehash: f41396e4aa0c7b6cc2f1768e0c2ba3b95ee31cd3
ms.lasthandoff: 02/08/2017


---
# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Elenco di porte e URL per consentire l'accesso a RemoteApp di Azure distribuito sulla rete virtuale cliente
> [!IMPORTANT]
> Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Se si distribuisce una raccolta ibrida o un cloud di Azure RemoteApp in una rete virtuale, esaminare le informazioni sulle porte seguenti. Per altre informazioni sulle reti virtuali, vedere la [panoramica sulle reti virtuali](../virtual-network/virtual-networks-overview.md). Se è stato creato un gruppo di sicurezza di rete (NSG) che limita il traffico alle risorse di rete virtuale nella raccolta, verificare che le porte seguenti siano accessibili e consentite tramite i criteri di sicurezza della rete virtuale. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete (NSG)](../virtual-network/virtual-networks-nsg.md).

## <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>La subnet di RemoteApp di Azure necessita dell'accesso ai seguenti endpoint e URL:
* *.servicebus.windows.net
* *. servicebus.net
* https://*.remoteapp.windowsazure.com  
* https://www.remoteapp.windowsazure.com 
* https://*remoteapp.windowsazure.com  
* https://*.core.windows.net  
* In uscita:TCP: TCP: 443, 9351, 9352, 10101-10175 
* Facoltativo: UDP: 10201-10275  

## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>I client RemoteApp di Azure devono accedere ai seguenti endpoint e URL:
Per client si intende desktop, dispositivi e così via, usati per connettersi alle app distribuite nella raccolta RemoteApp di Azure.

* https://telemetry.remoteapp.windowsazure.com  
* https://*.remoteapp.windowsazure.com (le porte UDP facoltative riguardano questo indirizzo) 
* https://login.windows.net  
* https://login.microsoftonline.com  
* https://www.remoteapp.windowsazure.com 
* https://*.core.windows.net  
* In uscita: TCP: 443  
* Facoltativo: UDP: 3391 



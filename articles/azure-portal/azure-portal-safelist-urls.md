---
title: Elenchi indirizzi attendibili gli URL del portale di Azure nel firewall o nel server proxy
description: Aggiungere questi URL al bypass del server proxy per comunicare con il portale di Azure e i relativi serviziAdd these URLs to proxy server bypass to communicate with the Azure portal and its services
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900652"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Elenchi indirizzi attendibili gli URL del portale di Azure nel firewall o nel server proxy

È possibile configurare i dispositivi di sicurezza locali per ignorare le restrizioni di sicurezza per gli URL del portale di Azure.You can configure on-premises security devices to bypass security restrictions for the Azure portal URLs. Questa configurazione può migliorare le prestazioni e la connettività tra la rete locale o WAN e il cloud di Azure.This configuration can improve performance and connectivity between your local- or wide-area network and the Azure cloud.

Gli amministratori di rete spesso distribuiscono server proxy, firewall o altri dispositivi.Network administrators often deploy proxy servers, firewalls, or other devices. Questi dispositivi aiutano a proteggere e dare il controllo su come gli utenti accedono a Internet. Le regole progettate per proteggere gli utenti possono talvolta bloccare o rallentare il traffico Internet legittimo correlato all'azienda. Questo traffico include le comunicazioni tra l'utente e Azure.This traffic includes communications between you and Azure. Per ottimizzare la connettività tra la rete e il portale di Azure e i relativi servizi, è consigliabile aggiungere gli URL del portale di Azure all'elenco indirizzi attendibili.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL del portale di Azure per il bypass proxyAzure portal URLs for proxy bypass

Gli endpoint URL da elencare attendibili per il portale di Azure sono specifici del cloud di Azure in cui viene distribuita l'organizzazione. Per consentire al traffico di rete verso questi endpoint di ignorare le restrizioni, selezionare il cloud. Aggiungere quindi l'elenco di URL al server proxy o al firewall.

#### <a name="public-cloud"></a>[Cloud pubblico](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Cloud del governo degli Stati Uniti](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Cina Governo Cloud](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Il traffico verso questi endpoint usa porte TCP standard per HTTP (80) e HTTPS (443).
>
>
## <a name="next-steps"></a>Passaggi successivi

Hai bisogno di indirizzi IP attendibili? Scaricare l'elenco degli intervalli IP del data center di Microsoft Azure per il cloud:

* [In tutto il mondo](https://www.microsoft.com/download/details.aspx?id=56519)
* [Governo degli Stati Uniti](https://www.microsoft.com/download/details.aspx?id=57063)
* [Germania](https://www.microsoft.com/download/details.aspx?id=57064)
* [Cina](https://www.microsoft.com/download/details.aspx?id=57062)

Altri servizi Microsoft usano URL e indirizzi IP aggiuntivi per la connettività. Per ottimizzare la connettività di rete per i servizi di Microsoft 365, vedere [Configurare la rete per Office 365.](/office365/enterprise/set-up-network-for-office-365)

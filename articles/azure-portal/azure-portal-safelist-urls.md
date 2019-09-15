---
title: Elenchi di sicurezza di portale di Azure URL | Microsoft Docs
description: Aggiungere questi URL al bypass del server proxy per comunicare con il portale di Azure e i relativi servizi
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 08/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 5b279a86df2024828044c32b7d188fa1d9545271
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995081"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Attendibilità degli URL portale di Azure nel firewall o nel server proxy

Per prestazioni ottimali e connettività tra la rete locale o l'area estesa e il cloud di Azure, configurare i dispositivi di sicurezza locali per ignorare le restrizioni di sicurezza per gli URL portale di Azure. Gli amministratori di rete spesso distribuiscono server proxy, firewall o altri dispositivi per proteggere e controllare il modo in cui gli utenti accedono a Internet. Tuttavia, le regole progettate per proteggere gli utenti possono a volte bloccare o rallentare il traffico Internet basato sull'azienda, incluse le comunicazioni tra l'utente e Azure. Per ottimizzare la connettività tra la rete e il portale di Azure e i relativi servizi, è consigliabile aggiungere URL portale di Azure all'elenchi di sicurezza.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL di portale di Azure per il bypass del proxy

Gli endpoint URL per la portale di Azure di sicurezza sono specifici del cloud di Azure in cui viene distribuita l'organizzazione. Selezionare il cloud, quindi aggiungere l'elenco di URL al server proxy o al firewall per consentire al traffico di rete verso questi endpoint di ignorare le restrizioni.

#### <a name="public-cloudtabpublic-cloud"></a>[Cloud pubblico](#tab/public-cloud)
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

#### <a name="us-government-cloudtabus-government-cloud"></a>[DEGLI STATI UNITI Cloud per enti pubblici](#tab/us-government-cloud)
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

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Cloud per enti pubblici Cina](#tab/china-government-cloud)
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
> Il traffico verso questi endpoint usa le porte TCP standard per HTTP (80) e HTTPS (443).
>
>
## <a name="next-steps"></a>Passaggi successivi

È necessario disporre di indirizzi IP attendibili? Scaricare l'elenco di Microsoft Azure intervalli IP del Data Center per il cloud:

* [Tutto il mondo](https://www.microsoft.com/download/details.aspx?id=56519)
* [DEGLI STATI UNITI Governo](http://www.microsoft.com/download/details.aspx?id=57063)
* [Germania](http://www.microsoft.com/download/details.aspx?id=57064)
* [Cina](http://www.microsoft.com/download/details.aspx?id=57062)

Altri servizi Microsoft usano URL e indirizzi IP aggiuntivi per la connettività. Per ottimizzare la connettività di rete per Microsoft 365 Services, vedere [configurare la rete per Office 365](/office365/enterprise/set-up-network-for-office-365).

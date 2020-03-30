---
title: Tunneling forzato di Firewall di AzureAzure Firewall forced tunneling
description: È possibile configurare il tunneling forzato per instradare il traffico associato a Internet a un firewall aggiuntivo o a un'appliance virtuale di rete aggiuntiva per un'ulteriore elaborazione.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597278"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Tunneling forzato di Firewall di Azure (anteprima)Azure Firewall forced tunneling (preview)

È possibile configurare Firewall di Azure per instradare tutto il traffico associato a Internet a un hop successivo designato anziché passare direttamente a Internet.You can configure Azure Firewall to route all Internet-bound traffic to a designated next hop instead of going directly to the Internet. Ad esempio, è possibile disporre di un firewall perimetrale locale o di un'altra appliance virtuale di rete (NVA) per elaborare il traffico di rete prima che venga passato a Internet.For example, you may have an on-premises edge firewall or other network virtual appliance (NVA) to process network traffic before it's passed to the Internet.

> [!IMPORTANT]
> Il tunneling forzato di Firewall di Azure è attualmente in anteprima pubblica.
>
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per impostazione predefinita, il tunneling forzato non è consentito in Firewall di Azure per garantire che vengano soddisfatte tutte le dipendenze di Azure in uscita. Le configurazioni della route definita dall'utente in *AzureFirewallSubnet* che hanno una route predefinita che non va direttamente a Internet sono disabilitate.

## <a name="forced-tunneling-configuration"></a>Configurazione del tunneling forzato

Per supportare il tunneling forzato, il traffico di gestione dei servizi è separato dal traffico dei clienti. È necessaria un'altra subnet dedicata denominata *AzureFirewallManagementSubnet* (dimensione minima della subnet /26) con il proprio indirizzo IP pubblico associato. L'unica route consentita in questa subnet è una route predefinita a Internet e la propagazione delle route BGP deve essere disabilitata.

Se si dispone di una route predefinita annunciata tramite BGP per forzare il traffico in locale, è necessario creare *AzureFirewallSubnet* e *AzureFirewallManagementSubnet* prima di distribuire il firewall e disporre di un UDR con una route predefinita a Internet e **la propagazione** della route del gateway di rete virtuale disabilitata.

All'interno di questa configurazione, AzureFirewallSubnet può ora includere route a qualsiasi firewall locale o NVA per elaborare il traffico prima che venga passato a Internet.Within this configuration, the *AzureFirewallSubnet* can now include routes to any on-premise firewall or NVA to process traffic before it's passed to the Internet. È anche possibile pubblicare queste route tramite BGP in *AzureFirewallSubnet* se la **propagazione** delle route del gateway di rete virtuale è abilitata in questa subnet.

Ad esempio, è possibile creare una route predefinita in *AzureFirewallSubnet* con il gateway VPN come hop successivo per ottenere il dispositivo locale. In alternativa, è possibile abilitare la **propagazione** delle route del gateway di rete virtuale per ottenere le route appropriate alla rete locale.

![Propagazione della route del gateway di rete virtualeVirtual network gateway route propagation](media/forced-tunneling/route-propagation.png)

Dopo aver configurato Firewall di Azure per supportare il tunneling forzato, non è possibile annullare la configurazione. Se si rimuovono tutte le altre configurazioni IP nel firewall, viene rimossa anche la configurazione IP di gestione e il firewall viene deallocato. L'indirizzo IP pubblico assegnato alla configurazione IP di gestione non può essere rimosso, ma è possibile assegnare un indirizzo IP pubblico diverso.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Distribuire e configurare Firewall di Azure in una rete ibrida tramite il portale di AzureTutorial: Deploy and configure Azure Firewall in a hybrid network using the Azure portal](tutorial-hybrid-portal.md)
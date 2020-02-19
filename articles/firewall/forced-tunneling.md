---
title: Tunneling forzato del firewall di Azure
description: È possibile configurare il tunneling forzato per instradare il traffico associato a Internet a un firewall aggiuntivo o a un'appliance virtuale di rete per un'ulteriore elaborazione.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 4093f91e55272a32ce7df4a78e2ee8b3ebed5fde
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444472"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Tunneling forzato del firewall di Azure (anteprima)

È possibile configurare il firewall di Azure per instradare tutto il traffico associato a Internet a un hop successivo designato anziché passare direttamente a Internet. Ad esempio, è possibile che si disponga di un firewall perimetrale locale o di un'altra appliance virtuale di rete per elaborare il traffico di rete prima che venga passato a Internet.

> [!IMPORTANT]
> Il tunneling forzato del firewall di Azure è attualmente disponibile in anteprima pubblica.
>
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per impostazione predefinita, il tunneling forzato non è consentito nel firewall di Azure per garantire che vengano soddisfatte tutte le dipendenze di Azure in uscita. Le configurazioni di route definite dall'utente (UDR) nella *AzureFirewallSubnet* che hanno una route predefinita che non passano direttamente a Internet sono disabilitate.

## <a name="forced-tunneling-configuration"></a>Configurazione del tunneling forzato

Per supportare il tunneling forzato, il traffico di gestione dei servizi è separato dal traffico del cliente. È necessaria una subnet dedicata aggiuntiva denominata *AzureFirewallManagementSubnet* con il proprio indirizzo IP pubblico associato. L'unica route consentita in questa subnet è una route predefinita a Internet e la propagazione della route BGP deve essere disabilitata.

Se si dispone di una route predefinita annunciata tramite BGP per forzare il traffico in locale, è necessario creare *AzureFirewallSubnet* e *AzureFirewallManagementSubnet* prima di distribuire il firewall e avere un UdR con una route predefinita a Internet e la propagazione della route del gateway di rete virtuale è disabilitata.

All'interno di questa configurazione, *AzureFirewallSubnet* può ora includere route a qualsiasi firewall o appliance virtuale locale per elaborare il traffico prima che venga passato a Internet. È anche possibile pubblicare queste route tramite BGP in *AzureFirewallSubnet* se la propagazione di route del gateway di rete virtuale è abilitata in questa subnet.

Dopo aver configurato il firewall di Azure per supportare il tunneling forzato, non è possibile annullare la configurazione. Se si rimuovono tutte le altre configurazioni IP nel firewall, viene rimossa anche la configurazione IP di gestione e il firewall viene deallocato. Non è possibile rimuovere l'indirizzo IP pubblico assegnato alla configurazione IP di gestione, ma è possibile assegnare un indirizzo IP pubblico diverso.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: distribuire e configurare il firewall di Azure in una rete ibrida usando il portale di Azure](tutorial-hybrid-portal.md)
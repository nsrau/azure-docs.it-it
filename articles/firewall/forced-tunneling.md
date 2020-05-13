---
title: Tunneling forzato del firewall di Azure
description: È possibile configurare il tunneling forzato per instradare il traffico associato a Internet a un firewall aggiuntivo o a un'appliance virtuale di rete per un'ulteriore elaborazione.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/11/2020
ms.author: victorh
ms.openlocfilehash: 463bccb29d59f06e7381d7d7123946029223a93a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199725"
---
# <a name="azure-firewall-forced-tunneling"></a>Tunneling forzato del firewall di Azure

È possibile configurare il firewall di Azure per instradare tutto il traffico associato a Internet a un hop successivo designato anziché passare direttamente a Internet. Ad esempio, è possibile che si disponga di un firewall perimetrale locale o di un'altra appliance virtuale di rete per elaborare il traffico di rete prima che venga passato a Internet.

Per impostazione predefinita, il tunneling forzato non è consentito nel firewall di Azure per garantire che vengano soddisfatte tutte le dipendenze di Azure in uscita. Le configurazioni di route definite dall'utente (UDR) nella *AzureFirewallSubnet* che hanno una route predefinita che non passano direttamente a Internet sono disabilitate.

## <a name="forced-tunneling-configuration"></a>Configurazione del tunneling forzato

Per supportare il tunneling forzato, il traffico di gestione dei servizi è separato dal traffico del cliente. È necessaria una subnet dedicata aggiuntiva denominata *AzureFirewallManagementSubnet* (dimensione minima della subnet/26) con il proprio indirizzo IP pubblico associato. L'unica route consentita in questa subnet è una route predefinita a Internet e la propagazione della route BGP deve essere disabilitata.

Se si dispone di una route predefinita annunciata tramite BGP per forzare il traffico in locale, è necessario creare *AzureFirewallSubnet* e *AzureFirewallManagementSubnet* prima di distribuire il firewall e avere un UdR con una route predefinita a Internet e la **propagazione della route del gateway di rete virtuale** è disabilitata.

All'interno di questa configurazione, *AzureFirewallSubnet* può ora includere route a qualsiasi firewall o appliance virtuale locale per elaborare il traffico prima che venga passato a Internet. È anche possibile pubblicare queste route tramite BGP in *AzureFirewallSubnet* se la **propagazione di route del gateway di rete virtuale** è abilitata in questa subnet.

Ad esempio, è possibile creare una route predefinita in *AzureFirewallSubnet* con il gateway VPN come hop successivo per ottenere il dispositivo locale. In alternativa, è possibile abilitare la **propagazione delle route del gateway di rete virtuale** per ottenere le route appropriate per la rete locale.

![Propagazione della route del gateway di rete virtuale](media/forced-tunneling/route-propagation.png)

Dopo aver configurato il firewall di Azure per supportare il tunneling forzato, non è possibile annullare la configurazione. Se si rimuovono tutte le altre configurazioni IP nel firewall, viene rimossa anche la configurazione IP di gestione e il firewall viene deallocato. Non è possibile rimuovere l'indirizzo IP pubblico assegnato alla configurazione IP di gestione, ma è possibile assegnare un indirizzo IP pubblico diverso.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Distribuire e configurare Firewall di Azure in una rete ibrida con il portale di Azure](tutorial-hybrid-portal.md)

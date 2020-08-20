---
title: Tunneling forzato di Firewall di Azure
description: È possibile configurare il tunneling forzato per instradare il traffico associato a Internet a un firewall aggiuntivo o a un'appliance virtuale di rete per ulteriori operazioni di elaborazione.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612597"
---
# <a name="azure-firewall-forced-tunneling"></a>Tunneling forzato di Firewall di Azure

Quando si configura una nuova istanza di Firewall di Azure, è possibile instradare tutto il traffico associato a Internet a un hop successivo designato anziché passare direttamente a Internet. Ad esempio, può essere presente un firewall perimetrale locale o un'altra appliance virtuale di rete per l'elaborazione del traffico di rete prima del passaggio a Internet. Non è tuttavia possibile configurare un firewall esistente per il tunneling forzato.

Per impostazione predefinita, il tunneling forzato non è consentito in Firewall di Azure per garantire che vengano soddisfatte tutte le relative dipendenze di Azure in uscita. In *AzureFirewallSubnet* sono disabilitate le configurazioni di route definite dall'utente che hanno una route predefinita che non passa direttamente a Internet.

## <a name="forced-tunneling-configuration"></a>Configurazione del tunneling forzato

Per supportare il tunneling forzato, il traffico di gestione dei servizi è separato dal traffico dei clienti. È necessaria una subnet aggiuntiva dedicata denominata *AzureFirewallManagementSubnet* (dimensione minima della subnet /26) con il relativo indirizzo IP pubblico associato. L'unica route consentita in questa subnet è una route predefinita per Internet e la propagazione della route BGP deve essere disabilitata.

Se si dispone di una route predefinita annunciata tramite BGP per forzare il traffico in locale, è necessario creare *AzureFirewallSubnet* e *AzureFirewallManagementSubnet* prima di distribuire il firewall e avere un UdR con una route predefinita a Internet e **propagare le route del gateway** disabilitate.

All'interno di questa configurazione, *AzureFirewallSubnet* può ora includere route a qualsiasi firewall locale o appliance virtuale di rete per elaborare il traffico prima che venga passato a Internet. È anche possibile pubblicare queste route tramite BGP in *AzureFirewallSubnet* se la **propagazione delle route del gateway** è abilitata in questa subnet.

Ad esempio, è possibile creare una route predefinita in *AzureFirewallSubnet* con il gateway VPN come hop successivo per ottenere il dispositivo locale. In alternativa, è possibile abilitare **propagazione delle route del gateway** per ottenere le route appropriate per la rete locale.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Propagazione della route del gateway di rete virtuale":::

Se si Abilita il tunneling forzato, il traffico associato a Internet viene inviato tramite SNAT a uno degli indirizzi IP privati del firewall in AzureFirewallSubnet, nascondendo l'origine dal firewall locale.

Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure invierà il traffico tramite SNAT a uno degli indirizzi IP privati firewall in AzureFirewallSubnet. Tuttavia, è possibile configurare il firewall di Azure in modo che **non** SNAT l'intervallo di indirizzi IP pubblici. Per altre informazioni, vedere [Intervalli di indirizzi IP privati SNAT di Firewall di Azure](snat-private-range.md).

Dopo aver configurato Firewall di Azure per supportare il tunneling forzato, non è possibile annullare la configurazione. Se si rimuovono tutte le altre configurazioni IP nel firewall, viene rimossa anche la configurazione IP di gestione e il firewall viene deallocato. Non è possibile rimuovere l'indirizzo IP pubblico assegnato alla configurazione IP di gestione, ma è possibile assegnare un indirizzo IP pubblico diverso.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Distribuire e configurare Firewall di Azure in una rete ibrida con il portale di Azure](tutorial-hybrid-portal.md)

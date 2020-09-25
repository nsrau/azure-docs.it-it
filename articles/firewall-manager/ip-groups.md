---
title: Gruppi IP nei criteri del firewall di Azure
description: I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole dei criteri del firewall di Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 5b3b3fb5e5440fea888654027f4fbf1a68b34141
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91252426"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>Gruppi IP nei criteri del firewall di Azure

I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per i criteri del firewall di Azure nei modi seguenti:

- Come tipo di origine nelle regole DNAT
- Come tipo di origine o di destinazione nelle regole di rete
- Come tipo di origine nelle regole dell'applicazione


Un gruppo IP può avere un solo indirizzo IP, più indirizzi IP o uno o più intervalli di indirizzi IP.

I gruppi IP possono essere riutilizzati nelle regole di DNAT, rete e applicazione del firewall di Azure per più firewall tra aree e sottoscrizioni in Azure. I nomi dei gruppi devono essere univoci. È possibile configurare un gruppo IP nel portale di Azure, nell'interfaccia della riga di comando di Azure o nell'API REST. Per iniziare, è disponibile un modello di esempio.

## <a name="sample-format"></a>Formato del campione

Gli esempi di formato di indirizzo IPv4 seguenti sono validi per l'utilizzo nei gruppi IP:

- Indirizzo singolo: 10.0.0.0
- Notazione CIDR: 10.1.0.0/32
- Intervallo di indirizzi: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Creare un gruppo IP

È possibile creare un gruppo IP usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'API REST. Per altre informazioni, vedere [creare un gruppo IP](../firewall/create-ip-group.md).

## <a name="browse-ip-groups"></a>Sfoglia gruppi IP
1. Nella barra di ricerca portale di Azure digitare **gruppi IP** e selezionarlo. È possibile visualizzare l'elenco dei gruppi di indirizzi IP oppure selezionare **Aggiungi** per creare un nuovo gruppo IP.
2. Selezionare un gruppo IP per aprire la pagina panoramica. È possibile modificare, aggiungere o eliminare indirizzi IP o gruppi IP.

   ![Panoramica sui gruppi IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Gestire un gruppo IP

È possibile visualizzare tutti gli indirizzi IP nel gruppo IP e le regole o le risorse a esso associate. Per eliminare un gruppo IP, è innanzitutto necessario annullare l'associazione del gruppo IP dalla risorsa che la utilizza.

1. Per visualizzare o modificare gli indirizzi IP, selezionare **indirizzi IP** in **Impostazioni** nel riquadro sinistro.
2. Per aggiungere uno o più indirizzi IP, selezionare **Aggiungi indirizzi IP**. Verrà visualizzata la pagina **trascina o Sfoglia** per un caricamento oppure è possibile immettere manualmente l'indirizzo.
3.    Selezione dei puntini di sospensione (**...**) a destra per modificare o eliminare gli indirizzi IP. Per modificare o eliminare più indirizzi IP, selezionare le caselle e selezionare **modifica** o **Elimina** nella parte superiore.
4. Infine, può esportare il file nel formato di file CSV.

> [!NOTE]
> Se si eliminano tutti gli indirizzi IP in un gruppo IP mentre è ancora in uso in una regola, la regola viene ignorata.


## <a name="use-an-ip-group"></a>Usare un gruppo IP

È ora possibile selezionare **gruppo IP** come tipo di **origine** o **destinazione** per gli indirizzi IP quando si crea un criterio con regole di DNAT, applicazione o rete.

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="Gruppi IP nel firewall":::

## <a name="ip-address-limits"></a>Limiti degli indirizzi IP

È possibile avere un massimo di 100 gruppi IP per Firewall con un massimo di 5000 singoli indirizzi IP o prefissi IP per ogni gruppo IP.

## <a name="related-azure-powershell-cmdlets"></a>Cmdlet di Azure PowerShell correlati

Per creare e gestire i gruppi IP, è possibile usare i cmdlet di Azure PowerShell seguenti:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallPolicyNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallpolicynetworkrule?view=azps-3.4.0)
- [New-AzFirewallPolicyApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallpolicyapplicationrule?view=azps-3.4.0)
- [New-AzFirewallPolicyNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallpolicynatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Proteggere la rete WAN virtuale con Gestione firewall di Azure](secure-cloud-network.md)
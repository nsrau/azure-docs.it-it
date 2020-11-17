---
title: Gruppi IP nel firewall di Azure
description: I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole del firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: c56298d68bddd326ed2b32215f30a9057fa51e85
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653114"
---
# <a name="ip-groups-in-azure-firewall"></a>Gruppi IP nel firewall di Azure

I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole del firewall di Azure nei modi seguenti:

- Come indirizzo di origine nelle regole DNAT
- Come indirizzo di origine o di destinazione nelle regole di rete
- Come indirizzo di origine nelle regole dell'applicazione


Un gruppo IP può avere un solo indirizzo IP, più indirizzi IP o uno o più intervalli di indirizzi IP.

I gruppi IP possono essere riutilizzati nelle regole di DNAT, rete e applicazione del firewall di Azure per più firewall tra aree e sottoscrizioni in Azure. I nomi dei gruppi devono essere univoci. È possibile configurare un gruppo IP nel portale di Azure, nell'interfaccia della riga di comando di Azure o nell'API REST. Per iniziare, è disponibile un modello di esempio.

## <a name="sample-format"></a>Formato del campione

Gli esempi di formato di indirizzo IPv4 seguenti sono validi per l'utilizzo nei gruppi IP:

- Indirizzo singolo: 10.0.0.0
- Notazione CIDR: 10.1.0.0/32
- Intervallo di indirizzi: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Creare un gruppo IP

È possibile creare un gruppo IP usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'API REST. Per altre informazioni, vedere [creare un gruppo IP](create-ip-group.md).

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

È ora possibile selezionare **gruppo IP** come tipo di **origine** o **destinazione** per gli indirizzi IP quando si creano le regole di DNAT, applicazione o rete del firewall di Azure.

![Gruppi IP nel firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Aree di disponibilità

I gruppi IP sono disponibili in tutte le aree del cloud pubblico.

## <a name="ip-address-limits"></a>Limiti degli indirizzi IP

È possibile avere un massimo di 100 gruppi IP per Firewall con un massimo di 5000 singoli indirizzi IP o prefissi IP per ogni gruppo IP.

## <a name="related-azure-powershell-cmdlets"></a>Cmdlet di Azure PowerShell correlati

Per creare e gestire i gruppi IP, è possibile usare i cmdlet di Azure PowerShell seguenti:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare un firewall di Azure](tutorial-firewall-deploy-portal.md).
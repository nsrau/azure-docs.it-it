---
title: Gruppi IP nel firewall di Azure
description: I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole del firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444485"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Gruppi IP (anteprima) nel firewall di Azure

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

È possibile creare un gruppo IP usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'API REST. Per altre informazioni, vedere [creare un gruppo IP (anteprima)](create-ip-group.md).

## <a name="browse-ip-groups"></a>Sfoglia gruppi IP
1. Nella barra di ricerca portale di Azure digitare **gruppi IP** e selezionarlo. È possibile visualizzare l'elenco dei gruppi di indirizzi IP oppure selezionare **Aggiungi** per creare un nuovo gruppo IP.
2. Selezionare un gruppo IP per aprire la pagina panoramica. È possibile modificare, aggiungere o eliminare indirizzi IP o gruppi IP.

   ![Panoramica sui gruppi IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Gestire un gruppo IP

È possibile visualizzare tutti gli indirizzi IP nel gruppo IP e le regole o le risorse a esso associate. Per eliminare un gruppo IP, è innanzitutto necessario annullare l'associazione del gruppo IP dalla risorsa che la utilizza.

1. Per visualizzare o modificare gli indirizzi IP, selezionare **indirizzi IP** in **Impostazioni** nel riquadro sinistro.
2. Per aggiungere uno o più indirizzi IP, selezionare **Aggiungi indirizzi IP**. Verrà visualizzata la pagina **trascina o Sfoglia** per un caricamento oppure è possibile immettere manualmente l'indirizzo.
3.  Selezione dei puntini di sospensione ( **...** ) a destra per modificare o eliminare gli indirizzi IP. Per modificare o eliminare più indirizzi IP, selezionare le caselle e selezionare **modifica** o **Elimina** nella parte superiore.
4. Infine, può esportare il file nel formato di file CSV.

> [!NOTE]
> Se si eliminano tutti gli indirizzi IP in un gruppo IP mentre è ancora in uso in una regola, la regola viene ignorata.


## <a name="use-an-ip-group"></a>Usare un gruppo IP

È ora possibile selezionare **gruppo IP** come tipo di **origine** o **destinazione** per gli indirizzi IP quando si creano le regole di DNAT, applicazione o rete del firewall di Azure.

> [!NOTE]
> I gruppi IP non sono supportati nei criteri firewall. Attualmente è supportata solo con le tradizionali regole del firewall.

![Gruppi IP nel firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Aree di disponibilità

I gruppi IP sono attualmente disponibili nelle aree seguenti:

- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centrali
- Stati Uniti centro-settentrionali
- Stati Uniti centro-occidentali
- Stati Uniti centro-meridionali
- Canada centrale
- Europa settentrionale
- Europa occidentale
- Francia centrale
- Regno Unito meridionale
- Australia orientale
- Australia centrale
- Australia sud-orientale

## <a name="related-azure-powershell-cmdlets"></a>Cmdlet di Azure PowerShell correlati

Per creare e gestire i gruppi IP, è possibile usare i cmdlet di Azure PowerShell seguenti:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare Firewall di Azure](tutorial-firewall-deploy-portal.md).
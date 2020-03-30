---
title: IP Groups in Azure Firewall
description: I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole di Firewall di Azure.IP groups allow you to group and manage IP addresses for Azure Firewall rules.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444485"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>IP Groups (preview) in Azure Firewall

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I gruppi IP consentono di raggruppare e gestire gli indirizzi IP per le regole di Firewall di Azure nei modi seguenti:IP Groups allow you to group and manage IP addresses for Azure Firewall rules in the following ways:

- Come indirizzo di origine nelle regole DNAT
- Come indirizzo di origine o di destinazione nelle regole di rete
- Come indirizzo di origine nelle regole dell'applicazioneAs a source address in application rules


Un gruppo IP può avere un singolo indirizzo IP, più indirizzi IP o uno o più intervalli di indirizzi IP.

IP Groups can be reused in Azure Firewall DNAT, network, and application rules for multiple firewalls across regions and subscriptions in Azure. I nomi dei gruppi devono essere univoci. È possibile configurare un gruppo IP nel portale di Azure, nell'interfaccia della riga di comando di Azure o nell'API REST. Viene fornito un modello di esempio per iniziare.

## <a name="sample-format"></a>Formato del campione

I seguenti esempi di formato di indirizzo IPv4 sono validi per l'utilizzo nei gruppi IP:

- Indirizzo singolo: 10.0.0.0
- Notazione CIDR: 10.1.0.0/32
- Intervallo di indirizzi: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Creazione di un gruppo IP

È possibile creare un gruppo IP usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'API REST. Per ulteriori informazioni, consultate [Creare un gruppo IP (anteprima).](create-ip-group.md)

## <a name="browse-ip-groups"></a>Sfoglia gruppi IP
1. Nella barra di ricerca del portale di Azure digitare **Gruppi IP** e selezionarla. È possibile visualizzare l'elenco dei gruppi IP oppure selezionare **Aggiungi** per creare un nuovo gruppo IP.
2. Selezionare un gruppo IP per aprire la pagina di panoramica. È possibile modificare, aggiungere o eliminare indirizzi IP o gruppi IP.

   ![Panoramica dei gruppi IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Gestire un gruppo IP

È possibile visualizzare tutti gli indirizzi IP nel gruppo IP e le regole o le risorse ad esso associate. Per eliminare un gruppo IP, è innanzitutto necessario dissociare il gruppo IP dalla risorsa che lo utilizza.

1. Per visualizzare o modificare gli indirizzi IP, selezionare **Indirizzi IP** in **Impostazioni** nel riquadro sinistro.
2. Per aggiungere uno o più indirizzi IP, selezionare **Aggiungi indirizzi IP**. Verrà visualizzata la pagina **Trascina o Sfoglia** per un caricamento oppure è possibile immettere l'indirizzo manualmente.
3.  Selezionando i puntini di sospensione (**...**) a destra per modificare o eliminare gli indirizzi IP. Per modificare o eliminare più indirizzi IP, selezionare le caselle e selezionare **Modifica** o **Elimina** nella parte superiore.
4. Infine, è possibile esportare il file nel formato di file CSV.

> [!NOTE]
> Se si eliminano tutti gli indirizzi IP in un gruppo IP mentre è ancora in uso in una regola, tale regola viene ignorata.


## <a name="use-an-ip-group"></a>Utilizzare un gruppo IP

È ora possibile selezionare Gruppo IP come tipo di origine o Tipo di destinazione per gli indirizzi IP quando si creano regole di rete, un'applicazione o un percorso di rete di Firewall di Azure.You can now select **IP Group** as a **Source type** or Destination **type** for the IP address(es) when you create Azure Firewall DNAT, application, or network rules.

> [!NOTE]
> I gruppi IP non sono supportati in Firewall Policy. Attualmente è supportato solo con le regole firewall tradizionali.

![Gruppi IP nel firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Aree di disponibilità

I gruppi IP sono attualmente disponibili nelle seguenti aree geografiche:

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

I cmdlet di Azure PowerShell seguenti possono essere usati per creare e gestire gruppi IP:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Rimuovi-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [Nuovo-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare Firewall di Azure](tutorial-firewall-deploy-portal.md).
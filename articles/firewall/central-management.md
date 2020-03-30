---
title: Gestione centralizzata di Firewall di AzureAzure Firewall central management
description: Informazioni sulla gestione centralizzata di Gestione firewall di AzureLearn about Azure Firewall Manager central management
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444550"
---
# <a name="azure-firewall-central-management"></a>Gestione centralizzata di Firewall di AzureAzure Firewall central management

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Se si gestiscono più firewall, si sa che la modifica continua delle regole del firewall rende difficile mantenerli sincronizzati. I team IT centrali hanno bisogno di un modo per definire i criteri firewall di base e applicarli a più business unit. Allo stesso tempo, i team DevOps desiderano creare i propri criteri firewall derivati locale per una migliore agilità.

Azure Firewall Manager Preview consente di risolvere questi problemi.


## <a name="azure-firewall-manager-preview"></a>Anteprima di Gestione firewall di AzureAzure Firewall Manager Preview

Azure Firewall Manager Preview è un servizio di gestione della sicurezza di rete che fornisce criteri di sicurezza e gestione delle route centralizzati per i perimetri di sicurezza basati su cloud. Consente ai team IT aziendali di definire in modo centralizzato le regole a livello di rete e di applicazione per il filtraggio del traffico tra più istanze di Firewall di Azure.It makes it easy for Enterprise IT teams to centrally define centrally network and application level rules for traffic filtering across multiple Azure Firewall instances. È possibile estendere diverse aree e sottoscrizioni di Azure nelle architetture hub e spoke per la governance e la protezione del traffico. Fornisce inoltre A DevOps una migliore agilità con i criteri di sicurezza del firewall locale derivati implementati tra le organizzazioni.

### <a name="firewall-policy"></a>Criteri firewall

Un criterio firewall è una risorsa di Azure che contiene raccolte di regole NAT, di rete e delle applicazioni e le impostazioni di Threat Intelligence.A Firewall policy is an Azure resource that contains NAT, network, and application rule collections and Threat Intelligence settings. Si tratta di una risorsa globale che può essere usata in più istanze di Firewall di Azure in *Hub virtuali protetti* e reti virtuali *Hub.* I nuovi criteri possono essere creati da zero o essere ereditati da criteri esistenti. L'ereditarietà consente di creare in DevOps criteri firewall locali in aggiunta ai criteri di base imposti dall'organizzazione. I criteri funzionano in diverse aree e sottoscrizioni.
 
È possibile creare criteri firewall e associazioni con Gestione firewall di Azure.You can create Firewall Policy and associations with Azure Firewall Manager. Tuttavia, è anche possibile creare e gestire criteri usando API REST, modelli, Azure PowerShell e CLI. Dopo aver creato un criterio, è possibile associarlo a un firewall in un hub WAN virtuale *rendendolo* un hub virtuale protetto e/o un firewall in una rete virtuale che lo rende *rete virtuale hub*.

### <a name="pricing"></a>Prezzi

I criteri vengono fatturati in base alle associazioni di firewall. I criteri con una o zero associazioni di firewall sono gratuiti. I criteri con più associazioni di firewall vengono fatturati a tariffa fissa. Per altre informazioni, vedere [Prezzi di Gestione firewall di Azure](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Partner di gestione del firewall di AzureAzure Firewall Management partners

Le soluzioni di terze parti leader seguenti supportano la gestione centralizzata di Firewall di Azure usando le API REST di Azure standard. Ognuna di queste soluzioni ha le sue caratteristiche e caratteristiche uniche:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Guardiano della Sicurezza Del Cloud Barracuda](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Firewall Manager Preview, vedere [Che cos'è Azure Firewall Manager Preview.](../firewall-manager/overview.md)
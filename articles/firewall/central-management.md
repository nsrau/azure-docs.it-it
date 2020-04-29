---
title: Gestione centrale del firewall di Azure
description: Informazioni sulla gestione centrale di gestione firewall di Azure
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444550"
---
# <a name="azure-firewall-central-management"></a>Gestione centrale del firewall di Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Se si gestiscono più firewall, è noto che le regole del firewall che cambiano continuamente rendono difficile mantenerle sincronizzate. I team IT centrali necessitano di un modo per definire i criteri del firewall di base e applicarli tra più business unit. Allo stesso tempo, i team DevOps desiderano creare criteri firewall derivati locali per una maggiore agilità.

Azure Firewall Manager Preview può aiutare a risolvere questi problemi.


## <a name="azure-firewall-manager-preview"></a>Anteprima di Azure Firewall Manager

Azure Firewall Manager Preview è un servizio di gestione della sicurezza di rete che fornisce criteri di sicurezza centralizzati e gestione delle route per i perimetri di sicurezza basati sul cloud. Consente ai team IT aziendali di definire in modo centralizzato le regole a livello di rete e di applicazione per il filtraggio del traffico tra più istanze del firewall di Azure. È possibile estendere diverse aree e sottoscrizioni di Azure nelle architetture Hub e spoke per la governance e la protezione del traffico. Offre anche una maggiore agilità DevOps con i criteri di sicurezza del firewall locali derivati implementati tra organizzazioni.

### <a name="firewall-policy"></a>Criteri firewall

Un criterio del firewall è una risorsa di Azure che contiene le raccolte di regole NAT, di rete e di applicazione e le impostazioni di intelligence per le minacce. Si tratta di una risorsa globale che può essere usata in più istanze del firewall di Azure in *Hub virtuali protetti* e *reti virtuali Hub*. I nuovi criteri possono essere creati da zero o essere ereditati da criteri esistenti. L'ereditarietà consente di creare in DevOps criteri firewall locali in aggiunta ai criteri di base imposti dall'organizzazione. I criteri funzionano in diverse aree e sottoscrizioni.
 
È possibile creare criteri e associazioni del firewall con gestione firewall di Azure. Tuttavia, è anche possibile creare e gestire un criterio usando l'API REST, i modelli, Azure PowerShell e l'interfaccia della riga di comando. Dopo aver creato un criterio, è possibile associarlo a un firewall in un hub WAN virtuale che lo rende un *hub virtuale protetto* e/o un firewall in una rete virtuale che rende la *rete virtuale Hub*it.

### <a name="pricing"></a>Prezzi

I criteri vengono fatturati in base alle associazioni di firewall. I criteri con una o zero associazioni di firewall sono gratuiti. I criteri con più associazioni di firewall vengono fatturati a tariffa fissa. Per altre informazioni, vedere [Prezzi di Gestione firewall di Azure](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Partner di gestione del firewall di Azure

Le seguenti soluzioni di terze parti principali supportano la gestione centrale del firewall di Azure con le API REST di Azure standard. Ognuna di queste soluzioni ha caratteristiche e caratteristiche specifiche:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [TUFIN Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'anteprima di Azure Firewall Manager, vedere [che cos'è l'anteprima di Azure Firewall Manager?](../firewall-manager/overview.md)
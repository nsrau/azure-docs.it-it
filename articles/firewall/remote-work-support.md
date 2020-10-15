---
title: Supporto per lavoro remoto del firewall di Azure
description: Questo articolo illustra come il firewall di Azure può supportare i requisiti di forza lavoro remoto.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 68789d3b8a4be51a381e95d6e6f840331b46b4e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400112"
---
# <a name="azure-firewall-remote-work-support"></a>Supporto per lavoro remoto del firewall di Azure

Il firewall di Azure è un servizio di sicurezza di rete gestito e basato sul cloud che protegge le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>Supporto per la distribuzione di Virtual Desktop Infrastructure (VDI)

Il lavoro dei criteri domestici richiede che molte organizzazioni IT affrontino le modifiche fondamentali della capacità, della rete, della sicurezza e della governance. I dipendenti non sono protetti dai criteri di sicurezza a più livelli associati ai servizi locali mentre si lavora da casa. Le distribuzioni di Virtual Desktop Infrastructure (VDI) in Azure possono aiutare le organizzazioni a rispondere rapidamente a questo ambiente mutevole. Tuttavia, è necessario un modo per proteggere l'accesso a Internet in ingresso/in uscita da e verso queste distribuzioni VDI. È possibile usare [le regole DNAT](rule-processing.md) del firewall di Azure insieme alle funzionalità di filtro basate su Intelligence per le [minacce](threat-intel.md) per proteggere le distribuzioni VDI.

## <a name="azure-windows-virtual-desktop-support"></a>Supporto per desktop virtuali Windows di Azure

Desktop virtuale di Windows è un servizio di virtualizzazione desktop e app completo in esecuzione in Azure. Si tratta dell'unica infrastruttura VDI (Virtual Desktop Infrastructure) che offre funzionalità di gestione semplificata, Windows 10 multisessione, ottimizzazioni per Microsoft 365 app per le aziende e supporto per gli ambienti Servizi Desktop remoto (RDS). Puoi distribuire e ridimensionare le tue app e i tuoi desktop Windows in Azure in pochi minuti e ottenere funzionalità predefinite di sicurezza e conformità. Per desktop virtuale Windows non è necessario aprire alcun accesso in ingresso alla rete virtuale. È tuttavia necessario consentire un set di connessioni di rete in uscita per le macchine virtuali di desktop virtuali Windows in esecuzione nella rete virtuale. Per altre informazioni, vedere [Usare Firewall di Azure per proteggere le distribuzioni di Desktop virtuale Windows](protect-windows-virtual-desktop.md).

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su [desktop virtuale di Windows](https://docs.microsoft.com/azure/virtual-desktop/).
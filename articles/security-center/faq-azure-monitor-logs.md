---
title: 'Domande frequenti sul Centro sicurezza di Azure: domande sugli MMO esistentiAzure Security Center FAQ - questions about existing MMO'
description: Queste domande frequenti rispondono alle domande per i clienti che già utilizzano Microsoft Monitoring Agent e considerano Azure Security Center, un prodotto che consente di prevenire, rilevare e rispondere alle minacce.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 5c433140c3982813e372fd3f63243a96197d220c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661890"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Domande frequenti per i clienti che usano già i log di Monitoraggio di AzureFAQ for customers already using Azure Monitor logs<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Il Centro sicurezza esegue l'override di eventuali connessioni esistenti tra le macchine virtuali e le aree di lavoro?

Se in una VM è già installato Microsoft Monitoring Agent come estensione di Azure, il Centro sicurezza non esegue l'override della connessione all'area di lavoro esistente. Il Centro sicurezza usa l'area di lavoro esistente. La macchina virtuale sarà protetta a condizione che la soluzione "Security" o "SecurityCenterFree" sia stata installata nell'area di lavoro a cui sta segnalando. 

Una soluzione Centro sicurezza viene installata nell'area di lavoro selezionata nella schermata Raccolta dati, se non è già presente, e la soluzione viene applicata solo alle macchine virtuali pertinenti. Quando viene aggiunta, la soluzione viene automaticamente distribuita per impostazione predefinita a tutti gli agenti di Windows e Linux connessi all'area di lavoro Log Analytics. Il [targeting della soluzione](../operations-management-suite/operations-management-suite-solution-targeting.md) consente di applicare un ambito alle soluzioni.

> [!TIP]
> Se Microsoft Monitoring Agent è installato direttamente nella macchina virtuale (non come estensione di Azure), il Centro sicurezza PC non installa Microsoft Monitoring Agent e il monitoraggio della sicurezza è limitato.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Il Centro sicurezza installa soluzioni nelle aree di lavoro di Log Analytics esistenti? Quali solo le implicazioni relative alla fatturazione?
Quando il Centro sicurezza rileva che una VM è già connessa a un'area di lavoro creata, il Centro sicurezza abilita soluzioni in questa area di lavoro in base al piano tariffario specifico. Le soluzioni vengono applicate solo alle macchine virtuali rilevanti di Azure tramite il [targeting della soluzione](../operations-management-suite/operations-management-suite-solution-targeting.md), quindi la fatturazione rimane invariata.

- **Livello Gratuito**: il Centro sicurezza installa la soluzione 'SecurityCenterFree' nell'area di lavoro. Non ti verrà addebitato il livello Gratuito.
- **Livello Standard**: il Centro sicurezza installa la soluzione 'Security' nell'area di lavoro.

   ![Soluzioni nell'area di lavoro predefinita](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Nell'ambiente sono già presenti aree di lavoro. È possibile usarle per raccogliere i dati di sicurezza?
Se in una VM è già installato Microsoft Monitoring Agent come estensione di Azure, il Centro sicurezza usa l'area di lavoro connessa esistente. Una soluzione del Centro sicurezza viene installata nell'area di lavoro, se non è già presente, e la soluzione viene applicata solo alle VM rilevanti tramite il [targeting della soluzione](../operations-management-suite/operations-management-suite-solution-targeting.md).

Quando il Centro sicurezza installa Microsoft Monitoring Agent nelle VM, usa le aree di lavoro predefinite create dal Centro sicurezza.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Nelle aree di lavoro è già presente una soluzione di sicurezza. Quali solo le implicazioni relative alla fatturazione?
La soluzione Security & Audit viene usata per abilitare le funzionalità del livello standard del Centro sicurezza per le macchine virtuali di Azure.The Security & Audit solution is used to enable Security Center standard tier features for Azure VMs. Se la soluzione Sicurezza e controllo è già installata in un'area di lavoro, il Centro sicurezza usa la soluzione esistente. La fatturazione rimane invariata.
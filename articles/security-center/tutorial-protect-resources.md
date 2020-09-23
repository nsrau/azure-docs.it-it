---
title: Esercitazione sui controlli di accesso e delle applicazioni - Centro sicurezza di Azure
description: Questa esercitazione illustra come configurare i criteri di accesso JIT alle macchine virtuali e i criteri di controllo delle applicazioni.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 56dd74fba46aa8b79c94b1460996bb6edb1ff93f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904606"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Esercitazione: Protezione delle risorse con Centro sicurezza di Azure
Centro sicurezza di Azure limita l'esposizione a minacce usando i controlli di accesso e delle applicazioni per bloccare le attività dannose. L'accesso JIT (Just-in-Time) alle macchine virtuali riduce l'esposizione agli attacchi grazie alla possibilità di negare l'accesso permanente alle macchine virtuali. In alternativa, è possibile fornire l'accesso controllato alle macchine virtuali solo quando è necessario. I controlli delle applicazioni adattivi consentono di applicare la protezione avanzata delle macchine virtuali contro il malware controllando quali applicazioni possono essere eseguite nelle macchine virtuali. Il Centro sicurezza usa le funzionalità di Machine Learning per analizzare i processi in esecuzione nella macchina virtuale e, grazie a questa funzionalità intelligente, consente di applicare regole di inserimento nell'elenco elementi consentiti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare i criteri di accesso JIT alle macchine virtuali
> * Configurazione dei criteri di controllo delle applicazioni

## <a name="prerequisites"></a>Prerequisiti
Per esaminare le funzionalità descritte in questa esercitazione, Azure Defender deve essere abilitato. È possibile provare Azure Defender gratuitamente. Per altre informazioni, vedere [Prova Azure Defender](security-center-pricing.md).

## <a name="manage-vm-access"></a>Gestire l'accesso alle macchine virtuali
L'accesso JIT alle macchine virtuali può essere usato per bloccare il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario.

Non è necessario lasciare aperte le porte di gestione in qualsiasi momento. Devono essere aperte solo durante la connessione alla macchina virtuale, ad esempio per eseguire attività di gestione o manutenzione. Quando la funzionalità JIT è abilitata, Centro sicurezza usa le regole del gruppo di sicurezza di rete, che limitano l'accesso alle porte di gestione per impedire che possano essere attaccate da utenti malintenzionati.

Seguire le istruzioni in [Proteggere le porte di gestione con l'accesso JIT](security-center-just-in-time.md).

## <a name="harden-vms-against-malware"></a>Implementare la protezione avanzata contro il malware delle macchine virtuali
I controlli delle applicazioni adattivi aiutano a definire un set di applicazioni che è possibile eseguire in gruppi di risorse configurati. Questo, oltre ad altri vantaggi, consente di implementare la protezione avanzata contro il malware delle macchine virtuali. Il Centro sicurezza usa le funzionalità di Machine Learning per analizzare i processi in esecuzione nella macchina virtuale e, grazie a questa funzionalità intelligente, consente di applicare regole di inserimento nell'elenco elementi consentiti.

Seguire le istruzioni in [Usare i controlli delle applicazioni adattivi per ridurre la superficie di attacco dei computer](security-center-adaptive-application.md).

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come limitare l'esposizione a minacce mediante le operazioni seguenti:

> [!div class="checklist"]
> * Configurazione di criteri di accesso JIT alle macchine virtuali per fornire accesso controllato alle macchine virtuali solo quando necessario
> * Configurazione di criteri di controllo delle applicazioni adattivo per controllare quali applicazioni possono essere eseguite nelle macchine virtuali

Passare all'esercitazione successiva per informazioni su come rispondere agli eventi imprevisti relativi alla sicurezza.

> [!div class="nextstepaction"]
> [Esercitazione: Rispondere agli eventi imprevisti relativi alla sicurezza](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png

---
title: Domande frequenti sul centro sicurezza di Azure-Domande sulle macchine virtuali
description: Domande frequenti sulle macchine virtuali nel centro sicurezza di Azure, un prodotto che consente di prevenire, rilevare e rispondere alle minacce
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
ms.openlocfilehash: 32023fca15de9d3f9258bc05166c3cff4184ea15
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499850"
---
# <a name="faq---questions-about-virtual-machines"></a>Domande frequenti sulle macchine virtuali


## <a name="what-types-of-virtual-machines-are-supported"></a>Quali tipi di macchine virtuali sono supportati?

Il monitoraggio e le indicazioni sono disponibili per le macchine virtuali create usando i [modelli di distribuzione classica e Resource Manager](../azure-resource-manager/management/deployment-models.md).

Per l'elenco delle piattaforme supportate vedere [Supported platforms in Azure Security Center](security-center-os-coverage.md) (Piattaforme supportate nel Centro sicurezza di Azure).


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Perché il Centro sicurezza di Azure non riconosce la soluzione antimalware in esecuzione nella VM Azure?

Il Centro sicurezza di Azure dispone di visibilità sull'antimalware installato tramite le estensioni di Azure. Ad esempio, il Centro sicurezza non è in grado di rilevare l'antimalware che è stato preinstallato in un'immagine fornita dall'utente o è stato installato nelle macchine virtuali mediante processi dell'utente (ad esempio sistemi di gestione della configurazione).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Perché viene visualizzato il messaggio "Dati di analisi mancanti" per la VM?

Questo messaggio viene visualizzato quando non sono presenti dati di analisi per una macchina virtuale. Dopo l'abilitazione della raccolta dei dati nel Centro sicurezza di Azure, l'analisi dei dati da popolare può richiedere tempo, in genere meno di un'ora. Dopo il popolamento iniziale dei dati di analisi, è possibile che si riceva questo messaggio perché non è presente alcun dato di analisi o non sono presenti dati di analisi recenti. Le analisi non saranno popolate per le macchine virtuali con stato arrestato. Questo messaggio potrebbe essere visualizzato anche se i dati di analisi non sono stati inseriti di recente, in conformità ai criteri di conservazione per l'agente Windows, che ha un valore predefinito di 30 giorni.


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Con quale frequenza il Centro sicurezza esegue l'analisi alla ricerca di vulnerabilità del sistema operativo, aggiornamenti del sistema e problemi di protezione degli endpoint?

Di seguito sono riportati i tempi di latenza per le analisi delle vulnerabilità, degli aggiornamenti e dei problemi del Centro sicurezza:

- Configurazioni di sicurezza del sistema operativo: i dati vengono aggiornati entro 48 ore
- Aggiornamenti di sistema: i dati vengono aggiornati entro 24 ore
- Problemi di protezione degli endpoint: i dati vengono aggiornati entro 8 ore

In genere, il Centro sicurezza esegue l'analisi dei nuovi dati ogni ora e aggiorna le indicazioni di conseguenza. 

> [!NOTE]
> Per raccogliere e archiviare i dati, il Centro sicurezza usa l'agente di Log Analytics. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](./security-center-enable-data-collection.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Perché viene visualizzato il messaggio "L'agente di macchine virtuali non è presente?"

Per abilitare la raccolta dei dati, l'agente di macchine virtuali deve essere installato nelle VM. Per impostazione predefinita, l'agente di macchine virtuali è installato nelle macchine virtuali distribuite da Azure Marketplace. Per altre informazioni su come installare l'agente di maccine virtuali in altre VM, vedere il post di blog [Estensioni e agente di macchine virtuali](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
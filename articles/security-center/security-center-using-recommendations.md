---
title: Uso delle raccomandazioni del Centro sicurezza di Azure per migliorare la sicurezza | Microsoft Docs
description: " Imparare a usare i criteri di sicurezza e le raccomandazioni nel Centro sicurezza di Azure per attenuare un attacco alla sicurezza. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603275"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Uso delle raccomandazioni del Centro sicurezza di Azure per migliorare la sicurezza
Configurando i criteri di sicurezza e implementando le raccomandazioni offerte dal Centro sicurezza di Azure, è possibile ridurre le probabilità di un importante evento di sicurezza. Questo articolo illustra come usare i criteri di sicurezza e le raccomandazioni nel Centro sicurezza di Azure per attenuare un attacco alla sicurezza. 

Il Centro sicurezza esegue automaticamente analisi continue per analizzare lo stato di sicurezza delle risorse di Azure.Security Center automatically runs continuous scans to analyze the security state of your Azure resources. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli di sicurezza necessari. Il Centro sicurezza aggiorna i consigli entro 24 ore, con le eccezioni seguenti:

- I consigli per la configurazione della sicurezza del sistema operativo vengono aggiornati entro 48 ore
- Endpoint Protection issues recommendations are updated within 8 hours

## <a name="scenario"></a>Scenario
In questo scenario viene illustrato come usare il Centro sicurezza per ridurre il rischio di eventi imprevisti della sicurezza monitorando e intervenendo sulle raccomandazioni del Centro sicurezza. Si fa riferimento a una società fittizia, Contoso, e ai ruoli presentati nella [guida alla pianificazione e gestione](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) del Centro sicurezza. Lo scenario si concentra sui ruoli degli utenti tipo seguenti:

![Ruoli dello scenario](./media/security-center-using-recommendations/scenario-roles.png)

Contoso di recente ha eseguito la migrazione di alcune delle proprie risorse locali in Azure. Contoso vuole proteggere le risorse e ridurre la vulnerabilità delle risorse nel cloud.

## <a name="use-azure-security-center"></a>Usare il Centro sicurezza di Azure
David, del reparto sicurezza IT di Contoso, ha già scelto di eseguire l'onboarding del Centro sicurezza nelle sottoscrizioni di Contoso al Centro sicurezza di Azure per prevenire e rilevare le vulnerabilità di sicurezza. 

Il Centro sicurezza analizza automaticamente lo stato di sicurezza delle risorse di Azure di Contoso e applica i criteri di sicurezza predefiniti. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea **suggerimenti** in base ai controlli impostati nei criteri di sicurezza. 

David esegue il livello standard di sicurezza di Azure, in tutte le sottoscrizioni per ottenere la suite completa di consigli e funzionalità di sicurezza disponibili. Jeff inoltre inboarda tutti i server locali esistenti che non sono ancora stati migrati nel cloud in modo che possano sfruttare il supporto ibrido del Centro sicurezza nei server [Windows](quick-onboard-windows-computer.md) e [Linux.](quick-onboard-linux-computer.md)

Jeff è un proprietario del carico di lavoro cloud. È responsabile dell'applicazione dei controlli di sicurezza in conformità con i criteri di sicurezza di Contoso. 

Jeff esegue queste attività:

- monitorare le raccomandazioni di sicurezza presenti nel Centro sicurezza
- Valutare le raccomandazioni sulla sicurezza e decidere se applicare o ignorare le raccomandazioni.
- applicare le raccomandazioni sulla sicurezza

### <a name="remediate-threats-using-recommendations"></a>Correggere le minacce usando le raccomandazioni
Come parte delle attività di monitoraggio quotidiane, Jeff accede ad Azure e apre il Centro sicurezza. 

1. Jeff seleziona le sottoscrizioni del carico di lavoro.

2. Jeff controlla il **punteggio sicuro** per ottenere un quadro generale di quanto siano sicure le sottoscrizioni e vede che il punteggio è 548.

3. Jeff deve decidere quali raccomandazioni gestire per prime. Così Jeff fa clic su Secure Score e inizia a gestire i consigli in base a quanto migliora il suo [impatto Secure Score](security-center-secure-score.md).

4. Poiché ha un numero elevato di server e macchine virtuali connesse, Jeff decide di concentrarsi su **Risorse di calcolo e app**.

5. Quando Jeff fa clic su **Calcolo e app,** visualizza un elenco di consigli e li gestisce in base all'impatto del punteggio sicuro.

6. Jeff dispone di numerose macchine virtuali con connessione Internet e, poiché le relative porte sono esposte, è preoccupato che un utente malintenzionato possa ottenere il controllo sui server. Così Jeff sceglie di utilizzare [**l'accesso just-in-time VM**](security-center-just-in-time.md).

Jeff continua a sfogliare le raccomandazioni con priorità alta e media, decidendo di volta in volta sull'implementazione. Per ogni raccomandazione, Jeff esamina le informazioni dettagliate fornite dal Centro sicurezza per comprendere quali risorse sono interessate, qual è l'impatto del punteggio sicuro, il significato di ogni raccomandazione e i passaggi di correzione per come ridurre ogni problema.

## <a name="conclusion"></a>Conclusioni
Il monitoraggio delle raccomandazioni nel Centro sicurezza contribuisce a eliminare le vulnerabilità di sicurezza prima che si verifichi un attacco. Quando si correggino i consigli, il punteggio sicuro e la postura di sicurezza dei carichi di lavoro migliorano. Il Centro sicurezza individua automaticamente le nuove risorse distribuite, le valuta in base ai criteri di sicurezza e offre nuove raccomandazioni per proteggerle.


## <a name="next-steps"></a>Passaggi successivi
Assicurarsi che sia implementato un processo di monitoraggio in cui controllare regolarmente le raccomandazioni nel Centro sicurezza, per assicurarsi di mantenere le risorse protette nel tempo.

Questo scenario ha illustrato come usare i criteri di sicurezza e le raccomandazioni nel Centro sicurezza di Azure per attenuare un attacco alla sicurezza.

Scopri come rispondere alle minacce con Gestione e risposta agli avvisi di [sicurezza.](security-center-managing-and-responding-alerts.md)

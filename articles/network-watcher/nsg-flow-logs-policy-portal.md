---
title: 'Guida introduttiva: distribuire e gestire i log di flusso NSG usando criteri di Azure'
titleSuffix: Azure Network Watcher
description: Questo articolo illustra come usare i criteri predefiniti per gestire la distribuzione dei log del flusso di NSG
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: damendo
ms.openlocfilehash: a603f55bd5bff7b2ed68623e9c712faaf8ac766f
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877029"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Guida introduttiva: distribuire e gestire i log di flusso NSG usando criteri di Azure 

## <a name="overview"></a>Panoramica
Criteri di Azure consente di imporre standard aziendali e di valutare la conformità su larga scala. I casi d'uso comuni per Criteri di Azure includono l'implementazione della governance per la coerenza delle risorse, la conformità alle normative, la sicurezza, i costi e la gestione. In questo articolo verranno usati due criteri predefiniti disponibili per i log di flusso NSG per gestire l'installazione dei log dei flussi. Il primo criterio contrassegna i gruppi senza i log di flusso abilitati. Il secondo criterio distribuisce automaticamente i log di flusso per gruppi senza i log di flusso abilitati. 

Se si sta creando un criterio di Azure per la prima volta, è possibile leggere: 
- [Panoramica di criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview) 
- [Esercitazione per la creazione di criteri](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Individuare i criteri
1. Passare alla portale di Azure – [Portal.Azure.com](https://portal.azure.com) 

Passare alla pagina Criteri di Azure cercando criteri nella Home page dei criteri della barra di ricerca superiore ![](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Passare alla scheda **assegnazioni** dal riquadro sinistro

![Scheda assegnazioni](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Fare clic sul pulsante **assegna criterio** 

![Pulsante Assegna criteri](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Fare clic sul menu tre punti in "definizioni criteri" per visualizzare i criteri disponibili

5. Usare il filtro dei tipi e scegliere "predefinito". Cercare quindi "Flow log"

Verranno visualizzati i due criteri predefiniti per l'elenco dei criteri dei log di flusso ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Scegliere il criterio che si vuole assegnare

- *"Il log di flusso deve essere configurato per ogni gruppo di sicurezza di rete"* è il criterio di controllo che contrassegna gruppi non conformi, ovvero gruppi senza registrazione del flusso abilitata
- *"Distribuisci una risorsa log di flusso con gruppo di sicurezza di rete di destinazione"* è il criterio con un'azione di distribuzione, che Abilita i log di flusso in tutti gruppi senza log di flusso

Di seguito sono riportate istruzioni separate per ogni criterio.  

## <a name="audit-policy"></a>Criterio di controllo 

### <a name="how-the-policy-works"></a>Funzionamento del criterio

I criteri controllano tutti gli oggetti ARM di tipo "Microsoft. Network/networkSecurityGroups", ovvero analizza tutti gruppi in un ambito specificato e verifica l'esistenza di log dei flussi collegati tramite la proprietà dei log di flusso di NSG. Se la proprietà non esiste, il NSG viene contrassegnato.

Se si vuole visualizzare la definizione completa del criterio, è possibile visitare la [scheda definizioni](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) e cercare "log dei flussi" per trovare il criterio

### <a name="assignment"></a>Assegnazione

1. Immettere i dettagli dei criteri

- Ambito: una sottoscrizione è la scelta comune. è anche possibile scegliere un gruppo di gestione o un gruppo di risorse come pertinente.  
- Definizione dei criteri: da scegliere, come illustrato nella sezione "individuare i criteri".
- Assegnaname: scegliere un nome descrittivo 

2. Fare clic su "verifica + crea" per esaminare l'assegnazione

Il criterio non richiede alcun parametro. Quando si assegna un criterio di controllo, non è necessario inserire i dettagli nella scheda "monitoraggio e aggiornamento".  

![Revisione dei criteri di controllo](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Risultati

Per controllare i risultati, aprire la scheda conformità e cercare il nome dell'assegnazione.
Una volta eseguito il criterio, viene visualizzata una schermata simile alla seguente. Se il criterio non è stato eseguito, attendere qualche minuto. 

![Risultati dei criteri di controllo](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Distribuisci criteri if-not-exists 

### <a name="policy-structure"></a>Struttura dei criteri

I criteri controllano tutti gli oggetti ARM di tipo "Microsoft. Network/networkSecurityGroups", ovvero analizza tutti gruppi in un ambito specificato e verifica l'esistenza di log dei flussi collegati tramite la proprietà dei log di flusso di NSG. Se la proprietà non esiste, il criterio distribuisce un log di flusso. 

Se si vuole visualizzare la definizione completa del criterio, è possibile visitare la [scheda definizioni](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) e cercare "log dei flussi" per trovare i criteri. 

### <a name="assignment"></a>Assegnazione

1. Immettere i dettagli dei criteri

- Ambito: una sottoscrizione è la scelta comune. è anche possibile scegliere un gruppo di gestione o un gruppo di risorse come pertinente.  
- Definizione dei criteri: da scegliere, come illustrato nella sezione "individuare i criteri".
- Assegnaname: scegliere un nome descrittivo 

2. Aggiungere i parametri dei criteri 

Il servizio Network Watcher è un servizio a livello di area. Questi parametri consentono l'azione del criterio di distribuzione dei log di flusso da eseguire. 
- Area NSG: aree di Azure a cui sono assegnati i criteri
- ID archiviazione: ID risorsa completo dell'account di archiviazione. Nota: questo account di archiviazione deve trovarsi nella stessa area del NSG. 
- Network Watchers RG: nome del gruppo di risorse che contiene la risorsa Network Watcher. Se non è stato rinominato, è possibile immettere ' NetworkWatcherRG ', che corrisponde all'impostazione predefinita.
- Nome Network Watcher: nome del servizio Network Watcher locale. Formato: NetworkWatcher_RegionName. Esempio: NetworkWatcher_centralus. Vedere l'elenco completo.

![Parametri dei criteri di DINE](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Aggiungere i dettagli di correzione

- Se si desidera che i criteri influiscano sulle risorse esistenti, selezionare l'opzione "Crea attività di monitoraggio". 
- "Creare un'identità gestita" deve essere già selezionata
- È stata selezionata la stessa posizione precedente per l'identità gestita 
- Per utilizzare questo criterio, è necessario disporre delle autorizzazioni di collaboratore o proprietario. Se si dispone di queste autorizzazioni, non verranno visualizzati errori.

![Correzione del criterio DINE](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. Fare clic su "verifica + crea" per esaminare l'assegnazione. verrà visualizzata una schermata simile alla seguente.

![Revisione dei criteri di DINE](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Risultati

Per controllare i risultati, aprire la scheda conformità e cercare il nome dell'assegnazione.
Verrà visualizzata una schermata simile alla seguente dopo il criterio. Se il criterio non è stato eseguito, attendere qualche minuto.

![Risultati dei criteri di DINE](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Passaggi successivi 

-   Usare questa [esercitazione](https://docs.microsoft.com/azure/network-watcher/quickstart-configure-network-security-group-flow-logs-from-arm-template) per approfondire l'uso dei modelli ARM per distribuire i log di flusso e analisi del traffico.
-   Altre informazioni su [Network Watcher](https://docs.microsoft.com/azure/network-watcher/)

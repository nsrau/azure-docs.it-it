---
title: Miglioramento dell'eccellenza operativa con Advisor
description: USA Azure Advisor per ottimizzare e maturare l'eccellenza operativa per le tue sottoscrizioni di Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 1cac5a3f93df8422a3896b1251857bf552731fb4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85125396"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Ottenere l'eccellenza operativa usando Azure Advisor

Le raccomandazioni di eccellenza operativa in Azure Advisor possono essere utili per: 
- Efficienza dei processi e dei flussi di lavoro.
- Gestibilità delle risorse.
- Procedure consigliate per la distribuzione. 

È possibile ottenere questi consigli nella scheda **Operational excellence** del dashboard di Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Crea avvisi di integrità dei servizi di Azure per ricevere una notifica quando si verificano problemi di Azure

Si consiglia di configurare gli avvisi di integrità dei servizi di Azure in modo da ricevere una notifica quando si verificano problemi relativi ai servizi di Azure. [Integrità dei servizi di Azure](https://azure.microsoft.com/features/service-health/) è un servizio gratuito che fornisce indicazioni e supporto personalizzati quando si è interessati da un problema di servizio di Azure. Advisor identifica le sottoscrizioni che non hanno gli avvisi configurati e ne consiglia la configurazione.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Progettare gli account di archiviazione per evitare di raggiungere il limite massimo di sottoscrizioni

Un'area di Azure può supportare un massimo di 250 account di archiviazione per sottoscrizione. Una volta raggiunto tale limite, non sarà possibile creare account di archiviazione in tale combinazione di area/sottoscrizione. Advisor controlla le sottoscrizioni e fornisce consigli per la progettazione per un minor numero di account di archiviazione per qualsiasi area/sottoscrizione vicina al raggiungimento del limite.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Verificare di poter consultare gli esperti di cloud di Azure quando necessario

Quando si esegue un carico di lavoro cruciale per l'azienda, è importante avere accesso al supporto tecnico quando necessario. Advisor identifica le potenziali sottoscrizioni cruciali per l'azienda che non hanno il supporto tecnico incluso nel piano di supporto. Si consiglia di eseguire l'aggiornamento a un'opzione che include il supporto tecnico.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>Eliminare e ricreare il pool per rimuovere un componente interno deprecato

Se il pool usa un componente interno deprecato, eliminare e ricreare il pool per migliorare la stabilità e le prestazioni.

## <a name="repair-invalid-log-alert-rules"></a>Ripristinare le regole di avviso del log non valide

Azure Advisor rileva le regole di avviso con query non valide specificate nella relativa sezione di condizione. È possibile creare regole di avviso del log in monitoraggio di Azure e usarle per eseguire query di analisi a intervalli specificati. I risultati della query determinano se è necessario attivare un avviso. Le query di analisi possono diventare non valide nel tempo a causa di modifiche nelle risorse, nelle tabelle o nei comandi a cui si fa riferimento. Advisor consiglia di correggere la query nella regola di avviso per impedirne la disabilitazione automatica e garantire il monitoraggio della copertura delle risorse in Azure. [Altre informazioni sulla risoluzione dei problemi relativi alle regole di avviso.](https://aka.ms/aa_logalerts_queryrepair)

## <a name="use-azure-policy-recommendations"></a>Usare le raccomandazioni relative ai criteri di Azure

Criteri di Azure è un servizio di Azure che è possibile usare per creare, assegnare e gestire i criteri. Questi criteri applicano le regole e gli effetti sulle risorse. Le seguenti raccomandazioni sui criteri di Azure consentono di ottenere l'eccellenza operativa: 

**Gestire i tag.** Questi criteri aggiungono o sostituiscono il tag e il valore specifici quando vengono aggiunte o aggiornate le risorse. È possibile monitorare e aggiornare le risorse esistenti attivando un'attività di correzione. Questo criterio non modifica i tag nei gruppi di risorse.

**Applicare i requisiti di conformità geografica.** Questi criteri consentono di limitare le posizioni che l'organizzazione può specificare durante la distribuzione delle risorse. 

**Specificare gli SKU delle macchine virtuali consentite per le distribuzioni.** Questo criterio consente di specificare un set di SKU di macchine virtuali che possono essere distribuiti dall'organizzazione.

**Applicare le *VM di controllo che non usano Managed disks*.**

**Abilitare *ereditare un tag dai gruppi di risorse*.** Questi criteri aggiungono o sostituiscono il tag e il valore specifici del gruppo di risorse padre quando una risorsa viene aggiunta o aggiornata. È possibile monitorare e aggiornare le risorse esistenti attivando un'attività di correzione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Attività iniziali](advisor-get-started.md)
* [Raccomandazioni sui costi di Advisor](advisor-cost-recommendations.md)
* [Raccomandazioni sulle prestazioni di Advisor](advisor-performance-recommendations.md)
* [Consigli sull'affidabilità di Advisor](advisor-high-availability-recommendations.md)
* [Raccomandazioni sulla sicurezza di Advisor](advisor-security-recommendations.md)
* [API REST Advisor](https://docs.microsoft.com/rest/api/advisor/)

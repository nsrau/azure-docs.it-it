---
title: Migliorare l'eccellenza operativa per le sottoscrizioni di Azure con Azure AdvisorImprove operational excellency for your Azure subscriptions with Azure Advisor
description: Usare Advisor per ottimizzare e ottenere maturi in eccellenza operativa per le sottoscrizioni di AzureUse Advisor to optimize and get mature in operational excellence for your Azure subscriptions
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443080"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Raggiungi l'eccellenza operativa con Azure Advisor

Le raccomandazioni di eccellenza operativa di Azure Advisor aiutano i clienti con l'efficienza dei processi e del flusso di lavoro, la gestibilità delle risorse e le procedure consigliate per la distribuzione. È possibile ottenere questi consigli da Advisor nella scheda **Eccellenza operativa** del dashboard di Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Creare avvisi di integrità dei servizi Azure per ricevere una notifica quando si verificano problemi in Azure

Si consiglia di impostare gli avvisi di integrità dei servizi Azure per ricevere una notifica quando si verificano problemi di servizio in Azure. [Integrità dei servizi di Azure](https://azure.microsoft.com/features/service-health/) è un servizio gratuito che fornisce supporto e indicazioni personalizzati quando si verifica un problema di servizio in Azure. Advisor identifica le sottoscrizioni che non dispongono degli avvisi configurati e ne consiglia la creazione.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Progettare gli account di archiviazione per evitare di raggiungere il limite massimo di sottoscrizioneDesign your storage accounts to prevent hitting the maximum subscription limit

Un'area di Azure può supportare un massimo di 250 account di archiviazione per ogni sottoscrizione. Una volta raggiunto il limite, non sarà possibile creare altri account di archiviazione in tale combinazione area/sottoscrizione. Advisor controllerà le sottoscrizioni e presenterà consigli sulle superfici per la progettazione di un numero inferiore di account di archiviazione per quelli vicini al raggiungimento del limite massimo.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Verificare di poter consultare gli esperti di cloud di Azure quando necessario

Quando si esegue un carico di lavoro aziendale critico, è importante avere accesso al supporto tecnico se necessario. Advisor identifica potenziali sottoscrizioni aziendali critiche che non hanno il supporto tecnico incluso nel piano di supporto e consiglia di eseguire l'aggiornamento a un'opzione che includa il supporto tecnico.

## <a name="repair-invalid-log-alert-rules"></a>Ripristinare le regole di avviso del log non valideRepair invalid log alert rules

Azure Advisor rileverà le regole di avviso con query non valide specificate nella relativa sezione relativa alle condizioni. Le regole di avviso del log vengono create in Monitoraggio di Azure e vengono usate per eseguire le query di analisi a intervalli specificati. I risultati della query determinano se è necessario attivare un avviso. Le query di analisi doverebbero diventare non valide nel corso del tempo a causa di modifiche nelle risorse, nelle tabelle o nei comandi di riferimento. Advisor consiglia di correggere la query nella regola di avviso per evitare che venga disabilitata automaticamente e garantire la copertura del monitoraggio delle risorse in Azure.Advisor recommend that you correct the query in the alert rule to prevent it from getting auto-disabled and ensure monitoring coverage of your resources in Azure. [Ulteriori informazioni sulla risoluzione dei problemi relativi alle regole di avviso](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Seguire le procedure consigliate usando Criteri di AzureFollow best practices using Azure Policy

Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire criteri. Questi criteri applicano regole ed effetti diversi alle risorse. Di seguito sono riportati i consigli sui criteri di Azure per ottenere l'eccellenza operativa:Below are the Azure policy recommendations to help you achieve operational excellency: 
1. Gestisci tag tramite criteri di Azure: questo criterio aggiunge o sostituisce il tag e il valore specificati quando una risorsa viene creata o aggiornata. È possibile correggere le risorse esistenti attivando un'attività di correzione. Inoltre, questo non modifica i tag nei gruppi di risorse.
2. Applicare i requisiti di conformità geografica usando Criteri di Azure: i criteri consentono di limitare le posizioni che l'organizzazione può specificare durante la distribuzione delle risorse. 
3. Specificare gli SKU di macchina virtuale consentiti per le distribuzioni: questo criterio consente di specificare un set di SKU di macchine virtuali che l'organizzazione può distribuire.
4. Applicare 'Controlla macchine virtuali che non usano dischi gestiti' usando i criteri di AzureEnforce 'Audit VMs that do not use managed disks' using Azure policy
5. Usare 'Eredita un tag dai gruppi di risorse' usando i criteri di Azure: il criterio aggiunge o sostituisce il tag e il valore specificati dal gruppo di risorse padre quando una risorsa viene creata o aggiornata. È possibile correggere le risorse esistenti attivando un'attività di correzione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Guida introduttiva](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Consigli di Advisor sulle prestazioni](advisor-performance-recommendations.md)
* [Consigli di Advisor sulla disponibilità elevata](advisor-high-availability-recommendations.md)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)

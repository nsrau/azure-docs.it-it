---
title: Migliorare l'eccellenza operativa per le sottoscrizioni di Azure con Azure Advisor | Microsoft Docs
description: USA Advisor per ottimizzare e ottenere una maturità operativa per le tue sottoscrizioni di Azure.
services: advisor
documentationcenter: NA
author: sagupt
ms.service: advisor
ms.topic: article
ms.date: 10/24/2019
ms.author: sagupt
ms.openlocfilehash: 7cf3d3b34d0921cd111f8111bc2008ef0eced962
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033290"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Ottenere l'eccellenza operativa con Azure Advisor

Azure Advisor consigli di eccellenza operativa aiutano i clienti a usare processi e efficienza dei flussi di lavoro, gestibilità delle risorse e procedure consigliate per la distribuzione. È possibile ottenere questi consigli da Advisor nella scheda **Operational excellence** del dashboard di Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Creare avvisi di integrità dei servizi Azure per ricevere una notifica quando si verificano problemi in Azure

Si consiglia di impostare gli avvisi di integrità dei servizi Azure per ricevere una notifica quando si verificano problemi di servizio in Azure. [Integrità dei servizi di Azure](https://azure.microsoft.com/features/service-health/) è un servizio gratuito che fornisce supporto e indicazioni personalizzati quando si verifica un problema di servizio in Azure. Advisor identifica le sottoscrizioni che non dispongono degli avvisi configurati e ne consiglia la creazione.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Progettare gli account di archiviazione per evitare di raggiungere il limite massimo di sottoscrizioni

Un'area di Azure può supportare un massimo di 250 account di archiviazione per sottoscrizione. Una volta raggiunto il limite, non sarà possibile creare altri account di archiviazione in tale combinazione di area/sottoscrizione. Advisor verificherà le sottoscrizioni e le raccomandazioni sulla superficie per la progettazione per un minor numero di account di archiviazione per qualsiasi valore vicino al raggiungimento del limite massimo.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Verificare di poter consultare gli esperti di cloud di Azure quando necessario

Quando si esegue un carico di lavoro aziendale critico, è importante avere accesso al supporto tecnico se necessario. Advisor identifica potenziali sottoscrizioni aziendali critiche che non hanno il supporto tecnico incluso nel piano di supporto e consiglia di eseguire l'aggiornamento a un'opzione che includa il supporto tecnico.

## <a name="repair-invalid-log-alert-rules"></a>Ripristinare le regole di avviso del log non valide

Azure Advisor rileverà le regole di avviso con query non valide specificate nella relativa sezione di condizione. Le regole di avviso del log vengono create in Monitoraggio di Azure e vengono usate per eseguire le query di analisi a intervalli specificati. I risultati della query determinano se è necessario attivare un avviso. Le query di analisi doverebbero diventare non valide nel corso del tempo a causa di modifiche nelle risorse, nelle tabelle o nei comandi di riferimento. Advisor consiglia di correggere la query nella regola di avviso per impedirne la disabilitazione automatica e garantire il monitoraggio della copertura delle risorse in Azure. [Ulteriori informazioni sulla risoluzione dei problemi relativi alle regole di avviso](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Seguire le procedure consigliate con criteri di Azure

Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire criteri. Questi criteri applicano regole e effetti differenti sulle risorse. Di seguito sono riportate le indicazioni per i criteri di Azure che consentono di ottenere l'eccellenza operativa: 
1. Gestione dei tag con criteri di Azure: questo criterio aggiunge o sostituisce il tag e il valore specificati quando viene creata o aggiornata una risorsa. È possibile correggere le risorse esistenti attivando un'attività di correzione. Inoltre, non modifica i tag nei gruppi di risorse.
2. Applicare i requisiti di conformità geografica usando criteri di Azure: il criterio consente di limitare le posizioni che l'organizzazione può specificare durante la distribuzione delle risorse. 
3. Specificare gli SKU delle macchine virtuali consentite per le distribuzioni: questo criterio consente di specificare un set di SKU di macchine virtuali che l'organizzazione può distribuire.
4. Applicare ' controlla macchine virtuali che non usano Managed disks ' usando criteri di Azure
5. Usare "eredita un tag dai gruppi di risorse" usando criteri di Azure: i criteri aggiungono o sostituiscono il tag e il valore specificati dal gruppo di risorse padre quando viene creata o aggiornata una risorsa. È possibile correggere le risorse esistenti attivando un'attività di correzione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [esercitazione introduttiva](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)

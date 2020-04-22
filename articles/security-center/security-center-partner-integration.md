---
title: Integrare soluzioni di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con i partner per una migliore sicurezza complessiva delle risorse di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758065"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrare soluzioni di sicurezza nel Centro sicurezza di Azure
Questo documento aiuta a gestire le soluzioni di sicurezza già connesse al Centro sicurezza di Azure e ad aggiungerne di nuove.

## <a name="integrated-azure-security-solutions"></a>Soluzioni di sicurezza di Azure integrate
Il Centro sicurezza semplifica l'abilitazione di soluzioni di sicurezza integrate in Azure. I vantaggi includono:

- **Distribuzione semplificata**: il Centro sicurezza offre provisioning semplificato delle soluzioni dei partner integrate. Per soluzioni come la valutazione antimalware e vulnerabilità, il Centro sicurezza può eseguire il provisioning dell'agente nelle macchine virtuali. Per le appliance firewall, il Centro sicurezza può occuparsi di gran parte della configurazione di rete necessaria.
- **Rilevamenti integrati:** gli eventi di sicurezza delle soluzioni partner vengono automaticamente raccolti, aggregati e visualizzati come parte degli avvisi e degli eventi del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: i clienti possono usare eventi di integrità integrati per monitorare tutte le soluzioni dei partner in un attimo. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.

Attualmente, le soluzioni di sicurezza integrate includono la valutazione delle vulnerabilità da parte di [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e Microsoft Application Gateway Web application application application firewall.

> [!NOTE]
> Il Centro sicurezza non installa l'agente Log Analytics nelle appliance virtuali partner perché la maggior parte dei fornitori di sicurezza proibisce gli agenti esterni in esecuzione sulle appliance.

Per ulteriori informazioni sull'integrazione degli strumenti di scansione delle vulnerabilità da Qualys, incluso uno scanner integrato disponibile per i clienti di livello standard, vedere: 

- [Scanner di vulnerabilità integrato per macchine virtuali](built-in-vulnerability-assessment.md).
- [Distribuzione di una soluzione di analisi delle vulnerabilità dei partner](partner-vulnerability-assessment.md).

Il Centro sicurezza offre anche l'analisi delle vulnerabilità per:

* Database SQL - vedere Esplorare i report di [valutazione della vulnerabilità nel dashboard di valutazione della vulnerabilità](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Immagini del Registro di sistema del contenitore di Azure: vedere Integrazione del Registro di sistema del contenitore di [Azure con il Centro sicurezza (anteprima)Azure](azure-container-registry-integration.md) Container Registry images - see Azure Container Registry integration with Security Center (Preview)

## <a name="how-security-solutions-are-integrated"></a>Modalità di integrazione delle soluzioni di sicurezza
Le soluzioni di sicurezza di Azure distribuite dal Centro sicurezza vengono connesse automaticamente. È anche possibile connettere altre origini dati di sicurezza, inclusi i computer in esecuzione in locale o in altri cloud.

[![Integrazione di soluzioni di partner](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gestire soluzioni di sicurezza di Azure integrate e altre origini dati

1. Dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/)aprire **Centro sicurezza**.

1. Dal menu del Centro sicurezza, selezionare **Soluzioni**di sicurezza .

Nella pagina **Soluzioni** di sicurezza è possibile visualizzare l'integrità delle soluzioni di sicurezza di Azure integrate ed eseguire attività di gestione di base.

### <a name="connected-solutions"></a>Soluzioni connesse

La sezione **Soluzioni connesse** include soluzioni di sicurezza attualmente connesse al Centro sicurezza. Mostra anche lo stato di integrità di ogni soluzione.  

![Soluzioni connesse](./media/security-center-partner-integration/connected-solutions.png)

Una soluzione partner può avere uno degli stati seguenti:

* **Sano** (verde) - nessun problema di salute.
* **Non integro** (rosso) - c'è un problema di salute che richiede attenzione immediata.
* **Interruzione della segnalazione** (arancione) - la soluzione ha smesso di segnalarne l'integrità.
* **Non segnalato** (grigio) - la soluzione non ha ancora segnalato nulla e non sono disponibili dati sanitari. Lo stato di una soluzione potrebbe non essere segnalato se è stata connessa di recente ed è ancora in fase di distribuzione.

> [!NOTE]
> Se i dati relativi allo stato di integrità non sono disponibili, il Centro sicurezza mostra la data e l'ora dell'ultimo evento ricevuto, per indicare se la soluzione invia segnalazioni o meno. Se non sono disponibili dati sull'integrità e non sono stati ricevuti avvisi negli ultimi 14 giorni, il Centro sicurezza indica che la soluzione non è integra o non è stata segnalata.
>
>

Selezionare **VISTA** per ulteriori informazioni e opzioni quali:

   - **Console soluzione:** apre l'esperienza di gestione per questa soluzione.
   - **Collega macchina virtuale** - Apre la pagina Collega applicazioni. In questo pannello è possibile connettere risorse alla soluzione del partner.
   - **Elimina soluzione**
   - **Configurare**

   ![Dettagli della soluzione di un partner](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Discovered solutions (Soluzioni individuate)

Il Centro sicurezza individua automaticamente le soluzioni di sicurezza in esecuzione in Azure ma non connesse al Centro sicurezza e visualizza le soluzioni nella sezione **Soluzioni individuate.** Queste soluzioni includono soluzioni di Azure, ad esempio [Azure AD Identity Protection,](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)e soluzioni per i partner.

> [!NOTE]
> Il livello Standard del Centro sicurezza è necessario a livello di sottoscrizione per la funzionalità di soluzioni individuate. Per altre informazioni sui piani tariffari, vedere [Prezzi.](security-center-pricing.md)
>

Selezionare **CONNECT** in una soluzione per l'integrazione con il Centro sicurezza e ricevere una notifica degli avvisi di sicurezza.

### <a name="add-data-sources"></a>Aggiungere origini dati

La sezione **Aggiungi origini dati** include altre origini dati disponibili che è possibile connettere. Per istruzioni sull'aggiunta di dati da una qualsiasi di queste origini, fare clic su **AGGIUNGI**.

![Origini dati](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza. Per informazioni correlate, vedere gli articoli seguenti:For related information, see the following articles:

* [Esportare avvisi e consigli di sicurezza](continuous-export.md). Informazioni su come configurare un'integrazione con Azure Sentinel o qualsiasi altro SIEM.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
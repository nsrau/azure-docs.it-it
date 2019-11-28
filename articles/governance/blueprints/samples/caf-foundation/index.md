---
title: Panoramica dell'esempio di progetto di definizione della base di Cloud Adoption Framework
description: Panoramica e architettura del progetto di esempio di definizione della base di Cloud Adoption Framework (CAF) per Azure.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: 8339b7335ff2946dcd602db6ece4637ac4bc1d92
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74545442"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Panoramica del progetto di esempio di definizione della base di Microsoft Cloud Adoption Framework per Azure

Il progetto di definizione della base di Microsoft Cloud Adoption Framework (CAF) per Azure consente di distribuire un set di infrastrutture di base e controlli di criteri richiesti per la prima applicazione Azure destinata ad ambienti di produzione. Questo progetto di base è basato sul modello consigliato disponibile in Cloud Adoption Framework.

## <a name="architecture"></a>Architettura

Il progetto di esempio di definizione della base di Cloud Adoption Framework distribuisce le risorse dell'infrastruttura consigliate in Azure che possono essere usate dalle organizzazioni per predisporre i controlli di base necessari per gestire l'ambiente cloud. Con questo esempio verranno distribuite e imposte risorse, criteri e modelli che permetteranno a un'organizzazione di iniziare a usare Azure in modo sicuro.

![Definizione della base di Microsoft Cloud Adoption Framework: l'immagine illustra gli elementi installati previsti dalle linee guida di Cloud Adoption Framework per la creazione di una base iniziale da usare con Azure](../../media/caf-blueprints/caf-foundation-architecture.png)

Questa implementazione include diversi servizi di Azure usati per offrire una base sicura, completamente monitorata e pronta per l'azienda. L'ambiente è costituito da:

- Un'istanza di [Azure Key Vault](../../../../key-vault/key-vault-overview.md) usata per ospitare i segreti per le macchine virtuali distribuite nell'ambiente dei servizi condivisi
- [Analisi dei log](../../../../azure-monitor/overview.md), distribuito per garantire la registrazione di tutte le azioni e tutti i servizi in una posizione centrale dal momento in cui viene avviata la distribuzione sicura negli [account di archiviazione](../../../../storage/common/storage-introduction.md) per la registrazione diagnostica
- [Centro sicurezza di Azure](../../../../security-center/security-center-intro.md) (versione standard), distribuito per fornire la protezione dalle minacce per i carichi di lavoro di cui è stata eseguita la migrazione
- Il progetto definisce e distribuisce anche [criteri di Azure](../../../policy/overview.md) per: 
  - Applicare l'assegnazione di tag (CostCenter) a gruppi di risorse
  - Accodare risorse nel gruppo di risorse con il tag CostCenter
  - Area di Azure consentita per risorse e gruppi di risorse
  - SKU dell'account di archiviazione consentiti (da scegliere durante la distribuzione)
  - SKU di macchine virtuali di Azure consentiti (da scegliere durante la distribuzione)
  - Richiedere la distribuzione di Network Watcher 
  - Richiedere la crittografia di trasferimento sicuro dell'account di archiviazione di Azure
  - Rifiutare tipi di risorsa (da scegliere durante la distribuzione)  
- Iniziative
  - Abilitare il monitoraggio nel Centro sicurezza di Azure (89 criteri)

Tutti questi elementi sono conformi a procedure comprovate pubblicate in [Centro architetture di Azure - Architetture di riferimento di Azure](/azure/architecture/reference-architectures/).

> [!NOTE]
> Il progetto di definizione della base di Cloud Adoption Framework predispone un'architettura di base per i carichi di lavoro.
> Sarà comunque necessario distribuire i carichi di lavoro su cui viene definita questa architettura di base.

Per altre informazioni, vedere [Microsoft Cloud Adoption Framework per Azure - Idoneità](/azure/architecture/cloud-adoption/ready/azure-readiness-guide/govern-org-compliance?tabs=AzurePolicy).

## <a name="next-steps"></a>Passaggi successivi

Sono state esaminate la panoramica e l'architettura del progetto di esempio di definizione della base di Cloud Adoption Framework.

> [!div class="nextstepaction"]
> [Progetto di definizione della base di Cloud Adoption Framework - Procedura di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
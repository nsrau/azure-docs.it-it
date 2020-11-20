---
title: Sicurezza e governance aziendale
titleSuffix: Azure Machine Learning
description: 'Usare Azure Machine Learning in modo sicuro: autenticazione, autorizzazione, sicurezza di rete, crittografia dei dati e monitoraggio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992030"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Sicurezza e governance aziendale per Azure Machine Learning

In questo articolo vengono fornite informazioni sulle funzionalità di sicurezza e governance disponibili per Azure Machine Learning. Queste funzionalità sono utili per gli amministratori, DevOps e MLOps, che desiderano creare una configurazione sicura conforme ai criteri aziendali. Con Azure Machine Learning e la piattaforma Azure, è possibile:

* Limitazione dell'accesso a risorse e operazioni da un account utente o da gruppi
* Limitare le comunicazioni di rete in ingresso e in uscita
* Crittografare i dati in transito e inattivi
* Analizza le vulnerabilità
* Applicare e controllare i criteri di configurazione

## <a name="restrict-access-to-resources-and-operations"></a>Limitare l'accesso a risorse e operazioni

[Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) è il provider del servizio di identità per Azure Machine Learning. Consente di creare e gestire gli oggetti di sicurezza (utente, gruppo, entità servizio e identità gestita) usati per l' _autenticazione_ nelle risorse di Azure. La funzionalità autenticazione a più fattori è supportata se Azure AD è configurata per usarla.

Ecco il processo di autenticazione per Azure Machine Learning usando l'autenticazione a più fattori in Azure AD:

1. Il client accede ad Azure AD e ottiene un token di Azure Resource Manager.
1. Il client presenta il token ad Azure Resource Manager e ad Azure Machine Learning.
1. Azure Machine Learning fornisce un token di servizio Machine Learning alla destinazione di calcolo dell'utente (ad esempio, Azure Machine Learning cluster di elaborazione). Il token viene usato dalla destinazione di calcolo dell'utente per richiamare il servizio Machine Learning al termine dell'esecuzione. L'ambito è limitato all'area di lavoro.

[![Autenticazione in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

A ogni area di lavoro è associata un' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) assegnata dal sistema con lo stesso nome dell'area di lavoro. Questa identità gestita viene utilizzata per accedere in modo sicuro alle risorse utilizzate dall'area di lavoro. Dispone delle seguenti autorizzazioni RBAC di Azure per le risorse collegate:

| Risorsa | Autorizzazioni |
| ----- | ----- |
| Area di lavoro | Collaboratore |
| Account di archiviazione | Collaboratore ai dati del BLOB di archiviazione |
| Insieme di credenziali delle chiavi | Accesso a tutte le chiavi, i segreti e i certificati |
| Registro Azure Container | Collaboratore |
| Gruppo di risorse che contiene l'area di lavoro | Collaboratore |
| Gruppo di risorse che contiene l'insieme di credenziali delle chiavi se diverso da quello che contiene l'area di lavoro. | Collaboratore |

Non è consigliabile che gli amministratori revochino l'accesso dell'identità gestita alle risorse indicate nella tabella precedente. È possibile ripristinare l'accesso tramite l' [operazione di risincronizzazione delle chiavi](how-to-change-storage-access-key.md).

Azure Machine Learning crea anche un'applicazione aggiuntiva (il nome inizia con `aml-` o `Microsoft-AzureML-Support-App-` ) con accesso a livello di collaboratore nella sottoscrizione per ogni area dell'area di lavoro. Se, ad esempio, si dispone di un'area di lavoro negli Stati Uniti orientali e una in Europa settentrionale nella stessa sottoscrizione, verranno visualizzate due applicazioni. Queste applicazioni consentono all'utente di gestire le risorse di calcolo con Azure Machine Learning.

È anche possibile configurare le proprie identità gestite per l'uso con macchine virtuali di Azure e Azure Machine Learning cluster di elaborazione. Con una macchina virtuale, l'identità gestita può essere usata per accedere all'area di lavoro dall'SDK, anziché l'account Azure AD di un singolo utente. Con un cluster di calcolo, l'identità gestita viene usata per accedere alle risorse, ad esempio gli archivi dati protetti che l'utente che esegue il processo di training potrebbe non avere accesso a. Per ulteriori informazioni, vedere [autenticazione per Azure machine learning area di lavoro](how-to-setup-authentication.md).

> [!TIP]
> Esistono alcune eccezioni all'uso di Azure AD e di controllo degli accessi in base al ruolo di Azure in Azure Machine Learning:
> * Facoltativamente, è possibile abilitare l'accesso __SSH__ alle risorse di calcolo, ad esempio Azure Machine Learning istanza di calcolo e il cluster di calcolo. L'accesso SSH si basa su coppie di chiavi pubbliche/private, non Azure AD. L'accesso SSH non è regolato da RBAC di Azure.
> * È possibile eseguire l'autenticazione a modelli distribuiti come servizi Web (endpoint di inferenza) usando l'autenticazione basata su __token__ o __chiave__ . Le chiavi sono stringhe statiche, mentre i token vengono recuperati utilizzando un oggetto Azure AD sicurezza. Per altre informazioni, vedere [configurare l'autenticazione per i modelli distribuiti come servizio Web](how-to-authenticate-web-service.md).

Per altre informazioni, vedere gli articoli seguenti:
* [Autenticazione per Azure Machine Learning area di lavoro](how-to-setup-authentication.md)
* [Gestire l'accesso ai Azure Machine Learning](how-to-assign-roles.md)
* [Connettersi ai servizi di archiviazione](how-to-access-data.md)
* [USA Azure Key Vault per i segreti durante il training](how-to-use-secrets-in-runs.md)
* [Usare Azure AD identità gestita con Azure Machine Learning](how-to-use-managed-identities.md)
* [Usare Azure AD identità gestita con il servizio Web](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Sicurezza e isolamento della rete

Per limitare l'accesso di rete alle risorse di Azure Machine Learning, è possibile usare [rete virtuale di Azure (VNet)](../virtual-network/virtual-networks-overview.md). Reti virtuali consentono di creare ambienti di rete parzialmente o completamente isolati dalla rete Internet pubblica. In questo modo si riduce la superficie di attacco per la soluzione, nonché le probabilità di exfiltration di dati.

È possibile usare un gateway di rete privata virtuale (VPN) per connettere i singoli client o la propria rete a VNet

L'area di lavoro Azure Machine Learning può usare il [collegamento privato di Azure](../private-link/private-link-overview.md) per creare un endpoint privato dietro la VNet. Fornisce un set di indirizzi IP privati che possono essere usati per accedere all'area di lavoro dall'interno di VNet. Alcuni dei servizi che Azure Machine Learning si basano su possono anche usare il collegamento privato di Azure, ma alcuni si basano sui gruppi di sicurezza di rete o sul routing definito dall'utente.

Per altre informazioni, vedere i documenti seguenti:

* [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)
* [Proteggere le risorse dell'area di lavoro](how-to-secure-workspace-vnet.md)
* [Proteggere l'ambiente di training](how-to-secure-training-vnet.md)
* [Ambiente di inferenza sicuro](how-to-secure-inferencing-vnet.md)
* [Usare studio in una rete virtuale protetta](how-to-enable-studio-virtual-network.md)
* [Usare DNS personalizzato](how-to-custom-dns.md)
* [Configurare il firewall](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Crittografia dei dati

Azure Machine Learning usa un'ampia gamma di risorse di calcolo e archivi dati nella piattaforma Azure. Per altre informazioni su come ognuno di questi supporti supporta la crittografia dei dati inattivi e in transito, vedere [crittografia dei dati con Azure Machine Learning](concept-data-encryption.md).

Quando si distribuiscono i modelli come servizi Web, è possibile abilitare la sicurezza a livello di trasporto (TLS) per crittografare i dati in transito. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione di un servizio Web protetto](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Analisi delle vulnerabilità

Il [Centro sicurezza di Azure](../security-center/security-center-introduction.md) offre la gestione unificata della sicurezza e la protezione avanzata dalle minacce nei carichi di lavoro cloud ibridi. Per Azure Machine Learning, è necessario abilitare l'analisi delle risorse di [azure container Registry](../container-registry/container-registry-intro.md) e del servizio Azure Kubernetes. Per altre informazioni, vedere [azure container Registry Image Scan by Security Center](../security-center/defender-for-container-registries-introduction.md) and [Azure Kubernetes Services Integration with Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Controllare e gestire la conformità

[Criteri di Azure](../governance/policy/index.yml) è uno strumento di governance che consente di garantire che le risorse di Azure siano conformi ai criteri. È possibile impostare criteri per consentire o applicare configurazioni specifiche, ad esempio se l'area di lavoro Azure Machine Learning usa un endpoint privato. Per altre informazioni sui criteri di Azure, vedere la [documentazione di criteri di Azure](../governance/policy/overview.md). Per altre informazioni sui criteri specifici di Azure Machine Learning, vedere [Audit and Manage compliance with Azure Policy](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i servizi Web di Azure Machine Learning con TLS](how-to-secure-web-service.md)
* [Come usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Usare Azure Machine Learning con Firewall di Azure](how-to-access-azureml-behind-firewall.md)
* [Usare Azure Machine Learning con Rete virtuale di Azure](how-to-network-security-overview.md)
* [Crittografia dei dati inattivi e in transito](concept-data-encryption.md)
* [Creare un'API per raccomandazioni in tempo reale in Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)

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
ms.date: 09/09/2020
ms.openlocfilehash: fb1f1d098970927ba04c840e77ec0a0b8d76ca02
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561319"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Sicurezza e governance aziendale per Azure Machine Learning

In questo articolo vengono illustrate le funzionalità di sicurezza disponibili per Azure Machine Learning.

Quando si usa un servizio cloud, è consigliabile limitare l'accesso solo agli utenti che ne hanno bisogno. Per iniziare, è necessario comprendere i modelli di autenticazione e autorizzazione usati dal servizio. È anche possibile limitare l'accesso alla rete o unire in modo sicuro le risorse nella rete locale con il cloud. Anche la crittografia dei dati è fondamentale, sia quando sono inattivi che durante lo spostamento tra i servizi. È anche possibile creare criteri per applicare determinate configurazioni o log quando vengono create configurazioni non conformi. Infine, è necessario poter monitorare il servizio e produrre un log di controllo di tutte le attività.

> [!NOTE]
> Le informazioni contenute in questo articolo sono destinate a Python SDK per Azure Machine Learning versione 1.0.83.1 o successiva.

## <a name="authentication--authorization"></a>Autorizzazione & di autenticazione

La maggior parte dell'autenticazione per Azure Machine Learning risorse USA Azure Active Directory (Azure AD) per l'autenticazione e il controllo degli accessi in base al ruolo (RBAC di Azure) per l'autorizzazione. Le eccezioni sono le seguenti:

* __SSH__ : è possibile abilitare l'accesso SSH ad alcune risorse di calcolo, ad esempio Azure Machine Learning istanza di calcolo. L'accesso SSH usa l'autenticazione basata su chiavi. Per altre informazioni sulla creazione di chiavi SSH, vedere [creare e gestire chiavi SSH](../virtual-machines/linux/create-ssh-keys-detailed.md). Per informazioni sull'abilitazione dell'accesso SSH, vedere [creare e gestire Azure Machine Learning istanza di calcolo](how-to-create-manage-compute-instance.md).
* __Modelli distribuiti come servizi Web__ : le distribuzioni di servizi Web possono usare il controllo di accesso basato su __chiavi__ o __token__. Le chiavi sono stringhe statiche. I token vengono recuperati usando un account Azure AD. Per altre informazioni, vedere [configurare l'autenticazione per i modelli distribuiti come servizio Web](how-to-authenticate-web-service.md).

I servizi specifici su cui si basa Azure Machine Learning, ad esempio i servizi di archiviazione dei dati di Azure, dispongono di metodi di autenticazione e autorizzazione personalizzati. Per ulteriori informazioni sull'autenticazione dei servizi di archiviazione, vedere [connettersi ai servizi di archiviazione](how-to-access-data.md).

### <a name="azure-ad-authentication"></a>Autenticazione di Azure AD

L'autenticazione a più fattori è supportata se Azure Active Directory (Azure AD) è configurato per usarla. Ecco illustrato il processo di autenticazione:

1. Il client accede ad Azure AD e ottiene un token di Azure Resource Manager.  Gli utenti e le entità servizio sono completamente supportati.
1. Il client presenta il token ad Azure Resource Manager e ad Azure Machine Learning.
1. Il servizio Machine Learning offre un token di servizio Machine Learning alla destinazione di calcolo dell'utente, ad esempio all'ambiente di calcolo di Machine Learning. Il token viene usato dalla destinazione di calcolo dell'utente per richiamare il servizio Machine Learning al termine dell'esecuzione. L'ambito è limitato all'area di lavoro.

[![Autenticazione in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Per ulteriori informazioni, vedere [autenticazione per Azure machine learning area di lavoro](how-to-setup-authentication.md).

### <a name="azure-rbac"></a>Controllo degli accessi in base al ruolo di Azure

È possibile creare più aree di lavoro, ciascuna delle quali può essere condivisa da più utenti. È possibile controllare le funzionalità o le operazioni dell'area di lavoro a cui gli utenti possono accedere assegnando il proprio account Azure AD ai ruoli RBAC di Azure. Di seguito sono riportati i ruoli predefiniti:

* Proprietario
* Collaboratore
* Reader

I proprietari e i collaboratori possono usare tutte le destinazioni di calcolo e gli archivi dati collegati all'area di lavoro.  

La tabella seguente elenca alcune delle principali operazioni di Azure Machine Learning e i ruoli che possono eseguirle:

| Operazioni di Azure Machine Learning | Proprietario | Collaboratore | Reader |
| ---- |:----:|:----:|:----:|
| Creare un'area di lavoro | ✓ | ✓ | |
| Condividere l'area di lavoro | ✓ | |  |
| Creare una destinazione di calcolo | ✓ | ✓ | |
| Collegare la destinazione di calcolo | ✓ | ✓ | |
| Collegare gli archivi dati | ✓ | ✓ | |
| Eseguire esperimento | ✓ | ✓ | |
| Visualizzare esecuzioni/metriche | ✓ | ✓ | ✓ |
| Registrare il modello | ✓ | ✓ | |
| Creare un'immagine | ✓ | ✓ | |
| Distribuire il servizio Web | ✓ | ✓ | |
| Visualizzare modelli o immagini | ✓ | ✓ | ✓ |
| Chiamare un servizio Web | ✓ | ✓ | ✓ |

Se i ruoli predefiniti non soddisfano le esigenze personali, è possibile creare ruoli personalizzati. I ruoli personalizzati controllano tutte le operazioni all'interno di un'area di lavoro, ad esempio la creazione di un calcolo, l'invio di un'esecuzione, la registrazione di un archivio dati o la distribuzione di un modello. I ruoli personalizzati possono disporre di autorizzazioni di lettura, scrittura o eliminazione per le varie risorse di un'area di lavoro, ad esempio cluster, archivi dati, modelli ed endpoint. È possibile rendere il ruolo disponibile per un livello di area di lavoro specifico, per un livello di gruppo di risorse specifico o per un livello di sottoscrizione specifico. Per altre informazioni, vedere [Gestire gli utenti e i ruoli in un'area di lavoro di Azure Machine Learning](how-to-assign-roles.md).

> [!IMPORTANT]
> Azure Machine Learning dipende da altri servizi di Azure, come l'archiviazione BLOB di Azure e i servizi Kubernetes di Azure. Ogni servizio di Azure ha le proprie configurazioni RBAC di Azure. Per ottenere il livello di controllo di accesso desiderato, potrebbe essere necessario applicare entrambe le configurazioni RBAC di Azure per Azure Machine Learning e quelle per i servizi usati con Azure Machine Learning.

> [!WARNING]
> Azure Machine Learning è supportata con la collaborazione Business to Business di Azure Machine Learning, ma attualmente non è supportata con la collaborazione Business to Consumer di Azure Active Directory.

### <a name="managed-identities"></a>Identità gestite

A ogni area di lavoro è associata anche un' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) assegnata dal sistema con lo stesso nome dell'area di lavoro. L'identità gestita viene utilizzata per accedere in modo sicuro alle risorse utilizzate dall'area di lavoro. Dispone delle seguenti autorizzazioni per le risorse collegate:

| Risorsa | Autorizzazioni |
| ----- | ----- |
| Area di lavoro | Collaboratore |
| Account di archiviazione | Collaboratore ai dati del BLOB di archiviazione |
| Insieme di credenziali delle chiavi | Accesso a tutte le chiavi, i segreti e i certificati |
| Registro Azure Container | Collaboratore |
| Gruppo di risorse che contiene l'area di lavoro | Collaboratore |
| Gruppo di risorse che contiene l'insieme di credenziali delle chiavi se diverso da quello che contiene l'area di lavoro. | Collaboratore |

Non è consigliabile che gli amministratori revochino l'accesso dell'identità gestita alle risorse indicate nella tabella precedente. È possibile ripristinare l'accesso usando l'operazione di risincronizzazione delle chiavi.

Azure Machine Learning crea un'applicazione aggiuntiva, il cui nome inizia con `aml-` o `Microsoft-AzureML-Support-App-`, con accesso a livello di collaboratore nella sottoscrizione per ogni area geografica dell'area di lavoro. Se, ad esempio, si dispone di un'area di lavoro negli Stati Uniti orientali e una in Europa settentrionale nella stessa sottoscrizione, verranno visualizzate due applicazioni. Queste applicazioni consentono all'utente di gestire le risorse di calcolo con Azure Machine Learning.

Facoltativamente, è possibile configurare le proprie identità gestite per l'uso con macchine virtuali di Azure e Azure Machine Learning cluster di elaborazione. Con una macchina virtuale, l'identità gestita può essere usata per accedere all'area di lavoro dall'SDK, anziché l'account Azure AD di un singolo utente. Con un cluster di calcolo, l'identità gestita viene usata per accedere alle risorse, ad esempio gli archivi dati protetti che l'utente che esegue il processo di training potrebbe non avere accesso a. Per ulteriori informazioni, vedere [autenticazione per Azure machine learning area di lavoro](how-to-setup-authentication.md).

## <a name="network-security-and-isolation"></a>Sicurezza e isolamento della rete

Per limitare l'accesso fisico alle risorse di Azure Machine Learning, è possibile usare rete virtuale di Azure (VNet). Reti virtuali consentono di creare ambienti di rete parzialmente o completamente isolati dalla rete Internet pubblica. In questo modo si riduce la superficie di attacco per la soluzione, nonché le probabilità di exfiltration di dati.

Per altre informazioni, vedere i documenti seguenti:

* [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)
* [Proteggere le risorse dell'area di lavoro](how-to-secure-workspace-vnet.md)
* [Proteggere l'ambiente di training](how-to-secure-training-vnet.md)
* [Ambiente di inferenza sicuro](how-to-secure-inferencing-vnet.md)
* [Usare studio in una rete virtuale protetta](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Crittografia dei dati

Azure Machine Learning usa un'ampia gamma di risorse di calcolo e archivi dati. Per altre informazioni su come ognuno di questi supporti supporta la crittografia dei dati inattivi e in transito, vedere [crittografia dei dati con Azure Machine Learning](concept-data-encryption.md).

### <a name="microsoft-generated-data"></a>Dati generati da Microsoft

Quando si usano servizi quali Machine Learning automatizzato, Microsoft può generare dati temporanei pre-elaborati per il training di più modelli. Questi dati vengono archiviati in un archivio dati dell'area di lavoro, che consente di applicare i controlli di accesso e la crittografia in modo appropriato.

Potrebbe anche essere necessario crittografare le [informazioni di diagnostica registrate nell'endpoint distribuito](how-to-enable-app-insights.md) nell'istanza di Azure Application Insights.

## <a name="monitoring"></a>Monitoraggio

Esistono diversi scenari di monitoraggio con Azure Machine Learning, a seconda del ruolo e degli elementi monitorati.

| Ruolo | Monitoraggio da usare | Descrizione |
| ---- | ----- | ----- |
| Admin, DevOps, MLOps | [Metriche di monitoraggio di Azure](#azure-monitor), [log attività](#activity-log), [analisi delle vulnerabilità](#vulnerability-scanning) | Informazioni sul livello di servizio |
| Data Scientist, MLOps | [Monitoraggio delle esecuzioni](#monitor-runs) | Informazioni registrate durante l'esecuzione del training |
| MLOps | [Raccolta dei dati del modello](how-to-enable-data-collection.md), [monitoraggio con Application Insights](how-to-enable-app-insights.md) | Informazioni registrate da modelli distribuiti come servizi Web o moduli IoT Edge|

### <a name="monitor-runs"></a>Monitoraggio delle esecuzioni

È possibile monitorare le esecuzioni degli esperimenti in Azure Machine Learning, incluse le informazioni di registrazione all'interno degli script di training. Queste informazioni possono essere visualizzate tramite l'SDK, l'interfaccia della riga di comando di Azure e studio. Per altre informazioni, vedere gli articoli seguenti:

* [Avviare, monitorare e annullare le esecuzioni di training](how-to-manage-runs.md)
* [Abilitare i log](how-to-track-experiments.md)
* [Visualizzare i log](how-to-monitor-view-training-logs.md)
* [Visualizzare le esecuzioni con TensorBoard](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Monitoraggio di Azure

È possibile usare le metriche di Monitoraggio di Azure per visualizzare e monitorare le metriche per l'area di lavoro Azure Machine Learning. Nel [portale di Azure](https://portal.azure.com) selezionare l'area di lavoro e quindi **Metrica** :

[![Screenshot che illustra le metriche di esempio per un'area di lavoro](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Le metriche includono informazioni su esecuzioni, distribuzioni e registrazioni.

Per altre informazioni, vedere [Metriche in Monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).

### <a name="activity-log"></a>Log attività

È possibile visualizzare il log attività di un'area di lavoro per visualizzare varie operazioni eseguite nell'area di lavoro. Il log include informazioni di base quali il nome dell'operazione, l'iniziatore dell'evento e il timestamp.

Questo screenshot mostra il log attività di un'area di lavoro:

[![Screenshot che mostra il log attività di un'area di lavoro](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

I dettagli della richiesta di punteggio vengono archiviati in Application Insights. Quando si crea un'area di lavoro, Application Insights viene creato nella sottoscrizione. Le informazioni registrate includono campi come:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Alcune azioni nell'area di lavoro Azure Machine Learning non registrano le informazioni nel log attività. Ad esempio, l'avvio di un'esecuzione del training e la registrazione di un modello non vengono registrate.
>
> Alcune di queste azioni vengono visualizzate nell'area **Attività** dell'area di lavoro, ma queste notifiche non specificano chi ha avviato l'attività.

### <a name="vulnerability-scanning"></a>Analisi delle vulnerabilità

Il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro cloud ibridi. Per Azure Machine Learning, è necessario abilitare l'analisi delle risorse di Azure Container Registry e del servizio Azure Kubernetes. Vedere [azure container Registry Image Scan by Security Center](../security-center/defender-for-container-registries-introduction.md) e [Azure Kubernetes Services Integration with Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Controllare e gestire la conformità

[Criteri di Azure](../governance/policy/index.yml) è uno strumento di governance che consente di garantire che le risorse di Azure siano conformi ai criteri. Con Azure Machine Learning è possibile assegnare i seguenti criteri:

* **Chiave gestita dal cliente** : controlla o impone se le aree di lavoro devono usare una chiave gestita dal cliente.
* **Collegamento privato** : controllare se le aree di lavoro usano un endpoint privato per comunicare con una rete virtuale.

Per altre informazioni sui criteri di Azure, vedere la [documentazione di criteri di Azure](../governance/policy/overview.md).

Per altre informazioni sui criteri specifici di Azure Machine Learning, vedere [Audit and Manage compliance with Azure Policy](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Blocchi risorse

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i servizi Web di Azure Machine Learning con TLS](how-to-secure-web-service.md)
* [Come usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Usare Azure Machine Learning con Firewall di Azure](how-to-access-azureml-behind-firewall.md)
* [Usare Azure Machine Learning con Rete virtuale di Azure](how-to-network-security-overview.md)
* [Crittografia dei dati inattivi e in transito](concept-data-encryption.md)
* [Creare un'API per raccomandazioni in tempo reale in Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)

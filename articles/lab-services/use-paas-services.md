---
title: Usare i servizi Platform-as-a-Service (PaaS) in Azure DevTest LabsUse Platform-as-a-Service (PaaS) services in Azure DevTest Labs
description: Informazioni su come usare i servizi Platform-as-a-Service (Pass) in Azure DevTest Labs.Learn how to use Platform-as-a-Service (Pass) services in Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169202"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Usare i servizi Platform-as-a-Service (PaaS) in Azure DevTest LabsUse Platform-as-a-Service (PaaS) services in Azure DevTest Labs
PaaS è supportato in DevTest Labs tramite la funzionalità ambienti. Gli ambienti nei laboratori DevTest sono supportati da modelli di Azure Resource Manager preconfigurati in un repository Git.Environments in DevTest Labs are supported by pre-configured Azure Resource Manager templates in a Git repository. Gli ambienti possono contenere risorse PaaS e IaaS. Consentono di creare sistemi complessi che possono includere risorse di Azure, ad esempio macchine virtuali, database, reti virtuali e app Web, personalizzate per l'interazione. Questi modelli consentono una distribuzione coerente e una migliore gestione degli ambienti tramite il controllo del codice sorgente. 

Un sistema correttamente configurato consente i seguenti scenari: 

- Sviluppatori di avere ambienti indipendenti e multipli
- Test su configurazioni diverse in modo asincronoTesting on different configurations asynchronously
- Integrazione nelle pipeline di gestione temporanea e produzione senza modifiche al modello
- Avere sia macchine di sviluppo che ambienti all'interno dello stesso laboratorio migliora la facilità di gestione e la rendicontazione dei costi.  

Il provider di risorse DevTest Labs crea risorse per conto dell'utente lab, pertanto non è necessario disporre di autorizzazioni aggiuntive all'utente del lab per consentire l'utilizzo delle risorse PaaS. L'immagine seguente mostra un cluster di Service Fabric come ambiente nel lab.

![Cluster di Service Fabric come ambienteService Fabric cluster as an environment](./media/create-environment-service-fabric-cluster/cluster-created.png)

Per altre informazioni sulla configurazione degli ambienti, vedere Creare ambienti multi-VM e risorse PaaS con i modelli di Azure Resource Manager.For more information on setting up environments, see [Create multi-VM environments and PaaS resources with Azure Resource Manager templates](devtest-lab-create-environment-from-arm.md). DevTest Labs include un repository pubblico di modelli di Azure Resource Manager che è possibile usare per creare ambienti senza doversi connettere a un'origine GitHub esterna manualmente. Per altre informazioni sugli ambienti pubblici, vedere [Configurare e usare ambienti pubblici in Azure DevTest Labs.For](devtest-lab-configure-use-public-environments.md)more information on public environments, see Configure and use public environments in Azure DevTest Labs.

Nelle organizzazioni di grandi dimensioni, i team di sviluppo forniscono in genere ambienti quali ambienti di test personalizzati/isolati. Potrebbero esserci ambienti che devono essere utilizzati da tutti i team all'interno di una business unit o di una divisione. Il gruppo IT potrebbe voler fornire i propri ambienti che possono essere utilizzati da tutti i team dell'organizzazione.  

## <a name="customizations"></a>Personalizzazioni

#### <a name="sandbox"></a>Sandbox 
Il proprietario del lab può personalizzare gli ambienti lab per modificare il ruolo dell'utente da **lettore** a **collaboratore** all'interno del gruppo di risorse. Questa funzionalità si trova nella pagina **Impostazioni lab** in Configurazione **e criteri** del lab. Questa modifica del ruolo consente all'utente di aggiungere o rimuovere risorse all'interno di tale ambiente. Se si vuole limitare ulteriormente l'accesso, usare i criteri di Azure.If you want to restrict the access further, use Azure policies. Questa funzionalità consente di personalizzare le risorse o la configurazione senza l'accesso a livello di sottoscrizione.

#### <a name="custom-tokens"></a>Token personalizzati
Sono disponibili alcune informazioni lab personalizzate che si trova all'esterno del gruppo di risorse ed è specifico per gli ambienti a cui il modello può accedere. Eccone alcuni: 

- Identificazione della rete di laboratorio
- Location
- Account di archiviazione in cui sono archiviati i file dei modelli di Resource Manager.Storage account in which the Resource Manager templates files are stored. 
 
#### <a name="lab-virtual-network"></a>Rete virtuale lab
L'articolo [Connessione di ambienti alla rete virtuale del lab](connect-environment-lab-virtual-network.md) descrive come `$(LabSubnetId)` modificare il modello di Resource Manager per usare il token. Quando viene creato un `$(LabSubnetId)` ambiente, il token viene sostituito dal primo contrassegno di subnet in cui l'opzione **di utilizzo nella creazione** di macchine virtuali è impostata su **true**. Permette al nostro ambiente di utilizzare reti create in precedenza. Se si desidera utilizzare gli stessi modelli di Resource Manager `$(LabSubnetId)` in ambienti in fase di test come gestione temporanea e produzione, usare come valore predefinito in un parametro di modello di Resource Manager.If you want to use the same Resource Manager templates in environments in test as staging and production, use as a default value in a Resource Manager template parameter. 

#### <a name="environment-storage-account"></a>Account di archiviazione dell'ambiente
DevTest Labs supporta l'utilizzo di modelli di [Resource Manager annidati.](../azure-resource-manager/templates/linked-templates.md) L'articolo[Distribuire modelli di Azure Resource Manager annidati per](deploy-nested-template-environments.md) gli ambienti di test illustra come usare `_artifactsLocation` e `_artifactsLocationSasToken` i token per creare un URI in un modello di Resource Manager nella stessa cartella o in una cartella annidata del modello principale. Per altre informazioni su questi due token, vedere la sezione **Relativa agli elementi** di distribuzione di Azure Resource Manager - Guida alle procedure [consigliate.](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)

## <a name="user-experience"></a>Esperienza utente

## <a name="developer"></a>Developer
Gli sviluppatori usano lo stesso flusso di lavoro per la creazione di una macchina virtuale per creare un ambiente specifico. Selezionano l'ambiente rispetto all'immagine della macchina e immettono le informazioni necessarie richieste dal modello. Ogni sviluppatore con un ambiente consente la distribuzione delle modifiche e il debug del ciclo interno migliorato. L'ambiente può essere creato in qualsiasi momento utilizzando il modello più recente.  Questa funzionalità consente di eliminare e ricreare gli ambienti per ridurre i tempi di inattività derivanti dalla creazione manuale del sistema o dal ripristino da test di errore.  

### <a name="testing"></a>Test
Gli ambienti DevTest Labs consentono test indipendenti di codice e configurazioni specifici in modo asincrono. La pratica comune consiste nel configurare l'ambiente con il codice della singola richiesta pull e avviare qualsiasi test automatizzato. Una volta che il test automatizzato è stato eseguito fino al completamento, qualsiasi test manuale può essere eseguito sull'ambiente specifico. Di solito questo processo viene eseguito come parte della pipeline CI/CD. 

## <a name="management-experience"></a>Esperienza di gestione

### <a name="cost-tracking"></a>Verifica dei costi
La funzionalità di rilevamento dei costi include risorse di Azure all'interno dei diversi ambienti come parte della tendenza generale dei costi. Il costo per risorse non suddivide le diverse risorse all'interno dell'ambiente, ma visualizza l'ambiente come un unico costo.

### <a name="security"></a>Security
Una sottoscrizione di Azure configurata correttamente con DevTest Labs può [limitare l'accesso alle risorse di Azure solo tramite il lab.](devtest-lab-add-devtest-user.md) Con gli ambienti, il proprietario di un lab può consentire agli utenti di accedere alle risorse PaaS con configurazioni approvate senza consentire l'accesso ad altre risorse di Azure.With environments, a lab owner can allow users to access PaaS resources with approved configurations without allowing access to any other Azure resources. Nello scenario in cui gli utenti del lab personalizzano gli ambienti, il proprietario del lab può consentire l'accesso dei collaboratori. L'accesso collaboratore consente all'utente lab di aggiungere o rimuovere risorse di Azure solo all'interno del gruppo di risorse gestite. Consente un monitoraggio e una gestione più semplici rispetto a quelli dei collaboratori dell'utente che accedono alla sottoscrizione.

### <a name="automation"></a>Automazione
L'automazione è un componente chiave per un ecosistema efficace e su larga scala. L'automazione è necessaria per gestire la gestione o il monitoraggio di più ambienti tra sottoscrizioni e lab.

### <a name="cicd-pipeline"></a>CI/CD Pipeline
I servizi PaaS in DevTest Labs consentono di migliorare la pipeline CI/CD avendo focalizzato le distribuzioni in cui l'accesso è controllato dal lab.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sugli ambienti, vedere gli articoli seguenti:See the following articles for details about environments: 

- 
- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Creare un ambiente con cluster di Service Fabric indipendente in Azure DevTest LabsCreate an environment with self-contained Service Fabric cluster in Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Connettere un ambiente alla rete virtuale del lab nei laboratori DevTest di AzureConnect an environment to your lab's virtual network in Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrare ambienti nelle pipeline CI/CD di Azure DevOpsIntegrate environments into your Azure DevOps CI/CD pipelines](integrate-environments-devops-pipeline.md)
 






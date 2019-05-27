---
title: Usare i servizi Platform-as-a-Service (PaaS) in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come usare i servizi Platform-as-a-Service (Pass) in Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 865ae0b3f7a7965698a67183a4c820ba71f49cd8
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833920"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Usare i servizi Platform-as-a-Service (PaaS) in Azure DevTest Labs
PaaS è supportata in DevTest Labs tramite la funzionalità di ambienti. Gli ambienti in DevTest Labs sono supportati da configurati in precedenza modelli Azure Resource Manager in un repository Git. Gli ambienti possono contenere risorse di PaaS e IaaS. Consentono di creare sistemi complessi che possono includere risorse di Azure come macchine virtuali, database, le reti virtuali e le app Web, che sono personalizzate per essere utilizzati insieme. Questi modelli consentono distribuzione coerente e la gestione migliorata di ambienti con controllo del codice sorgente. 

Oggetto impostato correttamente system consente gli scenari seguenti: 

- Agli sviluppatori di disporre di più ambienti e indipendente
- Test in diverse configurazioni in modo asincrono
- Integrazione in pipeline di gestione temporanea e produzione senza modifiche al modello
- Computer di sviluppo e di ambienti all'interno dello stesso laboratorio migliora la facilità di gestione e creazione di report di costo.  

Il provider di risorse di DevTest Labs consente di creare le risorse per conto dell'utente del lab, in modo che non le autorizzazioni aggiuntive devono essere indicati per l'utente del lab per abilitare l'uso delle risorse PaaS. L'immagine seguente mostra un cluster di Service Fabric come un ambiente nel lab.

![Cluster di Service Fabric come un ambiente](./media/create-environment-service-fabric-cluster/cluster-created.png)

Per ulteriori informazioni sulla configurazione di ambienti, vedere [creare ambienti con più macchine Virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs è un repository pubblico dei modelli di Azure Resource Manager che è possibile usare per creare gli ambienti senza connettersi a un'origine esterna di GitHub da soli. Per altre informazioni sugli ambienti pubblici, vedere [configurare e usare ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Nelle organizzazioni di grandi dimensioni, i team di sviluppo in genere forniscono ambienti, ad esempio gli ambienti di testing/isolamento personalizzato. Potrebbero essere presenti ambienti che devono essere utilizzati da tutti i team all'interno di una business unit o una divisione. Il gruppo IT potrebbe voler fornire loro ambienti che possono essere usati da tutti gli altri team dell'organizzazione.  

## <a name="customizations"></a>Personalizzazioni

#### <a name="sandbox"></a>Sandbox 
Il proprietario del lab possa personalizzare gli ambienti lab per il ruolo dell'utente da modificare **reader** al **collaboratore** all'interno del gruppo di risorse. Questa funzionalità è disponibile nel **delle impostazioni di Lab** pagina sotto il **configurazione e criteri** del lab. Questa modifica nel ruolo consente all'utente di aggiungere o rimuovere le risorse all'interno dell'ambiente. Se si desidera limitare ulteriormente l'accesso, usare i criteri di Azure. Questa funzionalità consente di personalizzare la configurazione senza l'accesso a livello di sottoscrizione o le risorse.

#### <a name="custom-tokens"></a>Token personalizzati
Vi sono riportate alcune informazioni lab personalizzato che sono di fuori del gruppo di risorse ed sono specifico per gli ambienti che può accedere il modello. Ecco alcuni di essi: 

- Identificativo rete lab
- Località
- Account di archiviazione in cui sono archiviati i file dei modelli di Resource Manager. 
 
#### <a name="lab-virtual-network"></a>Rete virtuale di Lab
Il [ci si connette gli ambienti di rete virtuale del lab](connect-environment-lab-virtual-network.md) articolo viene descritto come modificare il modello di Resource Manager per usare il `$(LabSubnetId)` token. Quando viene creato un ambiente, il `$(LabSubnetId)` token viene sostituito da del primo contrassegno di subnet in cui la **uso nella macchina virtuale creare** opzione è impostata su **true**. Consente l'ambiente da usare creata in precedenza le reti. Se si desidera utilizzare gli stessi modelli di Resource Manager in ambienti di test come gestione temporanea e produzione, usare `$(LabSubnetId)` come valore predefinito in un parametro di modello di Resource Manager. 

#### <a name="environment-storage-account"></a>Account di archiviazione di ambiente
DevTest Labs supporta l'utilizzo delle [modelli di Resource Manager nidificati](../azure-resource-manager/resource-group-linked-templates.md). Il [[distribuire modelli di Azure Resource Manager nidificati per gli ambienti di testing](deploy-nested-template-environments.md) articolo illustra come usare `_artifactsLocation` e `_artifactsLocationSasToken` i token per creare un URI a un modello di Resource Manager nella stessa cartella o in un tipo annidato cartella del modello principale. Per ulteriori informazioni su questi due token, vedere la **elementi di distribuzione** sezione [Azure Resource Manager-Guida alle procedure consigliate migliore](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Esperienza utente

## <a name="developer"></a>Developer
Gli sviluppatori di usano stesso flusso di lavoro per la creazione di una macchina virtuale per creare un ambiente specifico. Essi selezionare l'ambiente e l'immagine della macchina e immettere le informazioni necessarie dal modello. Ogni sviluppatore un ambiente consente la distribuzione delle modifiche e il debug migliorato ciclo interno. È possibile creare l'ambiente in qualsiasi momento usando il modello più recente.  Questa funzionalità consente gli ambienti essere eliminata e ricreata in modo da contribuire a ridurre il tempo di inattività di dover manualmente il sistema di creazione o il ripristino da errori di test.  

### <a name="testing"></a>Test
Gli ambienti DevTest Labs consentono di test indipendente di configurazioni e codice specifico in modo asincrono. La pratica comune consiste nel configurare l'ambiente con il codice dalla richiesta pull singolo e avviare qualsiasi esecuzione di test automatizzati. Dopo l'esecuzione di test automatizzati esecuzione fino al completamento, qualsiasi test manuale può essere eseguito nell'ambiente specifico. In genere questo processo avviene come parte della pipeline di integrazione continua/recapito Continuo. 

## <a name="management-experience"></a>Esperienza di gestione

### <a name="cost-tracking"></a>Verifica dei costi
La funzionalità di rilevamento costo include le risorse di Azure all'interno di ambienti diversi nell'ambito della complessiva tendenza dei costi. Il costo per le risorse non suddividere le diverse risorse all'interno dell'ambiente, ma viene visualizzato l'ambiente come un costo unico.

### <a name="security"></a>Security
Una sottoscrizione di Azure configurata correttamente con DevTest Labs può [limitare l'accesso alle risorse di Azure solo tramite i lab](devtest-lab-add-devtest-user.md). Con gli ambienti, un proprietario del lab può consentire agli utenti di accedere alle risorse PaaS con le configurazioni approvate senza consentire l'accesso a eventuali altre risorse di Azure. Nello scenario in cui gli utenti del lab personalizzare gli ambienti, il proprietario del lab può consentire l'accesso come collaboratore. L'accesso come collaboratore consente all'utente di laboratorio aggiungere o rimuovere le risorse di Azure solo all'interno del gruppo di risorse gestite. Consente di tenere traccia più facilmente e gestione rispetto a consentire all'utente l'accesso collaboratore alla sottoscrizione.

### <a name="automation"></a>Automazione
Automazione è un componente chiave per la vasta scala, ecosistema efficace. Automazione è necessaria gestire la gestione o il rilevamento di più ambienti in sottoscrizioni e laboratori.

### <a name="cicd-pipeline"></a>Pipeline CI/CD
I servizi PaaS in DevTest Labs consentono di migliorare la pipeline CI/CD da visto concentrato distribuzioni in cui l'accesso viene controllato il lab.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per informazioni dettagliate sugli ambienti: 

- 
- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare gli ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Creare un ambiente con cluster autonomo di Service Fabric in Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Connettere un ambiente di rete virtuale del lab in Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrare ambienti nelle tue pipeline CI/CD DevOps di Azure](integrate-environments-devops-pipeline.md)
 






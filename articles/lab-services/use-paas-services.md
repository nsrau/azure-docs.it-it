---
title: Usare i servizi PaaS (Platform-as-a-Service) in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come usare i servizi di piattaforma distribuita come servizio (Pass) in Azure DevTest Labs.
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
ms.openlocfilehash: a80a54f3dc760d80f713db9857cbef0c580e66d6
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621376"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Usare i servizi PaaS (Platform-as-a-Service) in Azure DevTest Labs
PaaS è supportato in DevTest Labs tramite la funzionalità degli ambienti. Gli ambienti in DevTest Labs sono supportati dai modelli di Azure Resource Manager preconfigurati in un repository git. Gli ambienti possono contenere sia risorse PaaS che IaaS. Consentono di creare sistemi complessi che possono includere risorse di Azure, ad esempio macchine virtuali, database, reti virtuali e app Web, che sono personalizzate per collaborare. Questi modelli consentono una distribuzione coerente e una gestione migliorata degli ambienti con il controllo del codice sorgente. 

Un sistema configurato correttamente consente gli scenari seguenti: 

- Sviluppatori che dispongono di ambienti indipendenti e multipli
- Test in configurazioni diverse in modo asincrono
- Integrazione nelle pipeline di gestione temporanea e di produzione senza modifiche ai modelli
- La presenza di computer e ambienti di sviluppo all'interno dello stesso Lab migliora la facilità di gestione e la creazione di report sui costi.  

Il provider di risorse DevTest Labs crea risorse per conto dell'utente del Lab, quindi non è necessario assegnare autorizzazioni aggiuntive all'utente del Lab per consentire l'uso delle risorse di PaaS. Nell'immagine seguente viene illustrato un cluster Service Fabric come ambiente nel Lab.

![Service Fabric cluster come ambiente](./media/create-environment-service-fabric-cluster/cluster-created.png)

Per per altre informazioni sulla configurazione degli ambienti, vedere [creare ambienti con più macchine virtuali e risorse PaaS con i modelli Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs dispone di un repository pubblico di modelli di Azure Resource Manager che è possibile usare per creare ambienti senza dover connettersi a un'origine GitHub esterna autonomamente. Per altre informazioni sugli ambienti pubblici, vedere [configurare e usare ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Nelle organizzazioni di grandi dimensioni, i team di sviluppo forniscono in genere ambienti come ambienti di test personalizzati o isolati. Potrebbero essere presenti ambienti che devono essere usati da tutti i team all'interno di una business unit o di una divisione. Il gruppo IT potrebbe voler fornire gli ambienti che possono essere usati da tutti i team dell'organizzazione.  

## <a name="customizations"></a>Personalizzazioni

#### <a name="sandbox"></a>Sandbox 
Il proprietario del Lab può personalizzare gli ambienti Lab per modificare il ruolo dell'utente dal **lettore** al **collaboratore** all'interno del gruppo di risorse. Questa funzionalità si trova nella pagina **impostazioni Lab** sotto la **configurazione e i criteri** del Lab. Questa modifica del ruolo consente all'utente di aggiungere o rimuovere risorse in tale ambiente. Se si vuole limitare ulteriormente l'accesso, usare i criteri di Azure. Questa funzionalità consente di personalizzare le risorse o la configurazione senza l'accesso a livello di sottoscrizione.

#### <a name="custom-tokens"></a>Token personalizzati
Sono disponibili alcune informazioni sul Lab personalizzate all'esterno del gruppo di risorse ed è specifico per gli ambienti a cui il modello può accedere. Eccone alcuni: 

- Identificazione rete lab
- Percorso
- Account di archiviazione in cui sono archiviati i file dei modelli di Gestione risorse. 
 
#### <a name="lab-virtual-network"></a>Rete virtuale Lab
Negli [ambienti di connessione all'](connect-environment-lab-virtual-network.md) articolo relativo alla rete virtuale del Lab viene descritto come modificare il modello di gestione risorse per l'uso del token di `$(LabSubnetId)`. Quando viene creato un ambiente, il token `$(LabSubnetId)` viene sostituito dal primo contrassegno di subnet in cui l'opzione **USA nella creazione della macchina virtuale** è impostata su **true**. Consente all'ambiente di usare le reti create in precedenza. Se si desidera utilizzare gli stessi modelli di Gestione risorse negli ambienti di test come gestione temporanea e produzione, utilizzare `$(LabSubnetId)` come valore predefinito in un parametro di modello di Gestione risorse. 

#### <a name="environment-storage-account"></a>Account di archiviazione dell'ambiente
DevTest Labs supporta l'uso di [modelli di gestione risorse annidati](../azure-resource-manager/resource-group-linked-templates.md). L'articolo [[distribuire modelli annidati di Azure Resource Manager per ambienti di testing](deploy-nested-template-environments.md) illustra come usare i token `_artifactsLocation` e `_artifactsLocationSasToken` per creare un URI per un modello di gestione risorse nella stessa cartella di o in una cartella nidificata del modello principale. Per ulteriori informazioni su questi due token, vedere la sezione relativa agli **artefatti di distribuzione** di [Azure Resource Manager-Guida alle procedure consigliate](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Esperienza utente

## <a name="developer"></a>Developer
Gli sviluppatori usano lo stesso flusso di lavoro per creare una macchina virtuale per creare un ambiente specifico. Selezionano l'ambiente e l'immagine del computer e immettono le informazioni necessarie necessarie per il modello. Ogni sviluppatore con un ambiente consente la distribuzione delle modifiche e il debug del ciclo interno migliorato. L'ambiente può essere creato in qualsiasi momento usando il modello più recente.  Questa funzionalità consente di eliminare e ricreare gli ambienti per ridurre il tempo di inattività dovuto alla creazione manuale del sistema o al ripristino dal test degli errori.  

### <a name="testing"></a>Test
Gli ambienti DevTest Labs consentono il test indipendente di codice e configurazioni specifiche in modo asincrono. La pratica comune è la configurazione dell'ambiente con il codice della singola richiesta pull e l'avvio di tutti i test automatizzati. Al termine dell'esecuzione dei test automatizzati, è possibile eseguire qualsiasi test manuale sull'ambiente specifico. Questo processo viene in genere eseguito come parte della pipeline CI/CD. 

## <a name="management-experience"></a>Esperienza di gestione

### <a name="cost-tracking"></a>Verifica dei costi
La funzionalità di rilevamento dei costi include le risorse di Azure all'interno di diversi ambienti come parte della tendenza complessiva dei costi. Il costo per le risorse non suddivide le diverse risorse all'interno dell'ambiente ma Visualizza l'ambiente come singolo costo.

### <a name="security"></a>Sicurezza
Una sottoscrizione di Azure opportunamente configurata con DevTest Labs può [limitare l'accesso alle risorse di Azure solo tramite il Lab](devtest-lab-add-devtest-user.md). Con gli ambienti, un proprietario del Lab può consentire agli utenti di accedere alle risorse di PaaS con configurazioni approvate senza consentire l'accesso ad altre risorse di Azure. Nello scenario in cui gli utenti del Lab personalizzano gli ambienti, il proprietario del Lab può consentire l'accesso come collaboratore. L'accesso come collaboratore consente all'utente del Lab di aggiungere o rimuovere una risorsa di Azure solo nel gruppo di risorse gestite. Consente di tenere traccia e gestire più facilmente e di consentire l'accesso dell'utente collaboratore alla sottoscrizione.

### <a name="automation"></a>Automazione
L'automazione è un componente chiave per un ecosistema efficace e su larga scala. L'automazione è necessaria per gestire la gestione o il rilevamento di più ambienti tra sottoscrizioni e laboratori.

### <a name="cicd-pipeline"></a>Pipeline CI/CD
I servizi PaaS in DevTest Labs consentono di migliorare la pipeline di integrazione continua/recapito continuo con distribuzioni mirate in cui l'accesso è controllato dal Lab.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sugli ambienti, vedere gli articoli seguenti: 

- 
- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare ambienti pubblici in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Creare un ambiente con un cluster Service Fabric autonomo in Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Connettere un ambiente alla rete virtuale del Lab in Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integra gli ambienti nelle pipeline CI/CD di Azure DevOps](integrate-environments-devops-pipeline.md)
 






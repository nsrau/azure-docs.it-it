---
title: "Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager | Microsoft Docs"
description: "Informazioni su come creare ambienti con più macchine virtuali e risorse PaaS in Azure DevTest Labs con un modello di Azure Resource Manager"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: tarcher
ms.openlocfilehash: 4e1aae6c041e4572e7e2281203f969e7649e1480
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager

Il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) consente di [creare e aggiungere una macchina virtuale a un lab](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-add-vm). Ciò vale per la creazione di una macchina virtuale alla volta. Tuttavia, se l'ambiente contiene più macchine virtuali, ogni macchina deve essere creata individualmente. Per scenari quali app Web multilivello o farm di SharePoint, è necessario un meccanismo che consenta la creazione di più macchine virtuali in un unico passaggio. I modelli di Azure Resource Manager consentono di definire l'infrastruttura e la configurazione della soluzione di Azure e di distribuire ripetutamente più macchine virtuali in modo coerente. Ciò offre i vantaggi seguenti:

- I modelli di Azure Resource Manager vengono caricati direttamente dal repository di controllo del codice sorgente (GitHub o Team Services Git).
- Una volta configurato, gli utenti possono creare un ambiente scegliendo un modello di Azure Resource Manager dal portale di Azure, così come avviene con altri tipi di [basi per VM](./devtest-lab-comparing-vm-base-image-types.md).
- È possibile eseguire il provisioning delle risorse di Azure PaaS in un ambiente usando un modello di Azure Resource Manager oltre che le macchine virtuali IaaS.
- È possibile tenere traccia del costo dell'ambiente insieme alle singole VM create da altri tipi di base.
- Le risorse di PaaS vengono create e visualizzate nella verifica dei costi. Tuttavia, l'arresto automatico della macchina virtuale non si applica alle risorse di PaaS.
- Gli utenti hanno lo stesso controllo dei criteri delle macchine virtuali per gli ambienti di quello che hanno per le macchine virtuali di un singolo lab.

Altre informazioni sui numerosi [vantaggi offerti dai modelli di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) per distribuire, aggiornare o eliminare tutte le risorse del lab con una sola operazione.

> [!NOTE]
> Quando si usa un modello di Resource Manager come base per creare più macchine virtuali lab, occorre tenere presente che ci sono alcune differenze tra la creazione di più macchine virtuali e una singola macchina virtuale. Use a virtual machine's Azure Resource Manager template (Usare un modello di Azure Resource Manager di una macchina virtuale) spiega più dettagliatamente queste differenze.
>
>

## <a name="configure-azure-resource-manager-template-repositories"></a>Configurare i repository del modello di Azure Resource Manager

Come procedura consigliata nei casi di infrastruttura come codice e configurazione come codice, i modelli di ambiente devono essere gestiti nel controllo del codice sorgente. Azure Labs DevTest applica questa procedura e carica tutti i modelli Azure Resource Manager direttamente dai repository VSTS Git o GitHub. Di conseguenza, i modelli di Resource Manager possono essere usati in tutto il ciclo di rilascio, dall'ambiente di test a quello di produzione.

Ci sono un paio di regole da seguire per organizzare i modelli di Azure Resource Manager in un repository:

- Il nome del file del modello master deve essere `azuredeploy.json`. 

    ![File principali del modello di Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Se si intende usare valori di parametro definiti in un file di parametri, il nome di tale file deve essere `azuredeploy.parameters.json`.
- È possibile usare i parametri `_artifactsLocation` e `_artifactsLocationSasToken` per costruire il valori URI parametersLink e consentire a DevTest Labs di gestire automaticamente i modelli nidificati. Per altre informazioni, vedere [How Azure DevTest Labs makes nested Resource Manager template deployments easier for testing environments (Come Azure DevTest Labs semplifica le distribuzioni di modelli di Resource Manager nidificati negli ambienti di test)](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- È possibile definire metadati per specificare il nome e la descrizione visualizzati del modello. I metadati deve essere contenuti in un file denominato `metadata.json`. Il file di metadati di esempio seguente mostra come specificare il nome e la descrizione visualizzati: 

```json
{
 
"itemDisplayName": "<your template name>",
 
"description": "<description of the template>"
 
}
```

La procedura seguente consente di aggiungere un repository nel lab tramite il portale di Azure. 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
1. Nell'elenco dei lab selezionare il lab desiderato.   
1. Nel pannello del lab selezionare **Configuration and Policies** (Configurazione e criteri).

    ![Configurazione e criteri](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Nell'elenco delle impostazioni **Configuration and Policies** (Configurazione e criteri) **Repository**. Il pannello **Repository** elenca i repository aggiunti al lab. Per tutti i lab viene automaticamente generato un repository denominato `Public Repo`, che si connette al repository [DevTest Labs di GitHub](https://github.com/Azure/azure-devtestlab), che contiene diversi elementi di macchina virtuale utilizzabili.

    ![Repository pubblico](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Selezionare **Aggiungi +** per aggiungere il repository di modelli di Azure Resource Manager.
1. Quando si apre il secondo pannello **Repository**, immettere le informazioni necessarie come indicato di seguito:
    - **Nome**: immettere il nome del repository usato nel lab.
    - **URL clone Git**: immettere l'URL del clone Git HTTPS copiato in precedenza da GitHub o da Visual Studio Team Services.  
    - **Ramo**: immettere il nome del ramo per accedere alle definizioni dei modelli di Azure Resource Manager. 
    - **Token di accesso personale**: il token di accesso personale usato per accedere in modo sicuro al repository. Per ottenere il token da Visual Studio Team Services, selezionare **&lt;YourName> > My profile > Security > Public access token** (NomeUtente > Profilo personale > Sicurezza > Token di accesso pubblico). Per ottenere il token da GitHub, selezionare l'avatar e quindi selezionare **Settings > Public access token** (Impostazioni > Token di accesso pubblico). 
    - **Percorsi delle cartelle**: usando uno dei due campi di input, immettere il percorso della cartella che inizia con la barra rovesciata - / - ed è relativo all'URI del clone Git delle definizioni di elemento (primo campo di input) o delle definizioni di modello di Azure Resource Manager.   
    
        ![Repository pubblico](./media/devtest-lab-create-environment-from-arm/repo-values.png)

1. Quando tutti i campi obbligatori sono stati immessi e hanno superato la verifica, selezionare **Salva**.

La sezione successiva illustra come creare ambienti da un modello di Azure Resource Manager.

## <a name="create-an-environment-from-an-azure-resource-manager-template"></a>Creare un ambiente da un modello di Azure Resource Manager

Dopo aver configurato un repository di modelli di Azure Resource Manager nel laboratorio, gli utenti del laboratorio possono creare un ambiente usando il portale di Azure, seguendo questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
1. Nell'elenco dei lab selezionare il lab desiderato.   
1. Nel pannello del lab selezionare **Aggiungi+**.
1. Il pannello **Choose a base** (Scegli una base) visualizza le immagini di base che è possibile usare con i modelli di Azure Resource Manager prima elencati. Selezionare il modello di Azure Resource Manager desiderato.

    ![Scegli una base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Nel pannello **Aggiungi**, immettere il **nome dell'ambiente**. Il nome dell'ambiente è quello visualizzato agli utenti nel laboratorio. I campi di input rimanenti vengono definiti nel modello di Azure Resource Manager. Se i valori predefiniti sono definiti nel modello o se è presente il file `azuredeploy.parameter.json`, i valori predefiniti vengono visualizzati in tali campi di input. Per i parametri di tipo *stringa protetta*, è possibile usare i segreti archiviati nell'[archivio segreto personale](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store) del lab.

    ![Pannello Aggiungi](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Esistono diversi valori di parametro che, anche se specificati, vengono visualizzati come valori vuoti. Pertanto, se gli utenti assegnano tali valori ai parametri in un modello di Azure Resource Manager, DevTest Labs non li visualizza; vengono invece mostrati campi di input vuoti nei quali gli utenti del lab devono inserire un valore al momento della creazione dell'ambiente.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Selezionare **Aggiungi** per creare l'ambiente. L'ambiente avvia immediatamente il provisioning con lo stato visualizzato nell'elenco **Macchine virtuali**. Un nuovo gruppo di risorse viene creato automaticamente dal lab per eseguire il provisioning di tutte le risorse definite nel modello di Azure Resource Manager.
1. Dopo aver creato l'ambiente, selezionarlo nell'elenco  **My virtual machines (Macchine virtuali personali)** per aprire il pannello del gruppo delle risorse e cercare tutte le risorse di cui si è eseguito il provisioning nell'ambiente.
    
    ![Elenco delle macchine virtuali](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   È possibile anche espandere l'ambiente per visualizzare solo l'elenco di macchine virtuali oggetto del provisioning nell'ambiente.
    
    ![Elenco delle macchine virtuali](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Fare clic su uno degli ambienti per visualizzare le azioni disponibili, quali l'applicazione di elementi, il collegamento di dischi dati, la modifica dell'ora di arresto automatico e altro.

    ![Azioni dell'ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver creato la macchina virtuale, è possibile connettersi ad essa selezionando **Connetti** nel pannello della macchina virtuale.
* Visualizzare e gestire le risorse in ambiente selezionando l'ambiente nell'elenco **My virtual machines (Macchine virtuali personali)** nel lab. 
* Esplorare i [modelli di Azure Resource Manager dalla raccolta di modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates)

---
title: Integrare ambienti in pipeline di Azure in Laboratori DevTest di AzureIntegrate environments into Azure Pipelines in Azure DevTest Labs
description: Informazioni su come integrare gli ambienti Azure DevTest Labs nelle pipeline di integrazione continua (CI) di Azure DevOps e nella distribuzione continua (CD).
services: devtest-lab,virtual-machines,lab-services
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
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169416"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrare ambienti nelle pipeline CI/CD di Azure DevOpsIntegrate environments into your Azure DevOps CI/CD pipelines
È possibile usare l'estensione Attività di Azure DevTest Labs installata in Servizi DevOps di Azure (precedentemente nota come Visual Studio Team Services) per integrare facilmente la pipeline di compilazione e rilascio di integrazione continua (CI)/distribuzione continua (CD) con Azure DevTest Labs.You can use the Azure DevTest Labs Tasks extension that is installed in Azure DevOps Services (formerly known as Visual Studio Team Services) to easily integrate your continuous integration (CI)/ continuous delivery (CD) build-and-release pipeline with Azure DevTest Labs. Queste estensioni semplificano la distribuzione rapida di un [ambiente](devtest-lab-test-env.md) per un'attività di test specifica e quindi lo eliminano al termine del test. 

In questo articolo viene illustrato come creare e distribuire un ambiente, quindi eliminare l'ambiente, tutto in un'unica pipeline completa. In genere ognuna di queste attività viene eseguita singolarmente nella pipeline di compilazione-test-distribuzione personalizzata. Le estensioni utilizzate in questo articolo si aggiungono a queste attività di [creazione/eliminazione di macchine virtuali DTL:](devtest-lab-integrate-ci-cd-vsts.md)

- Creazione di un ambiente
- Eliminare un ambiente

## <a name="before-you-begin"></a>Prima di iniziare
Prima di poter integrare la pipeline CI/CD con Azure DevTest Labs, installare l'estensione Attività di [Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) da Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Creare e configurare il lab per gli ambienti
In questa sezione viene descritto come creare e configurare un lab in cui verrà distribuito l'ambiente di Azure.This section describes how to create and configure a lab where the Azure environment will be deployed to.

1. [Creare un lab,](devtest-lab-create-lab.md) se non ne hai già uno. 
2. Configurare il lab e creare un modello di ambiente seguendo le istruzioni di questo articolo: [Creare ambienti multi-VM e risorse PaaS con i modelli](devtest-lab-create-environment-from-arm.md)di Azure Resource Manager.
3. Per questo esempio, usare un [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)modello di avvio rapido di Azure esistente.
4. Copiare la cartella **201-web-app-redis-cache-sql-database** nella cartella **ArmTemplate** nel repository configurato nel passaggio 2.

## <a name="create-a-release-definition"></a>Creare una definizione di versione
Per creare una definizione di versione, eseguire le operazioni seguenti:

1.  Nella scheda **Rilasci** **dell'hub Build & Release**selezionare il pulsante con il **segno più (-).**
2.  Nella finestra **Crea definizione di versione** selezionare il modello **Vuoto** e quindi fare clic su **Avanti**.
3.  Selezionare **Scegli dopo** e quindi **Crea** per creare una nuova definizione di versione con un ambiente predefinito e nessun elemento collegato.
4.  Per aprire il menu di scelta rapida, nella nuova definizione di versione selezionare i **conio ai trattini (...)** accanto al nome dell'ambiente e quindi selezionare **Configura variabili**.
5.  Nella finestra **Configura ambiente** immettere i valori seguenti per le variabili usate nelle attività della definizione di versione:
1.  Per **administratorLogin**, immettere il nome di accesso dell'amministratore SQL.
2.  Per **administratorLoginPassword**, immettere la password che deve essere utilizzata dall'account di accesso dell'amministratore SQL. Usare l'icona di lucchetto per nascondere e proteggere la password.
3.  Per **databaseName**immettere il nome del database SQL.
4.  Queste variabili sono specifiche per gli ambienti di esempio, ambienti diversi possono avere variabili diverse.

## <a name="create-an-environment"></a>Creare un ambiente
La fase successiva della distribuzione consiste nel creare l'ambiente da utilizzare a scopo di sviluppo o test.

1. Nella definizione di versione selezionare **Aggiungi attività**.
2. Nella scheda Attività aggiungere un'attività Crea ambiente di Azure DevTest Labs.On the **Tasks** tab, add an Azure DevTest Labs Create Environment task. Configurare le attività in questo modo:
    1. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints) (Endpoint di servizio di Azure Resource Manager).
2. In **Nome lab**selezionare il nome dell'istanza creata in precedenza.
3. Per **Nome repository**, selezionare il repository in cui è stato eseguito il push del modello di Resource Manager (201).
4. In **Nome modello**, selezionare il nome dell'ambiente salvato nel repository del codice sorgente. 
5. **Nome lab**, **Nome repository**e Nome **modello** sono le rappresentazioni descrittive degli ID risorsa di Azure. L'immissione manuale del nome descrittivo causerà errori, utilizzare gli elenchi a discesa per selezionare le informazioni.
6. In **Nome ambiente**immettere un nome per identificare in modo univoco l'istanza dell'ambiente all'interno del lab.  Deve essere univoco all'interno del laboratorio.
7. Il **File parametro** e i **parametri**consentono di passare parametri personalizzati all'ambiente. È possibile utilizzare uno o entrambi per impostare i valori dei parametri. Per questo esempio, verrà utilizzata la sezione Parameters. Utilizzare i nomi delle variabili definite nell'ambiente, ad esempio:`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Le informazioni all'interno del modello di ambiente possono essere passate nella sezione di output del modello. Selezionare Crea variabili di **output basate sull'output del modello** di ambiente in modo che altre attività possano usare i dati. `$(Reference name.Output Name)`è il modello da seguire. Ad esempio, se il nome di riferimento è DTL e il `$(DTL.location)`nome di output nel modello è il percorso la variabile sarebbe .

## <a name="delete-the-environment"></a>Eliminare l'ambiente
La fase finale consiste nell'eliminare l'ambiente distribuito nell'istanza di Azure DevTest Labs.The final stage is to delete the Environment that you deployed in your Azure DevTest Labs instance. In genere si elimina l'ambiente dopo aver eseguito le attività di sviluppo o eseguire i test necessari nelle risorse distribuite.

Nella definizione della versione selezionare Aggiungi attività e quindi nella scheda **Distribuisci** aggiungere un'attività Elimina ambiente di Azure DevTest Labs.In the release definition, select **Add tasks**, and then on the Deploy tab, add an **Azure DevTest Labs Delete Environment** task. Configurare l'app come segue:

1. Per eliminare la macchina virtuale, vedere Attività di [Azure DevTest Labs:To](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)delete the VM, see Azure DevTest Labs Tasks:
    1. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints) (Endpoint di servizio di Azure Resource Manager).
    2. In **Nome lab**selezionare il lab in cui è presente l'ambiente.
    3. In **Nome ambiente**immettere il nome dell'ambiente da rimuovere.
2. Immettere un nome per la definizione di versione e quindi salvarla.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 
- [Creare ambienti multi-VM con i modelli di Resource Manager.](devtest-lab-create-environment-from-arm.md)
- Guida introduttiva ai modelli di Resource Manager per l'automazione di DevTest Labs dal repository GitHub di [DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- [Pagina Risoluzione dei problemi di VSTS](/azure/devops/pipelines/troubleshooting)


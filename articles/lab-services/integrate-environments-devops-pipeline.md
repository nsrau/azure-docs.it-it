---
title: Integrare gli ambienti in pipeline di Azure in Azure DevTest Labs | Microsoft Docs
description: Descrive come integrare ambienti Azure DevTest Labs in Azure DevOps integrazione continua (CI) e le pipeline di recapito continuo (CD).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61318388"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrare ambienti nelle tue pipeline CI/CD DevOps di Azure
È possibile usare l'estensione viene installata in servizi di DevOps di Azure (precedentemente noto come Visual Studio Team Services) per integrare facilmente l'integrazione continua (CI) per Azure DevTest Labs Tasks / recapito continuo (CD) compilazione e rilascio pipeline con Azure DevTest Labs. Queste estensioni rendono più semplice distribuire rapidamente un' [ambiente](devtest-lab-test-env.md) per una determinata attività di test e quindi eliminarlo al termine di test. 

Questo articolo illustra come creare e distribuire un ambiente, quindi eliminare l'ambiente, tutto in una pipeline completa. In genere eseguire ognuna di queste attività singolarmente in proprie pipeline di compilazione, test e distribuzione personalizzata. Le estensioni utilizzate in questo articolo sono oltre a queste [creare o eliminare le attività delle VM DTL](devtest-lab-integrate-ci-cd-vsts.md):

- Creare un ambiente
- Eliminare un ambiente

## <a name="before-you-begin"></a>Prima di iniziare
Prima di poter integrare la pipeline CI/CD con Azure DevTest Labs, installare [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) estensione da Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Creare e configurare i lab per gli ambienti
Questa sezione descrive come creare e configurare un lab in cui verrà distribuito l'ambiente di Azure.

1. [Creare un lab](devtest-lab-create-lab.md) se non ne hai già uno. 
2. Configurare il laboratorio e creare un modello di ambiente seguendo le istruzioni in questo articolo: [Creare ambienti con più macchine Virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. Per questo esempio, usare un modello di avvio rapido di Azure esistente [ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Copia il **201-web-app-redis-cache-sql-database** nella cartella le **ArmTemplate** cartella nel repository di cui è stato configurato nel passaggio 2.

## <a name="create-a-release-definition"></a>Creare una definizione di versione
Per creare una definizione di versione, eseguire le operazioni seguenti:

1.  Nel **versioni** scheda della finestra di **hub compilazione e rilascio**, selezionare il **segno più (+)** pulsante.
2.  Nella finestra **Crea definizione di versione** selezionare il modello **Vuoto** e quindi fare clic su **Avanti**.
3.  Selezionare **Scegli dopo** e quindi **Crea** per creare una nuova definizione di versione con un ambiente predefinito e nessun elemento collegato.
4.  Per aprire il menu di scelta rapida, nella nuova definizione di versione, selezionare il **puntini di sospensione (...)**  successivo per il nome dell'ambiente, quindi fare clic **configurare le variabili**.
5.  Nella finestra **Configura ambiente** immettere i valori seguenti per le variabili usate nelle attività della definizione di versione:
1.  Per la **administratorLogin**, immettere il nome dell'account di accesso amministratore SQL.
2.  Per la **administratorLoginPassword**, immettere la password da usare dall'account di accesso amministratore SQL. Usare l'icona di lucchetto per nascondere e proteggere la password.
3.  Per la **databaseName**, immettere il nome del Database SQL.
4.  Queste variabili sono specifiche per gli ambienti di esempio, ambienti diversi possono avere diverse variabili.

## <a name="create-an-environment"></a>Creare un ambiente
La fase successiva della distribuzione consiste nel creare l'ambiente da utilizzare per lo sviluppo o a scopo di test.

1. Nella definizione di versione selezionare **Aggiungi attività**.
2. Nel **attività** scheda, aggiungere un'attività di Azure DevTest Labs crea ambiente. Configurare le attività in questo modo:
    1. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints) (Endpoint di servizio di Azure Resource Manager).
2. Per la **nome Lab**, selezionare il nome dell'istanza creata in precedenza *.
3. Per la **nome del Repository**, selezionare il repository in cui è stato inserito il modello di Resource Manager (201) *.
4. Per la **nome modello**, selezionare il nome dell'ambiente in cui è stato salvato per il repository di codice sorgente *. 
5. Il **nome Lab**, **nome del Repository**, e **nome modello** sono rappresentazioni di descrittivo di ID risorsa di Azure. Immettere manualmente il nome descrittivo verrà causare errori, utilizzare gli elenchi a discesa per selezionare le informazioni.
6. Per la **nome dell'ambiente**, immettere un nome per identificare in modo univoco l'istanza di ambiente all'interno del lab.  Deve essere univoco all'interno del lab.
7. Il **File dei parametri** e il **parametri**, consente di utilizzare parametri personalizzati da passare all'ambiente. Uno o entrambi utilizzabile per impostare i valori dei parametri. In questo esempio verrà utilizzata la sezione dei parametri. Usare i nomi delle variabili definite nell'ambiente, ad esempio: `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. Le informazioni all'interno del modello di ambiente possono essere passate nella sezione di output del modello. Controllare **variabili di output di crea l'output del modello di ambiente in base** in modo da altre attività possa usare i dati. `$(Reference name.Output Name)` è il modello da seguire. Ad esempio, se il nome di riferimento è stato DTL e il nome di output nel modello di percorso non è la variabile sarebbe `$(DTL.location)`.

## <a name="delete-the-environment"></a>Eliminare l'ambiente
La fase finale consiste nell'eliminare l'ambiente che è stato distribuito nell'istanza di Azure DevTest Labs. L'eliminazione dell'ambiente in genere dopo l'esecuzione delle attività di sviluppo o eseguire i test necessari per le risorse distribuite.

Nella definizione di versione, selezionare **aggiungere le attività**, quindi nel **Distribuisci** scheda, aggiungere un **Elimina ambiente di Azure DevTest Labs** attività. Configurare l'app come segue:

1. Per eliminare la macchina virtuale, vedere [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints) (Endpoint di servizio di Azure Resource Manager).
    2. Per la **nome Lab**, selezionare il lab in cui si trova l'ambiente.
    3. Per la **nome dell'ambiente**, immettere il nome dell'ambiente da rimuovere.
2. Immettere un nome per la definizione di versione e quindi salvarla.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 
- [Creare un ambiente di più macchine Virtuali con modelli di Resource Manager](devtest-lab-create-environment-from-arm.md).
- I modelli di avvio rapido di Resource Manager per l'automazione DevTest Labs dal [DevTest Labs GitHub repository](https://github.com/Azure/azure-quickstart-templates).
- [Pagina di risoluzione dei problemi relativi a Visual Studio Team Services](/azure/devops/pipelines/troubleshooting)


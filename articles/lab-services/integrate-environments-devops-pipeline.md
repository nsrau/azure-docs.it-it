---
title: Integrare gli ambienti in Azure Pipelines Azure DevTest Labs | Microsoft Docs
description: Informazioni su come integrare Azure DevTest Labs ambienti nelle pipeline di integrazione continua (CI) e recapito continuo (CD) di Azure DevOps.
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
ms.openlocfilehash: c665c65dcda2266a7acd7bc78726179d559f4d64
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163920"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integra gli ambienti nelle pipeline CI/CD di Azure DevOps
È possibile usare l'estensione Azure DevTest Labs Tasks installata in Azure DevOps Services (precedentemente nota come Visual Studio Team Services) per integrare facilmente la pipeline di compilazione e rilascio continua (CI)/recapito continuo (CD) con Azure DevTest Labs. Queste estensioni semplificano la distribuzione rapida di un [ambiente](devtest-lab-test-env.md) per un'attività di test specifica e quindi l'eliminazione al termine del test. 

Questo articolo illustra come creare e distribuire un ambiente, quindi eliminare l'ambiente, tutto in un'unica pipeline completa. Ognuna di queste attività viene normalmente eseguita singolarmente nella pipeline di compilazione, test e distribuzione personalizzata. Le estensioni usate in questo articolo sono in aggiunta alle [attività di creazione/eliminazione di macchine virtuali DTL](devtest-lab-integrate-ci-cd-vsts.md):

- Creazione di un ambiente
- Eliminare un ambiente

## <a name="before-you-begin"></a>Prima di iniziare
Prima di poter integrare la pipeline CI/CD con Azure DevTest Labs, installare [Azure DevTest Labs estensione attività](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) da Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Creare e configurare il Lab per gli ambienti
Questa sezione descrive come creare e configurare un Lab in cui verrà distribuito l'ambiente Azure.

1. [Creare un Lab](devtest-lab-create-lab.md) se non ne è già presente uno. 
2. Configurare il Lab e creare un modello di ambiente seguendo le istruzioni riportate in questo articolo: [creare ambienti con più macchine virtuali e risorse PaaS con i modelli Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. Per questo esempio, usare un modello di avvio rapido di Azure esistente [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Copiare la cartella **201-Web-App-Redis-cache-SQL-database** nella cartella **ArmTemplate** nel repository configurato nel passaggio 2.

## <a name="create-a-release-definition"></a>Creare una definizione di versione
Per creare una definizione di versione, eseguire le operazioni seguenti:

1.  Nella scheda **versioni** dell' **Hub compila & versione**Selezionare il pulsante con il **segno più (+)** .
2.  Nella finestra **Crea definizione di versione** selezionare il modello **Vuoto** e quindi fare clic su **Avanti**.
3.  Selezionare **Scegli dopo** e quindi **Crea** per creare una nuova definizione di versione con un ambiente predefinito e nessun elemento collegato.
4.  Per aprire il menu di scelta rapida, nella nuova definizione di versione selezionare i **puntini di sospensione (...)** accanto al nome dell'ambiente, quindi selezionare **Configura variabili**.
5.  Nella finestra **Configura ambiente** immettere i valori seguenti per le variabili usate nelle attività della definizione di versione:
1.  Per **administratorLogin**, immettere il nome di accesso dell'amministratore di SQL.
2.  Per **administratorLoginPassword**, immettere la password da usare con l'account di accesso dell'amministratore di SQL. Usare l'icona di lucchetto per nascondere e proteggere la password.
3.  Per **DatabaseName**, immettere il nome del database SQL.
4.  Queste variabili sono specifiche per gli ambienti di esempio, diversi ambienti possono avere variabili diverse.

## <a name="create-an-environment"></a>Creare un ambiente
La fase successiva della distribuzione consiste nel creare l'ambiente da utilizzare a scopo di sviluppo o test.

1. Nella definizione di versione selezionare **Aggiungi attività**.
2. Nella scheda **attività** aggiungere un'attività Azure DevTest Labs crea ambiente. Configurare le attività in questo modo:
    1. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints) (Endpoint di servizio di Azure Resource Manager).
2. Per **nome Lab**selezionare il nome dell'istanza creata in precedenza *.
3. Per **nome repository**selezionare il repository in cui è stato eseguito il push del modello di Gestione risorse (201) a *.
4. Per **nome modello**, selezionare il nome dell'ambiente salvato nel repository del codice sorgente *. 
5. Il **nome del Lab**, il **nome del repository**e il **nome del modello** sono le rappresentazioni descrittivi degli ID delle risorse di Azure. Se si immette manualmente il nome descrittivo, verranno utilizzati gli elenchi a discesa per selezionare le informazioni.
6. Per **nome ambiente**, immettere un nome per identificare in modo univoco l'istanza dell'ambiente nel Lab.  Deve essere univoco all'interno del Lab.
7. Il **file** di parametri e i **parametri**consentono di passare parametri personalizzati all'ambiente. Per impostare i valori dei parametri, è possibile utilizzare uno o entrambi. Per questo esempio verrà usata la sezione Parameters. Usare i nomi delle variabili definite nell'ambiente, ad esempio: `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Le informazioni all'interno del modello di ambiente possono essere passate nella sezione output del modello. Selezionare **crea variabili di output in base all'output del modello di ambiente in** modo che altre attività possano usare i dati. `$(Reference name.Output Name)` è il modello da seguire. Ad esempio, se il nome di riferimento era DTL e il nome di output nel modello era il percorso, la variabile verrebbe `$(DTL.location)`.

## <a name="delete-the-environment"></a>Eliminare l'ambiente
La fase finale consiste nell'eliminare l'ambiente distribuito nell'istanza di Azure DevTest Labs. L'ambiente viene normalmente eliminato dopo l'esecuzione delle attività di sviluppo o l'esecuzione dei test necessari sulle risorse distribuite.

Nella definizione di versione selezionare **Aggiungi attività**, quindi nella scheda **Distribuisci** aggiungere un'attività **Azure DevTest Labs Elimina ambiente** . Configurare l'app come segue:

1. Per eliminare la macchina virtuale, vedere [attività Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Per **Sottoscrizione di Gestione risorse di Azure** selezionare una connessione nell'elenco **Connessioni ai servizi di Azure disponibili** oppure creare una connessione con autorizzazioni con maggiori restrizioni alla sottoscrizione di Azure. Per altre informazioni, vedere [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints) (Endpoint di servizio di Azure Resource Manager).
    2. Per **nome Lab**selezionare il Lab in cui è presente l'ambiente.
    3. Per **nome ambiente**immettere il nome dell'ambiente da rimuovere.
2. Immettere un nome per la definizione di versione e quindi salvarla.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 
- [Creare ambienti con più macchine virtuali con modelli di gestione risorse](devtest-lab-create-environment-from-arm.md).
- Guida introduttiva ai modelli di Gestione risorse per l'automazione di DevTest Labs dal [repository GitHub di DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- [Pagina risoluzione dei problemi di VSTS](/azure/devops/pipelines/troubleshooting)


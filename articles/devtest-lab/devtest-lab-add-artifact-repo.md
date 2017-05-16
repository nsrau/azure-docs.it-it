---
title: Aggiungere un repository di elementi Git a un lab in Azure DevTest Labs | Documentazione Microsoft
description: Aggiungere un repository GitHub o Git di Visual Studio Team Services per gli elementi personalizzati in Azure DevTest Labs
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 55b8b97a8cabedf86e2b92d9490be74c72a5fb09
ms.lasthandoff: 04/27/2017


---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-azure-resource-manager-templates-for-use-in-azure-devtest-labs"></a>Aggiungere un repository Git per archiviare gli elementi personalizzati e i modelli di Azure Resource Manager per l'uso in Azure DevTest Labs

Se si vogliono [creare elementi personalizzati](devtest-lab-artifact-author.md) per le macchine virtuali nel lab o [usare modelli di Azure Resource Manager per creare un ambiente di test personalizzato](devtest-lab-create-environment-from-arm.md), è necessario anche aggiungere un repository Git privato per includere gli elementi o i modelli di Azure Resource Manager che crea il team. L'archivio può essere ospitato in [GitHub](https://github.com) o [Visual Studio Team Services (VSTS)](https://visualstudio.com).

Microsoft ha offerto un [repository Github degli elementi](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) che può essere distribuito così com'è o personalizzato per i lab dell'utente. Quando si personalizza o si crea un elemento, non è possibile archiviarlo nel repository pubblico, ma è necessario creare il proprio repository privato. 

Quando si crea una macchina virtuale, è possibile salvare il modello di Azure Resource Manager, personalizzarlo se si preferisce e quindi usarlo in un secondo momento per creare facilmente altre macchine virtuali. È necessario creare il proprio repository privato per archiviare i modelli di Azure Resource Manager personalizzati.  

* Per informazioni su come creare un archivio GitHub, vedere [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Per informazioni su come creare un progetto Team Services con un archivio Git, vedere [Connessione a Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Nella schermata seguente viene illustrato un esempio di come potrebbe apparire un archivio contenente gli elementi in GitHub:   
![Esempio di archivio elementi GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Ottenere credenziali e informazioni sul repository
Per aggiungere un repository al lab, è necessario prima ottenere alcune informazioni dal repository. Nelle sezioni seguenti viene illustrato come ottenere queste informazioni per i repository ospitati in GitHub e Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Ottenere l'URL clone dell'archivio GitHub e il token di accesso personale
Per ottenere l'URL clone del repository GitHub e il token di accesso personale, seguire questi passaggi:

1. Andare alla home page del repository GitHub contenente le definizioni degli elementi o dei modelli di Azure Resource Manager.
2. Selezionare **Clona o scarica**.
3. Selezionare il pulsante per copiare l' **URL del clone HTTPS** negli Appunti e salvare l'URL per un uso successivo.
4. Selezionare l'immagine del profilo nell'angolo superiore destro di GitHub e scegliere **Impostazioni**.
5. Nel menu **Impostazioni personali** a sinistra selezionare **Token di accesso personali**.
6. Selezionare **Genera nuovo token**.
7. Nella pagina **Nuovo token di accesso personale** immettere una **Descrizione del token**, accettare le voci predefinite in **Seleziona ambiti** e quindi scegliere **Genera token**.
8. Salvare il token generato, poiché sarà necessario successivamente.
9. A questo punto, è possibile chiudere GitHub.   
10. Passare alla sezione [Connettere il lab al repository](#connect-your-lab-to-the-repository) .

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Ottenere l'URL clone del repository di Visual Studio Team Services e il token di accesso personale
Per ottenere l'URL clone del repository di Visual Studio Team Services e il token di accesso personale, seguire questi passaggi:

1. Aprire la home page della raccolta del team (ad esempio, `https://contoso-web-team.visualstudio.com`) e quindi selezionare il progetto.
2. Nella home page del progetto, selezionare **Codice**.
3. Per visualizzare l'URL del clone, nella pagina **Codice** del progetto selezionare **Clone**.
4. Salvare l'URL poiché sarà necessario più avanti in questa esercitazione.
5. Per creare un Token di accesso personale, selezionare **Il mio profilo** nel menu a discesa dell’account utente.
6. Nella pagina delle informazioni del profilo selezionare **Sicurezza**.
7. Selezionare **Aggiungi** nella scheda **Sicurezza**.
8. Nella pagina **Crea un token di accesso personale** :

   * Immettere una **Descrizione** per il token.
   * Selezionare **180 giorni** dall'elenco **Scade in**.
   * Scegliere **Tutti gli account accessibili** dall'elenco **Account**.
   * Scegliere l’opzione **Tutti gli ambiti** .
   * Scegliere **Crea token**.
9. Al termine, verrà visualizzato il nuovo token nell'elenco **Token di accesso personale** . Selezionare **Copia token**e quindi salvare il valore del token da usare in un momento successivo.
10. Passare alla sezione [Connettere il lab al repository](#connect-your-lab-to-the-repository) .

## <a name="connect-your-lab-to-the-repository"></a>Connettere il lab al repository
1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.   
4. Nel pannello del lab selezionare **Configurazione e criteri**.
5. Dal pannello **Configurazione e criteri** del lab selezionare **Repository**.
6. Nel pannello **Repository** selezionare **+Aggiungi**.

    ![Pulsante di aggiunta repository](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
7. Nel secondo pannello **Repository** specificare quanto segue:

   * **Nome** : immettere un nome per il repository.
   * **Url clone Git** :-immettere l'URL del clone Git HTTPS copiato in precedenza da GitHub o Visual Studio Team Services.
   * **Ramo**: immettere il ramo per ottenere le definizioni.
   * **Token di accesso personale** : immettere il token di accesso personale ottenuto in precedenza da GitHub o Visual Studio Team Services.
   * **Percorsi cartella**: immettere almeno un percorso della cartella relativo all'URL del clone che contiene l'elemento o le definizioni dei modelli di Azure Resource Manager. Quando si specifica una sottodirectory, verificare di includere la barra rovesciata nel percorso della cartella.

     ![Pannello del repository](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
8. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato il repository Git privato, è possibile eseguire una o entrambe le operazioni seguenti, a seconda delle esigenze:
* Archiviare gli [elementi personalizzati](devtest-lab-artifact-author.md) che potranno essere usati successivamente per creare nuove macchine virtuali.
* [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md) e quindi archiviare i modelli nel repository privato.

Quando si crea una macchina virtuale, è possibile verificare che gli elementi o i modelli vengano aggiunti al repository Git. Saranno disponibili immediatamente nell'elenco degli elementi o modelli, con il nome del repository privato indicato nella colonna che specifica l'origine. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="related-blog-posts"></a>Post di blog correlati
* [How to troubleshoot failing Artifacts in AzureDevTestLabs (Come risolvere gli errori degli elementi in Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab (Aggiungere una macchina virtuale a un dominio di AD esistente usando un modello ARM in Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)


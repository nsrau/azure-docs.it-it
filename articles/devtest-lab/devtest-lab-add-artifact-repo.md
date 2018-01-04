---
title: Aggiungere un repository Git a un lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere un repository GitHub o Git di Visual Studio Team Services per l'origine degli elementi personalizzati in Azure DevTest Labs.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-craic
ms.openlocfilehash: 345d095762b75b7a177bf4c6e7a07360a9c14068
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Aggiungere un repository Git per archiviare elementi personalizzati e modelli di Resource Manager

È possibile [creare gli elementi personalizzati](devtest-lab-artifact-author.md) per le macchine virtuali nell'ambiente lab oppure [usare modelli di Azure Resource Manager per creare un ambiente di test personalizzato](devtest-lab-create-environment-from-arm.md). È necessario aggiungere un repository Git privato per gli elementi o i modelli di Resource Manager creati dal team. Il repository può essere ospitato in [GitHub](https://github.com) o in [Visual Studio Team Services](https://visualstudio.com).

È disponibile un [repository GitHub di elementi](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) che è possibile distribuire così com'è o personalizzare per i propri lab. Quando si personalizza o si crea un elemento, non è possibile archiviarlo nel repository pubblico. È necessario creare un repository privato per gli elementi personalizzati e quelli creati. 

Quando si crea una macchina virtuale, è possibile salvare il modello di Resource Manager, personalizzarlo, se lo si desidera, e quindi usarlo successivamente per creare altre macchine virtuali. È necessario creare il proprio repository privato per archiviare i modelli di Resource Manager personalizzati.  

* Per informazioni su come creare un archivio GitHub, vedere [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Per informazioni su come creare un progetto Team Services con un repository Git, vedere [Connect to Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online) (Stabilire la connessione a Visual Studio Team Services).

La figura seguente illustra un esempio di come potrebbe apparire un repository contenente elementi in GitHub:  

![Esempio di archivio elementi GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Ottenere credenziali e informazioni sul repository
Per aggiungere un repository al lab, è necessario prima ottenere alcune informazioni importanti dal repository. Le sezioni seguenti descrivono come ottenere le informazioni necessarie per i repository ospitati in GitHub o in Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Ottenere l'URL clone dell'archivio GitHub e il token di accesso personale

1. Andare alla home page del repository GitHub contenente le definizioni degli elementi o dei modelli di Resource Manager.
2. Selezionare **Clona o scarica**.
3. Per copiare l'URL negli Appunti, fare clic sul pulsante**HTTPS clone url** (URL clone HTTPS). Salvare l'URL per usarlo in seguito.
4. Nell'angolo superiore destro di GitHub selezionare l'immagine del profilo e quindi **Settings** (Impostazioni).
5. Nel menu **Impostazioni personali** a sinistra selezionare **Token di accesso personali**.
6. Selezionare **Genera nuovo token**.
7. Nella pagina **New personal access token** (Nuovo token di accesso personale) immettere una descrizione in **Token description** (Descrizione token). Accettare gli elementi predefiniti in **Select scopes** (Selezione ambiti) e quindi selezionare **Generate Token** (Genera token).
8. Salvare il token generato. Il token verrà usato in seguito.
9. Chiudere GitHub.   
10. Passare alla sezione [Connettere il lab al repository](#connect-your-lab-to-the-repository) .

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Ottenere l'URL clone del repository di Visual Studio Team Services e il token di accesso personale

1. Passare alla home page della raccolta del team (ad esempio, https://contoso-web-team.visualstudio.com) e quindi selezionare il progetto.
2. Nella home page del progetto, selezionare **Codice**.
3. Per visualizzare l'URL del clone, nella pagina **Codice** del progetto selezionare **Clone**.
4. Salvare l'URL. L'URL verrà usato in seguito.
5. Per creare un token di accesso personale, nel menu a discesa dell'account utente selezionare **Profilo personale**.
6. Nella pagina delle informazioni del profilo selezionare **Sicurezza**.
7. Selezionare **Aggiungi** nella scheda **Sicurezza**.
8. Nella pagina **Crea un token di accesso personale**:
   1. Immettere una **Descrizione** per il token.
   2. Nell'elenco **Scadenza tra** selezionare **180 giorni**.
   3. Nell'elenco **Account** selezionare **Tutti gli account accessibili**.
   4. Selezionare l'opzione **Tutti gli ambiti**.
   5. Selezionare **Crea token**.
9. Il nuovo token verrà visualizzato nell'elenco **Token di accesso personali**. Selezionare **Copia token**e quindi salvare il valore del token da usare in un momento successivo.
10. Passare alla sezione [Connettere il lab al repository](#connect-your-lab-to-the-repository) .

## <a name="connect-your-lab-to-the-repository"></a>Connettere il lab al repository
1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** nell'elenco di servizi.
3. Nell'elenco di lab selezionare il proprio lab. 
4. Selezionare **Configurazione e criteri** > **Repository** > **+ Aggiungi**.

    ![Pulsante di aggiunta repository](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Nel secondo pannello **Repository** specificare le seguenti informazioni:
  1. **Nome**. Immettere un nome per il repository.
  2. **URL clone GIT**. Immettere l'URL del clone HTTPS Git copiato in precedenza da GitHub o Visual Studio Team Services.
  3. **Ramo**. Per ottenere le definizioni, immettere il ramo.
  4. **Token di accesso personale**. Immettere il token di accesso personale ottenuto in precedenza da GitHub o Visual Studio Team Services.
  5. **Percorsi cartella**. Immettere almeno un percorso di cartella relativo all'URL del clone che contiene le definizioni degli elementi o dei modelli di Resource Manager. Quando si specifica una sottodirectory, assicurarsi di includere la barra nel percorso della cartella.

     ![Area del repository](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selezionare **Salva**.

### <a name="related-blog-posts"></a>Post di blog correlati
* [Risolvere i problemi relativi agli errori di elemento in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Join a VM to an existing Active Directory domain by using a Resource Manager template in DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Aggiungere una macchina virtuale a un dominio di Active Directory esistente usando un modello di Resource Manager in DevTest Labs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato il repository Git privato, è possibile eseguire una o entrambe le operazioni seguenti, a seconda delle esigenze:
* Archiviare gli [elementi personalizzati](devtest-lab-artifact-author.md). Sarà possibile usarli successivamente per creare nuove macchine virtuali.
* [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md). È quindi possibile archiviare i modelli nel repository privato.

Quando si crea una VM, è possibile verificare che gli elementi o i modelli vengano aggiunti al repository Git. Saranno immediatamente disponibili nell'elenco di elementi o modelli. Il nome del repository privato è visualizzato nella colonna che specifica l'origine. 

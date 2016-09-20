<properties
	pageTitle="Aggiungere un repository di elementi Git a un lab in Azure DevTest Labs | Microsoft Azure"
	description="Aggiungere un repository GitHub o Git di Visual Studio Team Services per gli elementi personalizzati in Azure DevTest Labs"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="tarcher"/>

# Aggiungere un repository di elementi Git a un lab in Azure DevTest Labs

> [AZURE.VIDEO how-to-add-your-private-artifacts-repository-in-a-devtest-lab]

In Azure DevTest Labs gli elementi sono *azioni* come l'installazione di software o l'esecuzione di comandi e script eseguite quando viene creata una VM. Per impostazione predefinita, un lab include gli elementi dell’archivio ufficiale di Lab di sviluppo e test. È possibile aggiungere un archivio elementi di Git al lab per includere gli elementi creati dal team. L’archivio può essere ospitato in [GitHub](https://github.com) o [Visual Studio Team Services (VSTS)](https://visualstudio.com).

- Per informazioni su come creare un archivio GitHub, vedere [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
- Per informazioni su come creare un progetto Team Services con un archivio Git, vedere [Connessione a Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Nella schermata seguente viene illustrato un esempio di come potrebbe apparire un archivio contenente gli elementi in GitHub: ![Esempio di archivio elementi GitHub](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)


## Ottenere credenziali e informazioni sul repository

Per aggiungere un repository di elementi al lab, è innanzitutto necessario ottenere alcune informazioni dal repository. Nelle sezioni seguenti viene illustrato come ottenere queste informazioni per i repository di elementi ospitati in GitHub e Visual Studio Team Services.

### Ottenere l'URL clone dell'archivio GitHub e il token di accesso personale

Per ottenere l'URL clone del repository GitHub e il token di accesso personale, seguire questi passaggi:

1. Andare alla Home page del repository GitHub contenente le definizioni degli elementi.

1. Selezionare **Clona o scarica**.

1. Selezionare il pulsante per copiare l'**URL del clone HTTPS** negli Appunti e salvare l'URL per un uso successivo.

1. Selezionare l'immagine del profilo nell'angolo superiore destro di GitHub e scegliere **Impostazioni**.

1. Nel menu **Impostazioni personali** a sinistra, selezionare **Token di accesso personali**.

1. Selezionare **Genera nuovo token**.

1. Nella pagina **Nuovo token di accesso personale** immettere una **Descrizione del token**, accettare le voci predefinite in **Seleziona ambiti**, e quindi scegliere **Genera token**.

1. Salvare il token generato, poiché sarà necessario successivamente.

1. A questo punto, è possibile chiudere GitHub.

1. Passare alla sezione [Connettere il lab al repository di elementi](#connect-your-lab-to-the-artifact-repository).

### Ottenere l'URL clone del repository di Visual Studio Team Services e il token di accesso personale

Per ottenere l'URL clone del repository di Visual Studio Team Services e il token di accesso personale, seguire questi passaggi:

1. Aprire la home page della raccolta del team (ad esempio, `https://contoso-web-team.visualstudio.com`), quindi selezionare il progetto dell’elemento.

1. Nella home page del progetto, selezionare **Codice**.

1. Per visualizzare l'URL del clone, nella pagina **Codice** del progetto, selezionare **Clone**.

1. Salvare l'URL poiché sarà necessario più avanti in questa esercitazione.

1. Per creare un Token di accesso personale, selezionare **Il mio profilo** nel menu a discesa dell’account utente.

1. Nella pagina delle informazioni del profilo selezionare **Sicurezza**.

1. Nella scheda **Sicurezza** selezionare **Aggiungi**.

1. Nella pagina **Crea un token di accesso personale**:

    - Immettere una **Descrizione** per il token.
    - Selezionare **180 giorni** dall’elenco **Scade in**.
    - Scegliere **Tutti gli account accessibili** dall’elenco **Account**.
    - Scegliere l’opzione **Tutti gli ambiti**.
    - Scegliere **Crea token**.

1. Al termine, verrà visualizzato il nuovo token nell'elenco **Token di accesso personale**. Selezionare **Copia token** e quindi salvare il valore del token da usare in un momento successivo.

1. Passare alla sezione [Connettere il lab al repository di elementi](#connect-your-lab-to-the-artifact-repository).

##Connettere il lab al repository di elementi

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello del lab selezionare **Configurazione**.

1. Nel pannello **Configurazione** del lab, selezionare **Repository elementi**.

1. Nel pannello **Repository elementi** selezionare **+Aggiungi**.

	![Pulsante Aggiungi repository elementi](./media/devtest-lab-add-artifact-repo/add-artifact-repo.png)
 
1. Nel secondo pannello **Repository elementi** specificare quanto segue:

    - **Nome**: immettere un nome per il repository.
    - **Url clone Git**:-immettere l'URL del clone Git HTTPS copiato in precedenza da GitHub o Visual Studio Team Services.
    - **Percorso della cartella**: immettere il percorso della cartella relativo all'URL del clone che contiene le definizioni degli elementi.
    - **Ramo**: immettere il ramo per ottenere le definizioni degli elementi.
    - **Token di accesso personale**: immettere il token di accesso personale ottenuto in precedenza da GitHub o Visual Studio Team Services.
     
	![Pannello Repository elementi](./media/devtest-lab-add-artifact-repo/artifact-repo-blade.png)

1. Selezionare **Salva**.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Post di blog correlati
- [How to troubleshoot failing Artifacts in AzureDevTestLabs (Come risolvere gli errori degli elementi in Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab (Aggiungere una macchina virtuale a un dominio di AD esistente usando un modello ARM in Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

<!---HONumber=AcomDC_0907_2016-->
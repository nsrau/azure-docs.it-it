---
title: Estendere i laboratori di Azure DevTest usando Le funzioni di Azure Documenti Microsoft
description: Informazioni su come estendere i laboratori di sviluppo di Azure usando Funzioni di Azure.Learn how to extend Azure DevTest Labs using Azure Functions.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014360"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Usare Funzioni di Azure per estendere DevTest Labs
È possibile usare Funzioni di Azure per supportare scenari aggiuntivi oltre a quelli già supportati da DevTest Labs.You can use Azure Functions to support additional scenarios beyond the ones that are already supported by DevTest Labs. Funzioni di Azure può essere usato per estendere la funzionalità incorporata del servizio per soddisfare le esigenze specifiche dell'azienda. Nell'elenco seguente vengono forniti alcuni dei possibili scenari. In questo articolo viene illustrato come implementare uno di questi scenari di esempio.

- Fornire un riepilogo di primo livello delle macchine virtuali (VM) nel labProvide a top-level summary of virtual machines (VMs) in the Lab
- [Configurare un lab per l'uso di un gateway desktop remoto](configure-lab-remote-desktop-gateway.md)
- Report di conformità nella pagina di supporto interno
- Consentire agli utenti di completare le operazioni che richiedono un aumento delle autorizzazioni nella sottoscrizioneEnable users to complete operations that require increased permissions in the subscription
- [Avvio di flussi di lavoro basati su eventi DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Panoramica
[Funzioni di Azure](../azure-functions/functions-overview.md) è una piattaforma di elaborazione senza server in Azure.Azure Functions is a serverless computing platform in Azure. L'uso di Funzioni di Azure in una soluzione con DevTest Labs consente di aumentare le funzionalità esistenti con il proprio codice personalizzato. Per altre informazioni su Funzioni di Azure, vedere [la documentazione](../azure-functions/functions-overview.md)relativa alle funzioni di Azure.For more information on Azure Functions, see Azure Functions documentation . Per illustrare in che modo Funzioni di Azure può aiutare a soddisfare i requisiti o scenari completi in DevTest Labs, questo articolo usa un esempio di fornire un riepilogo di primo livello delle macchine virtuali nel lab come segue:To illustrate how Azure Functions can help fulfill your requirements or complete scenarios in DevTest Labs, this article uses an example of providing a top-level summary of VMs in the Lab as follows:

**Requisiti di esempio/scenario:** gli utenti possono visualizzare i dettagli su tutte le macchine virtuali in un lab, inclusi il sistema operativo, il proprietario e gli eventuali elementi applicati.  Inoltre, se l'elemento **Applica aggiornamenti** di Windows non è stato applicato di recente, esiste un modo semplice per applicarlo.

Per completare lo scenario, si utilizzeranno due funzioni come descritto nel diagramma seguente:To complete the scenario, you will use two functions as described in the following diagram:  

![Flusso complessivo](./media/extend-devtest-labs-azure-functions/flow.png)

Il codice sorgente per queste funzioni di esempio si trova nel [repository DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (sono disponibili entrambe le implementazioni di C e PowerShell).

- **UpdateInternalSupportPage:** questa funzione esegue una query devTest Labs e aggiorna direttamente la pagina di supporto interna con i dettagli sulle macchine virtuali.
- **ApplyWindowsUpdateArtifact:** per una macchina virtuale in un lab, questa funzione applica l'elemento di aggiornamento di Windows.ApplyWindowsUpdateArtifact : For a VM in a lab, this function applies the **Windows update** artifact.

## <a name="how-it-works"></a>Funzionamento
Quando gli utenti selezionano la pagina **Supporto interno** in DevTest Labs, dispongono di una pagina precompilata con informazioni su macchine virtuali, proprietari di lab e contatti di supporto.  

Quando si seleziona il pulsante **Seleziona qui per aggiornare,** la pagina chiama la prima funzione di Azure: **UpdateInternalSupportPage**. La funzione esegue una query DevTest Labs per ottenere informazioni e quindi riscrive la pagina **Supporto interno** con le nuove informazioni.

È possibile eseguire un'azione aggiuntiva per tutte le macchine virtuali in cui gli elementi di Windows Update non sono stati applicati di recente, verrà visualizzato un pulsante per applicare gli aggiornamenti di Windows alla macchina virtuale. Quando si seleziona il pulsante**Esegui aggiornamento di Windows** per una macchina virtuale, la pagina chiama la seconda funzione di Azure: **ApplyWindowsUpdateArtifact**. Questa funzione controlla se la macchina virtuale è in esecuzione e, in tal caso, applica direttamente l'elemento di [Windows Update.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)

## <a name="step-by-step-walkthrough"></a>Procedura dettagliata
In questa sezione vengono fornite istruzioni dettagliate per la configurazione delle risorse di Azure necessarie per aggiornare la pagina **Supporto interno.** In questa procedura dettagliata viene fornito un esempio di estensione di DevTest Labs.This walkthrough provides one example of extending DevTest Labs. È possibile utilizzare questo modello per altri scenari.

### <a name="step-1-create-a-service-principal"></a>Passaggio 1: Creare un'entità servizioStep 1: Create a service principal 
Il primo passaggio consiste nell'ottenere un'entità servizio con autorizzazione per la sottoscrizione che contiene il lab. L'entità servizio deve usare l'autenticazione basata su password. Può essere eseguita con [l'interfaccia della riga di comando](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)di Azure, Azure [PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)o il portale di [Azure](../active-directory/develop/howto-create-service-principal-portal.md). Se si dispone già di un'entità servizio da usare, è possibile ignorare questo passaggio.

Annotare **l'ID applicazione,** **la chiave**e l'ID **tenant** per l'entità servizio. Saranno necessari più avanti in questa procedura dettagliata. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Passaggio 2: Scaricare l'esempio e aprirlo in Visual Studio 2019Step 2: Download the sample and open in Visual Studio 2019
Scaricare localmente una copia dell'esempio di Funzioni di Azure in [C,](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) clonando il repository o scaricandolo da [qui.](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Aprire la soluzione di esempio con Visual Studio 2019.Open the sample solution with Visual Studio 2019.  
1. Installare il carico di lavoro di sviluppo di **Azure** per Visual Studio se non è già installato. Può essere installato tramite la voce di menu **Strumenti** -> **Get Tools and Features).**

    ![Carico Sviluppo di Azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Compilare la soluzione. Selezionare **Compila** e quindi **compila soluzione** voce di menu.

### <a name="step-3-deploy-the-sample-to-azure"></a>Passaggio 3: Distribuire l'esempio in AzureStep 3: Deploy the sample to Azure
In Visual Studio, nella finestra **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **AzureFunctions** e quindi scegliere **Pubblica**. Seguire la procedura guidata per completare la pubblicazione in un'app per le funzioni di Azure nuova o esistente. Per informazioni dettagliate sullo sviluppo e la distribuzione di funzioni di Azure con Visual Studio, vedere Sviluppare funzioni di [Azure con Visual Studio.](../azure-functions/functions-develop-vs.md)

![Finestra di dialogo Pubblica](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Passaggio 4: Raccogliere le impostazioni dell'applicazioneStep 4: Gather application settings
Dopo aver pubblicato le funzioni, è necessario ottenere gli URL per queste funzioni dal portale di Azure.Once the functions are published, you need to get URLs for these functions from the Azure portal. 

1. Passare al [portale di Azure](https://portal.azure.com). 
1. Trovare l'app per le funzioni.
1. Nella pagina **App** per le funzioni selezionare la funzione. 
1. Selezionare **Ottieni URL funzione** come illustrato nell'immagine seguente. 

    ![URL delle funzioni di AzureAzure functions URLs](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Copiare e salvare l'URL. Ripetere questi passaggi per l'altra funzione di Azure.Repeat these steps for the other Azure function. 

Sono inoltre necessarie informazioni aggiuntive sull'entità servizio, ad esempio l'ID applicazione, la chiave e l'ID tenant.


### <a name="step-5--update-application-settings"></a>Passaggio 5: Aggiornare le impostazioni dell'applicazioneStep 5: Update Application Settings
In Visual Studio, dopo aver pubblicato la funzione di Azure, selezionare **Modifica impostazioni servizio app di Azure** in **Azioni**. Aggiornare le seguenti impostazioni dell'applicazione (remoto):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (impostazione predefinita 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Impostazioni dell'applicazione](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Passaggio 6: Testare la funzione di AzureStep 6: Test the Azure function
L'ultimo passaggio di questa procedura dettagliata consiste nel testare la funzione di Azure.The last step in this walkthrough is to test the Azure function.  

1. Passare alla funzione UpdateInternalSupportPage nell'app per le funzioni creata nel passaggio 3.Navigate to the **UpdateInternalSupportPage** function in the function app created in the step 3. 
1. Selezionare **Test** sul lato destro della pagina. 
1. Immettere le proprietà della route (LABNAME, RESOURCEGROUPNAME e SUBSCRIPTIONID).
1. Selezionare **Esegui** per eseguire la funzione.  

    Questa funzione aggiornerà la pagina di supporto interno del lab specificato. Include anche un pulsante per consentire agli utenti di chiamare direttamente la funzione la prossima volta

    ![Funzione di test](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Passaggi successivi
Funzioni di Azure consente di estendere le funzionalità di DevTest Labs oltre a ciò che è già incorporato e aiutare i clienti a soddisfare i requisiti specifici per i team. Questo modello può essere esteso & ulteriormente ampliato per coprire ancora di più.  Per altre informazioni su DevTest Labs, vedere gli articoli seguenti:To learn more about DevTest Labs, see the following articles: 

- [Architettura di riferimento aziendale DevTest Labs](devtest-lab-reference-architecture.md)
- [Domande frequenti](devtest-lab-faq.md)
- [Scalabilità verticale dei laboratori DevTestScaling up DevTest Labs](devtest-lab-guidance-scale.md)
- [Automazione dei laboratori DevTest con PowerShellAutomating DevTest Labs with PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)









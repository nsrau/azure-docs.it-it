---
title: Estendere Azure DevTest Labs usando funzioni di Azure | Microsoft Docs
description: Informazioni su come estendere Azure DevTest Labs usando funzioni di Azure.
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
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014360"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Usare funzioni di Azure per estendere DevTest Labs
È possibile usare funzioni di Azure per supportare scenari aggiuntivi oltre a quelli già supportati da DevTest Labs. Funzioni di Azure può essere usato per estendere la funzionalità incorporata del servizio per soddisfare le esigenze specifiche dell'azienda. Nell'elenco seguente sono riportati alcuni degli scenari possibili. Questo articolo illustra come implementare uno di questi scenari di esempio.

- Fornire un riepilogo di primo livello delle macchine virtuali (VM) nel Lab
- [Configurare un Lab per l'uso di un Gateway Desktop remoto](configure-lab-remote-desktop-gateway.md)
- Report di conformità nella pagina supporto interno
- Consentire agli utenti di completare le operazioni che richiedono autorizzazioni maggiori nella sottoscrizione
- [Avvio dei flussi di lavoro basati su eventi DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Panoramica
[Funzioni di Azure](../azure-functions/functions-overview.md) è una piattaforma di elaborazione senza server in Azure. L'uso di funzioni di Azure in una soluzione con DevTest Labs consente di aumentare le funzionalità esistenti con il codice personalizzato. Per altre informazioni su funzioni di Azure, vedere la [documentazione di funzioni di Azure](../azure-functions/functions-overview.md). Per illustrare il modo in cui funzioni di Azure può soddisfare i requisiti o gli scenari completi in DevTest Labs, questo articolo usa un esempio di come fornire un riepilogo di primo livello delle macchine virtuali nel Lab come indicato di seguito:

**Requisito/scenario di esempio**: Gli utenti possono visualizzare i dettagli su tutte le macchine virtuali in un Lab, inclusi il sistema operativo, il proprietario e gli eventuali artefatti applicati.  Inoltre, se l'elemento **Apply Windows Updates** non è stato applicato di recente, esiste un modo semplice per applicarlo.

Per completare lo scenario, si utilizzeranno due funzioni come descritto nel diagramma seguente:  

![Flusso generale](./media/extend-devtest-labs-azure-functions/flow.png)

Il codice sorgente per queste funzioni di esempio si trova nel [repository GitHub di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) ( C# sono disponibili entrambe le implementazioni di PowerShell).

- **UpdateInternalSupportPage**: Questa funzione esegue una query su DevTest Labs e aggiorna la pagina del supporto interno direttamente con i dettagli sulle macchine virtuali.
- **ApplyWindowsUpdateArtifact**: Per una macchina virtuale in un Lab, questa funzione applica l'artefatto di **Windows Update** .

## <a name="how-it-works"></a>Funzionamento
Quando gli utenti selezionano la pagina di **supporto interna** in DevTest Labs, hanno una pagina già popolata con informazioni su macchine virtuali, proprietari di Lab e contatti di supporto.  

Quando si seleziona il pulsante **Seleziona qui per aggiornare** , la pagina chiama la prima funzione di Azure: **UpdateInternalSupportPage**. La funzione esegue una query su DevTest Labs per informazioni e quindi riscrive la pagina di **supporto interna** con le nuove informazioni.

È possibile eseguire un'azione aggiuntiva, per tutte le macchine virtuali in cui gli artefatti Windows Update non sono stati applicati di recente, sarà disponibile un pulsante per applicare gli aggiornamenti di Windows alla macchina virtuale. Quando si seleziona il pulsante ***Esegui Windows Update** per una macchina virtuale, la pagina chiama la seconda funzione di Azure: **ApplyWindowsUpdateArtifact**. Questa funzione controlla se la macchina virtuale è in esecuzione e, in tal caso, applica direttamente l'artefatto [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) .

## <a name="step-by-step-walkthrough"></a>Procedura dettagliata
Questa sezione fornisce istruzioni dettagliate per la configurazione delle risorse di Azure necessarie per aggiornare la pagina del **supporto interno** . Questa procedura dettagliata fornisce un esempio di estensione di DevTest Labs. È possibile usare questo modello per altri scenari.

### <a name="step-1-create-a-service-principal"></a>Passaggio 1: Creare un'entità servizio 
Il primo passaggio consiste nell'ottenere un'entità servizio con l'autorizzazione per la sottoscrizione che contiene il Lab. L'entità servizio deve usare l'autenticazione basata su password. Questa operazione può essere eseguita con l'interfaccia della riga di comando di [Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)o l' [portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md). Se si dispone già di un'entità servizio da usare, è possibile ignorare questo passaggio.

Annotare l'ID **applicazione**, la **chiave**e l' **ID tenant** per l'entità servizio. Sarà necessario più avanti in questa procedura dettagliata. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Passaggio 2: Scaricare l'esempio e aprirlo in Visual Studio 2019
Scaricare una copia dell' [ C# esempio di funzioni di Azure](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) localmente (clonando il repository o scaricando il repository da [qui](https://github.com/Azure/azure-devtestlab/archive/master.zip)).  

1. Aprire la soluzione di esempio con Visual Studio 2019.  
1. Installare il carico di lavoro di **sviluppo di Azure** per Visual Studio, se non è già installato. Può essere installato tramite **strumenti** -> **Ottieni strumenti e funzionalità voce di** menu.

    ![Carico Sviluppo di Azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Compilare la soluzione. Selezionare **Compila** , quindi la voce di menu **Compila soluzione** .

### <a name="step-3-deploy-the-sample-to-azure"></a>Passaggio 3: Distribuire l'esempio in Azure
In Visual Studio, nella finestra di **Esplora soluzioni** , fare clic con il pulsante destro del mouse sul progetto **AzureFunctions** , quindi scegliere **pubblica**. Seguire la procedura guidata per completare la pubblicazione in un app per le funzioni di Azure nuovo o esistente. Per informazioni dettagliate sullo sviluppo e la distribuzione di funzioni di Azure con Visual Studio, vedere [sviluppare funzioni di Azure con Visual Studio](../azure-functions/functions-develop-vs.md).

![Finestra di dialogo Pubblica](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Passaggio 4:  Raccolta delle impostazioni dell'applicazione
Una volta pubblicate le funzioni, è necessario ottenere gli URL per queste funzioni dal portale di Azure. 

1. Passare al [portale di Azure](https://portal.azure.com). 
1. Trovare l'app per le funzioni.
1. Nella pagina **app** per le funzioni selezionare la funzione. 
1. Selezionare **Ottieni URL funzione** come illustrato nella figura seguente. 

    ![URL di funzioni di Azure](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Copiare e salvare l'URL. Ripetere questi passaggi per l'altra funzione di Azure. 

Sono inoltre necessarie informazioni aggiuntive sull'entità servizio, ad esempio ID applicazione, chiave e ID tenant.


### <a name="step-5--update-application-settings"></a>Passaggio 5:  Aggiornare le impostazioni dell'applicazione
In Visual Studio, dopo la pubblicazione della funzione di Azure, selezionare le **Impostazioni modifica servizio app Azure** in **azioni**. Aggiornare le impostazioni dell'applicazione seguenti (remoto):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (impostazione predefinita su 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Impostazioni dell'applicazione](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Passaggio 6: Testare la funzione di Azure
L'ultimo passaggio di questa procedura dettagliata consiste nel testare la funzione di Azure.  

1. Passare alla funzione **UpdateInternalSupportPage** nell'app per le funzioni creata nel passaggio 3. 
1. Selezionare **test** sul lato destro della pagina. 
1. Immettere nelle proprietà della route (LABNAME, RESOURCEGROUPNAME e SUBSCRIPTIONID).
1. Selezionare **Esegui** per eseguire la funzione.  

    Questa funzione aggiornerà la pagina di supporto interna del Lab specificato. Include anche un pulsante che consente agli utenti di chiamare direttamente la funzione la volta successiva

    ![Test (funzione)](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Passaggi successivi
Funzioni di Azure consente di estendere la funzionalità di DevTest Labs oltre ciò che è già integrato e aiuta i clienti a soddisfare i propri requisiti specifici per i team. Questo modello può essere esteso & ulteriormente ampliato per coprire ancora di più.  Per altre informazioni su DevTest Labs, vedere gli articoli seguenti: 

- [Architettura di riferimento per DevTest Labs Enterprise](devtest-lab-reference-architecture.md)
- [Domande frequenti](devtest-lab-faq.md)
- [Scalabilità verticale di DevTest Labs](devtest-lab-guidance-scale.md)
- [Automazione di DevTest Labs con PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)









---
title: Azure Policy extension for Visual Studio Code
description: Informazioni su come usare l'estensione Dei criteri di Azure per Visual Studio Code per cercare gli alias di Resource Manager.Learn how to use the Azure Policy extension for Visual Studio Code to look up Resource Manager aliases.
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 0e9123f2975bda0d61e9d6e9bf894ecd359e6c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264674"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Usare l'estensione Dei criteri di Azure per il codice di Visual StudioUse Azure Policy extension for Visual Studio Code

> Si applica all'estensione dei criteri di Azure **versione 0.0.21** e versioni più recentiApplies to Azure Policy extension version 0.0.21 and newer

Informazioni su come usare l'estensione dei criteri di Azure per Visual Studio Code per cercare alias ed [esaminare](../concepts/definition-structure.md#aliases) risorse e criteri. In primo luogo, verrà descritto come installare l'estensione dei criteri di Azure nel codice di Visual Studio.First, we'll describe how to install the Azure Policy extension in Visual Studio Code. Poi cammineremo attraverso come cercare alias.

Azure Policy extension for Visual Studio Code can be installed on all platforms that are supported by Visual Studio Code. Questo supporto include Windows, Linux e macOS.

> [!NOTE]
> Le modifiche apportate localmente ai criteri visualizzati nell'estensione dei criteri di Azure per il codice di Visual Studio non vengono sincronizzate con Azure.Changes made locally to policies viewed in the Azure Policy extension for Visual Studio Code aren't synced to Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi in questo articolo, è necessario quanto segue:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- [Codice di Visual Studio](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Installare l'estensione Dei criteri di AzureInstall Azure Policy extension

Dopo aver soddisfatto i prerequisiti, è possibile installare l'estensione dei criteri di Azure per Visual Studio Code attenendosi alla procedura seguente:After you meet the prerequisites, you can install Azure Policy extension for Visual Studio Code by following these steps:

1. Aprire Visual Studio Code.

1. Dalla barra dei menu, vai a **Visualizza** > **estensioni**.

1. Nella casella di ricerca immettere **Criteri di Azure.**

1. Selezionare **Criteri di Azure** nei risultati della ricerca e quindi selezionare **Installa**.

1. Selezionare **Ricarica** quando necessario.

## <a name="set-the-azure-environment"></a>Configurare l'ambiente di Azure

Per un utente cloud nazionale, seguire questi passaggi per impostare prima l'ambiente Azure:For a national cloud user, follow these steps to set the Azure environment first:

1. Selezionare **File, Preferenze e Impostazioni**.

1. Ricerca nella stringa seguente: _Azure: CloudSearch_ on the following string: Azure: Cloud

1. Selezionare la nazionale dall'elenco:

   ![Impostare l'accesso al cloud di Azure predefinito per il codice di Visual StudioSet default Azure cloud sign in for Visual Studio Code](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Connettersi a un account AzureConnect to an Azure account

Per valutare le risorse e gli alias di ricerca, è necessario connettersi all'account Azure.To evaluate resources and lookup aliases, you must connect to your Azure account. Seguire questi passaggi per connettersi ad Azure dal codice di Visual Studio:Follow these steps to connect to Azure from Visual Studio Code:

1. Accedere ad Azure dall'estensione Criteri di Azure o dalla tavolozza dei comandi.

   - Estensione Criteri di AzureAzure Policy extension

     Nell'estensione Criteri di Azure selezionare **Accedi ad Azure.**

     ![Accesso al cloud di Azure per il codice di Visual Studio dall'estensione dei criteri di AzureAzure cloud sign in for Visual Studio Code from Azure Policy extension](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Riquadro comandi

     Dalla barra dei menu, vai a **Visualizza** > **tavolozza dei comandi**e immetti **Azure: Accedi**.

     ![Azure cloud sign in for Visual Studio Code from Command Palette](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Seguire le istruzioni di accesso per accedere ad Azure.Follow the sign in instructions to sign in to Azure. Dopo aver connesso, il nome dell'account azure viene visualizzato nella barra di stato nella parte inferiore della finestra Del codice di Visual Studio.After you're connected, your Azure account name is shown on the status bar at the bottom of the Visual Studio Code window.

## <a name="select-subscriptions"></a>Selezionare le sottoscrizioni

Al primo accesso, solo le risorse e i criteri di sottoscrizione predefiniti vengono caricati dall'estensione Criteri di Azure.When you first sign in, only the default subscription resources and policies are loaded by the Azure Policy extension. Per aggiungere o rimuovere sottoscrizioni dalla visualizzazione di risorse e criteri, attenersi alla seguente procedura:

1. Avviare il comando di sottoscrizione dalla tavolozza dei comandi o dal piè di pagina della finestra.

   - Tavolozza dei comandi: 

     Dalla barra dei menu passare a **Visualizza** > **tavolozza comandi**e **immettere Azure: selezionare Sottoscrizioni**.

   - Piè di pagina finestra

     Nel piè di pagina della finestra nella parte inferiore dello schermo selezionare il segmento corrispondente ad **Azure: \<\>l'account.**

1. Utilizzare la casella di filtro per trovare rapidamente le sottoscrizioni in base al nome. Controllare quindi o rimuovere il controllo da ogni sottoscrizione per impostare le sottoscrizioni visualizzate dall'estensione Criteri di Azure.Then, check or remove the check from each subscription to set the subscriptions shown by the Azure Policy extension. Al termine dell'aggiunta o della rimozione delle sottoscrizioni da visualizzare, selezionare **OK**.

## <a name="search-for-and-view-resources"></a>Cercare e visualizzare le risorse

L'estensione Criteri di Azure elenca le risorse nelle sottoscrizioni selezionate per provider di risorse e per gruppo di risorse nel riquadro **Risorse.The** Azure Policy extension lists resources in the selected subscriptions by Resource Provider and by resource group in the Resources pane. La visualizzazione albero include i seguenti raggruppamenti di risorse all'interno della sottoscrizione selezionata o a livello di sottoscrizione:

- **Provider di risorse**
  - Ogni provider di risorse registrato con risorse e risorse figlio correlate con alias dei criteri
- **Gruppi di risorse**
  - Tutte le risorse in base al gruppo di risorse in cui si trovano

Per impostazione predefinita, l'estensione filtra la parte 'Provider di risorse' in base alle risorse esistenti e alle risorse con alias di criteri. Modificare questo comportamento in Criteri di**Azure** **delle estensioni** >  **delle impostazioni** > per visualizzare tutti i provider di risorse senza filtro.

I clienti con centinaia o migliaia di risorse in una singola sottoscrizione potrebbero preferire un modo ricercabile per individuare le risorse. L'estensione Criteri di Azure consente di cercare una risorsa specifica con i passaggi seguenti:The Azure Policy extension makes it possible to search for a specific resource with the following steps:

1. Avviare l'interfaccia di ricerca dall'estensione Criteri di Azure o dalla tavolozza dei comandi.

   - Estensione Criteri di AzureAzure Policy extension

     Nell'estensione Criteri di Azure passare il puntatore del mouse sul riquadro **Risorse** e selezionare i ellissi, quindi selezionare **Cerca risorse**.

   - Tavolozza dei comandi:

     Dalla barra dei menu, vai a **Visualizza** > **tavolozza dei comandi**e inserisci **Risorse: Risorse**.

1. Se per la visualizzazione sono selezionate più sottoscrizioni, utilizzare il filtro per selezionare la sottoscrizione in cui eseguire la ricerca.

1. Usare il filtro per selezionare il gruppo di risorse in cui eseguire la ricerca che fa parte della sottoscrizione scelta in precedenza.

1. Utilizzare il filtro per selezionare la risorsa da visualizzare. Il filtro funziona sia per il nome della risorsa che per il tipo di risorsa.

## <a name="discover-aliases-for-resource-properties"></a>Individuare gli alias per le proprietà delle risorseDiscover aliases for resource properties

Quando viene selezionata una risorsa, tramite l'interfaccia di ricerca o selezionandola nella visualizzazione albero, l'estensione Criteri di Azure apre il file JSON che rappresenta tale risorsa e tutti i valori delle proprietà di Resource Manager.When a resource is selected, whether through the search interface or by selecting it in the treeview, the Azure Policy extension opens the JSON file representing that resource and all it's Resource Manager property values.

Una volta aperta una risorsa, passando il puntatore del mouse sul nome o sul valore della proprietà di Resource Manager viene visualizzato l'alias dei criteri di Azure, se presente. In questo esempio, la `Microsoft.Compute/virtualMachines` risorsa è un tipo di risorsa e la proprietà **properties.storageProfile.imageReference.offer** viene passata il mouse. Al passaggio del mouse vengono visualizzati gli alias corrispondenti.

![L'estensione Di Criteri di Azure al passaggio del mouse mostra l'alias della proprietà di Resource ManagerAzure Policy extension hover shows Resource Manager property](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Cercare e visualizzare criteri e assegnazioni

L'estensione Criteri di Azure elenca i tipi di criteri e le assegnazioni dei criteri come visualizzazione albero per le sottoscrizioni selezionate da visualizzare nel riquadro **Criteri.The** Azure Policy extension lists policy types and policy assignments as a treeview for the subscriptions selected to be displayed in the Policies pane. I clienti con centinaia o migliaia di criteri o assegnazioni in una singola sottoscrizione potrebbero preferire un modo ricercabile per individuare i criteri o le assegnazioni. L'estensione Criteri di Azure consente di cercare un criterio o un'assegnazione specifica con i passaggi seguenti:The Azure Policy extension makes it possible to search for a specific policy or assignment with the following steps:

1. Avviare l'interfaccia di ricerca dall'estensione Criteri di Azure o dalla tavolozza dei comandi.

   - Estensione Criteri di AzureAzure Policy extension

     Nell'estensione Criteri di Azure passare il puntatore del mouse sul riquadro **Criteri** e selezionare i ellissi, quindi selezionare **Criteri di ricerca**.

   - Tavolozza dei comandi:

     Dalla barra dei menu, vai a **Visualizza** > **tavolozza dei comandi**e immetti **Criteri: Criteri di ricerca**.

1. Se per la visualizzazione sono selezionate più sottoscrizioni, utilizzare il filtro per selezionare la sottoscrizione in cui eseguire la ricerca.

1. Utilizzare il filtro per selezionare il tipo di criterio o l'assegnazione in cui eseguire la ricerca che fa parte della sottoscrizione scelta in precedenza.

1. Utilizzare il filtro per selezionare il criterio o da visualizzare. Il filtro funziona per _displayName_ per la definizione dei criteri o l'assegnazione dei criteri.

Quando si seleziona un criterio o un'assegnazione, tramite l'interfaccia di ricerca o selezionandolo nella visualizzazione albero, l'estensione Criteri di Azure apre il codice JSON che rappresenta il criterio o l'assegnazione e tutti i relativi valori delle proprietà di Resource Manager.When selecting a policy or assignment, whether through the search interface or by selecting it in the treeview, the Azure Policy extension opens the JSON that represents the policy or assignment and all its Resource Manager property values. L'estensione può convalidare lo schema JSON di Criteri di Azure aperto.

## <a name="sign-out"></a>Disconnessione

Dalla barra dei menu passare a **Visualizza** > **tavolozza comandi**e quindi immettere **Azure: Esci**.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare esempi in [Esempi di criteri di Azure](../samples/index.md).Review examples at Azure Policy samples .
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Rivedere [Informazioni sugli effetti dei criteri](../concepts/effects.md).
- Comprendere come creare criteri a livello di [codice.](programmatically-create.md)
- Informazioni su come [correggere le risorse non conformi.](remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).
---
title: Estensione di criteri di Azure per Visual Studio Code
description: Informazioni su come usare l'estensione di criteri di Azure per Visual Studio Code per cercare Gestione risorse alias.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 20af29c40ec13add90294e28d64f0166acc95319
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514810"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Usare l'estensione di criteri di Azure per Visual Studio Code

> Si applica alla versione dell'estensione criteri di Azure **0.0.21** e versioni successive

Informazioni su come usare l'estensione di criteri di Azure per Visual Studio Code per cercare gli [alias](../concepts/definition-structure.md#aliases) ed esaminare le risorse e i criteri. In primo luogo, viene descritto come installare l'estensione di criteri di Azure in Visual Studio Code. Verranno quindi illustrate le procedure per la ricerca degli alias.

L'estensione di criteri di Azure per Visual Studio Code può essere installata in tutte le piattaforme supportate da Visual Studio Code. Questo supporto include Windows, Linux e macOS.

> [!NOTE]
> Le modifiche apportate localmente ai criteri visualizzati nell'estensione criteri di Azure per Visual Studio Code non vengono sincronizzate con Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure descritte in questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Installare l'estensione criteri di Azure

Dopo aver soddisfatto i prerequisiti, è possibile installare l'estensione criteri di Azure per Visual Studio Code attenendosi alla procedura seguente:

1. Aprire Visual Studio Code.

1. Dalla barra dei menu passare a **visualizza** > **estensioni**.

1. Nella casella di ricerca immettere **criteri di Azure**.

1. Selezionare **criteri di Azure** nei risultati della ricerca e quindi selezionare **Installa**.

1. Selezionare **ricarica** quando necessario.

## <a name="set-the-azure-environment"></a>Configurare l'ambiente di Azure

Per un utente cloud nazionale, attenersi alla procedura seguente per impostare prima l'ambiente Azure:

1. Selezionare **File\Preferences\Settings**.

1. Eseguire una ricerca nella stringa seguente: _Azure: cloud_

1. Selezionare il cloud di Nation dall'elenco:

   ![Impostare l'accesso cloud predefinito di Azure per Visual Studio Code](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Connettersi a un account Azure

Per valutare le risorse e gli alias di ricerca, è necessario connettersi all'account Azure. Per connettersi ad Azure da Visual Studio Code, seguire questa procedura:

1. Accedere ad Azure dall'estensione criteri di Azure o dal riquadro comandi.

   - Estensione criteri di Azure

     Dall'estensione criteri di Azure selezionare **Accedi ad Azure**.

     ![Accesso al cloud di Azure per Visual Studio Code dall'estensione criteri di Azure](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Riquadro comandi

     Dalla barra dei menu passare a **visualizza** > **riquadro comandi**e immettere **Azure: Sign in (accedi**).

     ![Accesso al cloud di Azure per Visual Studio Code dal riquadro comandi](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Seguire le istruzioni di accesso per accedere ad Azure. Dopo la connessione, il nome dell'account Azure viene visualizzato sulla barra di stato nella parte inferiore della finestra di Visual Studio Code.

## <a name="select-subscriptions"></a>Selezionare le sottoscrizioni

Quando si accede per la prima volta, solo le risorse e i criteri di sottoscrizione predefiniti vengono caricati dall'estensione criteri di Azure. Per aggiungere o rimuovere sottoscrizioni dalla visualizzazione di risorse e criteri, attenersi alla procedura seguente:

1. Avviare il comando Subscription dal riquadro comandi o dal piè di pagina della finestra.

   - Tavolozza comandi: 

     Dalla barra dei menu passare a **visualizza** > **riquadro comandi**e immettere criteri di **Azure: selezionare le sottoscrizioni**.

   - Piè di pagina della finestra

     Nel piè di pagina della finestra nella parte inferiore della schermata selezionare il segmento che corrisponde ad **Azure: \<l'account\>** .

1. Utilizzare la casella filtro per trovare rapidamente le sottoscrizioni in base al nome. Quindi, selezionare o rimuovere il controllo da ogni sottoscrizione per impostare le sottoscrizioni visualizzate dall'estensione criteri di Azure. Al termine dell'aggiunta o della rimozione delle sottoscrizioni da visualizzare, selezionare **OK**.

## <a name="search-for-and-view-resources"></a>Cercare e visualizzare le risorse

L'estensione criteri di Azure elenca le risorse nelle sottoscrizioni selezionate per provider di risorse e per gruppo di risorse nel riquadro **risorse** . In TreeView sono inclusi i seguenti raggruppamenti di risorse all'interno della sottoscrizione selezionata o a livello di sottoscrizione:

- **Provider di risorse**
  - Ogni provider di risorse registrato con risorse e risorse figlio correlate con alias di criteri
- **Gruppi di risorse**
  - Tutte le risorse del gruppo di risorse in cui si trovano

Per impostazione predefinita, l'estensione filtra la parte "provider di risorse" per le risorse e le risorse esistenti con alias dei criteri. Modificare questo comportamento in **Settings** > **Extensions** > **criteri di Azure** per visualizzare tutti i provider di risorse senza filtro.

I clienti con centinaia o migliaia di risorse in una singola sottoscrizione possono preferire un metodo ricercabile per individuare le risorse. L'estensione criteri di Azure consente di cercare una risorsa specifica con i passaggi seguenti:

1. Avviare l'interfaccia di ricerca dall'estensione criteri di Azure o dal riquadro comandi.

   - Estensione criteri di Azure

     Dall'estensione criteri di Azure passare il puntatore del mouse sul pannello **risorse** e selezionare i puntini di sospensione, quindi selezionare **Cerca risorse**.

   - Tavolozza comandi:

     Dalla barra dei menu passare a **visualizza** > **riquadro comandi**e immettere **risorse: Cerca risorse**.

1. Se per la visualizzazione è selezionata più di una sottoscrizione, usare il filtro per selezionare la sottoscrizione in cui eseguire la ricerca.

1. Usare il filtro per selezionare il gruppo di risorse in cui eseguire la ricerca che fa parte della sottoscrizione selezionata in precedenza.

1. Usare il filtro per selezionare la risorsa da visualizzare. Il filtro funziona sia per il nome della risorsa che per il tipo di risorsa.

## <a name="discover-aliases-for-resource-properties"></a>Individuare gli alias per le proprietà delle risorse

Quando si seleziona una risorsa, tramite l'interfaccia di ricerca o selezionandola in TreeView, l'estensione di criteri di Azure apre il file JSON che rappresenta la risorsa e tutti i valori Gestione risorse proprietà.

Una volta aperta una risorsa, il puntatore del mouse sul nome o sul valore della proprietà Gestione risorse Visualizza l'alias di criteri di Azure, se disponibile. In questo esempio, la risorsa è un tipo di risorsa `Microsoft.Compute/virtualMachines` e la proprietà **Properties. storageProfile. imageReference. offer** viene posizionata sopra. Il passaggio del mouse Mostra gli alias corrispondenti.

![Il passaggio del mouse sull'estensione criteri di Azure Mostra Gestione risorse alias proprietà](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Cercare e visualizzare i criteri e le assegnazioni

L'estensione criteri di Azure elenca i tipi di criteri e le assegnazioni di criteri come TreeView per le sottoscrizioni selezionate per la visualizzazione nel riquadro **criteri** . I clienti con centinaia o migliaia di criteri o assegnazioni in una singola sottoscrizione possono preferire un metodo ricercabile per individuare i criteri o le assegnazioni. L'estensione criteri di Azure consente di cercare un criterio o un'assegnazione specifica con i passaggi seguenti:

1. Avviare l'interfaccia di ricerca dall'estensione criteri di Azure o dal riquadro comandi.

   - Estensione criteri di Azure

     Dall'estensione criteri di Azure passare il puntatore del mouse sul pannello **criteri** e selezionare i puntini di sospensione, quindi selezionare **criteri di ricerca**.

   - Tavolozza comandi:

     Dalla barra dei menu passare a **visualizza** > **riquadro comandi**e immettere **criteri: Cerca criteri**.

1. Se per la visualizzazione è selezionata più di una sottoscrizione, usare il filtro per selezionare la sottoscrizione in cui eseguire la ricerca.

1. Utilizzare il filtro per selezionare il tipo di criteri o l'assegnazione da ricercare che fa parte della sottoscrizione scelta in precedenza.

1. Utilizzare il filtro per selezionare i criteri o da visualizzare. Il filtro funziona per _DisplayName_ per la definizione dei criteri o l'assegnazione di criteri.

Quando si seleziona un criterio o un'assegnazione, tramite l'interfaccia di ricerca o selezionandola in TreeView, l'estensione di criteri di Azure apre il JSON che rappresenta il criterio o l'assegnazione e tutti i relativi valori Gestione risorse proprietà. L'estensione può convalidare lo schema JSON di criteri di Azure aperto.

## <a name="sign-out"></a>Disconnessione

Dalla barra dei menu passare a **visualizza** > **riquadro comandi**e quindi immettere **Azure: disconnettersi**.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come monitorare e [aggiornare le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
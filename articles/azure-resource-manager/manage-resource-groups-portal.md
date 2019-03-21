---
title: Gestire i gruppi di Azure Resource Manager usando il portale di Azure | Microsoft Docs
description: Usare il portale di Azure per gestire i gruppi di Azure Resource Manager.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: cbbc710c9408c84c601d7b9eb9560641b5313a05
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090934"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gestire i gruppi di risorse di Azure Resource Manager usando il portale di Azure

Informazioni su come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) per gestire i gruppi di risorse di Azure. Per gestire le risorse di Azure, vedere [la gestione di Azure Resource Manager usando il portale di Azure](./manage-resources-portal.md).

Altri articoli sulla gestione dei gruppi di risorse:

- [Gestire i gruppi di risorse di Azure utilizzando CLI di Azure](./manage-resources-cli.md)
- [Gestire i gruppi di risorse di Azure usando Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Che cos'è un gruppo di risorse

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica in cui vengono archiviati i metadati.

## <a name="create-resource-groups"></a>Creare gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **gruppi di risorse**

    ![aggiungere un gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selezionare **Aggiungi**.
4. Immettere i valori seguenti:

   - **Sottoscrizione** Selezionare la sottoscrizione di Azure. 
   - **Gruppo di risorse**: Immettere il nome di un nuovo gruppo di risorse. 
   - **Area**: Selezionare una località di Azure, ad esempio **Stati Uniti centrali**.

     ![Creare un gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selezionare **Rivedi e crea**
6. Selezionare **Create**. Bastano pochi secondi per creare un gruppo di risorse.
7. Selezionare **Aggiorna** dal menu in alto per aggiornare l'elenco di gruppi di risorse e quindi selezionare il gruppo di risorse appena creato per aprirlo. Oppure selezionare **Notification**(l'icona a forma di campanello) dall'inizio, quindi selezionare **passare al gruppo di risorse** per aprire il gruppo di risorse appena creato

    ![passare al gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Elencare i gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Per elencare i gruppi di risorse, selezionare **gruppi di risorse**

    ![esplorare i gruppi di risorse](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Per personalizzare le informazioni visualizzate per i gruppi di risorse, selezionare **Upravit sloupce**. Lo screenshot seguente mostra le colonne di aggiunta che è possibile aggiungere alla visualizzazione:

## <a name="open-resource-groups"></a>Gruppi di risorse Open

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Gruppi di risorse**.
3. Selezionare il gruppo di risorse che si desidera aprire.

## <a name="delete-resource-groups"></a>Eliminare gruppi di risorse

1. Aprire il gruppo di risorse che si desidera eliminare.  Visualizzare [aprire gruppi di risorse](#open-resource-groups).
2. Selezionare **Elimina gruppo di risorse**.

    ![eliminare il gruppo di risorse di azure](./media/manage-resource-groups-portal/delete-group.png)

Per altre informazioni sulla modalità Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [eliminazione del gruppo di risorse Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Distribuire risorse in un gruppo di risorse

Dopo aver creato un modello di Resource Manager, è possibile usare il portale di Azure per distribuire le risorse di Azure. Per creare un modello, vedere [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Per distribuire un modello usando il portale, vedere [distribuire le risorse con modelli di Resource Manager e portale di Azure](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Spostare in un gruppo di risorse o sottoscrizione

È possibile spostare le risorse nel gruppo a un altro gruppo di risorse. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Gruppi di risorse di blocco

Il blocco impedisce ad altri utenti nell'organizzazione modifichino o eliminino accidentalmente risorse critiche, come sottoscrizione di Azure, gruppo di risorse o risorsa. 

1. Aprire il gruppo di risorse che si desidera eliminare.  Visualizzare [aprire gruppi di risorse](#open-resource-groups).
2. Nel riquadro sinistro, selezionare **blocca**.
3. Per aggiungere un blocco per il gruppo di risorse, selezionare **Add**.
4. Immettere **il nome del blocco**, **tipo di blocco**, e **note**. I tipi di blocco includono **Read-only**, e **eliminare**.

    ![gruppo di risorse di azure di blocco](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Per altre informazioni, vedere [bloccare le risorse per impedire modifiche impreviste](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Gruppi di risorse di tag

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni, vedere [usando i tag per organizzare le risorse di Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Esportare i gruppi di risorse in modelli

Dopo aver impostato correttamente il gruppo di risorse, è possibile visualizzare il modello di Resource Manager per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

- Automatizzare le distribuzioni future della soluzione perché il modello contiene l'infrastruttura completa.
- Informazioni su sintassi del modello esaminando in oggetto notazione JSON (JavaScript) che rappresenta la soluzione.

Per esportare un modello sono disponibili due modi:

- È possibile esportare il modello effettivo usato per la distribuzione. Il modello esportato include tutti i parametri e le variabili uguali a quelli visualizzati nel modello originale. Questo approccio è utile quando si sono distribuite risorse tramite il portale e si vuole visualizzare il modello con cui sono state create. Il modello è immediatamente utilizzabile. 
- È possibile esportare un modello generato che rappresenta lo stato corrente del gruppo di risorse. Il modello esportato non si basa su un modello qualsiasi usato per la distribuzione, ma crea un modello che è uno "snapshot" o un "backup" del gruppo di risorse. Il modello esportato ha diversi valori hardcoded e probabilmente meno parametri di quelli che si definiscono in genere. Usare questa opzione per ridistribuire le risorse nello stesso gruppo di risorse. Per usare questo modello per un altro gruppo di risorse, è necessario apportare alcune importanti modifiche.

### <a name="export-templates-from-deployment-history"></a>Esportare i modelli dalla cronologia di distribuzione

Questo metodo consente di esportare i modelli per alcune distribuzioni. Se è stata modificata le risorse dal portale o risorse aggiunte/rimosse in distribuzioni multiple, vedere [esportare modelli da gruppi di risorse](#export-templates-from-resource-groups).

1. Aprire il gruppo di risorse che si desidera eliminare.  Visualizzare [aprire gruppi di risorse](#open-resource-groups).
2. Nel riquadro sinistro, selezionare **distribuzioni**, o selezionare il collegamento sotto **distribuzioni**.  Nella schermata seguente viene illustrato **Succeeded 4** perché si sono verificati quattro distribuzioni separate con quattro i nomi di distribuzione diversi. Si noterà **1 riuscito**.

    ![modelli di esportazione di gruppo di risorse di Azure](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history.png)

3. Selezionare una delle distribuzioni dall'elenco.
4. Nel riquadro sinistro, selezionare **modello**. Resource Manager recupera i sei file seguenti:

   - **Modello** : modello che definisce l'infrastruttura per la soluzione. Quando è stato creato l'account di archiviazione tramite il portale, Resource Manager ha usato un modello per distribuirlo e ha salvato tale modello come riferimento futuro.
   - **Parametri** : file dei parametri che può essere usato per passare i valori durante la distribuzione. Contiene i valori specificati durante la prima distribuzione. Quando si ridistribuisce il modello è possibile modificare qualsiasi valore.
   - **Interfaccia della riga di comando**: file di script dell'interfaccia della riga di comando di Azure che può essere usato per distribuire il modello.
   - **PowerShell** : file di script di Azure PowerShell che può essere usato per distribuire il modello.
   - **.NET** : classe .NET che può essere usata per distribuire il modello.
   - **Ruby** : classe Ruby che può essere usata per distribuire il modello.

     Per impostazione predefinita, il portale visualizza il modello.

5. Selezionare **scaricare** per esportare un modello nel computer locale.

    ![modelli di esportazione di gruppo di risorse di Azure](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history-download.png)

<a name="export-templates-from-resource-groups"></a>
### <a name="export-templates-from-resource-groups"></a>Esportare modelli da gruppi di risorse

Se si aver modificato le risorse dal portale o aggiungere o rimuovere le risorse in più distribuzioni, il modello recuperato dalla cronologia delle distribuzioni non riflette lo stato corrente del gruppo di risorse. Questa sezione illustra come esportare un modello che rispecchia tale stato. Si tratta di uno snapshot del gruppo di risorse, che è possibile usare per la ridistribuzione nello stesso gruppo di risorse. Per usare il modello esportato per altre soluzioni, è necessario apportare alcune importanti modifiche.

1. Aprire il gruppo di risorse che si desidera eliminare.  Visualizzare [aprire gruppi di risorse](#open-resource-groups).
2. Nel riquadro sinistro, selezionare **script di automazione**. Resource Manager recupera i sei file seguenti:

   - **Modello** : modello che definisce l'infrastruttura per la soluzione. Quando è stato creato l'account di archiviazione tramite il portale, Resource Manager ha usato un modello per distribuirlo e ha salvato tale modello come riferimento futuro.
   - **Parametri** : file dei parametri che può essere usato per passare i valori durante la distribuzione. Contiene i valori specificati durante la prima distribuzione. Quando si ridistribuisce il modello è possibile modificare qualsiasi valore.
   - **Interfaccia della riga di comando**: file di script dell'interfaccia della riga di comando di Azure che può essere usato per distribuire il modello.
   - **PowerShell** : file di script di Azure PowerShell che può essere usato per distribuire il modello.
   - **.NET** : classe .NET che può essere usata per distribuire il modello.
   - **Ruby** : classe Ruby che può essere usata per distribuire il modello.

     Per impostazione predefinita, il portale visualizza il modello.
3. Selezionare **scaricare** per esportare un modello nel computer locale.

Alcuni modelli esportati necessarie alcune modifiche prima di poter essere usati. Per informazioni su come sviluppare modelli, vedere la [esercitazioni dettagliate](/azure/azure-resource-manager/).

### <a name="export-template-before-deploying"></a>Esporta modello prima della distribuzione

È possibile usare il portale per definire una risorsa.  Prima di distribuire la risorsa, è possibile visualizzare ed esportare un modello. Per le istruzioni, vedere [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md).

### <a name="fix-export-issues"></a>Risolvere i problemi di esportazione

Non tutti i tipi di risorse supportano la funzione di esportazione del modello. Si verificano problemi di esportazione solo quando si esporta da un gruppo di risorse invece che dalla cronologia della distribuzione. Se la distribuzione più recente rappresenta con precisione lo stato corrente del gruppo di risorse, è consigliabile esportare il modello dalla cronologia della distribuzione invece che dal gruppo di risorse. Eseguire l'esportazione da un gruppo di risorse solo quando sono state apportate al gruppo di risorse modifiche non definite in un singolo modello.

Per risolvere i problemi di esportazione, aggiungere manualmente le risorse mancanti al modello. Il messaggio di errore include i tipi di risorsa che non possono essere esportati. Trovare il tipo di risorsa nelle [informazioni di riferimento sui modelli](/azure/templates/). Per aggiungere manualmente un gateway di rete virtuale, ad esempio, vedere le [informazioni di riferimento sul modello Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways). Il riferimento al modello fornisce il formato JSON per aggiungere la risorsa al modello.

Dopo avere ottenuto il formato JSON per la risorsa, è necessario ottenere i valori della risorsa. È possibile ottenere i valori per la risorsa usando l'operazione GET nell'API REST per il tipo di risorsa. Ad esempio, per ottenere i valori per il gateway di rete virtuale, vedere [Gateway di rete virtuale - Get](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="manage-access-to-resource-groups"></a>Gestire l'accesso ai gruppi di risorse

[Il controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per altre informazioni sulla sintassi del modello di Resource Manager, vedere [comprendere la struttura e sintassi di modelli Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere la [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere [riferimento a un modello](/azure/templates/).
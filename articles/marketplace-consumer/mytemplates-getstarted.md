---
title: Introduzione ai modelli privati | Documentazione Microsoft
description: Aggiungere, gestire e condividere modelli privati usando il portale di Azure, l&quot;interfaccia della riga di comando di Azure o PowerShell.
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: 01657619cbe579c6818a790cc3ab95a33936a565


---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Introduzione ai modelli privati nel portale di Azure
Un modello di [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) è un modello dichiarativo usato per definire la distribuzione. Permette di definire le risorse da distribuire per una soluzione e di specificare i parametri e le variabili che consentono di immettere valori per diversi ambienti. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione.

È possibile usare la nuova funzionalità **Modelli** nel [portale di Azure](https://portal.azure.com) insieme al provider di risorse **Microsoft.Gallery** come estensione di [Azure Marketplace](https://azure.microsoft.com/marketplace/), per consentire agli utenti di creare, gestire e distribuire modelli privati da una raccolta personale.

Questo documento descrive come aggiungere, gestire e condividere un **modello** privato con il portale di Azure.

## <a name="guidance"></a>Indicazioni
I suggerimenti riportati di seguito permettono di sfruttare al meglio i **modelli** per l'uso delle soluzioni:

* Un **modello** è una risorsa di incapsulamento contenente un modello di Resource Manager e metadati aggiuntivi. Si comporta in modo molto simile a un elemento di Marketplace. La differenza principale è che si tratta di un elemento privato, mentre gli elementi di Marketplace sono pubblici.
* La raccolta **Modelli** è efficace per gli utenti che vogliono personalizzare le distribuzioni.
* **modelli** permettono di usare un repository semplice all'interno di Azure.
* Iniziare con un modello di Resource Manager esistente. In [GitHub](https://github.com/Azure/azure-quickstart-templates) o nel post di blog relativo all'[esportazione modelli](../azure-resource-manager/resource-manager-export-template.md) è possibile trovare modelli a partire da un gruppo di risorse esistente.
* **modelli** sono legati all'utente che li pubblica. Il nome dell'autore è visibile a chiunque abbia accesso in lettura.
* **modelli** sono risorse di Resource Manager e non possono essere rinominati dopo la pubblicazione.

## <a name="add-a-template-resource"></a>Aggiungere una risorsa modello
Per creare una risorsa **modello** nel portale di Azure è possibile procedere in due modi.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Metodo 1: creare una nuova risorsa modello da un gruppo di risorse in esecuzione
1. Passare a un gruppo di risorse esistente nel portale di Azure. In **Impostazioni** selezionare **Esporta modello**.
2. Dopo aver esportato il modello di Resource Manager, fare clic sul pulsante **Salva modello** per salvarlo nel repository **Modelli**. Per informazioni dettagliate sull'esportazione di modelli, vedere questa [pagina](../azure-resource-manager/resource-manager-export-template.md).
   <br /><br />
   ![Esportazione di un gruppo di risorse](media/rg-export-portal1.PNG)  <br />
3. Fare clic sul pulsante di comando **Salva modello** .
   <br /><br />
4. Immettere le seguenti informazioni:
   
   * Nome: nome dell'oggetto modello. Nota: il nome è basato su Azure Resource Manager. È soggetto a tutte le limitazioni relative all'assegnazione dei nomi non può essere modificato dopo la creazione.
   * Descrizione: breve descrizione del modello.
     
     ![Salva modello](media/save-template-portal1.PNG)  <br />
5. Fare clic su **Save**.
   
   > [!NOTE]
   > In caso di errori del modello di Resource Manager esportato, le notifiche vengono visualizzate nel pannello Esporta modello. Sarà comunque possibile salvare il modello di Resource Manager nei modelli. Assicurarsi di controllare e correggere eventuali problemi del modello di Resource Manager prima di ridistribuire il modello di Resource Manager esportato.
   > 
   > 

### <a name="method-2--add-a-new-template-resource-from-browse"></a>Metodo 2: aggiungere una nuova risorsa modello dall'esplorazione
È anche possibile aggiungere un nuovo **modello** da zero usando il pulsante di comando +Aggiungi in **Esplora > Modelli**. È necessario specificare un nome, una descrizione e il file JSON del modello di Resource Manager.

![Aggiungi modello](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> Microsoft.Gallery è un provider di risorse di Azure basato su tenant. La risorsa modello è associata all'utente che l'ha creata. Non è associata a una sottoscrizione specifica. È necessario scegliere una sottoscrizione solo quando si distribuisce un modello.
> 
> 

## <a name="view-template-resources"></a>Visualizzare le risorse modello
È possibile visualizzare tutti i **modelli** disponibili in **Esplora > Modelli**. Sono inclusi i **modelli** creati dall'utente e quelli condivisi con l'utente a vari livelli di autorizzazione. Per altre informazioni sul [controllo di accesso](#access-control-for-a-tenant-resource-provider) , vedere la relativa sezione più avanti in questo articolo.

![Visualizza modello](media/view-template-portal1.PNG)  <br />

Per visualizzare i dettagli di un **modello** è possibile fare clic su un elemento nell'elenco.

![Visualizza modello](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Modificare una risorsa modello
Per avviare il flusso di modifica per un **modello** , fare clic con il pulsante destro del mouse sull'elemento nell'elenco di ricerca o scegliere il pulsante di comando Modifica.

![Modifica modello](media/edit-template-portal1a.PNG)  <br />

È possibile modificare la descrizione o il testo del modello di Resource Manager. Dato che si tratta di un nome di risorsa di Resource Manager, non è possibile modificare il nome. Quando si modifica il file JSON del modello di Resource Manager, viene eseguita la convalida per verificare che si tratti di un file JSON valido. Scegliere **OK** e quindi **Salva** per salvare il modello aggiornato.

![Modifica modello](media/edit-template-portal2a.PNG)  <br />

Verrà visualizzata una notifica che conferma il salvataggio del **modello** .

![Modifica modello](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Distribuire una risorsa modello
È possibile distribuire qualsiasi **modello** per cui si hanno autorizzazioni di **lettura**. Il flusso di distribuzione consente di avviare il pannello di distribuzione del modello di Azure standard. Compilare i valori per i parametri del modello di Resource Manager per procedere con la distribuzione.

![Modello di distribuzione](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Condividere una risorsa modello
È possibile condividere le risorse **modello** con altri utenti. La condivisione funziona in modo simile all' [assegnazione di ruoli per qualsiasi risorsa di Azure](../active-directory/role-based-access-control-configure.md). Il proprietario del **modello** fornisce le autorizzazioni ad altri utenti, che possono interagire con la risorsa modello. L'utente o il gruppo di utenti con cui viene condiviso il **modello** può visualizzare il modello di Resource Manager e le relative proprietà della raccolta.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Controllo di accesso per le risorse Microsoft.Gallery
| Ruolo | Autorizzazioni |
| --- | --- |
| Proprietario |Consente il controllo completo sulla risorsa modello, inclusa la condivisione. |
| Lettore |Consente l'autorizzazione di lettura ed esecuzione o distribuzione sulla risorsa modello. |
| Collaboratore |Consente l'autorizzazione di modifica ed eliminazione sulla risorsa modello. L'utente non può condividere il modello con altri. |

Fare clic con il pulsante destro del mouse sul pannello di visualizzazione di un elemento specifico e selezionare **Condividi** . Verrà avviata la condivisione.

![Condividi modello](media/share-template-portal1a.png)  <br />

 È possibile scegliere un ruolo e un utente o un gruppo a cui fornire l'accesso a un **modello**specifico. I ruoli disponibili sono Proprietario, Lettore e Collaboratore. Per altre informazioni sul [controllo di accesso](#access-control-for-a-tenant-resource-provider) , vedere la relativa sezione precedente.

![Condividi modello](media/share-template-portal2b.png)  <br />

![Condividi modello](media/share-template-portal3b.png)  <br />

Fare clic su **Seleziona** e quindi su **OK**. Ora è possibile visualizzare gli utenti o i gruppi aggiunti alla risorsa.

![Condividi modello](media/share-template-portal4b.png)  <br />

> [!NOTE]
> Un modello può essere condiviso solo con utenti e gruppi nello stesso tenant di Azure Active Directory. Se si condivide un modello con un indirizzo di posta elettronica che non è incluso nel tenant, viene inviato un invito a entrare nel tenant come guest.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla creazione di modelli di Resource Manager, vedere [Creazione di modelli](../azure-resource-manager/resource-group-authoring-templates.md)
* Per informazioni sulle funzioni disponibili in un modello di Resource Manager, vedere [Funzioni del modello](../azure-resource-manager/resource-group-template-functions.md)
* Per informazioni aggiuntive sulla progettazione di modelli, vedere [Procedure consigliate per la progettazione di modelli di Gestione risorse di Azure](../azure-resource-manager/best-practices-resource-manager-design-templates.md)




<!--HONumber=Feb17_HO3-->



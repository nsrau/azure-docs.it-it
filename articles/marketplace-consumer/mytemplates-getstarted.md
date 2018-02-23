---
title: Introduzione ai modelli privati | Documentazione Microsoft
description: Aggiungere, gestire e condividere modelli privati usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.
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
ms.openlocfilehash: c890339ba7677b23717a6e0437b5e936fdf8ab03
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Introduzione ai modelli privati nel portale di Azure
Un modello di [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) è un modello dichiarativo usato per definire la distribuzione. Permette di definire le risorse da distribuire per una soluzione e di specificare i parametri e le variabili che consentono di immettere valori per diversi ambienti. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione.

È possibile usare la nuova funzionalità **Modelli** nel [portale di Azure](https://portal.azure.com) insieme al provider di risorse **Microsoft.Gallery** come estensione di [Azure Marketplace](https://azure.microsoft.com/marketplace/), per consentire agli utenti di creare, gestire e distribuire modelli privati da una raccolta personale.

> [!NOTE]
> Invece di usare i modelli privati nel portale, Microsoft consiglia di creare un'applicazione di catalogo di servizi tramite [Applicazioni gestite](../managed-applications/overview.md). È possibile rendere disponibile l'applicazione di catalogo di servizi agli utenti dell'organizzazione.

Questo documento descrive come aggiungere, gestire e condividere un **modello** privato con il portale di Azure.

## <a name="guidance"></a>Indicazioni
I suggerimenti riportati di seguito permettono di sfruttare al meglio i **modelli** per l'uso delle soluzioni:

* Un **modello** è una risorsa di incapsulamento contenente un modello di Resource Manager e metadati aggiuntivi. Si comporta in modo simile a un elemento di Marketplace. La differenza principale è che si tratta di un elemento privato, mentre gli elementi di Marketplace sono pubblici.
* La raccolta **Modelli** è efficace per gli utenti che vogliono personalizzare le distribuzioni.
* **modelli** permettono di usare un repository semplice all'interno di Azure.
* Iniziare con un modello di Resource Manager esistente. In [GitHub](https://github.com/Azure/azure-quickstart-templates) o nel post di blog relativo all'[esportazione modelli](../azure-resource-manager/resource-manager-export-template.md) è possibile trovare modelli a partire da un gruppo di risorse esistente.
* **modelli** sono legati all'utente che li pubblica. Il nome dell'autore è visibile a chiunque abbia accesso in lettura.
* **modelli** sono risorse di Resource Manager e non possono essere rinominati dopo la pubblicazione.

## <a name="add-a-template-resource"></a>Aggiungere una risorsa modello
Per creare una risorsa **modello** nel portale di Azure è possibile procedere in due modi.

### <a name="method-1-create-a-new-template-resource-from-a-running-resource-group"></a>Metodo 1: creare una nuova risorsa modello da un gruppo di risorse in esecuzione
1. Passare a un gruppo di risorse esistente nel portale di Azure. In **Impostazioni** selezionare **Esporta modello**.
2. Dopo aver esportato il modello di Resource Manager, fare clic sul pulsante **Salva modello** per salvarlo nel repository **Modelli**. Per informazioni dettagliate sull'esportazione di modelli, vedere questa [pagina](../azure-resource-manager/resource-manager-export-template.md).
   <br /><br />
   ![Esportazione di un gruppo di risorse](media/rg-export-portal1.PNG)
3. Fare clic sul pulsante di comando **Salva modello** .
   <br /><br />
4. Immettere le seguenti informazioni:
   
   * Nome: nome dell'oggetto modello. Nota: questo campo è un nome basato su Azure Resource Manager. È soggetto a tutte le limitazioni relative all'assegnazione dei nomi non può essere modificato dopo la creazione.
   * Descrizione: breve descrizione del modello.
     
     ![Salva modello](media/save-template-portal1.PNG)
5. Fare clic su **Save**.
   
   > [!NOTE]
   > In caso di errori del modello di Resource Manager esportato, nel portale vengono visualizzate notifiche. Sarà comunque possibile salvare il modello di Resource Manager nei modelli. Assicurarsi di controllare e correggere eventuali problemi del modello di Resource Manager prima di ridistribuire il modello di Resource Manager esportato.
   > 
   > 

### <a name="method-2-add-a-new-template-resource-from-browse"></a>Metodo 2: aggiungere una nuova risorsa modello dall'esplorazione
È anche possibile aggiungere un nuovo **modello** da zero usando il pulsante di comando +Aggiungi in **Esplora > Modelli**. Specificare un nome, una descrizione e il file JSON del modello di Resource Manager.

![Aggiungi modello](media/add-template-portal1.PNG)

> [!NOTE]
> Microsoft.Gallery è un provider di risorse di Azure basato su tenant. La risorsa modello è associata all'utente che l'ha creata. Non è associata a una sottoscrizione specifica. Scegliere una sottoscrizione durante la distribuzione di un modello.
> 
> 

## <a name="view-template-resources"></a>Visualizzare le risorse modello
È possibile visualizzare tutti i **modelli** disponibili in **Esplora > Modelli**. I modelli disponibili includono i modelli creati dall'utente e quelli condivisi con l'utente a vari livelli di autorizzazione. Per altre informazioni, vedere [controllo di accesso](#access-control-for-a-tenant-resource-provider).

![Visualizza modello](media/view-template-portal1.PNG)

Per visualizzare i dettagli di un **modello** è possibile fare clic su un elemento nell'elenco.

![Visualizza modello](media/view-template-portal2c.png)

## <a name="edit-a-template-resource"></a>Modificare una risorsa modello
Per avviare il flusso di modifica per un **modello** , fare clic con il pulsante destro del mouse sull'elemento nell'elenco di ricerca o scegliere il pulsante di comando Modifica.

![Modifica modello](media/edit-template-portal1a.PNG)

È possibile modificare la descrizione o il testo del modello di Resource Manager. Poiché si tratta di un nome di risorsa di Resource Manager, non è possibile modificare il nome. Quando si modifica il file JSON del modello di Resource Manager, viene eseguita la convalida per verificare che si tratti di un file JSON valido. Scegliere **OK** e quindi **Salva** per salvare il modello aggiornato.

![Modifica modello](media/edit-template-portal2a.PNG)

Viene visualizzata una notifica che conferma il salvataggio del modello.

![Modifica modello](media/edit-template-portal3b.png)

## <a name="deploy-a-template-resource"></a>Distribuire una risorsa modello
È possibile distribuire qualsiasi **modello** per cui si hanno autorizzazioni di **lettura**. Compilare i valori per i parametri del modello di Resource Manager per procedere con la distribuzione.

![Modello di distribuzione](media/deploy-template-portal1b.png)

## <a name="share-a-template-resource"></a>Condividere una risorsa modello
È possibile condividere le risorse **modello** con altri utenti. La condivisione funziona in modo simile all' [assegnazione di ruoli per qualsiasi risorsa di Azure](../active-directory/role-based-access-control-configure.md). Il proprietario del **modello** fornisce le autorizzazioni ad altri utenti, che possono interagire con la risorsa modello. L'utente o il gruppo di utenti con cui viene condiviso il **modello** può visualizzare il modello di Resource Manager e le relative proprietà della raccolta.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Controllo di accesso per le risorse Microsoft.Gallery
| Ruolo | Autorizzazioni |
| --- | --- |
| Proprietario |Consente il controllo completo sulla risorsa modello, inclusa la condivisione. |
| Reader |Consente l'autorizzazione di lettura ed esecuzione o distribuzione sulla risorsa modello. |
| Collaboratore |Consente l'autorizzazione di modifica ed eliminazione sulla risorsa modello. L'utente non può condividere il modello con altri. |

Fare clic con il pulsante destro del mouse sul pannello di visualizzazione di un elemento specifico e scegliere **Condividi**.

![Condividi modello](media/share-template-portal1a.png)

 È possibile scegliere un ruolo e un utente o un gruppo a cui fornire l'accesso a un **modello**specifico. I ruoli disponibili sono Proprietario, Lettore e Collaboratore.

![Condividi modello](media/share-template-portal2b.png)

![Condividi modello](media/share-template-portal3b.png)

Fare clic su **Seleziona** e quindi su **OK**. Ora è possibile visualizzare gli utenti o i gruppi aggiunti alla risorsa.

![Condividi modello](media/share-template-portal4b.png)

> [!NOTE]
> Un modello può essere condiviso solo con utenti e gruppi nello stesso tenant di Azure Active Directory. Se si condivide un modello con un indirizzo di posta elettronica che non è incluso nel tenant, viene inviato un invito a entrare nel tenant come guest.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla creazione di modelli di Resource Manager, vedere [Creazione di modelli](../azure-resource-manager/resource-group-authoring-templates.md)
* Per informazioni sulle funzioni disponibili in un modello di Resource Manager, vedere [Funzioni del modello](../azure-resource-manager/resource-group-template-functions.md)


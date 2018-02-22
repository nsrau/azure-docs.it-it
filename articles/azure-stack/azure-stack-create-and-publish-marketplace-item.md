---
title: Creare e pubblicare un elemento del Marketplace nello Stack di Azure | Documenti Microsoft
description: Creare e pubblicare un elemento del Marketplace nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 77e5f60c-a86e-4d54-aa8d-288e9a889386
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5ac91dac3cb446abaf07492d8b6ec8aa0c120ef4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-publish-a-marketplace-item"></a>Creare e pubblicare un elemento del Marketplace

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

## <a name="create-a-marketplace-item"></a>Creare un elemento del Marketplace
1. [Scaricare](http://www.aka.ms/azurestackmarketplaceitem) lo strumento Azure Packager di raccolta e l'elemento dello Stack di Azure Marketplace di esempio.
2. Aprire l'elemento del Marketplace di esempio e rinominare il **SimpleVMTemplate** cartella. (Utilizzare lo stesso nome dell'elemento del Marketplace, ad esempio, **Contoso.TodoList**.) Questa cartella contiene:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Creare un modello di gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md) o scegliere un modello da GitHub. L'elemento del Marketplace Usa questo modello per creare una risorsa.
4. Per assicurarsi che la risorsa può essere distribuita correttamente, verificare il modello con le API di Microsoft Azure Stack.
5. Se il modello si basa su un'immagine di macchina virtuale, seguire le istruzioni per [aggiungere un'immagine di macchina virtuale di Azure Stack](azure-stack-add-vm-image.md).
6. Salvare il modello di gestione risorse di Azure nel **/Contoso.TodoList/DeploymentTemplates/** cartella.
7. Scegliere le icone e testo per l'elemento del Marketplace. Aggiungere le icone per il **icone** cartella e aggiungere testo al **risorse** file nel **stringhe** cartella. Usare la convenzione di denominazione Small, Medium, Large e Wide per le icone. Vedere [elemento del Marketplace riferimento all'interfaccia utente](#reference-marketplace-item-ui) per una descrizione dettagliata.
   
   > [!NOTE]
   > Tutte le dimensioni di quattro icona (piccolo, medio, grande, wide) sono necessari per la compilazione correttamente l'elemento del Marketplace.
   > 
   > 
8. Nel **manifest.json** file, modificare **nome** per il nome dell'elemento del Marketplace. Modificare inoltre **publisher** per il nome o la società.
9. In **elementi**, modificare **nome** e **percorso** per le informazioni corrette per il modello di gestione risorse di Azure che incluso.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Sostituire **elementi del Marketplace personale** con un elenco delle categorie in cui deve essere visualizzato l'elemento del Marketplace.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Per tutte le altre modifiche a manifest.json, fare riferimento a [riferimento: Marketplace elemento manifest.json](#reference-marketplace-item-manifestjson).
12. Per creare un pacchetto in un file .azpkg le cartelle, aprire un prompt dei comandi ed eseguire il comando seguente:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > Il percorso completo per il pacchetto di output deve esistere. Ad esempio, se il percorso di output è C:\MarketPlaceItem\yourpackage.azpkg, deve esistere nella cartella C:\MarketPlaceItem.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Pubblicare un elemento del Marketplace
1. Usare PowerShell o Azure Storage Explorer per caricare l'elemento del Marketplace (.azpkg) di archiviazione Blob di Azure. È possibile caricare in archiviazione di Azure Stack locale o caricare in archiviazione di Azure. (È un percorso temporaneo per il pacchetto). Assicurarsi che il blob è accessibile pubblicamente.
2. Nella macchina virtuale client nell'ambiente di Microsoft Azure Stack, assicurarsi che la sessione di PowerShell sia configurata con le credenziali di amministratore del servizio. È possibile trovare istruzioni su come autenticare PowerShell nello Stack di Azure in [distribuire un modello con PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Utilizzare il **Aggiungi AzureRMGalleryItem** cmdlet di PowerShell per pubblicare l'elemento del Marketplace allo Stack di Azure. Ad esempio: 
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Parametro | DESCRIZIONE |
   | --- | --- |
   | SubscriptionID |ID sottoscrizione amministratore. È possibile recuperarlo utilizzando PowerShell. Se si preferisce eseguire questa operazione nel portale, passare alla sottoscrizione del provider e copiare l'ID sottoscrizione. |
   | GalleryItemUri |URI del BLOB per il pacchetto di raccolta che è già stato caricato in un archivio. |
   | Apiversion |Impostare come **2015-04-01**. |
4. Passare al portale. È ora possibile visualizzare l'elemento del Marketplace nel portale, come un operatore o un utente.
   
   > [!NOTE]
   > Il pacchetto potrebbe richiedere alcuni minuti.
   > 
   > 
5. L'elemento del Marketplace ora è stato salvato a Stack Azure Marketplace. È possibile eliminarlo dal percorso di archiviazione Blob.
6. È possibile rimuovere un elemento del Marketplace tramite il **Remove AzureRMGalleryItem** cmdlet. Esempio:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > L'interfaccia utente di Marketplace potrebbe mostrare un errore dopo la rimozione di un elemento. Per correggere l'errore, fare clic su **impostazioni** nel portale. Selezionare quindi **annullare modifiche** in **personalizzazione portale**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Informazioni di riferimento: Manifest.json elemento Marketplace
### <a name="identity-information"></a>Informazioni sull'identità
| NOME | Obbligatoria | type | Vincoli | DESCRIZIONE |
| --- | --- | --- | --- | --- |
| NOME |X |string |[A-Za-z0-9] + | |
| Editore |X |string |[A-Za-z0-9] + | |
| Version |X |string |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadata
| NOME | Obbligatoria | type | Vincoli | DESCRIZIONE |
| --- | --- | --- | --- | --- |
| DisplayName |X |string |Indicazione di 80 caratteri |Il portale potrebbe non visualizzare correttamente il nome dell'elemento se è più lungo di 80 caratteri. |
| PublisherDisplayName |X |string |Indicazione di 30 caratteri |Il portale potrebbe non visualizzare correttamente il nome del server di pubblicazione in caso di più di 30 caratteri. |
| PublisherLegalName |X |string |Numero massimo di 256 caratteri | |
| Summary |X |string |Da 60 a 100 caratteri | |
| LongSummary |X |string |Da 140 a 256 caratteri |Non ancora applicabile nello Stack di Azure. |
| DESCRIZIONE |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 a 5.000 caratteri | |

### <a name="images"></a>Immagini
Marketplace utilizza le icone seguenti:

| NOME | Larghezza | Altezza: | Note |
| --- | --- | --- | --- |
| Ampia |255 px |115 px |È necessario sempre |
| Grande |115 px |115 px |È necessario sempre |
| Media |90 px |90 px |È necessario sempre |
| Piccolo |40 px |40 px |È necessario sempre |
| Schermata |533 px |32 px |Facoltativo |

### <a name="categories"></a>Categorie
Ogni elemento del Marketplace deve essere contrassegnato con una categoria che identifica in cui l'elemento viene visualizzato nel portale di interfaccia utente. È possibile scegliere una delle categorie esistenti nello Stack di Azure (calcolo, dati e archiviazione, e così via) o uno nuovo.

### <a name="links"></a>Collegamenti
Ogni elemento del Marketplace può includere vari collegamenti a contenuto aggiuntivo. I collegamenti vengono specificati come un elenco di nomi e di URI.

| NOME | Obbligatoria | type | Vincoli | DESCRIZIONE |
| --- | --- | --- | --- | --- |
| DisplayName |X |string |Numero massimo di 64 caratteri | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>Proprietà aggiuntive
Oltre ai metadati precedenti, gli autori di Marketplace possono fornire dati di coppia chiave/valore personalizzato nel formato seguente:

| NOME | Obbligatoria | type | Vincoli | DESCRIZIONE |
| --- | --- | --- | --- | --- |
| DisplayName |X |string |Numero massimo di 25 caratteri | |
| Valore |X |string |Numero massimo di 30 caratteri | |

### <a name="html-sanitization"></a>Purificazione HTML
Per qualsiasi campo che consente di HTML, sono consentiti elementi e gli attributi seguenti:

s1, S2, h3, h4, h5, p, ol, ul, li, un [destinazione | href], Brasile, sicuro, em, b, si

## <a name="reference-marketplace-item-ui"></a>: Riferimento Elemento del Marketplace dell'interfaccia utente
Icone e testo per gli elementi del Marketplace come illustrato nel portale di Azure Stack sono i seguenti.

### <a name="create-blade"></a>Pannello Crea
![Pannello Crea](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Pannello con i dettagli dell'elemento del Marketplace
![Pannello con i dettagli dell'elemento del Marketplace](media/azure-stack-marketplace-item-ui-reference/image3.png)


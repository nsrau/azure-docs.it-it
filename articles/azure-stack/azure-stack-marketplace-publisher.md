---
title: Usare il toolkit di Marketplace per creare e pubblicare elementi del marketplace | Documenti Microsoft
description: Informazioni su come creare rapidamente elementi del marketplace con la pubblicazione Toolkit
services: azure-stack
documentationcenter: 
author: HeathL17
manager: ByronR
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: helaw
ms.openlocfilehash: 5b2c04d2cbc06e1572dc2e40712f6cf9d886aa1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Aggiungere elementi del marketplace tramite lo strumento di distribuzione
Aggiungere il contenuto di [Stack Azure Marketplace](azure-stack-marketplace.md) rende disponibili le soluzioni e i tenant per la distribuzione.  Il Toolkit di Marketplace crea i file di Azure Marketplace pacchetti (.azpkg) in base i modelli di gestione risorse di Azure IaaS o estensioni di macchina virtuale.  È possibile utilizzare anche il Toolkit di Marketplace da pubblicare, creata con lo strumento o tramite file .azpkg [manuale](azure-stack-create-and-publish-marketplace-item.md) passaggi.  Questo argomento si descrive scaricare lo strumento, la creazione di un elemento del marketplace basato su un modello di macchina virtuale e pubblicare l'elemento dello Stack Azure Marketplace.     


## <a name="prerequisites"></a>Prerequisiti
 - È necessario eseguire il toolkit nell'host dello Stack di Azure o avere [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) connettività dal computer in cui viene eseguito lo strumento.

 - Scaricare il [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) ed estrarre.

 - Scaricare il [strumento Azure raccolta](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - La pubblicazione nel Marketplace richiede le icone e un file di anteprima.  È possibile utilizzare il proprio o salvare il [esempio](azure-stack-marketplace-publisher.md#support-files) file in locale per questo esempio.

## <a name="download-the-tool"></a>Scaricare lo strumento
Può essere il Toolkit di Marketplace [scaricato dal repository di strumenti di Azure Stack](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Creare elementi del marketplace
In questa sezione, utilizzare il Toolkit di Marketplace per creare un pacchetto di un elemento marketplace in formato .azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Fornire informazioni di marketplace con Creazione guidata
1. Eseguire il Toolkit di Marketplace da una sessione di PowerShell:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Fare clic su di **soluzione** scheda.  Questa schermata accetta informazioni sull'elemento del marketplace. Immettere informazioni sull'oggetto che si desidera venga visualizzato in marketplace.  È inoltre possibile specificare un [file dei parametri](azure-stack-marketplace-publisher.md#use-a-parameters-file) con cui prepopolare il modulo.  
    
    ![schermata dello schermo prima di Toolkit di Marketplace](./media/azure-stack-marketplace-publisher/image7.png)
3. Fare clic su **Sfoglia** e selezionare un file di immagine per ogni campo icona e una schermata.  È possibile utilizzare icone personalizzate o sulle icone nell'esempio di [i file di supporto](azure-stack-marketplace-publisher.md#support-files) sezione.
4. Una volta che vengono popolati tutti i campi, selezionare "Anteprima soluzione" per un'anteprima della soluzione nel Marketplace.  È possibile rivedere e modificare il testo, immagini e schermata prima di scegliere **Avanti**.  

### <a name="import-template-and-create-package"></a>Importa modello e Crea pacchetto
In questa sezione importare il modello e utilizzare l'input per la soluzione.

1.  Fare clic su **Sfoglia** e selezionare il *azuredeploy.json* dalla cartella 101-Simple-Windows-VM nei modelli scaricati.

    ![schermata della seconda schermata Toolkit di Marketplace](./media/azure-stack-marketplace-publisher/image8.png)
2.  La distribuzione guidata viene popolata con un *base* gli elementi di input e di passaggio per ogni parametro specificato nel modello.  È possibile aggiungere ulteriori passaggi e spostarsi input passaggi.  Ad esempio, potrebbe essere "front-end" e "Configurazione Back-End" passaggi per la soluzione.
3.  Specificare il percorso AzureGalleryPackager.exe.  
4.  Fare clic su **crea** e il Toolkit di Marketplace pacchetti della soluzione in un file .azpkg.  Al termine dell'operazione, la procedura guidata consente di visualizzare il percorso del file di soluzione e fornire la possibilità di continuare a pubblicare il pacchetto dello Stack di Azure.


## <a name="publish-marketplace-items"></a>Pubblicare elementi del marketplace
In questa sezione, l'elemento del marketplace la pubblicazione in Azure Marketplace dello Stack.

![schermata dello schermo prima di Toolkit di Marketplace](./media/azure-stack-marketplace-publisher/image9.png)

1.  La procedura guidata richiede le informazioni per pubblicare la soluzione:
    
    |Campo|Descrizione|
    |-----|-----|
    | Il nome di amministratore del servizio | Account di amministratore del servizio.  Esempio:ServiceAdmin@mydomain.onmicrosoft.com |
    | Password | Password dell'account di amministratore del servizio. |
    | Endpoint API | Endpoint di Azure Stack Azure Resource Manager.  Esempio: management.local.azurestack.external |
2.  Fare clic su **pubblica** e viene visualizzato il log di pubblicazione.
3.  Si è ora possibile distribuire l'elemento pubblicata tramite il portale di Azure Stack.


## <a name="use-a-parameters-file"></a>Utilizzare un file dei parametri
È anche possibile utilizzare un file dei parametri per completare le informazioni sull'elemento del marketplace.  

Il Toolkit di Marketplace include un *solution.parameters.ps1* è possibile utilizzare per creare un file di parametri.


## <a name="support-files"></a>File di supporto
| Descrizione | Esempio |
| ----- | ----- |
| icona PNG 40 x 40 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| icona PNG 90 x 90 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| icona PNG 115 x 115 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| icona di 255 x 115 PNG | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| Anteprima PNG 533 x 324 | ![](./media/azure-stack-marketplace-publisher/image5.png) |



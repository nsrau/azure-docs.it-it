---
title: Usare il toolkit di Marketplace per creare e pubblicare elementi di marketplace | Microsoft Docs
description: Informazioni su come creare rapidamente elementi del marketplace con il Toolkit di pubblicazione
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 0ade78dd992e8d1d2eda2cf27d44e52c4030563f
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630926"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Aggiungere elementi del marketplace tramite lo strumento di distribuzione
Aggiunta di contenuto per il [Marketplace Azure Stack](azure-stack-marketplace.md) rende disponibili per te e per i tenant per la distribuzione delle soluzioni.  Il Toolkit di Marketplace crea i file dei pacchetti di Marketplace di Azure (con estensione azpkg) basati sul modelli IaaS Azure Resource Manager o le estensioni di VM.  È anche possibile usare il Toolkit di Marketplace per pubblicare il file con estensione azpkg, creato con lo strumento o [manuale](azure-stack-create-and-publish-marketplace-item.md) passaggi.  In questo argomento consente di scaricare lo strumento, la creazione di un elemento del marketplace basato su un modello di macchina virtuale e quindi pubblicarla quell'elemento per il Marketplace di Azure Stack.     


## <a name="prerequisites"></a>Prerequisiti
 - È necessario eseguire il toolkit nell'host Azure Stack o avere [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) connettività dal computer in cui si esegue lo strumento.

 - Scaricare il [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) ed estrarre.

 - Scaricare il [dello strumento di creazione pacchetti di raccolta di Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Pubblicazione nel marketplace richiede le icone e un file di anteprima.  È possibile usare il proprio o salvare il [esempio](azure-stack-marketplace-publisher.md#support-files) file in locale per questo esempio.

## <a name="download-the-tool"></a>Scaricare lo strumento
Il Toolkit di Marketplace può essere [scaricato dal repository di strumenti di Azure Stack](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Creare elementi di marketplace
In questa sezione si usa il Toolkit di Marketplace per creare un pacchetto di elemento del marketplace nel formato con estensione azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Fornire informazioni di marketplace con Creazione guidata
1. Eseguire il Toolkit di Marketplace da una sessione di PowerShell:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Scegliere il **soluzione** scheda.  Questa schermata accetta l'elemento del marketplace di informazioni. Come si desidera venga visualizzato nel marketplace, immettere le informazioni sull'elemento.  È inoltre possibile specificare una [file dei parametri](azure-stack-marketplace-publisher.md#use-a-parameters-file) cui prepopolare il form.  
    
    ![screenshot della schermata prima di Toolkit di Marketplace](./media/azure-stack-marketplace-publisher/image7.png)
3. Fare clic su **esplorare** e selezionare un file di immagine per ogni campo icona e screenshot.  È possibile usare icone personalizzate o le icone di esempio nel [supportano file](azure-stack-marketplace-publisher.md#support-files) sezione.
4. Una volta che vengono popolati tutti i campi, selezionare "Soluzione di anteprima" per un'anteprima della soluzione in Marketplace.  È possibile rivedere e modificare il testo, immagini e schermata prima di fare clic **successivo**.  

### <a name="import-template-and-create-package"></a>Importa modello e creare pacchetti
In questa sezione Importa il modello e utilizzare l'input per la soluzione.

1.  Fare clic su **esplorare** e selezionare il *azuredeploy. JSON* dalla cartella 101-Simple-Windows-VM nei modelli scaricati.

    ![screenshot della schermata secondo Toolkit di Marketplace](./media/azure-stack-marketplace-publisher/image8.png)
2.  La distribuzione guidata viene popolata con un *base* gli elementi di input e di passaggio per ogni parametro specificato nel modello.  È possibile aggiungere ulteriori passaggi e spostare gli input tra i vari passaggi.  Ad esempio, è possibile "front-end" e "Configurazione Back-End" passaggi per la soluzione.
3.  Specificare il percorso AzureGalleryPackager.exe.  
4.  Fare clic su **Create** e il Toolkit di Marketplace dei pacchetti della soluzione in un file con estensione azpkg.  Al termine dell'operazione, la procedura guidata viene visualizzato il percorso al file di soluzione e offre la possibilità di continuare a pubblicare il pacchetto in Azure Stack.


## <a name="publish-marketplace-items"></a>Pubblicare elementi di marketplace
In questa sezione è pubblicare l'elemento del marketplace in Azure Stack Marketplace.

![screenshot della schermata prima di Toolkit di Marketplace](./media/azure-stack-marketplace-publisher/image9.png)

1.  La procedura guidata richiede le informazioni per pubblicare la soluzione:
    
    |Campo|DESCRIZIONE|
    |-----|-----|
    | Nome dell'amministratore del servizio | Account di amministratore del servizio.  Esempio:  ServiceAdmin@mydomain.onmicrosoft.com |
    | Password | Password dell'account di amministratore del servizio. |
    | Endpoint API | Endpoint di Azure Stack Azure Resource Manager.  Esempio: management.local.azurestack.external |
2.  Fare clic su **pubblica** e viene visualizzato il log di pubblicazione.
3.  A questo punto si è in grado di distribuire l'elemento pubblicato nel portale di Azure Stack.


## <a name="use-a-parameters-file"></a>Usare un file di parametri
È anche possibile usare un file di parametri per completare le informazioni sull'elemento di marketplace.  

Il Toolkit di Marketplace include un' *solution.parameters.ps1* è possibile usare per creare il proprio file di parametri.


## <a name="support-files"></a>File di supporto
| DESCRIZIONE | Esempio |
| ----- | ----- |
| icona di 40 x 40. PNG | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| icona con estensione png 90 x 90 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| icona di 115 x 115. PNG | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| icona di 255 x 115. PNG | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| anteprima con estensione png 533, 324 | ![](./media/azure-stack-marketplace-publisher/image5.png) |



---
title: Azure Marketplace per Azure per enti pubblici | Documentazione Microsoft
description: "Questo articolo fornisce un confronto delle funzionalità e alcune linee guida sullo sviluppo di applicazioni per Azure per enti pubblici."
services: azure-government
cloud: gov
documentationcenter: 
author: tsingh
manager: asimm
ms.assetid: 7790d3c5-d0fa-4662-b4f0-a174cb7d6c9f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: zakramer;tsingh;divacc
translationtype: Human Translation
ms.sourcegitcommit: 0839e8e57b2149e50d4512d28b1e57e6592be458
ms.openlocfilehash: 70821864520ff18ba8c64be0ea66376bccee7148


---
# <a name="azure-marketplace-for-azure-government"></a>Azure Marketplace per Azure per enti pubblici
I partner di Azure per enti pubblici interessati a pubblicare offerte in Azure Marketplace possono trovare tutti i dettagli in questo articolo.

Attualmente in Azure Marketplace per Azure per enti pubblici sono supportati modelli di soluzione e immagini di VM BYOL. La procedura consigliata prevede che i modelli di soluzione vengano esaminati prima della pubblicazione in Azure per enti pubblici per essere certi del funzionamento sia nel cloud pubblico di Azure che in quello di Azure per enti pubblici. Se si pubblica solo un'immagine di VM è possibile procedere con i passaggi per la pubblicazione più avanti in questo articolo.

## <a name="pre-publishing-validation-for-solution-templates"></a>Convalida pre-pubblicazione per i modelli di soluzione

Prima di pubblicare il modello di soluzione in Azure per enti pubblici, è consigliabile verificare alcuni aspetti comuni legati alle procedure consigliate per garantire il funzionamento del modello sia nel cloud pubblico di Azure che in Azure per enti pubblici.

1.  Verificare che gli endpoint non siano hardcoded nel modello di soluzione per il cloud pubblico di Azure. Altrimenti non saranno validi per gli altri ambienti di Azure. Modificare invece il modello di soluzione per richiedere una chiamata dell'API per effettuare il pull dell'endpoint valido:  

  Esempio:

  URI VHD non corretto (hardcoded) "uri": "[concat('https://', variables('storageAccountName'), '.blob.core.windows.net/', '/osdisk.vhd')]",

  URI VHD corretto (con riferimento)

  "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'osdisk.vhd')]",

  La sintassi corretta garantirà il corretto funzionamento del modello in qualsiasi cloud (per enti pubblici, pubblico di Azure, AzureStack, China e così via)

2.  Verificare che le risorse usate nel modello di soluzione siano disponibili nel sovereign cloud in cui si intende eseguire la pubblicazione.
RP in Azure e versione dell'API

    Identificare la disponibilità di Azure per enti pubblici tramite Esplora risorse nel portale:

  1.    Accedere al portale di Azure per enti pubblici
  2.    Avviare Esplora risorse, eseguendo la procedura illustrata qui https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-supported-services#supported-regions

  Estensioni usate più comunemente; disponibilità in Fairfax  

  | Server di pubblicazione/tipo | Versioni disponibili in Fairfax |
  | --- | --- |
  | Microsoft.OSTCExtensions/CustomScriptForLinux | 1; 1.1; 1.2.2.0; 1.3.0.2; 1.4.1.0; 1.5.2.0 |
  | Microsoft.Compute/CustomScriptExtension | 1.2; 1.3; 1.4; 1.7; 1.8 |
  | Microsoft.Azure.Extensions/DockerExtension | Non disponibile |
  | Microsoft.Azure.Extensions/CustomScript | 2.0.2 |
  | Microsoft.OSTCExtensions/LinuxDiagnostic | 2.0.9005; 2.1.9005; 2.2.9005; 2.3.9011 |
  | Microsoft.Powershell/DSC | 2.19.0.0 |

> [!NOTE]
> Se si inseriscono percorsi per un elenco di valori consentiti, è necessario aggiornare periodicamente i dati in base all'aggiunta di nuove aree.  


## <a name="publishing"></a>Pubblicazione
> [!NOTE]
> Se non si dispone della certificazione di partner di Azure Marketplace, prima di procedere eseguire le operazioni che illustrano come [pubblicare e gestire un'offerta](../marketplace-publishing/marketplace-publishing-getting-started.md).
>
>

### <a name="step-1"></a>Passaggio 1
Accedere al [portale di pubblicazione di Azure](https://publish.windowsazure.com).

### <a name="step-2"></a>Passaggio 2
Fare clic sull'offerta da pubblicare.

### <a name="step-3"></a>Passaggio 3
Fare clic su **SKUS** (SKU) e selezionare la casella **Azure Government Cloud** (Cloud di Azure per enti pubblici).

> [!NOTE]
> Sono supportate solo le SKU delle licenze Bring Your Own License (BYOL).  Questa opzione non è disponibile per le SKU con pagamento in base al consumo (Pay-as-You-Go, PayG).
>
>

![Pagina dell'offerta con le opzioni SKUS (SKU) e Azure Government Cloud (Cloud di Azure per enti pubblici)](./media/government-manage-marketplace-partner-1.png)

### <a name="step-4"></a>Passaggio 4
Fare clic sul link **+ Add Certification** (Aggiungi certificazione) per aggiungere link alle certificazioni per l'offerta.

![Pagina dell'offerta con il link Add Certification (Aggiungi certificazione)](./media/government-manage-marketplace-partner-2.png)

### <a name="step-5"></a>Passaggio 5
Per testare l'immagine nel portale di pubblicazione, richiedere un [account di prova](https://azuregov.microsoft.com/trial/azuregovtrial) per Microsoft Azure per enti pubblici.

Viene verificata l'idoneità come partner che opera per gli enti federali, statali o locali degli Stati Uniti, successivamente confermata tramite e-mail.  L'account di prova risulterà disponibile entro&3;-5 giorni lavorativi.

### <a name="step-6"></a>Passaggio 6
Fare clic su **Publish** (Pubblica) e quindi su **Push to Staging** (Metti in gestione temporanea).

![Opzioni Publish (Pubblica) e Push to Staging (Metti in gestione temporanea)](./media/government-manage-marketplace-partner-3.png)

Viene richiesto di immettere una sottoscrizione alla quale sia consentito accedere all'offerta in gestione temporanea. Immettere l'ID sottoscrizione dell'account di prova di Azure per enti pubblici appena acquisito.

![Prompt in cui si specificano gli ID sottoscrizione in grado di accedere all'offerta](./media/government-manage-marketplace-partner-4.png)

### <a name="step-7"></a>Passaggio 7
Una volta inserita l'offerta in gestione temporanea, è possibile testare l'immagine accedendo al [portale di Azure](https://portal.azure.us) con l'account di prova di Azure per enti pubblici.

### <a name="step-8"></a>Passaggio 8
Una volta convalidata l'immagine usando la sottoscrizione di prova, è possibile rendere disponibile l'offerta facendo clic su **Publish** (Pubblica) e richiedendo l'approvazione per passare alla produzione.

![Comando di richiesta dell'approvazione per passare alla produzione](./media/government-manage-marketplace-partner-5.png)

## <a name="next-steps"></a>Passaggi successivi
Per maggiori informazioni e aggiornamenti, iscriversi al [blog di Microsoft Azure per enti pubblici](https://blogs.msdn.microsoft.com/azuregov/).



<!--HONumber=Dec16_HO3-->



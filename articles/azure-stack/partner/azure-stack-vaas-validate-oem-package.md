---
title: Convalida pacchetti (OEM) original equipment manufacturer nella convalida di Azure Stack come servizio | Microsoft Docs
description: Informazioni su come verificare i pacchetti di OEM (OEM) con la convalida come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: cefa32c35df4e87d4d2b983ee8c4a16dc065e774
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160456"
---
# <a name="validate-oem-packages"></a>Convalidare i pacchetti dell'OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

È possibile testare un nuovo pacchetto di OEM quando è stata apportata una modifica al firmware o driver per la convalida di una soluzione completata. Quando il pacchetto è stato superato il test, è firmato da Microsoft. Il test deve contenere il pacchetto di estensione OEM aggiornato con i driver e firmware che hanno superato test di PC e il logo di Windows Server.

Tutti i test completati entro 24 ore con risultato **ha avuto esito positivo**. Se uno qualsiasi dei test avranno come risultato **non è stato possibile**, un bug in [Microsoft Collaborate](https://aka.ms/collaborate) e contattare Microsoft inviando un messaggio di posta elettronica [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

## <a name="get-your-oem-package-signed"></a>OEM di pacchetti firmati

1. Assicurarsi che sia stato applicato l'aggiornamento mensile corrente. Per la versione più recente, vedere la versione più recente nella [documentazione dell'operatore di Azure Stack > Panoramica > note sulla versione](https://docs.microsoft.com/azure/azure-stack/) .

    Gli aggiornamenti software Microsoft per Azure Stack sono designati utilizzando una convenzione di denominazione, ad esempio, 1803 che indica che l'aggiornamento è per il mese di marzo 2018. Per informazioni sui criteri di aggiornamento di Azure Stack, sono disponibili note sulla versione e cadenza, vedere [criteri di manutenzione Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

1. Controllare lo stato di integrità di sistema eseguendo **Test-AzureStack** come descritto in [eseguire un test di convalida per Azure Stack. Risolvere eventuali avvisi ed errori prima di avviare i test.

2. Nel [portale convalida](https://azurestackvalidation.com), selezionare una soluzione esistente. Se non è stato aggiunto la soluzione, vedere [aggiungere una nuova soluzione](azure-stack-vaas-validate-solution-new.md#add-a-new-solution).

3. Selezionare **avviare** nel **le convalide di pacchetti** riquadro per avviare un nuovo flusso di lavoro.

    ![Convalide di pacchetti](media/image3.png)

4.  Offre una funzionalità di diagnostica di stringa di connessione. Per istruzioni, vedere [configurare un account di archiviazione](azure-stack-vaas-set-up-account.md).

    Per ogni esecuzione di convalida del pacchetto, è necessario specificare un pacchetto di estensione OEM. Specificare il pacchetto OEM che è stato installato su una soluzione al momento della distribuzione di Azure Stack. Per istruzioni, vedere [creare un blob di archiviazione di Azure per archiviare i log](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    Un file JSON con le variabili di ambiente da utilizzare per completare l'input per i campi obbligatori per l'esecuzione evitare errori nell'immissione di dati. Per istruzioni, vedere [riceve un file di configurazione in una distribuzione di Azure Stack](azure-stack-vaas-parameters.md).

5. Eseguire i test.

6. Quando tutti i test è sono completata, invia il nome della convalida di soluzione e al pacchetto da [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) per richiedere la firma del pacchetto.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).

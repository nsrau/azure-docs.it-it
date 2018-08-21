---
title: Convalidare gli aggiornamenti software da Microsoft nella convalida di Azure Stack come servizio | Microsoft Docs
description: Informazioni su come convalidare gli aggiornamenti software da Microsoft con la convalida come servizio.
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
ms.openlocfilehash: a9e1bb2f134e0e51803266f5e7f94c05ab6966f2
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235406"
---
# <a name="validate-software-updates-from-microsoft"></a>Convalidare gli aggiornamenti software da Microsoft

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft rilascerà periodicamente aggiornamenti per il software Azure Stack. Questi aggiornamenti vengono forniti per i partner di CO-ingegneria Azure Stack prima viene reso disponibile pubblicamente in modo da poter convalidare gli aggiornamenti con le proprie soluzioni e fornire commenti e suggerimenti a Microsoft.

## <a name="test-an-existing-solution"></a>Testare una soluzione esistente

1. Accedi per il [portale convalida](https://azurestackvalidation.com).

2. Selezionare una soluzione esistente in cui l'aggiornamento da Microsoft è stato distribuito e selezionare **avviare** nel **convalida dei pacchetti** riquadro.

    ![Convalida del pacchetto](media/image3.png)

3. Immettere il nome di convalida.

4. Immettere l'URL per il pacchetto OEM che è stato installato sulla soluzione in fase di distribuzione. Usare l'URL per il pacchetto archiviato nel servizio blob di Azure. Per altre informazioni, vedere [creare un blob di archiviazione di Azure per archiviare i log](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

5. Selezionare **caricare** per aggiungere il file di configurazione di distribuzione. Vedere le [la convalida di una nuova soluzione di Azure Stack](azure-stack-vaas-validate-solution-new.md) per informazioni sul caricamento di file di configurazione di distribuzione.

6. Il file di configurazione di distribuzione deve quindi essere personalizzato con il file di parametri di ambiente corretto, vedere [parametri di ambiente](azure-stack-vaas-parameters.md#environment-parameters) per altri dettagli.

    > [!Note]   
    > Il file di configurazione di distribuzione è possibile personalizzare ulteriormente tramite l'aggiunta di parametri comuni di test. Per altre informazioni, vedere [parametri comuni del flusso di lavoro per la convalida di Azure Stack come servizio](azure-stack-vaas-parameters.md)

7. Il nome utente e la password per l'utente del tenant, amministratore del servizio e amministratore cloud devono essere immessi manualmente.

8. Specificare l'URL del BLOB di archiviazione di Azure per archiviare i log di diagnostica. Per altre informazioni, vedere [creare un blob di archiviazione di Azure per archiviare i log](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    > [!Note]  
    > Tag descrittivi possono essere immessi per etichettare il flusso di lavoro.

10. Selezionare **Submit** per salvare il flusso di lavoro.

Il flusso di lavoro di soluzione viene eseguita per circa 24 ore. Aggiungere un collegamento a o istruzioni sulla pianificazione di test. Cancella nello strumento.

Trovare altre informazioni sul monitoraggio dello stato di una convalida eseguire, vedere [monitorare un test ](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).

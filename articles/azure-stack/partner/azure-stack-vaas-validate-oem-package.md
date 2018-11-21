---
title: Convalidare i pacchetti dell'OEM (OEM) in Azure Stack di convalida come servizio | Microsoft Docs
description: Informazioni su come convalidare i pacchetti dell'OEM (OEM) con la convalida come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 8268a6b04d7ddbb35821999142d3a33bdd2bedcc
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261803"
---
# <a name="validate-oem-packages"></a>Convalidare i pacchetti dell'OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

È possibile testare un nuovo pacchetto di OEM quando è stata apportata una modifica al firmware o driver per la convalida di una soluzione completata. Quando il pacchetto è stato superato il test, è firmato da Microsoft. Il test deve contenere il pacchetto di estensione OEM aggiornato con i driver e firmware che hanno superato test di PC e il logo di Windows Server.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Prima di caricamento o l'invio dei pacchetti, esaminare [creare un pacchetto dell'OEM](azure-stack-vaas-create-oem-package.md) per il formato previsto del pacchetto e il contenuto.

## <a name="managing-packages-for-validation"></a>La gestione dei pacchetti per la convalida

Quando si usa la **la convalida del pacchetto** flusso di lavoro per convalidare un pacchetto, si dovrà fornire un URL a un **archiviazione blob di Azure**. Questo blob è il pacchetto OEM che è stato installato sulla soluzione in fase di distribuzione. Crea il blob usando l'Account di archiviazione di Azure creato durante l'installazione (vedere [configurare la convalida come le risorse di un servizio](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Prerequisito: Effettuare il provisioning di un contenitore di archiviazione

Creare un contenitore nell'account di archiviazione per i BLOB di pacchetto. Questo contenitore è utilizzabile per tutti i viene eseguita la convalida del pacchetto.

1. Nel [portale di Azure](https://portal.azure.com), passare all'account di archiviazione creato nel [configurare la convalida come le risorse di un servizio](azure-stack-vaas-set-up-resources.md).
2. Nel pannello sinistro sotto **servizio Blob**, selezionare **contenitori**.
3. Selezionare **+ contenitore** nel menu della barra e specificare un nome per il contenitore, ad esempio, `vaaspackages`.

### <a name="upload-package-to-storage-account"></a>Carica pacchetto all'account di archiviazione

1. Preparare il pacchetto da convalidare. Se il pacchetto contiene più file, comprimerlo in un `.zip` file.
2. Nel [portale di Azure](https://portal.azure.com), selezionare il contenitore del pacchetto e caricare il pacchetto facendo clic sulla **caricare** nella barra dei menu.
3. Selezionare il pacchetto `.zip` file da caricare. Mantenere valori predefiniti per **tipo Blob** (ovvero **Blob in blocchi**) e **dimensioni blocco**.

> [!NOTE]
> Verificare che il `.zip` contenuto viene inserito nella radice del `.zip` file. Non vi sarà alcun nelle sottocartelle nel pacchetto.

### <a name="generate-package-blob-url-for-vaas"></a>Generare l'URL blob pacchetto per VaaS

Quando si crea una **convalida dei pacchetti** flusso di lavoro nel portale di VaaS, si dovrà fornire un URL del blob di archiviazione di Azure contenente il pacchetto.

#### <a name="option-1-generating-an-account-sas-url"></a>Opzione 1: Generare un URL di firma di accesso condiviso dell'account

1. Nel [portale di Azure](https://portal.azure.com/), passare all'account di archiviazione e individuare il file zip contenente il pacchetto

2. Selezionare **genera firma di accesso condiviso** dal menu di scelta rapida

3. Selezionare **Read** da **autorizzazioni**

4. Impostare **ora di inizio** sull'ora corrente, e **ora di fine** almeno 48 ore dal **ora di inizio**. Se si prevede di eseguire altri test con lo stesso pacchetto, provare ad aumentare **ora di fine** per la lunghezza delle attività di test. I test pianificati tramite VaaS dopo **ora di fine** avranno esito negativo e una nuova firma di accesso condiviso verrà dovranno essere generati.

5. Selezionare **generare token di firma di accesso condiviso di blob e l'URL**

Uso **URL di firma di accesso condiviso di Blob** quando si avvia un nuovo **convalida dei pacchetti** flusso di lavoro nel portale di VaaS.

#### <a name="option-2-using-public-read-container"></a>Opzione 2: Uso di contenitore di lettura pubblico

> [!CAUTION]
> Questa opzione apre il contenitore per l'accesso anonimo di sola lettura.

1. Concessione **accesso solo per i BLOB in lettura pubblico** al contenitore del pacchetto, seguendo le istruzioni nella sezione [concedere le autorizzazioni agli utenti anonimi per contenitori e blob](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

2. Nel contenitore del pacchetto, selezionare questa opzione sul blob nel contenitore del pacchetto per aprire il riquadro proprietà.

3. Copia il **URL**. Utilizzare questo valore quando si avvia una nuova **convalida dei pacchetti** flusso di lavoro nel portale di VaaS.

## <a name="apply-monthly-update"></a>Applicare l'aggiornamento mensile

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>Creare un flusso di lavoro di convalida del pacchetto

1. Accedi per il [VaaS portale](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Selezionare **avviare** nel **convalida dei pacchetti** riquadro.

    ![Riquadro del flusso di lavoro del pacchetto convalide](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Immettere l'URL del blob di archiviazione di Azure per il pacchetto OEM che è stato installato sulla soluzione in fase di distribuzione. Per istruzioni, vedere [generare l'URL blob pacchetto per VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parametri di ambiente non possono essere modificati dopo la creazione di un flusso di lavoro.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Si verrà reindirizzati alla pagina di riepilogo di test.

## <a name="run-package-validation-tests"></a>Eseguire i test di convalida del pacchetto

Nel **riepilogo test di convalida del pacchetto** pagina, verrà visualizzato un elenco dei test necessari per completare la convalida. Test in questo flusso di lavoro eseguito circa 24 ore.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

Quando tutti i test è sono completata, invia il nome della soluzione VaaS e convalida del pacchetto per [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) per richiedere la firma del pacchetto.

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md)
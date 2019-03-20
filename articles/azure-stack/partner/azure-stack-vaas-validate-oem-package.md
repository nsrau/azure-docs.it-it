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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aae3ec8ff713959c5cc2485951aba025a6f89a1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113283"
---
# <a name="validate-oem-packages"></a>Convalidare i pacchetti dell'OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

È possibile testare un nuovo pacchetto di OEM quando è stata apportata una modifica al firmware o driver per la convalida di una soluzione completata. Quando il pacchetto è stato superato il test, è firmato da Microsoft. Il test deve contenere il pacchetto di estensione OEM aggiornato con i driver e firmware che hanno superato test di PC e il logo di Windows Server.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Prima di caricamento o l'invio dei pacchetti, esaminare [creare un pacchetto dell'OEM](azure-stack-vaas-create-oem-package.md) per il formato previsto del pacchetto e il contenuto.

## <a name="managing-packages-for-validation"></a>La gestione dei pacchetti per la convalida

Quando si usa la **la convalida del pacchetto** flusso di lavoro per convalidare un pacchetto, si dovrà fornire un URL a un **archiviazione blob di Azure**. Questo blob è che il test firmato pacchetto OEM che verrà installato come parte del processo di aggiornamento. Crea il blob usando l'Account di archiviazione di Azure creato durante l'installazione (vedere [configurare la convalida come le risorse di un servizio](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Prerequisiti: Effettuare il provisioning di un contenitore di archiviazione

Creare un contenitore nell'account di archiviazione per i BLOB di pacchetto. Questo contenitore è utilizzabile per tutti i viene eseguita la convalida del pacchetto.

1. Nel [portale di Azure](https://portal.azure.com), passare all'account di archiviazione creato nel [configurare la convalida come le risorse di un servizio](azure-stack-vaas-set-up-resources.md).

2. Nel pannello sinistro sotto **servizio Blob**, selezionare **contenitori**.

3. Selezionare **+ contenitore** dalla barra dei menu.
    1. Specificare un nome per il contenitore, ad esempio, `vaaspackages`.
    1. Selezionare il livello di accesso da usare per i client non autenticati, ad esempio VaaS. Per informazioni dettagliate su come concedere accesso VaaS ai pacchetti in ogni scenario, vedere [gestione livello di accesso del contenitore](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Carica pacchetto all'account di archiviazione

1. Preparare il pacchetto da convalidare. Si tratta di un `.zip` file il cui contenuto deve corrispondere alla struttura descritta in [creare un pacchetto dell'OEM](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Verificare che il `.zip` contenuto viene inserito nella radice del `.zip` file. Non vi sarà alcun nelle sottocartelle nel pacchetto.

1. Nel [portale di Azure](https://portal.azure.com), selezionare il contenitore del pacchetto e caricare il pacchetto facendo clic sulla **caricare** nella barra dei menu.

1. Selezionare il pacchetto `.zip` file da caricare. Mantenere valori predefiniti per **tipo Blob** (vale a dire **Blob in blocchi**) e **dimensioni blocco**.

### <a name="generate-package-blob-url-for-vaas"></a>Generare l'URL blob pacchetto per VaaS

Quando si crea una **convalida dei pacchetti** flusso di lavoro nel portale di VaaS, si dovrà fornire un URL del blob di archiviazione di Azure contenente il pacchetto. Alcuni *interattiva* test, inclusi **verifica di aggiornamento mensile AzureStack** e **verifica del pacchetto estensione OEM**, richiedono anche un URL per i BLOB di pacchetto.

#### <a name="handling-container-access-level"></a>Gestione livello di accesso del contenitore

Il livello di accesso minimo richiesto da VaaS dipende dal fatto che si sta creando un flusso di lavoro di convalida del pacchetto o pianificazione un' *interattiva* di test.

Nel caso di **privato** e **Blob** accedere a livelli, è necessario concedere temporaneamente l'accesso al blob di pacchetto fornendo VaaS una [firma di accesso condiviso](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). Il **contenitore** a livello di accesso non è necessario generare gli URL di firma di accesso condiviso, ma consente accesso non autenticato al contenitore e ai relativi BLOB.

|Livello di accesso | Requisiti del flusso di lavoro | Requisito di test |
|---|---------|---------|
|Privato | Generare un URL di firma di accesso condiviso per blob di pacchetto ([Option 1](#option-1-generate-a-blob-sas-url)). | Generare un URL di firma di accesso condiviso a livello di account e aggiungere manualmente il nome del blob del pacchetto ([Option 2](#option-2-construct-a-container-sas-url)). |
|BLOB | Specificare la proprietà URL blob ([opzione 3](#option-3-grant-public-read-access)). | Generare un URL di firma di accesso condiviso a livello di account e aggiungere manualmente il nome del blob del pacchetto ([Option 2](#option-2-construct-a-container-sas-url)). |
|Contenitore | Specificare la proprietà URL blob ([opzione 3](#option-3-grant-public-read-access)). | Specificare la proprietà URL blob ([opzione 3](#option-3-grant-public-read-access)).

Le opzioni per la concessione dell'accesso ai pacchetti sono ordinate dal accesso minimi per l'accesso maggiore.

#### <a name="option-1-generate-a-blob-sas-url"></a>Opzione 1: Generare un URL di firma di accesso condiviso di blob

Usare questa opzione se il livello di accesso del contenitore di archiviazione è impostato su **privato**, in cui il contenitore non Abilita accesso in lettura pubblico al contenitore o ai relativi BLOB.

> [!NOTE]
> Questo metodo non funziona per *interattiva* i test. Vedere [opzione 2: Costruire un URL di firma di accesso condiviso del contenitore](#option-2-construct-a-container-sas-url).

1. Nel [portale di Azure](https://portal.azure.com/), passare all'account di archiviazione e individuare il file zip contenente il pacchetto.

2. Selezionare **genera firma di accesso condiviso** dal menu di scelta rapida.

3. Selezionare **Read** dalla **autorizzazioni**.

4. Impostare **ora di inizio** sull'ora corrente, e **ora di fine** almeno 48 ore dal **ora di inizio**. Se si creano altri flussi di lavoro con lo stesso pacchetto, provare ad aumentare **ora di fine** per la lunghezza delle attività di test.

5. Selezionare **Genera token di firma di accesso condiviso e URL**.

Usare la **URL di firma di accesso condiviso di Blob** quando fornendo pacchetto blob gli URL per il portale.

#### <a name="option-2-construct-a-container-sas-url"></a>Opzione 2: Costruire un URL di firma di accesso condiviso del contenitore

Usare questa opzione se il livello di accesso del contenitore di archiviazione è impostato su **privati** ed è necessario fornire un URL di blob di pacchetto a un *interattivo* di test. Questo URL è anche utilizzabile a livello di flusso di lavoro.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Selezionare **Blob** dalla **opzioni di servizi consentiti**. Deselezionare le altre opzioni.

1. Selezionare **contenitore** e **oggetto** dalla **tipi di risorse consentiti**.

1. Selezionare **Read** e **elenco** dalla **disponga delle autorizzazioni**. Deselezionare le altre opzioni.

1. Selezionare **ora di inizio** come ora corrente e **ora di fine** per almeno 14 giorni dalla **ora di inizio**. Se si prevede di eseguire altri test con lo stesso pacchetto, provare ad aumentare **ora di fine** per la lunghezza delle attività di test. I test pianificati tramite VaaS dopo **ora di fine** avranno esito negativo e una nuova firma di accesso condiviso verrà dovranno essere generati.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Il formato deve apparire come segue: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Modificare l'URL di firma di accesso condiviso generato per includere il contenitore del pacchetto, `{containername}`e il nome del blob di pacchetto, `{mypackage.zip}`, come indicato di seguito:  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Utilizzare questo valore quando fornendo pacchetto blob gli URL per il portale.

#### <a name="option-3-grant-public-read-access"></a>Opzione 3: Concedere l'accesso in lettura pubblico

Usare questa opzione se sono accettabile per consentire l'accesso per i singoli BLOB o, nel caso di client non autenticati *interattiva* dei test, il contenitore.

> [!CAUTION]
> Questa opzione apre il BLOB per l'accesso anonimo di sola lettura.

1. Impostare il livello di accesso del contenitore del pacchetto su **Blob** oppure **contenitore** seguendo le istruzioni nella sezione [concedere le autorizzazioni agli utenti anonimi per contenitori e blob](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > Se si specifica un URL del pacchetto a un *interattiva* test, è necessario concedere **accesso in lettura pubblico completo** al contenitore per continuare il test.

1. Nel contenitore del pacchetto, selezionare il blob di pacchetto per aprire il riquadro proprietà.

1. Copia il **URL**. Utilizzare questo valore quando fornendo pacchetto blob gli URL per il portale.

## <a name="create-a-package-validation-workflow"></a>Creare un flusso di lavoro di convalida del pacchetto

1. Accedi per il [VaaS portale](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Selezionare **avviare** nel **convalida dei pacchetti** riquadro.

    ![Riquadro del flusso di lavoro del pacchetto convalide](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Immettere Azure Storage blob URL per il test firmato pacchetto OEM che richiedono una firma di Microsoft. Per istruzioni, vedere [generare l'URL blob pacchetto per VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parametri di ambiente non possono essere modificati dopo la creazione di un flusso di lavoro.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Si verrà reindirizzati alla pagina di riepilogo di test.

## <a name="required-tests"></a>Test necessari

I test seguenti sono necessari per la convalida del pacchetto OEM:

- Verifica del pacchetto estensione di OEM
- Motore di simulazione di cloud

## <a name="run-package-validation-tests"></a>Eseguire i test di convalida del pacchetto

1. Nel **riepilogo test di convalida del pacchetto** pagina, si esegue un subset dei test elencati appropriate al proprio scenario.

    Nei flussi di lavoro di convalida **pianificazione** un test Usa i parametri comuni del flusso di lavoro a livello specificato durante la creazione del flusso di lavoro (vedere [parametri comuni del flusso di lavoro per la convalida dello Stack di Azure come servizio](azure-stack-vaas-parameters.md)). Se uno qualsiasi dei valori dei parametri di test diventano non valido, è necessario resupply li come indicato nelle [modificare i parametri del flusso di lavoro](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Pianificazione di un test di convalida su un'istanza esistente creerà una nuova istanza al posto di istanza precedente nel portale. I log per l'istanza precedente verranno mantenuti ma non sono accessibili dal portale.  
    > Una volta che un test è stata completata, il **pianificazione** azione viene disabilitata.

2. Selezionare l'agente che verrà eseguito il test. Per informazioni sull'aggiunta di locale l'esecuzione agenti di test, vedere [distribuire l'agente locale](azure-stack-vaas-local-agent.md).

3. Per completare la verifica del pacchetto di estensione OEM, selezionare **pianificazione** dal menu di scelta rapida per aprire un prompt dei comandi per la pianificazione dell'istanza di test.

4. Esaminare i parametri di test e quindi selezionare **Submit** per pianificare la verifica del pacchetto di estensione OEM per l'esecuzione.

    Verifica del pacchetto estensione OEM è suddiviso in due passaggi manuali: Aggiornamento di Azure Stack e aggiornamento OEM.

   1. **Selezionare** "Run" nell'interfaccia utente per eseguire lo script relativi alle verifiche preliminari. Si tratta di un test automatizzato che richiede circa 5 minuti e non richiede alcun intervento.

   1. Dopo aver completato lo script relativi alle verifiche preliminari, eseguire il passaggio manuale: **installare** l'aggiornamento più recente dello Stack di Azure usando il portale di Azure Stack.

   1. **Eseguire** Test-AzureStack sul modulo. Se si verificano problemi, non continuare con il test e contattare [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

       Per informazioni su come eseguire il comando Test-AzureStack, vedere [lo stato del sistema Azure Stack convalidare](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test).

   1. **Selezionare** "Avanti" per eseguire lo script postcheck. Si tratta di un test automatizzato che contrassegna la fine del processo di aggiornamento di Azure Stack.

   1. **Selezionare** "Esegui" per eseguire lo script relativi alle verifiche preliminari per l'aggiornamento di OEM.

   1. Dopo aver completato la verifica preliminare, eseguire il passaggio manuale: **installare** il pacchetto di estensione OEM tramite il portale.

   1. **Eseguire** Test-AzureStack sul modulo.

      > [!NOTE]
      > Come in precedenza, non continuare con il test e contattare [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) se ha esito negativo. Questo passaggio è fondamentale poiché consentirà di risparmiare è una ridistribuzione.

   1. **Selezionare** "Avanti" per eseguire lo script postcheck. Ciò contrassegna la fine del passaggio di aggiornamento dell'OEM.

   1. Rispondere a eventuali domande rimanenti alla fine del test e **seleziona** "Submit".

   1. Ciò contrassegna la fine del test interattiva.

5. Esaminare il risultato per la verifica del pacchetto di estensione OEM. Il test è stato completato correttamente, pianificare il motore di simulazione del Cloud per l'esecuzione.

Per inviare un richiesta di firma del pacchetto, inviare [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) il nome della soluzione e il nome di convalida del pacchetto associato a questa esecuzione.

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md)

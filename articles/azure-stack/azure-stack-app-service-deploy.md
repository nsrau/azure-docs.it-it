---
title: 'Distribuire servizi App: Stack Azure | Documenti Microsoft'
description: Linee guida dettagliate per la distribuzione di servizio App di Azure Stack
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: dba3da6f1aaf47c1b518fe0dba7ea22ae555c8c0
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Aggiungere un provider di risorse del servizio App di Azure Stack

Un operatore di cloud di Azure Stack, è possibile assegnare agli utenti la possibilità di creare applicazioni web e API. A tale scopo, è necessario aggiungere il [il provider di risorse di servizio App](azure-stack-app-service-overview.md) alla distribuzione di Azure Stack, come descritto in questo articolo. Dopo aver installato il provider di risorse del servizio App, è possibile includerla nelle offerte e piani. Gli utenti possono eseguire la sottoscrizione per ottenere il servizio e avviare la creazione di applicazioni.

> [!IMPORTANT]
> Prima di eseguire il programma di installazione, assicurarsi di aver seguito le istruzioni riportate in [prima di iniziare](azure-stack-app-service-before-you-get-started.md).
> 
>



## <a name="run-the-app-service-resource-provider-installer"></a>Eseguire l'installazione di provider di risorse di servizio App

L'installazione del provider di risorse di servizio App nel proprio ambiente dello Stack di Azure può richiedere fino a un'ora. Durante questo processo, il programma di installazione eseguirà il:

* Creare un contenitore blob nell'account di archiviazione di Azure Stack specificato.
* Creare una zona DNS e le voci per il servizio App.
* Registrare il provider di risorse del servizio App.
* Registrare gli elementi della raccolta di servizio App.

Per distribuire il provider di risorse di servizio App, seguire questi passaggi:

1. Eseguire appservice.exe come amministratore (azurestack\CloudAdmin).

2. Fare clic su **servizio App di distribuzione nel cloud Azure Stack**.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image01.png)

3. Verificare e accettare le condizioni di licenza Software Microsoft e quindi fare clic su **Avanti**.

4. Verificare e accettare le condizioni di licenza di terze parti e quindi fare clic su **Avanti**.

5. Assicurarsi che le informazioni di configurazione di servizio App cloud siano corrette. Se si utilizza le impostazioni predefinite durante la distribuzione del Kit di sviluppo dello Stack di Azure, è possibile accettare i valori predefiniti di seguito. Tuttavia, se state personalizzate le opzioni per la distribuzione di Azure Stack, è necessario modificare i valori in questa finestra in modo da riflettere che. Ad esempio, se si utilizza il mycloud.com suffisso di dominio, per management.mycloud.com necessario modificare l'endpoint. Dopo aver verificato le informazioni, fare clic su **Avanti**.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image02.png)

6. Nella pagina successiva:
    1. Fare clic su di **Connetti** accanto al pulsante il **sottoscrizioni di Azure Stack** casella.
        - Se si usa Azure Active Directory (Azure AD), immettere l'account amministratore di Azure AD e la password forniti quando è stato distribuito Azure Stack. Fare clic su **Sign In**.
        - Se si utilizza Active Directory Federation Services (ADFS), specificare l'account amministratore. ad esempio cloudadmin@azurestack.local. Immettere la password e fare clic su **Accedi**.
    2. Nel **sottoscrizioni di Azure Stack** , selezionare la sottoscrizione.
    3. Nel **percorsi Stack Azure** , selezionare il percorso che corrisponde all'area in cui esegue la distribuzione. Ad esempio, selezionare **locale** se la distribuzione al Kit di sviluppo dello Stack di Azure.
    4. Immettere un **nome gruppo di risorse** per la distribuzione di servizio App. Per impostazione predefinita, viene impostata su **APPSERVICE\<area\>**.
    5. Immettere il **nome Account di archiviazione** che si desidera che il servizio App di creare come parte dell'installazione. Per impostazione predefinita, viene impostata su **appsvclocalstor**.
    6. Fare clic su **Avanti**.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image03.png)

7. Immettere le informazioni per la condivisione di file e quindi fare clic su **Avanti**. L'indirizzo della condivisione file è necessario utilizzare il nome di dominio completo del File Server, ad esempio \\\appservicefileserver.local.cloudapp.azurestack.external\websites o l'indirizzo IP, ad esempio \\\10.0.0.1\websites.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image04.png)

8. Nella pagina successiva:
    1. Nel **ID applicazione identità** , immettere il GUID per l'applicazione in uso per l'identità.
    2. Nel **file di certificato di identità applicazione** casella, immettere (o passare a) il percorso del file di certificato.
    3. Nel **password certificato di identità applicazione** , immettere la password per il certificato. Questa password è quello che si sia preso nota quando si usa lo script per creare i certificati.
    4. Nel **file del certificato radice Azure Resource Manager** casella, immettere (o passare a) il percorso del file di certificato.
    5. Fare clic su **Avanti**.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image05.png)

9. Per ognuna delle tre caselle di file del certificato, fare clic su **Sfoglia** e passare al file di certificato appropriato. È necessario fornire la password per ogni certificato. Questi certificati sono quelli creati nel [passaggio di creazione certificati richiesti](azure-stack-app-service-deploy.md#create-the-required-certificates). Fare clic su **Avanti** dopo aver immesso tutte le informazioni.

    | Box | Esempio di nome file di certificato |
    | --- | --- |
    | **File di certificato SSL predefinito di servizio App** | \_. appservice.local.AzureStack.external.pfx |
    | **File del certificato SSL API del servizio App** | api.appservice.local.AzureStack.external.pfx |
    | **File del certificato SSL di server di pubblicazione del servizio App** | ftp.appservice.local.AzureStack.external.pfx |

    Se si usa un suffisso di dominio diverso durante la creazione di certificati, non utilizzare i nomi dei file di certificato *locale. AzureStack.external*. Utilizzare invece le informazioni sul dominio personalizzato.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image06.png)    

10. Immettere i dettagli di SQL Server per l'istanza del server utilizzato per ospitare i database di provider di risorse di servizio App e quindi fare clic su **Avanti**. Il programma di installazione convalida le proprietà di connessione SQL.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image07.png)    

11. Esaminare le opzioni di SKU e l'istanza del ruolo. Le impostazioni predefinite vengono popolate con il numero minimo di istanza e lo SKU minima per ogni ruolo in una distribuzione ASDK. Viene fornito un riepilogo dei requisiti di memoria e CPU virtuali per la pianificazione della distribuzione. Dopo aver effettuato le selezioni, fare clic su **Avanti**.

    > [!NOTE]
    > Per le distribuzioni di produzione, le istruzioni disponibili in [pianificazione della capacità di ruoli del server di servizio App di Azure in Azure Stack](azure-stack-app-service-capacity-planning.md).
    > 
    >

    | Ruolo | Istanze minima | SKU minimo | Note |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A1 - (1 CPU virtuale, 1792 MB) | Gestisce e mantiene l'integrità del cloud di servizio App. |
    | gestione | 1 | Standard_A2 - (Vcpu 2, 3584 MB) | Gestisce gli endpoint di gestione risorse di Azure App Service e API, estensioni portale (amministrazione, tenant, il portale di funzioni) e il servizio dati. Per supportare il failover, aumentare le istanze consigliate a 2. |
    | Autore | 1 | Standard_A1 - (1 CPU virtuale, 1792 MB) | Pubblica il contenuto tramite distribuzione web e FTP. |
    | FrontEnd | 1 | Standard_A1 - (1 CPU virtuale, 1792 MB) | Le richieste vengono indirizzate alle applicazioni di servizio App. |
    | Lavoro condiviso | 1 | Standard_A1 - (1 CPU virtuale, 1792 MB) | Gli host o applicazioni API App web e le funzioni di Azure. Si potrebbe voler aggiungere più istanze. Un operatore, è possibile definire l'offerta e scegliere il livello di qualsiasi SKU. I livelli è necessario disporre almeno di una CPU virtuali. |

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image08.png)    

    > [!NOTE]
    > **Windows Server 2016 Core non è un'immagine di piattaforma supportata per l'utilizzo con il servizio App di Azure nello Stack di Azure**.

12. Nel **selezionare immagine della piattaforma** scegliere l'immagine di macchina virtuale di distribuzione Windows Server 2016 da quelli disponibili nel provider di risorse di calcolo per il cloud di servizio App. Fare clic su **Avanti**.

13. Nella pagina successiva:
     1. Immettere il nome utente amministratore della macchina virtuale ruolo di lavoro e la password.
     2. Immettere il nome utente amministratore di altri ruoli macchina virtuale e la password.
     3. Fare clic su **Avanti**.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image09.png)    

14. Nella pagina di riepilogo:
    1. Verificare le selezioni effettuate. Per apportare modifiche, utilizzare il **precedente** pulsanti a visitare pagine precedenti.
    2. Se le configurazioni sono corrette, selezionare la casella di controllo.
    3. Per avviare la distribuzione, fare clic su **Avanti**.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image10.png)    

15. Nella pagina successiva:
    1. Tenere traccia dello stato di installazione. Servizio App nello Stack di Azure richiede circa 60 minuti per distribuire in base alle selezioni impostazione predefinita.
    2. Una volta completato correttamente il programma di installazione, fare clic su **uscita**.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy/image11.png)    


## <a name="validate-the-app-service-on-azure-stack-installation"></a>Convalidare il servizio App in installazione di Azure Stack

1. Nel portale di amministrazione di Stack di Azure, passare a **Amministrazione - servizio App**.

2. In panoramica in stato, verificare che il **stato** Mostra **tutti i ruoli sono pronti**.

    ![Gestione del servizio App](media/azure-stack-app-service-deploy/image12.png)    

## <a name="test-drive-app-service-on-azure-stack"></a>Test di servizio App di Azure stack

Dopo avere distribuito e registrare il provider di risorse del servizio App, testarlo per assicurarsi che gli utenti possono distribuire App web e API.

> [!NOTE]
> È necessario creare un'offerta con lo spazio dei nomi di Microsoft all'interno del piano. Quindi è necessario disporre di una sottoscrizione di tenant che sottoscrive l'offerta. Per ulteriori informazioni, vedere [Crea offerta](azure-stack-create-offer.md) e [crea piano](azure-stack-create-plan.md).
>
Si *deve* ha una sottoscrizione tenant a creare applicazioni che utilizzano il servizio App nello Stack di Azure. Le uniche funzionalità che è possibile completare un amministratore del servizio all'interno del portale di amministrazione sono correlate per l'amministrazione del provider di risorse del servizio App. Queste funzionalità includono l'aggiunta di capacità e la configurazione delle origini di distribuzione, SKU e piani di lavoro.
>
A partire dalla technical preview terzo, per creare web API e Azure funzioni App, è necessario utilizzare il portale tenant e dispone di una sottoscrizione tenant.

1. Nel portale tenant di Azure Stack, fare clic su **New** > **Web e dispositivi mobili** > **App Web**.

2. Nel **App Web** pannello, digitare un nome nella **app Web** casella.

3. In **gruppo di risorse**, fare clic su **New**. Digitare un nome nella **gruppo di risorse** casella.

4. Fare clic su **Piano di servizio app/Località** > **Crea nuovo**.

5. Nel **piano di servizio App** pannello, digitare un nome nella **piano di servizio App** casella.

6. Fare clic su **tariffario** > **gratuito condiviso** o **condiviso condiviso** > **selezionare**  >   **OK** > **creare**.

7. In meno di un minuto, viene visualizzato un riquadro per la nuova app web nel dashboard. Fare clic sul riquadro.

8. Nel **App Web** pannello, fare clic su **Sfoglia** per visualizzare il sito Web predefinito per questa applicazione.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Distribuire un sito Web WordPress, DNN o Django (facoltativo)

1. Nel portale tenant di Azure Stack, fare clic su  **+** , passare a Azure Marketplace, distribuire un sito Web Django e attendere il completamento. La piattaforma web Django utilizza un database di sistema basato su file. Non richiede alcun provider di risorse aggiuntive, ad esempio SQL o MySQL.

2. Se è stato distribuito un provider di risorse MySQL, è possibile distribuire un sito Web WordPress dal Marketplace. Quando viene richiesto per i parametri del database, immettere il nome utente come  *User1@Server1* , con il nome utente e il nome del server di propria scelta.

3. Se è stato distribuito un provider di risorse di SQL Server, è possibile distribuire un sito Web Learning Marketplace. Quando viene richiesto per i parametri del database, scegliere un database nel computer che esegue SQL Server è connesso al provider di risorse.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile provare altre [piattaforma come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md).

- [Provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

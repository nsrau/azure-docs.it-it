---
title: 'Distribuire servizi App: Azure Stack | Microsoft Docs'
description: Indicazioni dettagliate per la distribuzione di servizio App di Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: anwestg
ms.openlocfilehash: ae742bbe1e5b751a8a8cabf378afd049728c3b8a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717506"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Aggiungere un provider di risorse del servizio App in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Usare le indicazioni fornite in questo articolo per distribuire il servizio App in Azure Stack.

>[!IMPORTANT]  
>Applicare l'aggiornamento 1807 al sistema integrato Azure Stack o distribuire più recente Azure Stack Development Kit (ASDK) prima della distribuzione del servizio App di Azure 1.3.

È possibile concedere agli utenti la possibilità di creare applicazioni web e API. Per consentire agli utenti di creare queste applicazioni, è necessario:

 - Aggiungere il [provider di risorse del servizio App](azure-stack-app-service-overview.md) per la distribuzione di Azure Stack usando i passaggi descritti in questo articolo.
 - Dopo aver installato il provider di risorse del servizio App, è possibile includerla in piani e offerte. Gli utenti possono eseguire la sottoscrizione per il servizio e per iniziare la creazione di applicazioni.

> [!IMPORTANT]  
> Prima di eseguire il programma di installazione di provider di risorse, assicurarsi di aver seguito le indicazioni fornite in [prima di iniziare a](azure-stack-app-service-before-you-get-started.md).

## <a name="run-the-app-service-resource-provider-installer"></a>Eseguire l'installazione di provider risorse servizio App

L'installazione del provider di risorse del servizio App richiede almeno un'ora. Il periodo di tempo necessita dipende dal ruolo quante istanze è la distribuzione. Durante la distribuzione, il programma di installazione esegue le attività seguenti:

 - Creare un contenitore blob nell'account di archiviazione di Azure Stack specificato.
 - Creare una zona DNS e le voci per il servizio App.
 - Registrare il provider di risorse del servizio App.
 - Registrare gli elementi della raccolta di servizio App.

Per distribuire il provider di risorse del servizio App, seguire questa procedura:

1. Eseguire appservice.exe come amministratore da un computer che può accedere l'Endpoint di gestione risorse di Azure di Azure Stack Admin.

2. Selezionare **distribuzione servizio App o l'aggiornamento alla versione più recente**.

    ![Programma di installazione del servizio App][1]

3. Verificare e accettare le condizioni di licenza Software Microsoft e quindi selezionare **successivo**.

4. Verificare e accettare le condizioni di licenza di terze parti e quindi selezionare **successivo**.

5. Assicurarsi che le informazioni di configurazione del servizio App cloud siano corrette. Se si usa le impostazioni predefinite durante la distribuzione di Azure Stack Development Kit (ASDK), è possibile accettare i valori predefiniti. Tuttavia, se le opzioni sono personalizzate quando si è distribuito il ASDK o esegue la distribuzione in un sistema integrato Azure Stack, è necessario modificare i valori in questa finestra in modo da riflettere le differenze.

   Ad esempio, se si usa il mycloud.com suffisso di dominio, necessario modificare l'endpoint di Azure Resource Manager di Azure Stack Tenant alla gestione. &lt;regione&gt;. mycloud.com. Esaminare queste impostazioni e quindi selezionare **successivo** per salvare le impostazioni.

   ![Programma di installazione del servizio App][2]

6. Nella pagina successiva di programma di installazione del servizio App, seguire questa procedura:

    a. Selezionare **Connect** accanto al **sottoscrizioni di Azure Stack**.

     - Se si usa Azure Active Directory (Azure AD), immettere l'account amministratore di Azure AD e la password specificata quando è stato distribuito Azure Stack. Selezionare **Accedi**.
     - Se si usa Active Directory Federation Services (ADFS), specificare l'account di amministratore. Ad esempio: cloudadmin@azurestack.local. Immettere la password e quindi selezionare **Accedi**.

   b. Nelle **sottoscrizioni di Azure Stack**, selezionare la **sottoscrizione del Provider predefinito**.

     >[!NOTE]
     >Attualmente, servizio App può essere distribuito solo per i **sottoscrizione del Provider predefinito**.

   c. Nel **località di Azure Stack**, selezionare il percorso che corrisponde all'area di cui si esegue la distribuzione. Ad esempio, selezionare **locale** se la distribuzione in Azure Stack Development Kit.

    ![Programma di installazione del servizio App][3]

7. A questo punto è possibile distribuire in una rete virtuale esistente che è stato configurato [attenendosi alla procedura seguente](azure-stack-app-service-before-you-get-started.md#virtual-network), o lasciare il programma di installazione del servizio App di creare una nuova rete virtuale e subnet. Per creare una rete virtuale, seguire questa procedura:

   a. Selezionare **crea rete virtuale con le impostazioni predefinite**, accettare le impostazioni predefinite e quindi selezionare **successivo**.

   b. In alternativa, selezionare **usare rete virtuale esistente e le subnet**. Completare le azioni seguenti:

     - Selezionare il **gruppo di risorse** che contiene la rete virtuale.
     - Scegliere il **rete virtuale** nome che si desidera distribuire.
     - Selezionare i valori corretti **Subnet** i valori per ogni subnet ruolo richiesto.
     - Selezionare **Avanti**.

   ![Programma di installazione del servizio App][4]

8. Immettere le informazioni per la condivisione file e quindi selezionare **successivo**. L'indirizzo della condivisione file deve usare il nome di dominio completo (FQDN) o l'indirizzo IP del File Server. Ad esempio, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, o \\\10.0.0.1\websites.

   >[!NOTE]
   >Il programma di installazione prova a verificare la connettività alla condivisione file prima di procedere. Tuttavia, se si distribuisce in una rete virtuale esistente, questo test di connettività potrebbe non riuscire. Si riceve un avviso e un prompt dei comandi per continuare. Se la condivisione file di informazioni siano corrette, continua la distribuzione.

   ![Programma di installazione del servizio App][7]

9. Nella pagina successiva di programma di installazione del servizio App, seguire questa procedura:

   a. Nel **ID applicazione identità** casella, immettere il GUID per l'applicazione in uso per l'identità (da Azure AD).

   b. Nel **file di certificato di identità applicazione** casella, immettere (o passare a) il percorso del file del certificato.

   c. Nel **password certificato di identità applicazione** immettere la password per il certificato. Questa password è quella che si sia preso nota quando si usa lo script per creare i certificati.

   d. Nel **file di certificato radice di Azure Resource Manager** casella, immettere (o passare a) il percorso del file del certificato.

   e. Selezionare **Avanti**.

   ![Programma di installazione del servizio App][9]

10. Per ognuna delle caselle tre certificato file, selezionare **esplorare** e passare al file di certificato appropriato. È necessario fornire la password per ogni certificato. Questi certificati sono quelli che è stato creato nel [passaggio di creazione dei certificati richiesti](azure-stack-app-service-before-you-get-started.md#get-certificates). Selezionare **successivo** dopo aver immesso tutte le informazioni.

    | Box | Esempio di nome file di certificato |
    | --- | --- |
    | **File certificato SSL predefinito del servizio App** | \_.appservice.local.AzureStack.external.pfx |
    | **File di certificato SSL di API del servizio App** | api.appservice.local.AzureStack.external.pfx |
    | **File del certificato SSL del server di pubblicazione servizio App** | ftp.appservice.local.AzureStack.external.pfx |

    Se si usa un suffisso di dominio diverso durante la creazione di certificati, non usano nomi di file di certificato *locale. AzureStack.external*. In alternativa, usare le informazioni di dominio personalizzato.

    ![Programma di installazione del servizio App][10]

11. Immettere i dettagli di SQL Server per l'istanza del server utilizzato per ospitare i database del provider risorse servizio App e quindi selezionare **successivo**. Il programma di installazione convalida le proprietà di connessione SQL.

    > [!NOTE]
    > Il programma di installazione prova a verificare la connettività a SQL Server prima di procedere. Tuttavia, se si distribuisce in una rete virtuale esistente, potrebbe essere file di questo test di connettività. Si riceve un avviso e un prompt dei comandi per continuare. Se le informazioni di SQL Server siano corrette, continuare la distribuzione.
    >
    > Servizio App di Azure in Azure Stack 1.3 e versioni successive, il programma di installazione verificherà che SQL Server disponga di indipendenza del database abilitata a livello di SQL Server.  In caso contrario, verrà richiesto con l'eccezione seguente:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > Vedere le [note sulla versione per il servizio App di Azure in Azure Stack 1.3](azure-stack-app-service-release-notes-update-three.md) per altri dettagli.

    ![Programma di installazione del servizio App][11]

12. Esaminare l'istanza del ruolo e le opzioni dello SKU. Le impostazioni predefinite popolare con il numero minimo di istanze e lo SKU per ogni ruolo in una distribuzione ASDK minimo. Viene fornito un riepilogo dei requisiti di memoria e vCPU per la pianificazione della distribuzione. Dopo aver effettuato le selezioni effettuate, selezionare **successivo**.

    >[!NOTE]
    >Per le distribuzioni di produzione, seguire le istruzioni disponibili nel [pianificazione della capacità per i ruoli server di servizio App di Azure in Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Ruolo | Numero minimo di istanze | SKU minimo | Note |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2 - (2 vCPU, 3584 MB) | Gestito e mantenuto l'integrità del cloud del servizio App. |
    | Gestione | 1 | Standard_A2 - (2 Vcpu, 3584 MB) | Gestisce gli endpoint di Azure Resource Manager di App Service e API, le estensioni del portale (amministrazione, tenant, il portale di funzioni) e il servizio dati. Per supportare il failover, aumentare le istanze delle 2 consigliate. |
    | Editore | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Pubblica il contenuto tramite distribuzione web e FTP. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Indirizza le richieste alle applicazioni di servizio App. |
    | Ruolo di lavoro condiviso | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Host web o applicazioni API e App di funzioni di Azure. È possibile aggiungere altre istanze. Un operatore, è possibile definire l'offerta e scegliere qualsiasi livello SKU. I livelli devono avere almeno una CPU virtuale. |

    ![Programma di installazione del servizio App][13]

    >[!NOTE]
    >**Windows Server 2016 Core non è un'immagine di piattaforma supportati per l'uso con il servizio App di Azure in Azure Stack.  Non usare le immagini di valutazione per le distribuzioni di produzione.**

13. Nel **Seleziona immagine di piattaforma** , Seleziona immagine di macchina virtuale Windows Server 2016 di distribuzione dalle immagini disponibili nel provider di risorse di calcolo per il cloud del servizio App. Selezionare **Avanti**.

14. Nella pagina successiva di programma di installazione del servizio App, seguire questa procedura:

     a. Immettere il nome utente amministratore di macchine virtuali ruolo di lavoro e la password.

     b. Immettere il nome utente amministratore di altri ruoli macchina virtuale e la password.

     c. Selezionare **Avanti**.

    ![Programma di installazione del servizio App][15]

15. Nella pagina di riepilogo di programma di installazione del servizio App, seguire questa procedura:

    a. Verificare le selezioni effettuate. Per apportare modifiche, usare il **Previous** pulsanti visitare le pagine precedenti.

    b. Se le configurazioni siano corrette, selezionare la casella di controllo.

    c. Per avviare la distribuzione, selezionare **successivo**.

    ![Programma di installazione del servizio App][16]

16. Nella pagina successiva di programma di installazione del servizio App, seguire questa procedura:

    a. Monitorare lo stato di installazione. Servizio App in Azure Stack richiede circa 60 minuti per distribuire in base alle selezioni impostazione predefinita.

    b. Dopo che il programma di installazione è stata completata correttamente, selezionare **Exit**.

    ![Programma di installazione del servizio App][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Convalidare il servizio App nell'installazione di Azure Stack

1. Nel portale di amministrazione di Azure Stack, passare a **Amministrazione - servizio App**.

2. Nella panoramica sotto lo stato, verificare che il **lo stato** Mostra **tutti i ruoli sono pronti**.

    ![Gestione del servizio App](media/azure-stack-app-service-deploy/image12.png)

   >[!IMPORTANT]
   >Se si distribuisce in una rete virtuale esistente e si usa un indirizzo IP interno per la connessione per il file server, è necessario aggiungere una regola di sicurezza in uscita. Questa regola consente il traffico tra la subnet del ruolo di lavoro e file server SMB.  A tale scopo, passare a WorkersNsg nel portale di amministrazione e aggiungere una regola di sicurezza in uscita con le proprietà seguenti:<br>
    >  - Origine: tutti
    >  - Intervallo di porte di origine: *
    >  - Destinazione: Gli indirizzi IP
    >  - Intervallo di indirizzi IP di destinazione: intervallo di indirizzi IP per il file server
    >  - Intervallo di porte di destinazione: 445
    >  - Protocollo: TCP
    >  - Azione: Consenti
    >  - Priorità: 700
    >  - Nome: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Il test drive di servizio App in Azure Stack

Dopo che si distribuisce e si registra il provider di risorse del servizio App, testarla per assicurarsi che gli utenti possono distribuire App web e API.

>[!NOTE]
>È necessario creare un'offerta con lo spazio dei nomi Microsoft. Web nel piano. È anche necessaria una sottoscrizione di tenant che sottoscrive l'offerta. Per altre informazioni, vedere [Crea offerta](azure-stack-create-offer.md) e [crea piano](azure-stack-create-plan.md).
>
>Si *necessario* dispone di una sottoscrizione di tenant per creare applicazioni che utilizzano il servizio App in Azure Stack. Le uniche operazioni che possono essere completati un amministratore del servizio nel portale di amministrazione sono correlate per l'amministrazione del provider di risorse del servizio App. Ciò include l'aggiunta di capacità, la configurazione delle origini di distribuzione e l'aggiunta di piani di lavoro e gli SKU.
>
>Per creare web, API e Azure funzioni delle App, è necessario usare il portale tenant e avere una sottoscrizione tenant.
>

Per creare un'app web di test, seguire questa procedura:

1. Nel portale per gli utenti Azure Stack, selezionare **+ crea una risorsa** > **Web e dispositivi mobili** > **App Web**.

2. Sotto **App Web**, immettere un nome nella **app Web**.

3. Sotto **gruppo di risorse**, selezionare **New**. Immettere un nome per il **gruppo di risorse**.

4. Selezionare **piano di servizio App/località** > **Crea nuovo**.

5. Sotto **piano di servizio App**, immettere un nome per il **piano di servizio App**.

6. Selezionare **piano tariffario** > **gratuito condiviso** oppure **Shared-Shared** > **selezionare**  >  **OK** > **creare**.

7. Nel dashboard viene visualizzato un riquadro per la nuova app web. Selezionare il riquadro.

8. Sul **App Web**, selezionare **Sfoglia** per visualizzare il sito Web predefinito per questa app.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Distribuisci un sito Web WordPress, DNN o Django (facoltativo)

1. Nel portale tenant di Azure Stack, selezionare **+**, aprire il Marketplace di Azure, distribuire un sito Web Django e quindi attendere il completamento della distribuzione. La piattaforma web di Django Usa un database di sistema in base al file. Non richiede alcun provider di risorse aggiuntive, ad esempio SQL o MySQL.

2. Se è stata distribuita anche un provider di risorse MySQL, è possibile distribuire un sito Web WordPress dal Marketplace. Quando richiesto per i parametri del database, immettere il nome utente *User1@Server1*, con il nome utente e il nome del server di propria scelta.

3. Se è stata distribuita anche un provider di risorse di SQL Server, è possibile distribuire un sito di rete neurale profonda dal Marketplace. Quando richiesto per i parametri del database, scegliere un database in computer che esegue SQL Server che è connesso al provider di risorse.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile provare altri [piattaforma distribuita come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md).

 - [Provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md)
 - [Provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png

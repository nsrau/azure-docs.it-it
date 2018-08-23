---
title: Distribuire il servizio App in un ambiente offline in Azure Stack | Microsoft Docs
description: Indicazioni dettagliate su come distribuire il servizio App in un ambiente Azure Stack disconnesso protetto da AD FS.
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
ms.date: 08/15/2018
ms.author: anwestg
ms.openlocfilehash: 9e36e470c3516c55089ce1e44540b6b1eacbb6b2
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139736"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Aggiungere un provider di risorse del servizio App a un ambiente Azure Stack disconnesso protetto da AD FS

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!IMPORTANT]
> Applicare l'aggiornamento 1807 al sistema integrato Azure Stack o distribuire il kit di sviluppo di Azure Stack più recente prima della distribuzione del servizio App di Azure 1.3.
>
>

Seguendo le istruzioni riportate in questo articolo, è possibile installare il [provider di risorse del servizio App](azure-stack-app-service-overview.md) in un ambiente Azure Stack:

- non è connesso a Internet
- protetta da Active Directory Federation Services (ADFS).

Per aggiungere il provider di risorse del servizio App per la distribuzione di Azure Stack non in linea, è necessario completare le attività di primo livello:

1. Completare la [passaggi preliminari](azure-stack-app-service-before-you-get-started.md) (come acquistare i certificati, che può richiedere alcuni giorni per la ricezione).
2. [Scaricare ed estrarre i file di installazione e helper](azure-stack-app-service-before-you-get-started.md) a un computer connesso a Internet.
3. Creare un pacchetto di installazione offline.
4. Eseguire il file di programma di installazione appservice.exe.

## <a name="create-an-offline-installation-package"></a>Creare un pacchetto di installazione offline

Per distribuire il servizio App in un ambiente disconnesso, è innanzitutto necessario creare un pacchetto di installazione offline in un computer connesso a Internet.

1. Eseguire il programma di installazione AppService.exe in un computer connesso a Internet.

2. Fare clic su **avanzate** > **creare il pacchetto di installazione offline**.

    ![Programma di installazione del servizio App][1]

3. Il programma di installazione del servizio App crea un pacchetto di installazione offline e viene visualizzato il percorso a esso. È possibile fare clic su **Apri cartella** per aprire la cartella in Esplora file.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy-offline/image02.png)

4. Copiare il pacchetto di installazione offline e il programma di installazione (AppService.exe) nel computer host Azure Stack.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Completare l'installazione offline di servizio App in Azure Stack

1. Eseguire appservice.exe come amministratore da un computer in grado di raggiungere l'endpoint di Azure Stack amministrazione Azureresourcemanager.

2. Fare clic su **avanzate** > **completare l'installazione offline**.

    ![Programma di installazione del servizio App][2]

3. Passare al percorso del pacchetto di installazione offline creato in precedenza e quindi fare clic su **successivo**.

    ![Programma di installazione del servizio App](media/azure-stack-app-service-deploy-offline/image04.png)

4. Verificare e accettare le condizioni di licenza Software Microsoft e quindi fare clic su **successivo**.

5. Verificare e accettare le condizioni di licenza di terze parti e quindi fare clic su **successivo**.

6. Assicurarsi che le informazioni di configurazione del servizio App cloud siano corrette. Se si usa le impostazioni predefinite durante la distribuzione di Azure Stack Development Kit, è possibile accettare i valori predefiniti di seguito. Tuttavia, se le opzioni sono personalizzate quando si è distribuito Azure Stack o si esegue la distribuzione in un sistema integrato, è necessario modificare i valori in questa finestra per riflettere il valore. Ad esempio, se si usa il mycloud.com suffisso di dominio, necessario modificare l'endpoint di Azure Resource Manager di Azure Stack Tenant alla gestione. <region>. mycloud.com. Dopo aver verificato le informazioni, fare clic su **successivo**.

    ![Programma di installazione del servizio App][3]

7. Nella pagina successiva:
    1. Fare clic sui **Connect** accanto alle **sottoscrizioni di Azure Stack** casella.
        - Specificare l'account di amministratore. Ad esempio: cloudadmin@azurestack.local. Immettere la password e fare clic su **Accedi**.
    2. Nel **delle sottoscrizioni di Azure Stack** , quindi selezionare la **sottoscrizione del Provider predefinito**.
    
    > [!NOTE]
    > Servizio App può essere distribuito solo nel **sottoscrizione del Provider predefinito** in questo momento.  In un futuro aggiornamento App servizio verranno distribuiti nella nuova sottoscrizione di misurazione introdotta in Azure Stack 1804 e tutte le distribuzioni esistenti verranno migrate anche a questa nuova sottoscrizione.
    >
    >
    
    3. Nel **località di Azure Stack** selezionare il percorso che corrisponde all'area di cui si esegue la distribuzione. Ad esempio, selezionare **locale** se la distribuzione in Azure Stack Development Kit.
    4. Fare clic su **Avanti**.

    ![Programma di installazione del servizio App][4]

8. È ora possibile distribuire in una rete virtuale esistente configurato tramite la procedura [qui](azure-stack-app-service-before-you-get-started.md#virtual-network), o consentire il programma di installazione del servizio App di creare una rete virtuale e subnet associate.
    1. Selezionare **crea rete virtuale con le impostazioni predefinite**, accettare le impostazioni predefinite e quindi fare clic su **successivo**, o;
    2. Selezionare **usare rete virtuale esistente e le subnet**.
        1. Selezionare il **gruppo di risorse** che contiene la rete virtuale;
        2. Scegliere i valori corretti **rete virtuale** nome si desidera distribuire in;
        3. Selezionare i valori corretti **Subnet** i valori per ogni subnet ruolo necessari;
        4. Fare clic su **Avanti**

    ![Programma di installazione del servizio App][5]

9. Immettere le informazioni per la condivisione file e quindi fare clic su **successivo**. L'indirizzo della condivisione file deve usare il nome di dominio completo o indirizzo IP del File Server. Ad esempio, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, o \\\10.0.0.1\websites

    > [!NOTE]
    > Il programma di installazione tenta di testare la connettività alla condivisione file prima di procedere.  Tuttavia, se si sceglie di distribuire in una rete virtuale esistente, il programma di installazione potrebbe non essere in grado di connettersi alla condivisione file e viene visualizzato un avviso che chiede se si desidera continuare.  Verificare le informazioni di condivisione file e continuare se sono corrette.
    >
    >

   ![Programma di installazione del servizio App][8]

10. Nella pagina successiva:
    1. Nel **ID applicazione identità** casella, immettere il GUID per l'applicazione in uso per l'identità (da Azure AD).
    2. Nel **file di certificato di identità applicazione** casella, immettere (o passare a) il percorso del file del certificato.
    3. Nel **password certificato di identità applicazione** immettere la password per il certificato. Questa password è quella che si sia preso nota quando si usa lo script per creare i certificati.
    4. Nel **file di certificato radice di Azure Resource Manager** casella, immettere (o passare a) il percorso del file del certificato.
    5. Fare clic su **Avanti**.

    ![Programma di installazione del servizio App][10]

11. Per ognuna delle tre caselle di file del certificato, fare clic su **esplorare** e quindi passare al file del certificato appropriato. È necessario fornire la password per ogni certificato. Questi certificati sono quelli che è stato creato nel [passaggio di creazione dei certificati richiesti](azure-stack-app-service-before-you-get-started.md#get-certificates). Fare clic su **successivo** dopo aver immesso tutte le informazioni.

    | Box | Esempio di nome file di certificato |
    | --- | --- |
    | **File certificato SSL predefinito del servizio App** | \_.appservice.local.AzureStack.external.pfx |
    | **File di certificato SSL di API del servizio App** | api.appservice.local.AzureStack.external.pfx |
    | **File del certificato SSL del server di pubblicazione servizio App** | ftp.appservice.local.AzureStack.external.pfx |

    Se si usa un suffisso di dominio diverso durante la creazione di certificati, non usano nomi di file di certificato *locale. AzureStack.external*. In alternativa, usare le informazioni di dominio personalizzato.

    ![Programma di installazione del servizio App][11]

12. Immettere i dettagli di SQL Server per l'istanza del server usato per ospitare i database del provider risorse servizio App e quindi fare clic su **successivo**. Il programma di installazione convalida le proprietà di connessione SQL. Si **necessario** immettere l'indirizzo ip interno o nome di dominio completo per il nome di SQL Server.

    > [!NOTE]
    > Il programma di installazione tenta di testare la connettività a SQl Server prima di procedere.  Tuttavia, se si sceglie di distribuire in una rete virtuale esistente, il programma di installazione potrebbe non essere in grado di connettersi a SQL Server e viene visualizzato un avviso che chiede se si desidera continuare.  Verificare le informazioni di SQL Server e continuare se sono corrette.
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
   
   ![Programma di installazione del servizio App][12]

13. Esaminare l'istanza del ruolo e le opzioni dello SKU. I valori predefiniti vengono popolati con il numero minimo di istanze e lo SKU per ogni ruolo in una distribuzione ASDK minimo. Viene fornito un riepilogo dei requisiti di memoria e vCPU per la pianificazione della distribuzione. Dopo aver effettuato le selezioni, fare clic su **successivo**.

     > [!NOTE]
     > Per le distribuzioni di produzione, seguire le indicazioni fornite in [pianificazione della capacità per i ruoli server di servizio App di Azure in Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Ruolo | Numero minimo di istanze | SKU minimo | Note |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2 - (2 vCPU, 3584 MB) | Gestito e mantenuto l'integrità del cloud del servizio App. |
    | Gestione | 1 | Standard_A2 - (2 Vcpu, 3584 MB) | Gestisce gli endpoint di Azure Resource Manager di App Service e API, le estensioni del portale (amministrazione, tenant, il portale di funzioni) e il servizio dati. Per supportare il failover, aumentare le istanze delle 2 consigliate. |
    | Editore | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Pubblica il contenuto tramite distribuzione web e FTP. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Indirizza le richieste alle applicazioni di servizio App. |
    | Ruolo di lavoro condiviso | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Host web o applicazioni API e App di funzioni di Azure. È possibile aggiungere altre istanze. Un operatore, è possibile definire l'offerta e scegliere qualsiasi livello SKU. I livelli devono avere almeno una CPU virtuale. |

    ![Programma di installazione del servizio App][14]

    > [!NOTE]
    > **Windows Server 2016 Core non è un'immagine di piattaforma supportata per l'uso con il servizio App di Azure in Azure Stack.  Non usare le immagini di valutazione per le distribuzioni di produzione.  Servizio App di Azure in Azure Stack è necessario che Microsoft.NET 3.5.1 SP1 viene attivata nell'immagine usata per la distribuzione.   Marketplace diffuso Windows Server 2016 le immagini non dispongono di questa funzionalità è abilitata.**

14. Nel **Seleziona immagine di piattaforma** , Seleziona immagine di macchina virtuale Windows Server 2016 la distribuzione tra quelli disponibili nel provider di risorse di calcolo per il cloud del servizio App. Fare clic su **Avanti**.

15. Nella pagina successiva:
     1. Immettere il nome utente amministratore di macchine virtuali ruolo di lavoro e la password.
     2. Immettere il nome utente amministratore di altri ruoli macchina virtuale e la password.
     3. Fare clic su **Avanti**.

    ![Programma di installazione del servizio App][16]

16. Nella pagina di riepilogo:
    1. Verificare le selezioni effettuate. Per apportare modifiche, usare il **Previous** pulsanti visitare le pagine precedenti.
    2. Se le configurazioni siano corrette, selezionare la casella di controllo.
    3. Per avviare la distribuzione, fare clic su **successivo**.

    ![Programma di installazione del servizio App][17]

17. Nella pagina successiva:
    1. Monitorare lo stato di installazione. Servizio App in Azure Stack richiede circa 60 minuti per distribuire in base alle selezioni impostazione predefinita.
    2. Dopo che il programma di installazione è stata completata correttamente, fare clic su **Exit**.

    ![Programma di installazione del servizio App][18]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Convalidare il servizio App nell'installazione di Azure Stack

1. Nel portale di amministrazione di Azure Stack, passare a **Amministrazione - servizio App**.

2. Nella panoramica sotto lo stato, verificare che il **lo stato** Mostra **tutti i ruoli sono pronti**.

    ![Gestione del servizio App](media/azure-stack-app-service-deploy/image12.png)
    
> [!NOTE]
> Se si sceglie di distribuire in una rete virtuale esistente e un indirizzo IP interno per la connessione per il file server, è necessario aggiungere una regola di sicurezza in uscita, consentendo il traffico tra la subnet del ruolo di lavoro e file server SMB.  A tale scopo, passare a WorkersNsg nel portale di amministrazione e aggiungere una regola di sicurezza in uscita con le proprietà seguenti:
> * Origine: tutti
> * Intervallo di porte di origine: *
> * Destinazione: Gli indirizzi IP
> * Intervallo di indirizzi IP di destinazione: intervallo di indirizzi IP per il file server
> * Intervallo di porte di destinazione: 445
> * Protocollo: TCP
> * Azione: Consenti
> * Priorità: 700
> * Nome: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Il test drive di servizio App in Azure Stack

Dopo che si distribuisce e si registra il provider di risorse del servizio App, testarla per assicurarsi che gli utenti possono distribuire App web e API.

> [!NOTE]
> È necessario creare un'offerta con lo spazio dei nomi Microsoft. Web all'interno del piano. Quindi è necessario disporre di una sottoscrizione di tenant che sottoscrive questa offerta. Per altre informazioni, vedere [Crea offerta](azure-stack-create-offer.md) e [crea piano](azure-stack-create-plan.md).
>
Si *necessario* dispone di una sottoscrizione di tenant per creare applicazioni che utilizzano il servizio App in Azure Stack. Le uniche funzionalità che un amministratore del servizio è completata entro il portale di amministrazione sono correlate per l'amministrazione del provider di risorse del servizio App. Queste funzionalità includono l'aggiunta di capacità, la configurazione delle origini di distribuzione e l'aggiunta di piani di lavoro e gli SKU.
>
A partire da technical preview terzo, per creare web, API e Azure funzioni delle App, è necessario usare il portale tenant e avere una sottoscrizione tenant.

1. Nel portale tenant di Azure Stack, fare clic su **New** > **Web e dispositivi mobili** > **App Web**.

2. Nel **App Web** digitare un nome nel pannello il **app Web** casella.

3. Sotto **gruppo di risorse**, fare clic su **New**. Digitare un nome nella **gruppo di risorse** casella.

4. Fare clic su **Piano di servizio app/Località** > **Crea nuovo**.

5. Nel **piano di servizio App** digitare un nome nel pannello il **piano di servizio App** casella.

6. Fare clic su **piano tariffario** > **gratuito condiviso** oppure **Shared-Shared** > **selezionare**  >   **OK** > **creare**.

7. In meno di un minuto, viene visualizzato un riquadro per la nuova app web nel dashboard. Fare clic sul riquadro.

8. Nel **App Web** pannello, fare clic su **Sfoglia** per visualizzare il sito Web predefinito per questa app.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Distribuisci un sito Web WordPress, DNN o Django (facoltativo)

1. Nel portale tenant di Azure Stack, fare clic su **+**, aprire il Marketplace di Azure, distribuire un sito Web Django e attendere il completamento corretto. La piattaforma web di Django Usa un database di sistema in base al file. Non richiede alcun provider di risorse aggiuntive, ad esempio SQL o MySQL.

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
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png

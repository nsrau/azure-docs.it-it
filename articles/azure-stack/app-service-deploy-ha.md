---
title: Distribuire il servizio App di Azure Stack in una configurazione a disponibilità elevata | Microsoft Docs
description: Informazioni su come distribuire il servizio App in Azure Stack tramite una configurazione a disponibilità elevata.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/13/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: db95be94028fcf16871a9dcfee5f0d87eb5d2cdc
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285667"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Distribuire il servizio App in una configurazione a disponibilità elevata

Questo articolo illustra come usare elementi del marketplace Azure Stack per distribuire il servizio App di Azure Stack in una configurazione a disponibilità elevata. Oltre gli elementi disponibili nel marketplace, questa soluzione Usa anche il [appservice-condivisione file a sqlserver-disponibilità elevata](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) modello di avvio rapido di Azure Stack. Questo modello consente di automatizzare la creazione di un'infrastruttura a disponibilità elevata per l'hosting di provider di risorse del servizio App. Servizio App viene quindi installato in questa infrastruttura di macchine Virtuali a disponibilità elevata. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Distribuire VM a disponibilità elevata dell'infrastruttura di servizio App
Il [appservice-condivisione file a sqlserver-disponibilità elevata](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) modello di avvio rapido di Azure Stack semplifica la distribuzione del servizio App in una configurazione a disponibilità elevata. Si devono essere distribuito nella sottoscrizione del Provider predefinito. 

Quando usato per creare una risorsa personalizzata in Azure Stack, il modello crea:
- Una rete virtuale e subnet obbligatorie.
- Gruppi di sicurezza di rete per file server, SQL Server e le subnet di Active Directory Domain Services (AD DS).
- Account di archiviazione per i dischi delle VM e server di controllo di cluster cloud.
- Un servizio di bilanciamento del carico interno per le macchine virtuali SQL con indirizzo IP privato associato al listener di SQL AlwaysOn.
- Tre set di disponibilità per il dominio di Active Directory, cluster di file server e il cluster SQL.
- Cluster SQL a due nodi.
- Cluster a due nodi file server.
- Due controller di dominio.

### <a name="required-azure-stack-marketplace-items"></a>Necessari elementi del marketplace Azure Stack
Prima di usare questo modello, assicurarsi che la segue [elementi del marketplace Azure Stack](azure-stack-marketplace-azure-items.md) sono disponibili nell'istanza di Azure Stack:

- Immagine Windows Server 2016 Datacenter Core (per AD DS e file server di macchine virtuali)
- SQL Server 2016 SP2 in Windows Server 2016 (Enterprise)
- Ultima estensione SQL IaaS 
- Ultima versione di PowerShell Desired State Configuration di estensione 

> [!TIP]
> Revisione [il file Leggimi modello](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) su GitHub per altre informazioni sui requisiti di modello e i valori predefiniti. 

### <a name="deploy-the-app-service-infrastructure"></a>Distribuire l'infrastruttura del servizio App
Usare i passaggi descritti in questa sezione per creare una distribuzione personalizzata usando il **appservice-condivisione file a sqlserver-disponibilità elevata** modello di avvio rapido di Azure Stack.

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Selezionare **\+** **crea una risorsa** > **Custom**, quindi **distribuzione modello**.

   ![Distribuzione del modello personalizzato](media/app-service-deploy-ha/1.png)


3. Nel **distribuzione personalizzata** blade, selezionare **modifica modello** > **modello di avvio rapido** e quindi usare l'elenco a discesa di modelli personalizzati disponibili per Selezionare il **appservice-condivisione file a sqlserver-disponibilità elevata** modello, fare clic su **OK**e quindi **Salva**.

   ![Selezionare il modello di avvio rapido appservice-condivisione file a sqlserver-disponibilità elevata](media/app-service-deploy-ha/2.png)

4. Nel **distribuzione personalizzata** blade, selezionare **Upravit parametry** e scorrere verso il basso per esaminare i valori di modello predefiniti. Modificare questi valori in base alle esigenze per fornire tutte le informazioni sui parametri necessari e quindi fare clic su **OK**.<br><br> Come minimo, specificare le password complesse per i parametri ADMINPASSWORD FILESHAREOWNERPASSWORD, FILESHAREUSERPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD e SQLLOGINPASSWORD.
    
   ![Modificare i parametri di distribuzione personalizzato](media/app-service-deploy-ha/3.png)

5. Nel **distribuzione personalizzata** pannello verificare **sottoscrizione del Provider predefinito** è selezionato come la sottoscrizione da usare e quindi creare un nuovo gruppo di risorse o selezionare un gruppo di risorse esistente, per l'oggetto personalizzato distribuzione.<br><br> Successivamente, selezionare la località del gruppo di risorse (**locale** per le installazioni ASDK) e quindi fare clic su **crea**. Le impostazioni di distribuzione personalizzato verranno convalidate prima dell'avvio di distribuzione del modello.

    ![Creare una distribuzione personalizzata](media/app-service-deploy-ha/4.png)

6. Nel portale di amministrazione, selezionare **gruppi di risorse** e quindi il nome del gruppo di risorse creato per la distribuzione personalizzata (**app-service-a disponibilità elevata** in questo esempio). Visualizzare lo stato della distribuzione per verificare che tutte le distribuzioni sono state completate correttamente.

   > [!NOTE]
   > La distribuzione del modello richiederà circa un'ora per il completamento.

   [![](media/app-service-deploy-ha/5-sm.png "Esaminare lo stato di distribuzione modello")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Output del modello di record
Dopo il modello di distribuzione viene completata correttamente, restituisce la distribuzione del modello di record. È necessario fornire queste informazioni quando si esegue il programma di installazione del servizio App. 

Prendere nota ognuno di questi valori di output:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Seguire questi passaggi per individuare i valori di output del modello:

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Nel portale di amministrazione, selezionare **gruppi di risorse** e quindi il nome del gruppo di risorse creato per la distribuzione personalizzata (**app-service-a disponibilità elevata** in questo esempio). 

3. Fare clic su **distribuzioni** e selezionare **template**.

    ![Distribuzione di template](media/app-service-deploy-ha/6.png)

4. Dopo aver selezionato il **template** distribuzione, seleziona **output** e registrare l'output del parametro di modello. Queste informazioni saranno necessarie durante la distribuzione di servizio App.

    ![Output del parametro](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Distribuire il servizio App in una configurazione a disponibilità elevata
Seguire i passaggi descritti in questa sezione per distribuire il servizio App di Azure Stack in una configurazione a disponibilità elevata in base il [appservice-condivisione file a sqlserver-disponibilità elevata](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) modello di avvio rapido di Azure Stack. 

Dopo aver installato il provider di risorse del servizio App, è possibile includerla in piani e offerte. Gli utenti possono eseguire la sottoscrizione per il servizio e per iniziare la creazione di applicazioni.

> [!IMPORTANT]
> Prima di eseguire il programma di installazione di provider di risorse, assicurarsi che è stato letto le note sulla versione, che accompagnano ogni versione del servizio App, per informazioni sulle nuove funzionalità, correzioni e i problemi noti che potrebbero influire sulla distribuzione.

### <a name="prerequisites"></a>Prerequisiti
Prima di poter eseguire il programma di installazione del servizio App, sono necessari come descritto in diversi passaggi il [prima di iniziare con il servizio App sull'articolo di Azure Stack](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> Non tutti i passaggi descritti nella prima di iniziare a articolo sono necessari perché la richiesta di modello consente di configurare l'infrastruttura di macchine virtuali per l'utente. 

- [Scaricare il servizio App installer e script helper](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Scaricare l'estensione script personalizzato più recente in Azure Stack marketplace](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace).
- [Generare i certificati richiesti](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Creare l'applicazione di ID in base al provider di identità che scelto per Azure Stack. Un'applicazione di ID possono essere eseguita per entrambi [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) oppure [Active Directory Federation Services](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) e registrare l'ID applicazione.
- Assicurarsi che sia stato aggiunto l'immagine di Windows Server 2016 Datacenter nel Marketplace di Azure Stack. È necessario per l'installazione del servizio App.

### <a name="deploy-app-service-in-highly-available-configuration"></a>Distribuire il servizio App nella configurazione a disponibilità elevata
L'installazione del provider di risorse del servizio App richiede almeno un'ora. Il periodo di tempo necessita dipende dal ruolo quante istanze è la distribuzione. Durante la distribuzione, il programma di installazione esegue le attività seguenti:

- Creare un contenitore blob nell'account di archiviazione di Azure Stack specificato.
- Creare una zona DNS e le voci per il servizio App.
- Registrare il provider di risorse del servizio App.
- Registrare gli elementi della raccolta di servizio App.

Per distribuire il provider di risorse del servizio App, seguire questa procedura:

1. Eseguire il programma di installazione di servizio App scaricato in precedenza (**appservice.exe**) come amministratore da un computer che può accedere l'Endpoint di gestione risorse di Azure di Azure Stack Admin.

2. Selezionare **distribuzione servizio App o l'aggiornamento alla versione più recente**.

    ![Distribuire o eseguire l'aggiornamento](media/app-service-deploy-ha/01.png)

3. Accettare le condizioni di licenza Microsoft e fare clic su **successivo**.

    ![Condizioni di licenza Microsoft](media/app-service-deploy-ha/02.png)

4. Accettare le condizioni di licenza non Microsoft e fare clic su **successivo**.

    ![Condizioni di licenza non Microsoft](media/app-service-deploy-ha/03.png)

5. Fornire il servizio App di configurazione dell'endpoint cloud per l'ambiente Azure Stack.

    ![Configurazione dell'endpoint di servizio App cloud](media/app-service-deploy-ha/04.png)

6. **Connettere** alla sottoscrizione di Azure Stack da utilizzare per l'installazione e scegliere la località. 

    ![Connettersi alla sottoscrizione di Azure Stack](media/app-service-deploy-ha/05.png)

7. Selezionare **usare rete virtuale esistente e le subnet** e il **Resource Group Name** per il gruppo di risorse usato per distribuire il modello a disponibilità elevata.<br><br>Successivamente, selezionare la rete virtuale creata come parte della distribuzione del modello e quindi selezionare la subnet del ruolo appropriato tra le opzioni di elenco a discesa. 

    ![Selezione della rete virtuale](media/app-service-deploy-ha/06.png)

8. Fornire che il modello registrato in precedenza restituisce informazioni per il percorso della condivisione file e i parametri del proprietario condivisione file. Al termine, fare clic su **successivo**.

    ![Informazioni di output di condivisione file](media/app-service-deploy-ha/07.png)

9. Poiché il computer in uso per installare il servizio App non si trova nella stessa rete virtuale come file server usato per ospitare la condivisione di file di servizio App, non sarà in grado di risolvere il nome. Si tratta di un comportamento previsto.<br><br>Verificare che le informazioni immesse per la condivisione di file di informazioni di account e di percorso UNC siano corrette e premere **Sì** nella finestra di dialogo di avviso per continuare l'installazione del servizio App.

    ![Finestra di dialogo di errore previsto](media/app-service-deploy-ha/08.png)

10. Specificare l'ID applicazione identità e il percorso e le password per i certificati di identità e fare clic su **successivo**:
    - Certificato di identità dell'applicazione (nel formato **sso.appservice.local.azurestack.external.pfx**)
    - Certificato radice di Azure Resource Manager (**AzureStackCertificationAuthority.cer**)

    ![ID applicazione e certificato radice](media/app-service-deploy-ha/008.png)

10. Successivamente, fornire le rimanenti informazioni necessarie per i certificati seguenti e fare clic su **successivo**:
    - Certificato SSL di Azure Stack predefinito (nel formato **_.appservice.local.azurestack.external.pfx**)
    - Certificato SSL dell'API (nel formato **api.appservice.local.azurestack.external.pfx**)
    - Certificato del Publisher (nel formato **ftp.appservice.local.azurestack.external.pfx**) 

    ![Certificati di configurazione aggiuntive](media/app-service-deploy-ha/09.png)

11. Fornire le informazioni di connessione di SQL Server usando le informazioni di connessione di SQL Server dagli output della distribuzione modello disponibilità elevata:

    ![Informazioni di connessione di SQL Server](media/app-service-deploy-ha/10.png)

12. Poiché il computer in uso per installare il servizio App non si trova nella stessa rete virtuale di SQL server utilizzato per ospitare i database del servizio App, non sarà in grado di risolvere il nome.  Si tratta di un comportamento previsto.<br><br>Verificare che le informazioni immesse per le informazioni di nome e gli account di SQL Server siano corrette e premere **Sì** per continuare l'installazione del servizio App. Fare clic su **Avanti**.

    ![Informazioni di connessione di SQL Server](media/app-service-deploy-ha/11.png)

13. Accettare i valori di configurazione del ruolo predefinito o modificare i valori consigliati e fare clic su **successivo**.<br><br>È consigliabile che i valori predefiniti per le istanze del ruolo infrastruttura del servizio App da modificare come indicato di seguito per le configurazioni a disponibilità elevata:

    |Ruolo|Predefinito|Consiglio a disponibilità elevata|
    |-----|-----|-----|
    |Ruolo controller|2|2|
    |Ruolo di gestione|1|3|
    |Ruolo server di pubblicazione|1|3|
    |Ruolo front-end|1|3|
    |Ruolo di lavoro condivise|1|10|
    |     |     |     |

    ![Valori delle istanze ruolo infrastruttura](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Il passaggio dai valori predefiniti a quelli consigliati in questo modo aumentano tutoral i requisiti hardware per l'installazione del servizio App. Un totale di 26 core e 46,592 MB di RAM è necessario per supportare le VM consigliate 21 anziché 32.256 MB di RAM e 18 core predefinito per le macchine 15 virtuali.

14. Selezionare l'immagine della piattaforma da usare per l'installazione la VM dell'infrastruttura di servizio App e fare clic su **successivo**:

    ![Selezione di immagini della piattaforma](media/app-service-deploy-ha/13.png)

15. Fornire informazioni sulle credenziali ruolo di infrastruttura da usare e fare clic su servizio App **successivo**:

    ![Credenziali del ruolo infrastruttura](media/app-service-deploy-ha/14.png)

16. Esaminare le informazioni da utilizzare per distribuire il servizio App e fare clic su **successivo** per iniziare la distribuzione. 

    ![Riepilogo dell'installazione della revisione](media/app-service-deploy-ha/15.png)

17. Esaminare l'avanzamento della distribuzione del servizio App. L'operazione può richiedere più di un'ora a seconda della configurazione di distribuzione specifica e hardware. Dopo che il programma di installazione è stata completata correttamente, selezionare **Exit**.

    ![Installazione completata](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>Passaggi successivi

[Scalabilità orizzontale del servizio App](azure-stack-app-service-add-worker-roles.md). Si potrebbe essere necessario aggiungere aggiuntivi del servizio App infrastruttura ruolo worker per soddisfare la domanda previsto per l'applicazione nell'ambiente in uso. Per impostazione predefinita, il servizio App in Azure Stack supporta i livelli gratuito e condiviso ruolo di lavoro. Per aggiungere altri piani di lavoro, è necessario aggiungere altri ruoli di lavoro.

[Configurare le origini di distribuzione](azure-stack-app-service-configure-deployment-sources.md). Configurazione aggiuntiva è necessaria per supportare la distribuzione su richiesta da più provider del controllo sorgente come GitHub, BitBucket, OneDrive e DropBox.

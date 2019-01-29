---
title: Distribuire un cloud ibrido con Azure e Azure Stack | Microsoft Docs
description: Informazioni su come distribuire un cloud ibrido con Azure e Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: cedc1ec84c049d779f09c31be1aa1a3af63dfe5b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094156"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>Esercitazione: Distribuire una soluzione cloud ibrida con Azure e Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Questa esercitazione illustra come distribuire una soluzione cloud ibrida che usa il cloud pubblico di Azure e il cloud privato di Azure Stack.

Usando una soluzione cloud ibrida, è possibile combinare i vantaggi di conformità di un cloud privato con la scalabilità del cloud pubblico. Inoltre, gli sviluppatori possono sfruttare i vantaggi dell'ecosistema di sviluppatori Microsoft e applicare le proprie competenze per gli ambienti cloud e locali.

## <a name="overview-and-assumptions"></a>Panoramica e presupposti

È possibile seguire questa esercitazione per configurare un flusso di lavoro che consente agli sviluppatori di distribuire un'applicazione web identici in un cloud pubblico e un cloud privato. Questa applicazione sarà in grado di accedere a una rete instradabile non Internet ospitata nel cloud privato. Queste applicazioni web monitorate e quando si verifica un picco di traffico, un programma modifica il record DNS per reindirizzare il traffico al cloud pubblico. Quando si raggiunge il livello prima il picco di traffico, il traffico viene indirizzato al cloud privato.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> - Distribuire un server di database di SQL Server ibrida-connesso.
> - Connettere un'app web in Azure globale a una rete ibrida.
> - Configurare il DNS per la scalabilità tra cloud.
> - Configurare i certificati SSL per la scalabilità tra cloud.
> - Configurare e distribuire l'applicazione web.
> - Creare un profilo di Traffic Manager e configurarlo per la scalabilità tra cloud.
> - Configurazione di Application Insights, monitoraggio e avviso per un aumento del traffico.
> - Configurare il traffico automatica cambio tra globale di Azure e Azure Stack.

### <a name="assumptions"></a>Presupposti

Questa esercitazione si presuppone una conoscenza di base di Azure globali e Azure Stack. Per altre informazioni prima di iniziare l'esercitazione, vedere questi articoli:

 - [Introduzione ad Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Concetti chiave di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

Questa esercitazione si presuppone inoltre che si dispone di una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, verificare che è possibile soddisfare i requisiti seguenti:

- Un Azure Stack Development Kit (ASDK) o una sottoscrizione su un sistema integrato di Azure Stack. Per distribuire un database di Azure Stack Development Kit, seguire le istruzioni riportate in [distribuire il ASDK usando il programma di installazione](../asdk/asdk-deploy.md).
- L'installazione di Azure Stack deve essere installato quanto segue:
  - Il servizio App di Azure. Funziona con l'operatore di Stack di Azure per distribuire e configurare il servizio App di Azure nell'ambiente. Questa esercitazione richiede il servizio App ha il ruolo di lavoro dedicato disponibili almeno uno (1).
  - Un'immagine di Windows Server 2016
  - Windows Server 2016 con un'immagine di Microsoft SQL Server
  - I piani appropriati e le offerte
 - Un nome di dominio per l'applicazione web. Se non si ha un nome di dominio è possibile acquistarne uno da un provider di dominio, ad esempio GoDaddy, Bluehost e InMotion.
- Un certificato SSL per il dominio da un'autorità di certificazione attendibile, ad esempio LetsEncrypt.
- Un'applicazione web che comunica con un database di SQL Server e supporta Application Insights. È possibile scaricare il [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) app di esempio da GitHub.
- Una rete ibrida tra una rete virtuale di Azure e una rete virtuale di Azure Stack. Per istruzioni dettagliate, vedere [configurare la connettività cloud ibrida con Azure e Azure Stack](azure-stack-solution-hybrid-connectivity.md).

- Ibrido/integrazione continua (CI/CD) distribuzione pipeline con un agente di compilazione privata in Azure Stack. Per istruzioni dettagliate, vedere [configurare identità del cloud ibrido con le applicazioni di Azure e Azure Stack](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>Distribuire un server di database di SQL Server ibrida-connesso

1. Accedere al portale utenti di Azure Stack.

2. Nel **Dashboard**, selezionare **Marketplace**.

    ![Marketplace di Azure Stack](media/azure-stack-solution-hybrid-cloud/image1.png)

3. Nella **Marketplace**, selezionare **Compute**, quindi scegliere **altre**. Sotto **altre**, selezionare il **licenza gratuita di SQL Server: SQL Server 2017 Developer in Windows Server** immagine.

    ![Selezionare un'immagine di macchina virtuale](media/azure-stack-solution-hybrid-cloud/image2.png)

4. In **senza licenza di SQL Server: SQL Server 2017 Developer in Windows Server** selezionate **Create**.

5. Sul **nozioni di base > Configura impostazioni di base**, fornire un **nome** per la macchina virtuale (VM), una **nome utente** per SA di SQL Server e un **Password** per l'amministratore del servizio.  Dal **sottoscrizione** elenco a discesa selezionare la sottoscrizione a cui viene eseguita la distribuzione. Per la **gruppo di risorse**, usare **sceglierne uno esistente** e inserire la macchina virtuale nello stesso gruppo di risorse dell'App web di Azure Stack.

    ![Configurare le impostazioni di base per la macchina virtuale](media/azure-stack-solution-hybrid-cloud/image3.png)

6. Sotto **dimensioni**, scegliere una dimensione per la macchina virtuale. Per questa esercitazione, è consigliabile A2_Standard o un DS2_V2_Standard.

7. Sotto **Impostazioni > Configura funzionalità facoltative**, configurare le impostazioni seguenti:

    - **Account di archiviazione**. Creare un nuovo account se necessario.
    - **Rete virtuale**

      > [!Important]  
      > Assicurarsi che la macchina virtuale di SQL Server viene distribuita nella stessa rete virtuale come i gateway VPN.

    - **Indirizzo IP pubblico**. È possibile usare le impostazioni predefinite.
    - **Gruppo di sicurezza di rete** (NSG). Creare un nuovo NSG.
    - **Estensioni e il monitoraggio**. Mantenere le impostazioni predefinite.
    - **Account di archiviazione di diagnostica**. Creare un nuovo account se necessario.
    - Selezionare **OK** per salvare la configurazione.

    ![Configurare le funzionalità facoltative](media/azure-stack-solution-hybrid-cloud/image4.png)

1. Sotto **impostazioni di SQL Server**, configurare le impostazioni seguenti:
   - Per la **connettività SQL**, selezionare questa opzione per **pubblico (Internet)**.
   - Per la **porta**, il valore predefinito, mantenere **1433**.
   - Per la **autenticazione di SQL Server**, selezionare **abilitare**.

     > [!Note]  
     > Quando si abilita l'autenticazione di SQL, deve popolano automaticamente con le informazioni di "SQLAdmin" configurate nel **nozioni di base**.

   - Per il resto delle impostazioni, mantenere i valori predefiniti. Selezionare **OK**.

    ![Configurare le impostazioni di SQL Server](media/azure-stack-solution-hybrid-cloud/image5.png)

9. Sul **Summary**, esaminare la configurazione della macchina virtuale e quindi selezionare **OK** per avviare la distribuzione.

    ![Riepilogo della configurazione](media/azure-stack-solution-hybrid-cloud/image6.png)

10. Richiederà del tempo per creare la nuova macchina virtuale. È possibile visualizzare lo stato delle macchine virtuali in **macchine virtuali**.

    ![Macchine virtuali](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Creare App web in Azure e Azure Stack

Servizio App di Azure semplifica l'esecuzione e la gestione di un'applicazione web. Perché Azure Stack è coerente con Azure, è possibile eseguire il servizio App in entrambi gli ambienti. Si userà il servizio App per ospitare l'applicazione.

### <a name="create-web-apps"></a>Creare App web

1. Creare un'app web in Azure seguendo le istruzioni disponibili nel [gestire un piano di servizio App in Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan). Assicurarsi di che inserire l'app web nella stessa sottoscrizione e gruppo di risorse della rete ibrida.

2. Ripetere il passaggio precedente (1) in Azure Stack.

### <a name="add-route-for-azure-stack"></a>Aggiungere Route per Azure Stack

Il servizio App in Azure Stack deve essere instradabile da Internet pubblico per consentire agli utenti di accedere all'applicazione. Se lo Stack di Azure è accessibile da Internet, prendere nota dell'indirizzo IP pubblico o URL per l'app web di Azure Stack.

Se si usa un ASDK, puoi [configurare un mapping NAT statico](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal) per esporre il servizio App esterno all'ambiente virtuale.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Connettere un'app web in Azure a una rete ibrida

Per fornire la connettività tra il front-end in Azure e il database di SQL Server in Azure Stack, l'app web essere connesso alla rete ibrida tra Azure e Azure Stack. Per abilitare la connettività, è necessario:

- Configura connettività Point-To-Site
- Configurare l'app Web
- Modificare il gateway di rete locale in Azure Stack.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Configurare la rete virtuale di Azure per la connettività point-to-site

Il gateway di rete virtuale sul lato Azure della rete ibrida deve consentire le connessioni point-to-site per l'integrazione con servizio App di Azure.

1. In Azure, passare alla pagina del gateway di rete virtuale. Sotto **le impostazioni**, selezionare **configurationPoint-to-site**.

    ![Opzione Point-to-site](media/azure-stack-solution-hybrid-cloud/image8.png)

2. Selezionare **Configura ora** Configura point-to-site.

    ![Avviare la configurazione da punto a sito](media/azure-stack-solution-hybrid-cloud/image9.png)

3. Nel **Point-to-site** configurazione immettere l'intervallo di indirizzi IP privato che si desidera utilizzare nella **pool di indirizzi**.

   > [!Note]  
   > Assicurarsi che l'intervallo specificato non si sovrapponga agli intervalli di indirizzi già usati dalle subnet nei componenti di Azure o Azure Stack globale della rete ibrida.

   Sotto **tipo di Tunnel**, deselezionare le **VPN IKEv2**. Selezionare **salvare** per completare la configurazione da punto a sito.

   ![Impostazioni Point-to-site](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>Integrare l'applicazione di servizio App di Azure con la rete ibrida

1. Per connettersi all'applicazione di rete virtuale di Azure, seguire le istruzioni in [abilitazione dell'integrazione rete virtuale](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration).

2. Passare a **impostazioni** per il piano di servizio App che ospita l'applicazione web. In **Impostazioni** selezionare **Rete**.

    ![Configurare le impostazioni di rete](media/azure-stack-solution-hybrid-cloud/image11.png)

3. Nelle **integrazione rete virtuale**, selezionare **fare clic qui per gestire**.

    ![Gestire l'integrazione rete virtuale](media/azure-stack-solution-hybrid-cloud/image12.png)

4. Selezionare la rete virtuale che si desidera configurare. Sotto **IP indirizzi INSTRADATI alla rete virtuale**, immettere l'intervallo di indirizzi IP per rete virtuale di Azure, la rete virtuale di Azure Stack e gli spazi degli indirizzi da punto a sito. Selezionare **salvare** per convalidare e salvare le impostazioni.

    ![Intervalli di indirizzi IP per il routing](media/azure-stack-solution-hybrid-cloud/image13.png)

Per altre informazioni sull'integrazione del servizio App con reti virtuali di Azure, vedere [integrare un'app in una rete virtuale di Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet).

### <a name="configure-the-azure-stack-virtual-network"></a>Configurare la rete virtuale di Azure Stack

Il gateway di rete locale nella rete virtuale di Azure Stack può essere configurato per instradare il traffico dall'intervallo di indirizzi da punto a sito di servizio App.

1. In Azure Stack, passare a **gateway di rete locale**. In **Impostazioni** selezionare **Configurazione**.

    ![Opzione di configurazione del gateway](media/azure-stack-solution-hybrid-cloud/image14.png)

2. Nella **spazio indirizzi**, immettere l'intervallo di indirizzi da punto a sito per il gateway di rete virtuale nella finestra Seleziona Azure.l **salvare** per convalidare e salvare la configurazione.

    ![Spazio degli indirizzi da punto a sito](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>Configurare il DNS per la scalabilità tra cloud

Configurando correttamente DNS per le applicazioni tra cloud, gli utenti possono accedere le istanze di Azure e Azure Stack globale dell'app web. La configurazione del DNS per questa esercitazione consente inoltre di instradare il traffico di gestione traffico di Azure quando il carico aumenta o diminuisce.

Questa esercitazione Usa DNS di Azure per gestire il DNS. (I domini del servizio app non funzioneranno.)

### <a name="create-subdomains"></a>Creare sottodomini

Poiché gestione traffico si basa su DNS CNAME, è necessario un sottodominio per instradare correttamente il traffico agli endpoint. Per altre informazioni sui mapping del dominio e record DNS, vedere [eseguire il mapping di domini con gestione traffico](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

Per Azure endpoint si creerà un sottodominio che gli utenti possono utilizzare per accedere all'app web. Per questa esercitazione, è possibile usare **app.northwind.com**, ma è necessario personalizzare questo valore in base al proprio dominio.

È anche necessario creare un sottodominio di un record per l'endpoint di Azure Stack. È possibile usare **azurestack.northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Configurare un dominio personalizzato in Azure

1. Aggiungere il **app.northwind.com** nome host per l'app web di Azure dal [mapping di un record CNAME a servizio App di Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack"></a>Configurare domini personalizzati in Azure Stack

1. Aggiungere il **azurestack.northwind.com** nome host per l'app web di Azure Stack tramite [mapping di un record nel servizio App di Azure di](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Usare l'indirizzo IP instradabile su Internet per l'applicazione di servizio App.

2. Aggiungere il **app.northwind.com** nome host per l'app web di Azure Stack tramite [mapping di un record CNAME a servizio App di Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Usare il nome host che è configurato nel passaggio precedente (1) come destinazione per il record CNAME.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>Configurare i certificati SSL per la scalabilità tra cloud

È necessario assicurarsi che i dati sensibili raccolti dall'applicazione web sono protetto in transito da e quando sono inattivi nel database SQL.

È possibile configurare le applicazioni web di Azure e Azure Stack per usare i certificati SSL per tutto il traffico in ingresso.

### <a name="add-ssl-to-azure-and-azure-stack"></a>Aggiungere SSL in Azure e Azure Stack

Per aggiungere SSL in Azure:

1. Assicurarsi che il certificato SSL ottenuto sia valido per il sottodominio che è stato creato. (È possibile usare certificati con caratteri jolly).

2. In Azure, seguire le istruzioni di **preparare l'app web** e **associare il certificato SSL** sezioni del [associare un certificato SSL personalizzato esistente alle App Web di Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) articoli. Selezionare **basato su SNI SSL** come la **tipo SSL**.

3. Reindirizzare tutto il traffico alla porta HTTPS. Seguire le istruzioni nel **imporre HTTPS** sezione il [associare un certificato SSL personalizzato esistente alle App Web di Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) articolo.

Per aggiungere SSL in Azure Stack:

- Ripetere i passaggi 1-3 usata per Azure.

## <a name="configure-and-deploy-the-web-application"></a>Configurare e distribuire l'applicazione web

Si sarà configurare il codice dell'applicazione ai dati di telemetria di report all'istanza corretta di Application Insights e configurare le applicazioni web con le stringhe di connessione corretta. Per altre informazioni su Application Insights, vedere [What ' s Application Insights?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Aggiungi Application Insights

1. Aprire l'applicazione web in Microsoft Visual Studio.

2. [Aggiungere Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry) al progetto per trasmettere i dati di telemetria da Application Insights Usa per creare avvisi quando il traffico web aumenta o diminuisce.

### <a name="configure-dynamic-connection-strings"></a>Configurare le stringhe di connessione dinamica

Ogni istanza dell'applicazione web utilizzerà un metodo diverso per la connessione al database SQL. L'applicazione di Azure Usa l'indirizzo IP privato della macchina virtuale SQL Server (VM) e l'applicazione in Azure Stack Usa l'indirizzo IP pubblico della VM di SQL Server.

> [!Note]  
> In un sistema integrato dello Stack di Azure, l'indirizzo IP pubblico non deve essere instradabile su Internet. In un Azure Stack Development Kit (ASDK), l'indirizzo IP pubblico non è instradabile all'esterno di ASDK.

È possibile utilizzare le variabili di ambiente del servizio App di passare una stringa di connessione diversi a ogni istanza dell'applicazione.

1. Aprire l'applicazione in Visual Studio.

2. Aprire Startup.cs e trovare il blocco di codice seguente:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Sostituire il blocco di codice precedente con il codice seguente, che usa una stringa di connessione definita nel file appSettings. JSON:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>Configurare le impostazioni dell'applicazione di servizio App

1. Creare stringhe di connessione per Azure e Azure Stack. Le stringhe devono essere lo stesso, tranne gli indirizzi IP che vengono usati.

2. In Azure e Azure Stack, aggiungere la stringa di connessione appropriata [come impostazione dell'applicazione](https://docs.microsoft.com/azure/app-service/web-sites-configure) nell'applicazione web, usando `SQLCONNSTR\_` come prefisso del nome.

3. **Salvare** le impostazioni dell'app web e riavviare l'applicazione.

## <a name="enable-automatic-scaling-in-global-azure"></a>Abilitare la scalabilità automatica in Azure globale

Quando si crea l'app web in un ambiente del servizio App inizia con una sola istanza. È possibile scalare orizzontalmente automaticamente per aggiungere istanze per fornire che più risorse di calcolo per l'app. Analogamente, è possibile automaticamente ridimensionare in e ridurre il numero di istanze le esigenze delle app.

> [!Note]  
> È necessario disporre di un piano di servizio App per configurare la scalabilità orizzontale e scalabilità in. Se non si dispone di un piano, crearne uno prima di iniziare i passaggi successivi.

### <a name="enable-automatic-scale-out"></a>Abilitare la scalabilità automatica orizzontale

1. In Azure, trovare il piano di servizio App per i siti che si desidera scalare in orizzontale e quindi selezionare **Scale-out (piano di servizio App)**.

    ![Scalabilità orizzontale](media/azure-stack-solution-hybrid-cloud/image16.png)

2. Selezionare **abilitare la scalabilità automatica**.

    ![Abilitare il ridimensionamento automatico](media/azure-stack-solution-hybrid-cloud/image17.png)

3. Immettere un nome per **nome dell'impostazione di scalabilità automatica**. Per il **predefinite** regole di scalabilità automatica, selezionare **scalabilità basata su una metrica**. Impostare il **limiti per le istanze** a **minimo: 1**, **massimo: 10**, e **predefinito: 1**.

    ![Configura la scalabilità automatica](media/azure-stack-solution-hybrid-cloud/image18.png)

4. Selezionare **+ Aggiungi una regola**.

5. Nelle **origine metrica**, selezionare **risorsa corrente**. Usare i criteri e le azioni seguenti per la regola.

**Criteri**

1. Sotto **aggregazione temporale,** selezionate **medio**.

2. Sotto **nome metrica**, selezionare **Percentuale CPU**.

3. Sotto **Operator**, selezionare **maggiore**.

   - Impostare il **soglia** al **50**.
   - Impostare il **Duration** al **10**.

**Azione**

1. Sotto **operazione**, selezionare **incrementare Count by**.

2. Impostare il **numero di istanze** al **2**.

3. Impostare il **raffreddamento** al **5**.

4. Selezionare **Aggiungi**.

5. Selezionare il **+ Aggiungi una regola**.

6. Nelle **origine metrica**, selezionare **risorsa corrente.**

   > [!Note]  
   > La risorsa corrente conterrà il nome/GUID del piano di servizio App e il **tipo di risorsa** e **risorsa** elenchi a discesa verranno visualizzato in grigio.

### <a name="enable-automatic-scale-in"></a>Abilitare la scalabilità automatica in

Quando il traffico diminuisce, l'applicazione web di Azure può automaticamente ridurre il numero di istanze attive per ridurre i costi. Questa azione è meno aggressiva rispetto a scalabilità orizzontale per ridurre al minimo l'impatto sugli utenti dell'applicazione.

1. Passare al **predefinite** scalabilità orizzontale condizione, selezionare **+ Aggiungi una regola**. Usare i criteri e le azioni seguenti per la regola.

**Criteri**

1. Sotto **aggregazione temporale,** selezionate **medio**.

2. Sotto **nome metrica**, selezionare **Percentuale CPU**.

3. Sotto **Operator**, selezionare **minore**.

   - Impostare il **soglia** al **30**.
   - Impostare il **Duration** al **10**.

**Azione**

1. Sotto **operazione**, selezionare **diminuire Count by**.

   - Impostare il **numero di istanze** al **1**.
   - Impostare il **raffreddamento** al **5**.

2. Selezionare **Aggiungi**.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Creare un profilo di gestione traffico e configurare la scalabilità tra cloud

È possibile creare un profilo di Traffic Manager in Azure e quindi configurare gli endpoint per abilitare la scalabilità tra cloud.

### <a name="create-traffic-manager-profile"></a>Crea profilo di gestione traffico

1. Selezionare **crea una risorsa**
2. Selezionare **Networking**
3. Selezionare **profilo di gestione traffico** e configurare le opzioni seguenti:

   - Nelle **nome**, immettere un nome per il tuo profilo. Questo nome **necessario** essere univoco all'interno della zona trafficmanager.net e viene usato per creare un nuovo nome DNS (ad esempio, northwindstore.trafficmanager.net).
   - Per la **metodo di Routing**, selezionare la **ponderato**.
   - Per la **sottoscrizione**, selezionare la sottoscrizione che si desidera creare il profilo in.
   - Nelle **gruppo di risorse**, creare un nuovo gruppo di risorse per questo profilo.
   - In **Località del gruppo di risorse** selezionare la località del gruppo di risorse. Questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sul profilo di Traffic Manager che verrà distribuito a livello globale.

4. Selezionare **Create**.

    ![Crea profilo di gestione traffico](media/azure-stack-solution-hybrid-cloud/image19.png)

 Quando la distribuzione globale del profilo di Traffic Manager è stata completata, viene visualizzata nell'elenco delle risorse per il gruppo di risorse creato in.

### <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico

1. Cercare il profilo di Traffic Manager creato. (Se si è arrivati al gruppo di risorse per il profilo, selezionare il profilo).

2. Nelle **profilo di gestione traffico**, in **impostazioni**, selezionare **endpoint**.

3. Selezionare **Aggiungi**.

4. Nelle **aggiungere endpoint**, utilizzare le seguenti impostazioni per Azure Stack:

   - Per la **tipo**, selezionare **endpoint esterno**.
   - Immettere un **nome** per questo endpoint.
   - Per la **il nome di dominio completo (FQDN) o IP** immettere l'URL esterno per l'app web di Azure Stack.
   - Per la **peso**, il valore predefinito, mantenere **1**. Questo valore di ponderazione in tutto il traffico verso questo endpoint se è integro.
   - Lasciare **Aggiungi come disabilitato** deselezionata.

5. Selezionare **OK** per salvare l'endpoint di Azure Stack.

Si configurerà quindi l'endpoint di Azure.

1. Sul **profilo di gestione traffico**, selezionare **endpoint**.
2. Selezionare **+Aggiungi**.
3. Sul **aggiungere endpoint**, utilizzare le seguenti impostazioni per Azure:

   - Per la **tipo**, selezionare **endpoint di Azure**.
   - Immettere un **nome** per questo endpoint.
   - Per la **tipo di risorsa di destinazione**, selezionare **servizio App**.
   - Per la **risorsa di destinazione**, selezionare **scegliere un servizio app** per visualizzare un elenco di App Web nella stessa sottoscrizione.
   - In **Risorsa** selezionare il servizio App che si vuole aggiungere come primo endpoint.
   - Per la **peso**, selezionare **2**. In questo modo tutto il traffico instradato a questo endpoint se l'endpoint primario non è integro o si dispone di un regola o un avviso che nuovamente indirizza il traffico all'attivazione.
   - Lasciare **Aggiungi come disabilitato** deselezionata.

4. Selezionare **OK** per salvare l'endpoint di Azure.

Dopo che entrambi gli endpoint sono configurati, elencate nelle **profilo di gestione traffico** quando si seleziona **endpoint**. L'esempio nella schermata seguente mostra due endpoint, con le informazioni di stato e la configurazione per ognuno di essi.

![Endpoint](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Configurazione di Application Insights, monitoraggio e avviso

Azure Application Insights consente di monitorare l'applicazione e inviare avvisi in base alle condizioni configurate. Sono riportati alcuni esempi: l'applicazione non è disponibile, si verificano errori o è che mostra i problemi di prestazioni.

Si userà le metriche di Application Insights per creare avvisi. Quando si attivano gli avvisi, le applicazioni Web passa automaticamente dallo Stack di Azure ad Azure per scalare in orizzontale, istanza e quindi eseguire il backup in Azure stack per la scalabilità in.

### <a name="create-an-alert-from-metrics"></a>Creare un avviso dalle metriche

Passare al gruppo di risorse per questa esercitazione e quindi selezionare l'istanza di Application Insights aprire **Application Insights**.

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

Si userà questa vista per creare un avviso di aumento e una scala nell'avviso.

### <a name="create-the-scale-out-alert"></a>Creare l'avviso di aumento

1. Sotto **CONFIGURE**, selezionare **avvisi (versione classica)**.
2. Selezionare **Aggiungi avviso per la metrica (versione classica)**.
3. Nelle **Aggiungi regola**, configurare quanto segue:

   - Per la **Name**, immettere **Burst nel Cloud Azure**.
   - Oggetto **descrizione** è facoltativo.
   - Sotto **origine**, **avviso**, selezionare **metriche**.
   - Sotto **criteri**, selezionare la sottoscrizione, gruppo di risorse per il profilo di Traffic Manager e il nome del profilo di Traffic Manager per la risorsa.

4. Per la **Metric**, selezionare **velocità delle richieste**.
5. Per la **Condition**, selezionare **maggiore**.
6. Per la **soglia**, immettere **2**.
7. Per la **periodo**, selezionare **negli ultimi 5 minuti**.
8. Sotto **una notifica tramite**:
   - Selezionare la casella di controllo **inviare tramite posta elettronica a proprietari, collaboratori e lettori**.
   - Immettere l'indirizzo di posta elettronica **aggiuntivi email(s) amministratore**.

9. Nella barra dei menu, selezionare **salvare**.

### <a name="create-the-scale-in-alert"></a>Creare la scala in avviso

1. Sotto **CONFIGURE**, selezionare **avvisi (versione classica)**.
2. Selezionare **Aggiungi avviso per la metrica (versione classica)**.
3. Nelle **Aggiungi regola**, configurare quanto segue:

   - Per la **Name**, immettere **scalabilità in Azure Stack**.
   - Oggetto **descrizione** è facoltativo.
   - Sotto **origine**, **avviso**, selezionare **metriche**.
   - Sotto **criteri**, selezionare la sottoscrizione, gruppo di risorse per il profilo di Traffic Manager e il nome del profilo di Traffic Manager per la risorsa.

4. Per la **Metric**, selezionare **velocità delle richieste**.
5. Per la **Condition**, selezionare **minore**.
6. Per la **soglia**, immettere **2**.
7. Per la **periodo**, selezionare **negli ultimi 5 minuti**.
8. Sotto **una notifica tramite**:
   - Selezionare la casella di controllo **inviare tramite posta elettronica a proprietari, collaboratori e lettori**.
   - Immettere l'indirizzo di posta elettronica **aggiuntivi email(s) amministratore**.

9. Nella barra dei menu, selezionare **salvare**.

La schermata seguente mostra gli avvisi per la scalabilità orizzontale e scalabilità in.

   ![Avvisi (versione classica)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Reindirizzare il traffico tra Azure e Azure Stack

È possibile configurare il passaggio manuale o automatico del traffico delle app Web, il passaggio tra Azure e Azure Stack.

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Configurare un passaggio manuale tra Azure e Azure Stack

Quando il sito Web raggiunge le soglie configurate, si riceverà un avviso. Utilizzare questa procedura manualmente reindirizzare il traffico in Azure.

1. Nel portale di Azure, selezionare il profilo di Traffic Manager.

    ![Endpoint di Gestione traffico](media/azure-stack-solution-hybrid-cloud/image20.png)

2. Selezionare **Endpoint**.
3. Selezionare il **endpoint di Azure**.
4. Sotto **lo stato** , selezionare **Enabled**e quindi selezionare **Salva**.

    ![Abilitare l'endpoint di Azure](media/azure-stack-solution-hybrid-cloud/image23.png)

5. Sul **endpoint** per il profilo di Traffic Manager, selezionare **endpoint esterno**.
6. Sotto **lo stato** , selezionare **disabilitato**, quindi selezionare **Salva**.

    ![Disabilitare l'endpoint di Azure Stack](media/azure-stack-solution-hybrid-cloud/image24.png)

Dopo aver configurati gli endpoint, il traffico delle applicazioni passa all'App web di tipo scale-out di Azure anziché l'app web di Azure Stack.

 ![Endpoint modificati](media/azure-stack-solution-hybrid-cloud/image25.png)

Per ripristinare il flusso di Azure Stack, usare i passaggi precedenti per:

- Abilitare l'endpoint di Azure Stack
- Disabilitare l'endpoint di Azure

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Configurare un passaggio automatico tra Azure e Azure Stack

È anche possibile usare monitoraggio di Application Insights, se l'applicazione viene eseguita un [senza server](https://azure.microsoft.com/overview/serverless-computing/) ambiente fornito da funzioni di Azure.

In questo scenario, è possibile configurare Application Insights per usare un webhook che chiama un'app per le funzioni. Questa app automaticamente Abilita o disabilita un endpoint in risposta a un avviso.

Utilizzare la procedura seguente come guida per configurare il passaggio automatico del traffico.

1. Creare un'app per le funzioni di Azure.
2. Creare una funzione attivata tramite HTTP.
3. Importare gli SDK di Azure Resource Manager, App Web e gestione traffico.
4. Sviluppare codice per:

   - Eseguire l'autenticazione alla sottoscrizione di Azure.
   - Usare un parametro che attiva o disattiva l'endpoint di gestione traffico per indirizzare il traffico di Azure o Azure Stack.

5. Salvare il codice e aggiungere l'URL dell'app per le funzioni con i parametri appropriati per il **Webhook** sezione delle impostazioni di regola di avviso di Application Insights.
6. Il traffico viene reindirizzato automaticamente quando viene generato un avviso di Application Insights.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli Cloud di Azure, vedere [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns).

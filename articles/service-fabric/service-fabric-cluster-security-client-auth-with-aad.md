
<properties
   pageTitle="Sicurezza del cluster di Service Fabric: autenticazione client con Azure Active Directory | Microsoft Azure"
   description="Questo articolo descrive come creare un cluster di Service Fabric usando Azure Active Directory (AAD) per l'autenticazione client"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2016"
   ms.author="seanmck"/>

# ANTEPRIMA: Creare un cluster di Service Fabric usando Azure Active Directory per l'autenticazione client

È possibile proteggere l'accesso agli endpoint di gestione di un cluster di Service Fabric usando Azure Active Directory (AAD). Questo articolo descrive come creare gli elementi di AAD necessari, come popolarli durante la creazione del cluster e come connettersi al cluster in un secondo momento.

>[AZURE.IMPORTANT] L'integrazione di AAD con i cluster di Service Fabric è attualmente in fase di anteprima. Quanto descritto in questo articolo è disponibile nel runtime di Service Fabric 5.0, ma non è consigliabile usarlo per i cluster di produzione in questo momento.

## Modellare un cluster di Service Fabric in AAD

AAD consente alle organizzazioni, note come tenant, di gestire l'accesso degli utenti alle applicazioni, che si dividono in applicazioni con un'interfaccia utente di accesso basata sul Web e applicazioni con un'esperienza client nativa. In questo documento si presuppone che sia già stato creato un tenant. In caso contrario, vedere prima di tutto [Come ottenere un tenant di Azure Active Directory](../active-directory/active-directory-howto-tenant.md).

I cluster di Service Fabric offrono una serie di punti di ingresso alle relative funzionalità di gestione, tra cui [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), basato sul Web, e [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Verranno quindi create due applicazioni AAD per controllare l'accesso al cluster, un'applicazione Web e un'applicazione nativa.

Per semplificare alcuni dei passaggi richiesti per la configurazione di AAD con un cluster di Service Fabric è stato creato un set di script di Windows PowerShell.

>[AZURE.NOTE] Questi passaggi devono essere eseguiti *prima* di creare il cluster. Nei casi in cui gli script prevedono endpoint e nomi dei cluster è quindi necessario usare i valori pianificati anziché valori già creati.

1. [Scaricare gli script][sf-aad-ps-script-download] sul computer.

2. Fare clic con il pulsante destro del mouse sul file con estensione zip, scegliere **Proprietà**, quindi selezionare la casella di controllo **Sblocca**.

3. Estrarre il file con estensione zip.

4. Eseguire `SetupApplications.ps1`, fornendo i parametri TenantId, ClusterName e WebApplicationReplyUrl. Ad esempio:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Il **TenantId** è reperibile nell'URL del tenant nel portale di Azure classico. Il GUID incorporato nell'URL è il TenantId. Ad esempio:

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    **ClusterName** verrà usato come prefisso per le applicazioni AAD create dallo script. Non è necessario che corrisponda esattamente al nome effettivo del cluster, dato che il suo unico scopo è semplificare il mapping degli elementi AAD al cluster di Service Fabric con cui vengono usati.

    **WebApplicationReplyUrl** è l'endpoint predefinito che AAD restituisce agli utenti dopo aver completato la procedura di accesso. Deve essere impostato sull'endpoint di Service Fabric Explorer per il cluster, che per impostazione predefinita è:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Verrà richiesto di accedere a un account con privilegi amministrativi per il tenant AAD. Al termine dell'operazione, lo script crea l'applicazione Web e l'applicazione nativa per rappresentare il cluster di Service Fabric. Tra le applicazioni del tenant nel [portale di Azure classico][azure-classic-portal] dovrebbero essere visualizzate due nuove voci:

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    Tenere aperta la finestra di PowerShell perché, quando verrà creato il cluster nella sezione successiva, lo script stamperà il codice JSON richiesto dal modello di Azure Resource Manager (ARM).

    ![L'output dello script SetupApplication include il codice JSON richiesto dal modello di Azure Resource Manager][setupapp-script-output]

## Creare il cluster

Ora che sono state create le applicazioni AAD, è possibile creare il cluster di Service Fabric. Attualmente, il portale di Azure non supporta la configurazione dell'autenticazione AAD per i cluster di Service Fabric. Per farlo sarà quindi necessario usare un modello di Azure Resource Manager in Visual Studio o in PowerShell.

Si noti che AAD viene usato soltanto per l'autenticazione client nel cluster. Per creare un cluster protetto è necessario fornire anche un certificato, che verrà usato per proteggere le comunicazioni tra i nodi del cluster e per fornire l'autenticazione server per gli endpoint di gestione del cluster. È disponibile un [modello di Azure Resource Manager per un cluster protetto nella raccolta di avvio rapido di Azure][secure-cluster-arm-template]. In alternativa è possibile seguire le istruzioni fornite nel file Leggimi del [progetto di gruppo di risorse di Service Fabric in Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Aggiungere l'output del frammento di codice del modello di Azure Resource Manager dallo script `SetupApplication` come peer di fabricSettings, managementEndpoint e così via. Se la finestra è stata chiusa, il frammento di codice è disponibile di seguito:

```json
  "azureActiveDirectory": {
    "tenantId": "<your_tenant_id>",
    "clusterApplication": "<your_cluster_application_client_id>",
    "clientApplication": "<your_native_application_client_id>"
  }
```

clusterApplication fa riferimento all'applicazione Web creata nella sezione precedente. Il relativo ID è reperibile nell'output dello script SetupApplication, in cui viene indicato come `WebAppId`. clientApplication fa riferimento all'applicazione nativa e il relativo ID client è disponibile nell'output di SetupApplication come NativeClientAppId.

## Assegnare utenti ai ruoli

Dopo aver creato le applicazioni per rappresentare il cluster, è necessario assegnare gli utenti ai ruoli supportati da Service Fabric, ovvero sola lettura e amministratore. Questa operazione può essere eseguita nel [portale di Azure classico][azure-classic-portal].

1. Passare al tenant e scegliere Applicazioni.
2. Scegliere l'applicazione Web, che avrà un nome simile a `myTestCluster_Cluster`.
3. Fare clic sulla scheda Utenti.
4. Scegliere un utente per l'assegnazione e quindi fare clic sul pulsante **Assegna** nella parte inferiore della schermata.

    ![Pulsante di assegnazione di utenti ai ruoli][assign-users-to-roles-button]

5. Selezionare il ruolo da assegnare all'utente.

    ![Assegnare utenti ai ruoli][assign-users-to-roles-dialog]

>[AZURE.NOTE] Per altre informazioni sui ruoli in Service Fabric, vedere [Controllo di accesso basato sui ruoli per i client di Service Fabric](service-fabric-cluster-security-roles.md).

## Connettersi al cluster

Quando si passa a Service Fabric Explorer in un cluster abilitato per AAD si viene reindirizzati automaticamente a una pagina di accesso protetta.

Per connettersi da un client nativo come Windows PowerShell o Visual Studio, è necessario indicare AAD come meccanismo di accesso e quindi specificare un'identificazione personale del certificato server, che viene usata per convalidare l'identità dell'endpoint. I dettagli relativi ai due punti di ingresso sono visualizzati di seguito.

### Connessione da Visual Studio

In Visual Studio è possibile modificare il profilo di pubblicazione per aggiungere gli attributi necessari, come illustrato di seguito:

```xml
<ClusterConnectionParameters     
    ConnectionEndpoint="<your_cluster_endpoint>:19000"  
    AzureActiveDirectory="true"
    ServerCertThumbprint="<your_cert_thumbprint>"
    />
```

Quando si pubblica nel cluster, in Visual Studio viene visualizzata una finestra popup di accesso che permette di autenticarsi nel cluster.

![Finestra di accesso di AAD durante la pubblicazione da Visual Studio][vs-publish-aad-login]

### Connessione da Windows PowerShell

In PowerShell è possibile specificare i parametri necessari al cmdlet Connect-ServiceFabricCluster, come illustrato di seguito:

```PowerShell
Connect-ServiceFabricCluster -AzureActiveDirectory -ConnectionEndpoint <cluster_endpoint>:19000 -ServerCertThumbprint <server_cert_thumbprint>
```

Come accade in Visual Studio, anche in PowerShell viene visualizzata una finestra di accesso protetta per l'autenticazione.

>[AZURE.NOTE] Per impostazione predefinita, il gateway TCP di Service Fabric usato da PowerShell e Visual Studio è in ascolto sulla porta 19000. Se è stata configurata una porta diversa, è consigliabile usarla quando si specifica l'endpoint di connessione.

## Problemi noti

### Errore di autenticazione client nativo a causa dell'indirizzo di risposta non corrispondente

Durante l'autenticazione da un client nativo, ad esempio Visual Studio o PowerShell, potrebbe essere visualizzato un messaggio di errore simile al seguente:

*L'indirizzo di risposta http://localhost/ non corrisponde all'indirizzo di risposta configurato per l'applicazione &lt;GUID dell'applicazione client del &lt;cluster*

Per risolvere il problema, aggiungere **http://<i></i>localhost** come URI di reindirizzamento alla definizione dell'applicazione client del cluster in AAD, oltre all'indirizzo "urn:ietf:wg:oauth:2.0:oob" che è già presente.

## Passaggi successivi

- Altre informazioni su come [Proteggere un cluster di Service Fabric](service-fabric-cluster-security.md).
- Informazioni su come [Pubblicare un'applicazione in un cluster remoto tramite Visual Studio](service-fabric-publish-app-remote-cluster.md).

<!-- Links -->
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[secure-cluster-arm-template]: https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad
[aad-graph-api-docs]: https://msdn.microsoft.com/it-IT/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com

<!-- Images -->
[assign-users-to-roles-button]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles.png
[setupapp-script-output]: ./media/service-fabric-cluster-security-client-auth-with-aad/setupapp-script-arm-json-output.png
[vs-publish-aad-login]: ./media/service-fabric-cluster-security-client-auth-with-aad/vs-login-prompt.png

<!---HONumber=AcomDC_0518_2016-->
---
title: Pubblicare un&quot;app in un cluster remoto con Visual Studio | Documentazione Microsoft
description: Scoprire come pubblicare un&quot;applicazione in un cluster remoto Service Fabric usando Visual Studio.
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: faecd892-eb54-4d9c-8023-c67442afb8e8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/29/2016
ms.author: cawa
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: c440c520d84fc503ff9e705555449e92555d4721
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017


---
# <a name="deploy-and-remove-applications-using-visual-studio"></a>Distribuire e rimuovere applicazioni con Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

L'estensione Service Fabric di Azure per Visual Studio offre un modo semplice, ripetibile e configurabile tramite script per pubblicare un'applicazione su un cluster Service Fabric.

## <a name="the-artifacts-required-for-publishing"></a>Gli elementi necessari per la pubblicazione
### <a name="deploy-fabricapplicationps1"></a>Deploy-FabricApplication.ps1
Script di PowerShell che usa un percorso del profilo di pubblicazione come parametro per la pubblicazione di applicazioni dell'infrastruttura di servizi. Lo script fa parte dell'applicazione ed è possibile modificarlo in base alle esigenze dell'applicazione.

### <a name="publish-profiles"></a>Profili di pubblicazione
Una cartella nel progetto di applicazione di Service Fabric denominata **PublishProfiles** contiene i file XML che archiviano le informazioni essenziali per la pubblicazione di un'applicazione, ad esempio:

* Parametri di connessione del cluster Service Fabric
* Percorso di un file di parametri dell'applicazione
* Impostazioni di aggiornamento

Per impostazione predefinita, l'applicazione include tre profili di pubblicazione: Local.1Node.xml, Local.5Node.xml e Cloud.xml. È possibile aggiungere altri profili copiando e incollando uno dei file predefiniti.

### <a name="application-parameter-files"></a>File di parametri dell'applicazione
Una cartella nel progetto dell'applicazione di Service Fabric denominata **ApplicationParameters** contiene i file XML per i valori dei parametri del manifesto dell'applicazione specificati dall'utente. I file manifesto dell'applicazione possono essere parametrizzati in modo che si possano usare diversi valori per le impostazioni di distribuzione. Per altre informazioni sulla parametrizzazione dell'applicazione, vedere l'articolo relativo alla [gestione di più ambienti in Service Fabric](service-fabric-manage-multiple-environment-app-configuration.md).

> [!NOTE]
> Per i servizi attore, è necessario compilare il progetto prima di tentare la modifica del file in un editor o tramite la finestra di dialogo di pubblicazione. Questo accade perché una parte dei file manifesto viene generata durante la compilazione.

## <a name="to-publish-an-application-using-the-publish-service-fabric-application-dialog-box"></a>Per pubblicare un'applicazione usando la finestra di dialogo Pubblica applicazione di Service Fabric
I passaggi seguenti illustrano come pubblicare un'applicazione usando la finestra di dialogo **Pubblica applicazione di Service Fabric** fornita dagli strumenti di Visual Studio per Service Fabric.

1. Nel menu di scelta rapida del progetto di applicazione di Service Fabric scegliere **Pubblica...** per visualizzare la finestra di dialogo **Pubblica applicazione di Service Fabric**.
   
    ![Finestra di dialogo **Pubblica applicazione di Service Fabric**][0]
   
    Il file selezionato nell'elenco a discesa **Profilo di destinazione** si trova nella stessa posizione di tutte le impostazioni, ad eccezione di **Versioni manifesto**. È possibile riusare un profilo esistente o crearne uno nuovo selezionando **<Gestione profili...>** nell'elenco a discesa **Profilo di destinazione**. Dopo aver scelto un profilo di pubblicazione, il relativo contenuto viene visualizzato nei campi corrispondenti della finestra di dialogo. Per salvare le modifiche in qualsiasi momento, scegliere il collegamento **Salva profilo** .    
2. Nella sezione **Endpoint connessione** specificare un endpoint di pubblicazione del cluster di Service Fabric locale o remoto. Per aggiungere o modificare l'endpoint di connessione, fare clic sull'elenco a discesa **Endpoint connessione** . Nell'elenco vengono visualizzati gli endpoint di connessione del cluster di Service Fabric disponibili in cui è possibile eseguire la pubblicazione in base alle sottoscrizioni di Azure. Se non è già stato effettuato l'accesso a Visual Studio, verrà richiesto di farlo.
   
    Usare la finestra di dialogo di selezione cluster per scegliere tra le sottoscrizioni e i cluster disponibili.
   
    ![Finestra di dialogo per la **selezione del cluster di Service Fabric**][1]
   
   > [!NOTE]
   > Se si vuole pubblicare in un endpoint arbitrario (ad esempio un party cluster), vedere la sezione **Pubblicazione in un endpoint di cluster arbitrario** riportata di seguito.
   > 
   > 
   
    Dopo aver scelto un endpoint, Visual Studio convalida la connessione al cluster dell'infrastruttura di servizi selezionato. Se il cluster non è sicuro, Visual Studio non può connettersi immediatamente al cluster. Tuttavia, se il cluster è sicuro, è necessario installare un certificato nel computer locale prima di procedere. Per altre informazioni, vedere l'articolo relativo alla [configurazione di connessioni protette](service-fabric-visualstudio-configure-secure-connections.md) . Al termine, fare clic sul pulsante **OK** . Il cluster selezionato viene visualizzato nella finestra di dialogo **Pubblica applicazione di Service Fabric** .
3. Nell'elenco a discesa **File di parametri dell'applicazione** passare a un file di parametri dell'applicazione. Un file di parametri dell'applicazione contiene valori specificati dall'utente per i parametri nel file manifesto dell'applicazione. Per aggiungere o modificare un parametro, scegliere il pulsante **Modifica** . Immettere o modificare il valore del parametro nella griglia **Parametri** . Al termine, scegliere il pulsante **Salva** .
   
    ![Finestra di dialogo **Modifica parametri**][2]
4. Usare la casella di controllo **Aggiorna l'applicazione** per specificare se questa azione di pubblicazione è un aggiornamento. Le operazioni di pubblicazione di aggiornamento differiscono dalle normali operazioni di pubblicazione. Per un elenco delle differenze, vedere l'articolo relativo all' [aggiornamento di un'applicazione dell'infrastruttura di servizi](service-fabric-application-upgrade.md) . Per configurare le impostazioni di aggiornamento, scegliere il collegamento **Configura impostazioni di aggiornamento** . Viene visualizzato l'editor del parametro di aggiornamento. Per altre informazioni sui parametri di aggiornamento, vedere l'articolo relativo alla [configurazione dell'aggiornamento di un'applicazione dell'infrastruttura di servizi](service-fabric-visualstudio-configure-upgrade.md) .
5. Scegliere il pulsante **Versioni del manifesto...** per visualizzare la finestra di dialogo **Modifica versioni**. Affinché l'aggiornamento sia effettivo, è necessario aggiornare le versioni del servizio e dell'applicazione. Per informazioni sull'impatto delle versioni del manifesto del servizio e dell'applicazione su un processo di aggiornamento, vedere l' [esercitazione sull'aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade-tutorial.md) .
   
    ![Finestra di dialogo **Modifica versioni**][3]
   
    Se le versioni del servizio e dell'applicazione usano il controllo delle versioni semantico, ad esempio 1.0.0, o valori numerici in formato 1.0.0.0, selezionare l'opzione **Aggiorna automaticamente le versioni del servizio e dell'applicazione** . Se si seleziona questa opzione, i numeri di versione del servizio e dell'applicazione vengono aggiornati automaticamente ogni volta che viene aggiornata la versione di un codice, una configurazione o un pacchetto dati. Se si preferisce modificare le versioni manualmente, deselezionare la casella di controllo per disabilitare questa funzionalità.
   
   > [!NOTE]
   > Per visualizzare tutte le voci del pacchetto di un progetto attore, compilare innanzitutto il progetto in modo da generare le voci nei file manifesto del servizio.
   > 
   > 
6. Dopo aver specificato tutte le impostazioni necessarie, scegliere il pulsante **Pubblica** per pubblicare l'applicazione nel cluster dell'infrastruttura di servizi selezionato. Le impostazioni specificate vengono applicate al processo di pubblicazione.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Pubblicare in un endpoint di cluster arbitrario (inclusi i party cluster)
L'esperienza di pubblicazione di Visual Studio è ottimizzata per la pubblicazione in cluster remoti associati a una delle sottoscrizioni di Azure. Tuttavia, è possibile eseguire la pubblicazione in endpoint arbitrari (ad esempio, party cluster di Service Fabric) modificando direttamente il profilo di pubblicazione XML. Come indicato in precedenza, per impostazione predefinita sono disponibili tre profili di pubblicazione, **Local.1Node.xml**, **Local.5Node.xml** e **Cloud.xml**, ma è possibile crearne altri per ambienti diversi. Ad esempio, è possibile creare un profilo per la pubblicazione nei party cluster denominato **Party.xml**.

Se ci si connette a un cluster non protetto, è necessario unicamente l'endpoint di connessione del cluster, ad esempio `partycluster1.eastus.cloudapp.azure.com:19000`. In tal caso, l'endpoint di connessione nel profilo di pubblicazione sarà simile al seguente:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Se ci si connette a un cluster protetto, è necessario anche fornire i dettagli del certificato client dell'archivio locale da usare per l'autenticazione. Per altre informazioni, vedere l'articolo relativo alla [configurazione di connessioni protette a un cluster di Service Fabric](service-fabric-visualstudio-configure-secure-connections.md).

  Dopo aver configurato il profilo di pubblicazione, è possibile farvi riferimento nella finestra di dialogo di pubblicazione come illustrato di seguito.

  ![Nuovo profilo di pubblicazione nella finestra di dialogo di pubblicazione][4]

  Notare che, in questo caso, il nuovo profilo di pubblicazione punta a uno dei file di parametri dell'applicazione predefiniti. Questo va bene se si vuole pubblicare la stessa configurazione dell'applicazione in un certo numero di ambienti. Al contrario, nei casi in cui si desidera avere diverse configurazioni per ogni ambiente su cui pubblicare, è opportuno creare un file di parametri dell'applicazione corrispondente.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come automatizzare il processo di pubblicazione in un ambiente di integrazione continua, vedere [Configurare l'integrazione continua per un'applicazione di Service Fabric](service-fabric-set-up-continuous-integration.md).

[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png


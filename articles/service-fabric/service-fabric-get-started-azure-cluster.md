---
title: Configurare un cluster di Azure Service Fabric | Microsoft Docs
description: Guida introduttiva. Creare un cluster Windows o Linux di Service Fabric in Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 813217567c7e1fa5fc9ac11492933ad0c34553d1
ms.lasthandoff: 04/18/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Creare il primo cluster di Service Fabric di Azure
Un [cluster di Service Fabric](service-fabric-deploy-anywhere.md) è un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi. Questa Guida introduttiva consente di creare un cluster con cinque nodi, in esecuzione in Windows o Linux, tramite il [portale di Azure](http://portal.azure.com) in pochi minuti.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com).

## <a name="create-the-cluster"></a>Creare il cluster

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** dal pannello **Nuovo** e quindi selezionare **Cluster di Service Fabric** dal pannello **Calcolo**.
3. Compilare il modulo **Informazioni di base** di Service Fabric. Per **Sistema operativo** selezionare la versione di Windows o Linux per l'esecuzione dei nodi del cluster. Il nome utente e la password immessi in questo modulo verranno usati per accedere alla macchina virtuale. Per **Gruppo di risorse** creare un nuovo gruppo. Un gruppo di risorse è un contenitore logico in cui vengono create e gestite collettivamente le risorse di Azure. Al termine fare clic su **OK**.

    ![Output installazione del cluster][cluster-setup-basics]

4. Compilare il modulo **Configurazione cluster**.  Per **Numero di tipi di nodo** immettere "1" e impostare [Livello di durabilità](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) su "Bronze".

5. Selezionare **Configurare il tipo di ogni nodo** e compilare il modulo **Configurazione del tipo di nodo**. I tipi di nodo definiscono le dimensioni della VM, il numero di VM, gli endpoint personalizzati e altre impostazioni per le VM di quel tipo. Ogni tipo di nodo definito viene configurato come set di scalabilità di macchine virtuali, che viene usato per distribuire e gestire macchine virtuali come set. Ogni tipo di nodo può essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse.  Il primo tipo di nodo o nodo primario ospita i servizi di sistema di Service Fabric e deve avere almeno cinque VM.

    La [pianificazione della capacità](service-fabric-cluster-capacity.md) è un passaggio importante per qualsiasi distribuzione di produzione.  Per questa Guida introduttiva, tuttavia, non vengono eseguite applicazioni, quindi selezionare una VM con dimensioni *DS1_v2 Standard*.  Selezionare "Silver" per il [Livello di affidabilità](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) e specificare una capacità pari a 5 per il set di scalabilità di macchine virtuali.  

    Gli endpoint personalizzati aprono porte in Azure Load Balancer, per consentire di connettersi alle applicazioni in esecuzione nel cluster.  Immettere "80, 8172" per aprire le porte 80 e 8172.

    Non selezionare la casella **Configura impostazioni avanzate**, che consente di personalizzare gli endpoint di gestione TCP/HTTP, gli intervalli di porte dell'applicazione, i [vincoli della selezione](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) e le [proprietà di capacità](service-fabric-cluster-resource-manager-metrics.md).    

    Selezionare **OK**.

6. Nel modulo **Configurazione cluster** impostare **Diagnostica** su **On**.  Per questa Guida introduttiva non è necessario immettere alcuna proprietà per le [impostazioni dell'infrastruttura](service-fabric-cluster-fabric-settings.md).  In **Versione dell'infrastruttura** selezionare la modalità di aggiornamento **Automatico**, in modo che Microsoft aggiorni automaticamente la versione del codice dell'infrastruttura che esegue il cluster.  Impostare la modalità su **Manuale** se si vuole [scegliere una versione supportata](service-fabric-cluster-upgrade.md) a cui eseguire l'aggiornamento. 

    ![Configurazione del tipo di nodo][node-type-config]

    Selezionare **OK**.

7. Compilare il modulo **Sicurezza**.  Per questa Guida introduttiva selezionare **Senza protezione**.  È consigliabile creare un cluster sicuro per i carichi di lavoro di produzione, tuttavia, perché chiunque si può connettere in modo anonimo a un cluster senza protezione ed eseguire operazioni di gestione.  

    I certificati vengono usati in Service Fabric per fornire l'autenticazione e la crittografia e proteggere i vari aspetti di un cluster e delle sue applicazioni. Per altre informazioni sull'uso dei certificati in Service Fabric, vedere [Scenari di sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md).  Per abilitare l'autenticazione utente tramite Azure Active Directory o per configurare i certificati per la sicurezza dell'applicazione, [creare un cluster da un modello di Resource Manager](service-fabric-cluster-creation-via-arm.md).

    Selezionare **OK**.

8. Esaminare il riepilogo.  Se si vuole scaricare un modello di Resource Manager basato sulle impostazioni immesse, selezionare **Scarica modello e parametri**.  Selezionare **Crea** per creare il cluster.

    È possibile visualizzare lo stato di avanzamento del processo di creazione nell'area delle notifiche: fare clic sull'icona a forma di campana accanto alla barra di stato nell'angolo superiore destro della schermata. Se durante la creazione del cluster si è fatto clic su **Aggiungi alla Schermata iniziale** durante la creazione del cluster, la voce **Distribuzione di Cluster di Service Fabric** viene aggiunta alla **schermata iniziale**.

## <a name="view-cluster-status"></a>Visualizzare lo stato del cluster
Al termine della creazione del cluster, è possibile esaminare il cluster nel pannello **Panoramica** del portale. È ora possibile visualizzare i dettagli del cluster nel dashboard, inclusi l'endpoint pubblico del cluster e un link per Service Fabric Explorer.

![Stato del cluster][cluster-status]

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualizzare il cluster con Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) rappresenta un ottimo strumento per la visualizzazione del cluster e la gestione delle applicazioni.  Service Fabric Explorer è un servizio in esecuzione nel cluster.  È possibile accedere a questo servizio tramite un Web browser facendo clic sul collegamento **Service Fabric Explorer** della pagina **Panoramica** del cluster nel portale.  È anche possibile immettere l'indirizzo direttamente nel browser: [http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

Il dashboard del cluster offre una panoramica del cluster, incluso un riepilogo dell'integrità delle applicazioni e dei nodi. La visualizzazione dei nodi mostra il layout fisico del cluster. Per un determinato nodo, è possibile esaminare le applicazioni con il codice distribuito in quel nodo.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="connect-to-the-cluster-using-powershell"></a>Connessione al cluster mediante PowerShell
Verificare che il cluster sia in esecuzione connettendosi tramite PowerShell.  Il modulo ServiceFabric di PowerShell viene installato con [Service Fabric SDK](service-fabric-get-started.md).  Il cmdlet [Connect-ServiceFabricCluster](/powershell/module/ServiceFabric/Connect-ServiceFabricCluster) stabilisce una connessione al cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Per altri esempi di connessione a un cluster, vedere [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md). Dopo la connessione al cluster, usare il cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) per visualizzare un elenco dei nodi presenti nel cluster e informazioni di stato per ogni nodo. **HealthState** deve essere *OK* per ogni nodo.

```powershell
PS C:\> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
```

## <a name="remove-the-cluster"></a>Rimuovere il cluster
Un cluster di Service Fabric è costituito da altre risorse di Azure oltre alla risorsa cluster stessa. Per eliminare completamente un cluster Service Fabric è necessario eliminare anche tutte le risorse di cui è composto. Il modo più semplice per eliminare il cluster e tutte le rispettive risorse consiste nell'eliminare il gruppo di risorse. Per altri modi per eliminare un cluster o per eliminare solo alcune risorse in un gruppo di risorse, vedere [Eliminare un cluster](service-fabric-cluster-delete.md)

Eliminare un gruppo di risorse nel portale di Azure:
1. Passare al cluster Service Fabric da eliminare.
2. Fare clic sul nome del **Gruppo di risorse** nella pagina delle informazioni di base del cluster.
3. Nella pagina **Informazioni di base** del gruppo di risorse fare clic su **Elimina** e seguire le istruzioni visualizzate nella pagina per completare l'eliminazione del gruppo di risorse.
    ![Eliminare il gruppo di risorse][cluster-delete]

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato configurato un cluster di sviluppo autonomo, provare a eseguire queste operazioni:
* [Creare un cluster sicuro nel portale](service-fabric-cluster-creation-via-portal.md)
* [Creare un cluster da un modello](service-fabric-cluster-creation-via-arm.md) 
* [Distribuire le app tramite PowerShell](service-fabric-deploy-remove-applications.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png

---
title: Proteggere un cluster in esecuzione in Windows usando la protezione di Windows | Microsoft Docs
description: Informazioni su come configurare la sicurezza da nodo a nodo e da client a nodo in un cluster autonomo in esecuzione in Windows usando la protezione di Windows.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 6294a6fac8748f70c807df76feb425cb627bf4c3
ms.lasthandoff: 03/15/2017


---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteggere un cluster autonomo in Windows usando la protezione di Windows
Per impedire l'accesso non autorizzato a un cluster di Service Fabric, è necessario proteggere il cluster. La sicurezza è importante soprattutto quando il cluster esegue carichi di lavoro di produzione. Questo articolo descrive come configurare la sicurezza da nodo a nodo e da client a nodo usando la protezione di Windows nel file *ClusterConfig.JSON*.  Il processo corrisponde al passaggio di configurazione della sicurezza in [Creare un cluster autonomo in esecuzione su Windows Server](service-fabric-cluster-creation-for-windows-server.md). Per altre informazioni sull'uso della protezione di Windows in Service Fabric, vedere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

> [!NOTE]
> È opportuno considerare con attenzione la selezione della sicurezza da nodo a nodo, perché non esiste un aggiornamento del cluster da una scelta di sicurezza a un'altra. Per modificare la selezione della sicurezza, è necessario ricompilare il cluster completo.
>
>

## <a name="configure-windows-security"></a>Configurare la funzionalità di sicurezza di Windows  
Il file di configurazione di esempio *ClusterConfig.Windows.JSON* scaricato con il pacchetto del cluster autonomo [Microsoft.Azure.ServiceFabric.WindowsServer<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) contiene un modello per la configurazione della protezione di Windows. La sicurezza di Windows viene configurata nella sezione **Proprietà** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **Impostazione di configurazione** | **Descrizione** |
| --- | --- |
| ClusterCredentialType |Impostare su *Windows* per abilitare la protezione di Windows. |
| ServerCredentialType |Impostare su *Windows* per abilitare la protezione di Windows per i client.<br /><br />Questa impostazione indica che i client del cluster e il cluster stesso sono in esecuzione in un dominio di Active Directory. |  
| WindowsIdentities |Contiene le identità del client e del cluster. |  
| ClusterIdentity |Usare il nome di un gruppo di computer, dominio\gruppo di computer, per configurare la sicurezza da nodo a nodo. |  
| ClientIdentities |Configura la sicurezza da client a nodo. Matrice di account utente del client. |  
| Identità |Aggiungere l'utente del dominio, dominio\nome utente, per l'identità del client. |  
| IsAdmin |Impostare su true per indicare che l'utente del dominio ha un accesso client come amministratore oppure su false per indicare un accesso client come utente. |  

La [sicurezza da nodo a nodo](service-fabric-cluster-security.md#node-to-node-security) viene configurata usando **ClusterIdentity**. I nodi devono riconoscersi per creare relazioni di trust reciproche. Per favorire il riconoscimento, creare un gruppo di dominio che includa tutti i nodi del cluster. Il nome del gruppo dovrà essere specificato in **ClusterIdentity**. Per altre informazioni, vedere l'articolo su come [Creare un gruppo in Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).  

La [sicurezza da client a nodo](service-fabric-cluster-security.md#client-to-node-security) viene configurata usando **ClientIdentities**. Per stabilire una relazione di trust tra un client e il cluster, è necessario configurare il cluster in modo che riconosca le identità dei client che il cluster può considerare attendibili. È possibile stabilire una relazione di trust in due modi diversi:

- Specificare gli utenti del gruppo di dominio che possono connettersi.
- Specificare gli utenti del nodo del dominio che possono connettersi.

Infrastruttura di servizi supporta due tipi di controllo di accesso diversi per i client connessi a un cluster di Infrastruttura di servizi: amministratore e utente. La funzionalità di controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinati tipi di operazioni del cluster per diversi gruppi di utenti, rendendo il cluster più sicuro.  Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.  

La sezione **security** dell'esempio seguente configura la protezione di Windows, specifica che i computer in *ServiceFabric/clusterA.contoso.com* fanno parte del cluster e specifica che a *CONTOSO\usera* è consentito l'accesso client come amministratore:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric non devono essere distribuito in un controller di dominio. Verificare che ClusterConfig.json non includa l'indirizzo IP del controller di dominio quando si usa un gruppo di computer o un account del servizio gestito del gruppo.
>
>

## <a name="next-steps"></a>Passaggi successivi
Dopo la configurazione della sicurezza di Windows nel file *ClusterConfig.JSON* , riprendere il processo di creazione del cluster in [Create a standalone cluster running on Windows](service-fabric-cluster-creation-for-windows-server.md)(Creare un cluster autonomo in esecuzione in Windows).

Per altre informazioni sulla sicurezza da nodo a nodo e da client a nodo e sul controllo degli accessi in base al ruolo, vedere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

Per alcuni esempi di connessione tramite PowerShell o FabricClient, vedere [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md).


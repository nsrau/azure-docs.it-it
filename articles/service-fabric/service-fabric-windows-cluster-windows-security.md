<properties
   pageTitle="Proteggere un cluster in esecuzione in Windows tramite la funzionalità di sicurezza di Windows | Microsoft Azure"
   description="Informazioni su come configurare la sicurezza da nodo a nodo e da client a nodo in un cluster autonomo in esecuzione su Windows usando la sicurezza di Windows."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>  


# Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows

Per impedire l'accesso non autorizzato a un cluster di Service Fabric, è necessario proteggerlo, in particolare quando sono in esecuzione carichi di lavoro di produzione. Questo articolo descrive come configurare la sicurezza da nodo a nodo e da client a nodo tramite la funzionalità di sicurezza di Windows nel file *ClusterConfig.JSON*, che corrisponde alla procedura di configurazione della sicurezza descritta in [Create a standalone cluster running on Windows](service-fabric-cluster-creation-for-windows-server.md) (Creare un cluster autonomo in esecuzione in Windows). Per altre informazioni sull'uso della sicurezza di Windows in Service Fabric, vedere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

>[AZURE.NOTE]
È opportuno considerare con attenzione la selezione della sicurezza da nodo a nodo, perché non esiste un aggiornamento del cluster da una scelta di sicurezza a un'altra. La modifica della selezione della sicurezza richiedere una ricompilazione completa del cluster.

## Configurare la funzionalità di sicurezza di Windows
Il file di configurazione di esempio *ClusterConfig.Windows.JSON* scaricato con il pacchetto del cluster autonomo [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) contiene un modello per la configurazione della sicurezza di Windows. La sicurezza di Windows viene configurata nella sezione **Proprietà**:

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

|**Impostazioni di configurazione**|**Descrizione**|
|-----------------------|--------------------------|
|ClusterCredentialType|La sicurezza di Windows viene abilitata impostando il parametro **ClusterCredentialType** su *Windows*.|
|ServerCredentialType|La sicurezza di Windows per i client viene abilitata impostando il parametro **ServerCredentialType** su *Windows*. Questa impostazione indica che i client del cluster e il cluster stesso sono in esecuzione in un dominio di Active Directory.|
|WindowsIdentities|Contiene le identità del client e del cluster.|
|ClusterIdentity|Configura la sicurezza da nodo a nodo. Elenco con valori delimitati da virgole di nomi di computer o account del servizio gestito del gruppo.|
|ClientIdentities|Configura la sicurezza da client a nodo. Matrice di account utente del client.|
|Identità|Identità del client, un utente del dominio.|
|IsAdmin|True indica che l'utente del dominio ha un accesso client come amministratore, mentre false indica un accesso client come utente.|

[Sicurezza da nodo a nodo](service-fabric-cluster-security.md#node-to-node-security) è un'impostazione configurata tramite **ClusterIdentity**. Per creare relazioni di trust tra i nodi, è necessario che si riconoscano. Questa operazione può essere eseguita in due modi diversi: specificarel'account del servizio gestito del gruppo che include tutti i nodi del cluster oppure specificare le identità di nodo del dominio di tutti i nodi del cluster. È consigliabile usare l'approccio degli [account del servizio gestiti del gruppo](https://technet.microsoft.com/library/hh831782.aspx), in particolare per i cluster più grandi (più di 10 nodi) o per i cluster soggetti a probabile crescita o riduzione. Questo approccio consente l'aggiunta o la rimozione dei nodi dagli account del servizio gestito del gruppo, senza richiedere modifiche al manifesto del cluster. Non richiede inoltre la creazione di un gruppo di dominio per cui agli amministratori del cluster siano stati concessi i diritti di accesso per aggiungere e rimuovere membri. Per altre informazioni, vedere [Introduzione gli account del servizio gestiti del gruppo](http://technet.microsoft.com/library/jj128431.aspx).

[Sicurezza da client a nodo](service-fabric-cluster-security.md#client-to-node-security) è un'impostazione configurata tramite **ClientIdentities**. Per stabilire un trust tra un client e il cluster, è necessario configurare il cluster in modo che riconosca quali identità dei client può considerare attendibili. Questa operazione può essere eseguita in due modi diversi: specificare se la connessione è consentita agli utenti dei gruppo di dominio o agli utenti dei nodi di dominio. Infrastruttura di servizi supporta due tipi di controllo di accesso diversi per i client connessi a un cluster di Infrastruttura di servizi: amministratore e utente. La funzionalità di controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinati tipi di operazioni del cluster per diversi gruppi di utenti, rendendo il cluster più sicuro. Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.

La sezione **security** dell'esempio seguente configura la sicurezza di Windows e specifica che i computer in *ServiceFabric/clusterA.contoso.com* fanno parte del cluster e che a *CONTOSO\\usera* è consentito l'accesso client come amministratore:

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

## Passaggi successivi

Dopo la configurazione della sicurezza di Windows nel file *ClusterConfig.JSON*, riprendere il processo di creazione del cluster in [Create a standalone cluster running on Windows](service-fabric-cluster-creation-for-windows-server.md) (Creare un cluster autonomo in esecuzione in Windows).

Per altre informazioni sulla sicurezza da nodo a nodo e da client a nodo e sul controllo degli accessi in base al ruolo, vedere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

Per alcuni esempi di connessione tramite PowerShell o FabricClient, vedere [Connect to a secure cluster](service-fabric-connect-to-secure-cluster.md) (Connettersi a un cluster sicuro).

<!---HONumber=AcomDC_0831_2016-->
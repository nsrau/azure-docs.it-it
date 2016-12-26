---
title: Ridimensionare il cluster del servizio contenitore di Azure con l&quot;interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Come ridimensionare il cluster del servizio contenitore di Azure usando l&quot;interfaccia della riga di comando di Azure.
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e8df2e68b1b7018d76da89ba9ab332b6ea216fb


---
# <a name="scale-an-azure-container-service"></a>Ridimensionare il servizio contenitore di Azure
È possibile aumentare il numero di nodi desiderato del servizio contenitore di Azure usando l'interfaccia della riga di comando di Azure. Quando per la scalabilità si usa l'interfaccia della riga di comando di Azure, quest'ultima restituisce un nuovo file di configurazione che rappresenta la modifica apportata al contenitore.

## <a name="about-the-command"></a>Informazioni sul comando
Per consentire l'interazione con i contenitori di Azure, è necessario che l'interfaccia della riga di comando di Azure si trovi in modalità Azure Resource Manager. È possibile passare alla modalità Resource Manager chiamando `azure config mode arm`. Il comando `acs` dispone di un comando figlio denominato `scale` che esegue tutte le operazioni di ridimensionamento per un servizio contenitore. È possibile ottenere informazioni sui diversi parametri usati nel comando di ridimensionamento eseguendo `azure acs scale --help`. L'output dovrebbe essere simile al seguente:

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Usare il comando per il ridimensionamento
Per ridimensionare un servizio contenitore, è innanzitutto necessario conoscere il **gruppo di risorse** e il **nome del servizio contenitore di Azure**. È inoltre necessario specificare il nuovo numero di agenti. Se si specifica un numero più piccolo è possibile ridurre le prestazioni, mentre se si specifica un numero più grande è possibile aumentarle.

Prima di eseguire il ridimensionamento, è opportuno conoscere il numero corrente di agenti per un servizio contenitore. Usare il comando `azure acs show <resource group> <ACS name>` per restituire la configurazione del servizio contenitore di Azure. Notare il valore <mark>Count</mark> risultante.

#### <a name="see-current-count"></a>Valore Count corrente
```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Ridimensionare al nuovo valore Count
Come probabilmente ovvio, è possibile ridimensionare il servizio contenitore chiamando `azure acs scale` e specificando il **gruppo di risorse**, il **nome del servizio contenitore di Azure** e il **numero di agenti**. Quando si ridimensiona un servizio contenitore, l'interfaccia della riga di comando di Azure restituisce una stringa JSON che rappresenta la nuova configurazione del servizio contenitore, incluso il nuovo numero di agenti.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un cluster](container-service-deployment.md)




<!--HONumber=Nov16_HO3-->



---
title: Autorizzazioni RBAC necessarie per l'utilizzo delle funzionalità
titleSuffix: Azure Network Watcher
description: Informazioni su quali autorizzazioni di controllo degli accessi in base al ruolo di Azure sono necessarie per lavorare con le funzionalità di Network Watcher.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: damendo
ms.openlocfilehash: f8743f19d6cd262ad140659be55a4fc57e842564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840554"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Autorizzazioni di controllo degli accessi in base al ruolo necessarie per lavorare con le funzionalità di Network Watcher

Il controllo degli accessi in base al ruolo (RBAC) di Azure consente di assegnare solo le azioni specifiche ai membri dell'organizzazione necessarie per completare le responsabilità a loro assegnate. Per usare le funzionalità di Network Watcher è necessario assegnare l'account a cui si effettua l'accesso in Azure ai ruoli incorporati di [Proprietario](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [Collaboratore](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), o [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor), o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a cui sono assegnate le azioni elencate per ciascuna funzionalità Network Watcher nelle sezioni che seguono. Per altre informazioni sulle funzionalità di Network Watcher, vedere [Informazioni su Network Watcher](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Ottenere un'istanza di Network Watcher                                          |
| Microsoft.Network/networkWatchers/write                             | Creare o aggiornare un'istanza di Network Watcher                             |
| Microsoft.Network/networkWatchers/delete                            | Eliminare un'istanza di Network Watcher                                       |

## <a name="nsg-flow-logs"></a>Log del flusso del gruppo di sicurezza di rete

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Configurare un log del flusso                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Effettuare una query dello stato per un log del flusso                                    |

## <a name="connection-troubleshoot"></a>Risoluzione dei problemi relativi alle connessioni

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Avviare un test per la risoluzione dei problemi di connessione
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Effettuare una query dei risultati di un test per la risoluzione dei problemi di connessione                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Eseguire un test per la risoluzione dei problemi di connessione                             |

## <a name="connection-monitor"></a>Monitoraggio connessione

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Avviare il monitoraggio di una connessione                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Interrompere il monitoraggio di una connessione                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Effettuare una query del monitoraggio di una connessione                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Ottenere il monitoraggio di una connessione                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Creare un monitoraggio della connessione                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Eliminare il monitoraggio di una connessione                                    |

## <a name="packet-capture"></a>Acquisizione pacchetti

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Effettuare una query dello stato di un'acquisizione di pacchetti                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Interrompere un'acquisizione di pacchetti                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Ottenere un'acquisizione di pacchetti                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Creare un'acquisizione di pacchetti                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Eliminare un'acquisizione di pacchetti                                        |

## <a name="ip-flow-verify"></a>Verifica del flusso IP

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Verificare un flusso dell'IP                                              |

## <a name="next-hop"></a>Hop successivo

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Ottenere l'hop successivo da una macchina virtuale                                     |

## <a name="network-security-group-view"></a>Visualizzazione dei gruppi di sicurezza di rete

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Visualizzare i gruppi di sicurezza                                           |

## <a name="topology"></a>Topologia

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Ottenere la topologia                                                   |

## <a name="reachability-report"></a>Report di raggiungibilità

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Ottenere un report di raggiungibilità di Azure                               |


## <a name="additional-actions"></a>Azioni aggiuntive

Le funzionalità di Network Watcher richiedono anche le azioni seguenti:

| Azione(e)                                                           | Descrizione                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Read                                     | Utilizzato per recuperare le assegnazioni di ruolo RBAC e le definizioni dei criteri          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | Utilizzato per enumerare tutti i gruppi di risorse in una sottoscrizioneUsed to enumerate all the resource groups in a subscription    |
| Microsoft.Storage/storageAccounts/Read                              | Utilizzato per ottenere le proprietà per l'account di archiviazione specificatoUsed to get the properties for the specified storage account   |
| Microsoft.Storage/storageAccounts/listServiceSas/Azione, </br> Microsoft.Storage/storageAccounts/listAccountSas/Azione, <br> Microsoft.Storage/storageAccounts/listKeys/Action| Utilizzato per recuperare le firme di accesso condiviso (SAS) consentendo [l'accesso sicuro all'account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) e scrivere nell'account di archiviazione |
| Microsoft.Compute/virtualMachines/Lettura, </br> Microsoft.Compute/virtualMachines/Write| Utilizzato per accedere alla macchina virtuale, eseguire un'acquisizione di pacchetti e caricarla nell'account di archiviazioneUsed to log in to the VM, do a packet capture and upload it to storage account|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Utilizzato per verificare se l'estensione Network Watcher è presente e installare se necessario |
| Microsoft.Compute/virtualMachineScaleSets/Lettura, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Utilizzato per accedere ai set di scalabilità delle macchine virtuali, eseguire acquisizioni di pacchetti e caricarli nell'account di archiviazione|
| Microsoft.Compute/virtualMachineScaleSets/extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Utilizzato per verificare se l'estensione Network Watcher è presente e installare se necessario |
| Microsoft.Insights/alertRules/*                                     | Utilizzato per impostare avvisi di metrica                                     |
| Microsoft.Support/*                                                 | Utilizzato per creare e aggiornare i ticket di supporto da Network Watcher |

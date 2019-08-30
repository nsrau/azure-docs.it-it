---
title: Autorizzazioni necessarie per usare le funzionalità di Network Watcher di Azure | Microsoft Docs
description: Informazioni su quali autorizzazioni di controllo degli accessi in base al ruolo di Azure sono necessarie per lavorare con le funzionalità di Network Watcher.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 5bd7e30a6a95d60bda4b7c3da44be1b8046bb71f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163804"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Autorizzazioni di controllo degli accessi in base al ruolo necessarie per lavorare con le funzionalità di Network Watcher

Il controllo degli accessi in base al ruolo (RBAC) di Azure consente di assegnare solo le azioni specifiche ai membri dell'organizzazione necessarie per completare le responsabilità a loro assegnate. Per usare le funzionalità di Network Watcher è necessario assegnare l'account a cui si effettua l'accesso in Azure ai ruoli incorporati di [Proprietario](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [Collaboratore](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), o [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor), o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a cui sono assegnate le azioni elencate per ciascuna funzionalità Network Watcher nelle sezioni che seguono. Per altre informazioni sulle funzionalità di Network Watcher, vedere [Informazioni su Network Watcher](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Azione                                                              | DESCRIZIONE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Ottenere un'istanza di Network Watcher                                          |
| Microsoft.Network/networkWatchers/write                             | Creare o aggiornare un'istanza di Network Watcher                             |
| Microsoft.Network/networkWatchers/delete                            | Eliminare un'istanza di Network Watcher                                       |

## <a name="nsg-flow-logs"></a>Log del flusso del NSG

| Azione                                                              | DESCRIZIONE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Configurare un log del flusso                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Effettuare una query dello stato per un log del flusso                                    |

## <a name="connection-troubleshoot"></a>Risoluzione dei problemi di connessione

| Azione                                                              | Descrizione                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Avviare un test per la risoluzione dei problemi di connessione
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Effettuare una query dei risultati di un test per la risoluzione dei problemi di connessione                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Eseguire un test per la risoluzione dei problemi di connessione                             |

## <a name="connection-monitor"></a>Monitoraggio connessione

| Azione                                                              | DESCRIZIONE                                                           |
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

| Azione                                                              | DESCRIZIONE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Verificare un flusso dell'IP                                              |

## <a name="next-hop"></a>Hop successivo

| Azione                                                              | DESCRIZIONE                                                           |
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

| Azione/i                                                           | Descrizione                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Read                                     | Utilizzato per recuperare le assegnazioni di ruolo e le definizioni dei criteri RBAC          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | Utilizzato per enumerare tutti i gruppi di risorse in una sottoscrizione    |
| Microsoft.Storage/storageAccounts/Read                              | Usato per ottenere le proprietà per l'account di archiviazione specificato   |
| Microsoft. storage/storageAccounts/listServiceSas/Action, </br> Microsoft. storage/storageAccounts/listAccountSas/Action, <br> Microsoft.Storage/storageAccounts/listKeys/Action| Usato per recuperare le firme di accesso condiviso (SAS) che abilitano [l'accesso sicuro all'account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) e la scrittura nell'account di archiviazione |
| Microsoft. Compute/virtualMachines/Read, </br> Microsoft.Compute/virtualMachines/Write| Usato per accedere alla macchina virtuale, eseguire un'acquisizione di pacchetti e caricarla nell'account di archiviazione|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Usato per verificare se è presente Network Watcher estensione e installarlo se necessario |
| Microsoft. Compute/virtualMachineScaleSets/Read, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Usato per accedere ai set di scalabilità di macchine virtuali, eseguire acquisizioni di pacchetti e caricarli nell'account di archiviazione|
| Microsoft. Compute/virtualMachineScaleSets/Extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Usato per verificare se è presente Network Watcher estensione e installarlo se necessario |
| Microsoft.Insights/alertRules/*                                     | Usato per impostare gli avvisi delle metriche                                     |
| Microsoft.Support/*                                                 | Usato per creare e aggiornare i ticket di supporto da Network Watcher |

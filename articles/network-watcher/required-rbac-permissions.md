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
ms.openlocfilehash: 8c8fe6125d9c638fedadc3d299ff0ac0d601fd61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685703"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Autorizzazioni di controllo degli accessi in base al ruolo necessarie per lavorare con le funzionalità di Network Watcher

Il controllo degli accessi in base al ruolo (RBAC) di Azure consente di assegnare solo le azioni specifiche ai membri dell'organizzazione necessarie per completare le responsabilità a loro assegnate. Per usare le funzionalità di Network Watcher è necessario assegnare l'account a cui si effettua l'accesso in Azure ai ruoli incorporati di [Proprietario](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [Collaboratore](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), o [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor), o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a cui sono assegnate le azioni elencate per ciascuna funzionalità Network Watcher nelle sezioni che seguono. Per altre informazioni sulle funzionalità di Network Watcher, vedere [Informazioni su Network Watcher](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Azione                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Ottenere un'istanza di Network Watcher                                          |
| Microsoft.Network/networkWatchers/write                             | Creare o aggiornare un'istanza di Network Watcher                             |
| Microsoft.Network/networkWatchers/delete                            | Eliminare un'istanza di Network Watcher                                       |

## <a name="nsg-flow-logs"></a>Log del flusso del gruppo di sicurezza di rete

| Azione                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Configurare un log del flusso                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Effettuare una query dello stato per un log del flusso                                    |

## <a name="connection-troubleshoot"></a>Risoluzione dei problemi relativi alle connessioni

| Azione                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Avviare un test per la risoluzione dei problemi di connessione
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Effettuare una query dei risultati di un test per la risoluzione dei problemi di connessione                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Eseguire un test per la risoluzione dei problemi di connessione                             |

## <a name="connection-monitor"></a>Monitoraggio connessione

| Azione                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Avviare il monitoraggio di una connessione                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Interrompere il monitoraggio di una connessione                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Effettuare una query del monitoraggio di una connessione                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Ottenere il monitoraggio di una connessione                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Creare un monitoraggio della connessione                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Eliminare il monitoraggio di una connessione                                    |

## <a name="packet-capture"></a>Acquisizione pacchetti

| Azione                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Effettuare una query dello stato di un'acquisizione di pacchetti                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Interrompere un'acquisizione di pacchetti                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Ottenere un'acquisizione di pacchetti                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Creare un'acquisizione di pacchetti                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Eliminare un'acquisizione di pacchetti                                        |

## <a name="ip-flow-verify"></a>Verifica del flusso IP

| Azione                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Verificare un flusso dell'IP                                              |

## <a name="next-hop"></a>Hop successivo

| Azione                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Ottenere l'hop successivo da una macchina virtuale                                     |

## <a name="network-security-group-view"></a>Visualizzazione dei gruppi di sicurezza di rete

| Azione                                                              | Name                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Visualizzare i gruppi di sicurezza                                           |

## <a name="topology"></a>Topologia

| Azione                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Ottenere la topologia                                                   |

## <a name="reachability-report"></a>Report di raggiungibilità

| Azione                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Ottenere un report di raggiungibilità di Azure                               |

## <a name="additional-actions"></a>Azioni aggiuntive

Le funzionalità di Network Watcher richiedono anche le azioni seguenti:

- Microsoft.Authorization/\*/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachines/extensions/Read
- Microsoft.Compute/virtualMachines/extensions/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Compute/virtualMachineScaleSets/extensions/Read
- Microsoft.Compute/virtualMachineScaleSets/extensions/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*

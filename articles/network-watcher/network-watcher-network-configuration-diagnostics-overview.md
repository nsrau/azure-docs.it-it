---
title: Introduzione alla diagnostica della configurazione di rete in Azure Network Watcher | Microsoft Docs
description: Questa pagina offre una panoramica della diagnostica della configurazione di rete Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 4d308b8a1a589308e2481c8bb9e0dc9ec64be25b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996698"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Introduzione alla diagnostica della configurazione di rete in Azure Network Watcher

Lo strumento di diagnostica della configurazione di rete aiuta i clienti a comprendere quali flussi di traffico verranno consentiti o negati nella rete virtuale di Azure insieme a informazioni dettagliate per il debug. Può aiutare a comprendere se le regole di NSG sono configurate correttamente. 

## <a name="pre-requisites"></a>Prerequisiti
Per usare la diagnostica della configurazione di rete, è necessario abilitare Network Watcher nella sottoscrizione. Per abilitare, vedere [creare un'istanza di Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create) .

## <a name="background"></a>Background

- Le risorse in Azure sono connesse tramite reti virtuali (reti virtuali) e subnet. È possibile gestire la sicurezza di questi reti virtuali e subnet usando un gruppo di sicurezza di rete (NSG).
- Un NSG contiene un elenco di regole di sicurezza che consentono o negano il traffico di rete alle risorse a cui è connessa. Gruppi può essere associato a subnet, singole VM o singole interfacce di rete collegate alle macchine virtuali. 
- Tutti i flussi di traffico nella rete vengono valutati usando le regole nel NSG applicabile.
- Le regole vengono valutate in base al numero di priorità dal più basso al più alto 

## <a name="how-does-network-configuration-diagnostic-work"></a>Funzionamento della diagnostica della configurazione di rete 

Per un determinato flusso, lo strumento NCD esegue una simulazione del flusso e restituisce un valore che indica se il flusso deve essere consentito (o negato) e informazioni dettagliate sulle regole che consentono o negano il flusso.  I clienti devono fornire i dettagli di un flusso come origine, destinazione, protocollo e così via. Lo strumento restituisce un valore che indica se il traffico è stato consentito o negato, le regole NSG valutate per il flusso specificato e i risultati della valutazione per ogni regola.

## <a name="next-steps"></a>Passaggi successivi

Usare la diagnostica della configurazione di rete tramite altre interfacce
 - [REST API](https://docs.microsoft.com/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic?view=azps-4.6.1)
 - [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_run_configuration_diagnostic)


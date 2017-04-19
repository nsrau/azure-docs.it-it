---
title: Gruppi di risorse per macchine virtuali Windows in Azure | Documentazione Microsoft
description: Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione dei gruppi di risorse nei servizi di infrastruttura di Azure.
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0fbcabcd-e96d-4d71-a526-431984887451
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: ce4c94a2c8983862a311d296e1a95ecabb4c9f61
ms.lasthandoff: 03/31/2017


---
# <a name="azure-resource-group-guidelines-for-windows-vms"></a>Linee guida sui gruppi di risorse di Azure per macchine virtuali Windows

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Questo articolo si incentra su come creare in modo logico un ambiente e raggruppare tutti i componenti in gruppi di risorse.

## <a name="implementation-guidelines-for-resource-groups"></a>Linee guida sui gruppi di risorse
Decisioni:

* Si desidera creare gruppi di risorse con i componenti dell'infrastruttura di base oppure tramite la distribuzione di applicazioni complete?
* È necessario limitare l'accesso ai gruppi di risorse usando controlli di accesso in base ai ruoli?

Attività:

* Definire i componenti dell'infrastruttura di base e i gruppi di risorse dedicati necessari.
* Esaminare la modalità di implementazione dei modelli di Resource Manager per ottenere distribuzioni coerenti e riproducibili.
* Definire i ruoli di accesso utente necessari per controllare l'accesso ai gruppi di risorse.
* Creare il set di gruppi di risorse usando la usando la convenzione di denominazione scelta. È possibile usare Azure PowerShell o il portale.

## <a name="resource-groups"></a>Gruppi di risorse
Azure consente di raggruppare in modo logico risorse correlate, ad esempio account di archiviazione, reti virtuali e macchine virtuali per distribuirle, gestirle ed eseguirne la manutenzione come una singola entità. Questo approccio semplifica la distribuzione delle applicazioni e al contempo mantiene insieme tutte le risorse correlate per facilitarne la gestione o per consentire altri accessi al gruppo di risorse. Per informazioni complete sui gruppi di risorse, leggere la [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Una funzionalità chiave per i gruppi di risorse è la possibilità di realizzare un ambiente personalizzato grazie ai modelli. Un modello è semplicemente un file JSON che dichiara archiviazione, rete e risorse di calcolo. È anche possibile definire qualsiasi script personalizzato o configurazioni correlati da applicare. Questi modelli consentono di creare distribuzioni coerenti e riproducibili delle applicazioni. Ciò facilita la compilazione di un ambiente nella fase di sviluppo e l'uso dello stesso modello per creare una distribuzione di produzione, o viceversa. Per altre informazioni sull'uso dei modelli, leggere [la procedura dettagliata sui modelli](../../azure-resource-manager/resource-manager-template-walkthrough.md) che descrive i passaggi per la compilazione di un modello.

Esistono due diversi approcci alla progettazione dell'ambiente con gruppi di risorse:

* Gruppi di risorse per ogni distribuzione dell'applicazione, che contengono account di archiviazione, reti virtuali e subnet, macchine virtuali, servizi di bilanciamento del carico e così via.
* Gruppi di risorse centralizzate che contengono la rete virtuale di base e le subnet oppure gli account di archiviazione. Le applicazioni si trovano quindi nei propri gruppi di risorse che contengono solo macchine virtuali, servizi di bilanciamento del carico, interfacce di rete e così via.

Procedendo con la scalabilità orizzontale, i gruppi di risorse centralizzati contenenti la rete virtuale e le subnet semplificano la creazione di connessioni di rete cross-premise per opzioni di connettività ibrida. L'approccio alternativo prevede di avere singole applicazioni dotate di una propria rete virtuale che richiede configurazione e manutenzione.  [I controlli di accesso in base al ruolo](../../active-directory/role-based-access-control-what-is.md) rappresentano una modalità granulare per controllare l'accesso ai gruppi di risorse. Per le applicazioni di produzione, è possibile controllare gli utenti che possono accedere alle risorse. Per le risorse dell'infrastruttura di base è possibile limitare l'accesso solo ai tecnici dell'infrastruttura che le usano. I proprietari dell'applicazione possono accedere solo ai componenti dell'applicazione nel proprio gruppo di risorse e non all'infrastruttura di Azure di base dell'ambiente. Quando si progetta l'ambiente, tenere in considerazione gli utenti che dovranno accedere alle risorse e creare gruppi di risorse adeguati. 

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]



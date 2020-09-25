---
title: Configurare un endpoint privato (anteprima)
titleSuffix: Azure Machine Learning
description: Usare il collegamento privato di Azure per accedere in modo sicuro all'area di lavoro di Azure Machine Learning da una rete virtuale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/21/2020
ms.openlocfilehash: 619960238125191e7bd4e702a49016c8fd58c847
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296655"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Configurare il collegamento privato di Azure per un'area di lavoro Azure Machine Learning (anteprima)

In questo documento si apprenderà come usare il collegamento privato di Azure con l'area di lavoro Azure Machine Learning. Per informazioni sulla configurazione di una rete virtuale per Azure Machine Learning, vedere [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)

> [!IMPORTANT]
> L'uso del collegamento privato di Azure con Azure Machine Learning area di lavoro è attualmente disponibile in anteprima pubblica. Questa funzionalità è disponibile solo nelle aree geografiche seguenti:
>
> * **Stati Uniti orientali**
> * **Stati Uniti centro-meridionali**
> * **Stati Uniti occidentali**
> * **Stati Uniti occidentali 2**
> * **Canada centrale**
> * **Asia sud-orientale**
> * **Giappone orientale**
> * **Europa settentrionale**
> * **Australia orientale**
> * **Regno Unito meridionale**
>
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il collegamento privato di Azure consente di connettersi all'area di lavoro usando un endpoint privato. L'endpoint privato è un set di indirizzi IP privati all'interno della rete virtuale. È quindi possibile limitare l'accesso all'area di lavoro solo per gli indirizzi IP privati. Il collegamento privato consente di ridurre il rischio di exfiltration dei dati. Per altre informazioni sugli endpoint privati, vedere l'articolo [Collegamento privato di Azure](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> Il collegamento privato di Azure non produce il piano di controllo di Azure (operazioni di gestione), ad esempio l'eliminazione dell'area di lavoro o la gestione delle risorse di calcolo. Ad esempio la creazione, l'aggiornamento o l'eliminazione di una destinazione di calcolo. Queste operazioni vengono eseguite sulla rete Internet pubblica come di consueto. Le operazioni del piano dati, ad esempio l'uso di Azure Machine Learning Studio, le API (incluse le pipeline pubblicate) o l'SDK usano l'endpoint privato.
>
> Se si usa Mozilla Firefox, è possibile che si verifichino problemi durante il tentativo di accedere all'endpoint privato per l'area di lavoro. Questo problema può essere correlato a DNS su HTTPS in Mozilla. È consigliabile usare Microsoft Edge di Google Chrome come soluzione alternativa.

> [!TIP]
> Azure Machine Learning istanza di calcolo può essere usata con un'area di lavoro e un endpoint privato. Questa funzionalità è attualmente disponibile in anteprima pubblica nelle aree **Stati Uniti orientali**, **Stati Uniti centro-meridionali** e **Stati Uniti occidentali 2** .

## <a name="prerequisites"></a>Prerequisiti

Se si prevede di usare un'area di lavoro con collegamento privato abilitato con una chiave gestita dal cliente, è necessario richiedere questa funzionalità usando un ticket di supporto. Per altre informazioni, vedere [gestire e aumentare le quote](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Creare un'area di lavoro che usa un endpoint privato

> [!IMPORTANT]
> Attualmente è supportata solo l'abilitazione di un endpoint privato quando si crea una nuova area di lavoro Azure Machine Learning.

Il modello in [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) può essere usato per creare un'area di lavoro con un endpoint privato.

Per informazioni sull'uso di questo modello, inclusi gli endpoint privati, vedere [usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Uso di un'area di lavoro su un endpoint privato

Poiché la comunicazione con l'area di lavoro è consentita solo dalla rete virtuale, tutti gli ambienti di sviluppo che usano l'area di lavoro devono essere membri della rete virtuale. Ad esempio, una macchina virtuale nella rete virtuale.

> [!IMPORTANT]
> Per evitare l'interferenza temporanea della connettività, Microsoft consiglia di scaricare la cache DNS nei computer che si connettono all'area di lavoro dopo aver abilitato il collegamento privato. 

Per informazioni sulle macchine virtuali di Azure, vedere la [documentazione relativa alle macchine virtuali](/azure/virtual-machines/).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla protezione dell'area di lavoro Azure Machine Learning, vedere l'articolo [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md) .

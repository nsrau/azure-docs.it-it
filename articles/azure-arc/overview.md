---
title: Panoramica di Azure Arc
description: Informazioni su Azure Arc e su come può essere usato dai clienti per abilitare la gestione e la governance delle risorse ibride con altri servizi e funzionalità di Azure.
ms.date: 08/25/2020
ms.topic: overview
ms.openlocfilehash: 46bd2089dbc2794d0fb98ceb9a5e97011e36d6ea
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89052045"
---
# <a name="azure-arc-overview"></a>Panoramica di Azure Arc

Oggi le aziende faticano a controllare e gestire ambienti che diventano sempre più complessi. Questi ambienti si estendono tra diversi data center, più cloud e reti perimetrali. Ogni ambiente e ogni cloud prevedono un proprio set di strumenti di gestione indipendenti che è necessario conoscere e usare.

Parallelamente, i nuovi modelli operativi DevOps e ITOps sono difficili da implementare, perché gli strumenti esistenti non includono il supporto per i nuovi modelli nativi del cloud.

Azure Arc semplifica la governance e la gestione grazie a una piattaforma di gestione coerente multi-cloud e locale. Azure Arc consente di gestire l'intero ambiente in un unico riquadro, proiettando le risorse esistenti in Azure Resource Manager. È ora possibile gestire macchine virtuali, cluster Kubernetes e database come se fossero in esecuzione in Azure. Indipendentemente dalla posizione di tali risorse, è possibile usare i servizi e le funzionalità di gestione di Azure già note. Con Azure Arc è possibile continuare a usare ITOps tradizionali, introducendo allo stesso tempo procedure DevOps per supportare i nuovi modelli nativi del cloud nel proprio ambiente.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagramma del piano di controllo di gestione di Azure Arc" border="false":::

Attualmente, Azure Arc consente di gestire i tipi di risorse seguenti ospitati all'esterno di Azure:

* Server, sia computer fisici che macchine virtuali che eseguono Windows o Linux.
* Cluster Kubernetes, che supportano più distribuzioni di Kubernetes.
* Servizi dati di Azure, ovvero il database SQL di Azure e PostgreSQL Hyperscale.

## <a name="what-does-azure-arc-deliver"></a>Che cosa offre Azure Arc?

Le funzionalità chiave di Azure Arc includono:

* Implementazione di funzionalità coerenti per inventario, gestione, governance e sicurezza dei server nell'ambiente.

* Configurazione di [estensioni della macchina virtuale di Azure](./servers/manage-vm-extensions.md) per usare i servizi di gestione di Azure per il monitoraggio, la protezione e l'aggiornamento die server.

* Gestione e governance dei cluster Kubernetes su larga scala. 

* Uso della configurazione basata su GitOps come strumento di gestione del codice per distribuire le applicazioni e la configurazione in uno o più cluster direttamente dal controllo del codice sorgente, ad esempio GitHub.

* Conformità e configurazione Zero Touch per i cluster Kubernetes con Criteri di Azure.

* Esecuzione dei servizi dati di Azure in qualsiasi ambiente Kubernetes, in particolare Istanza gestita di SQL di Azure e Database di Azure per PostgreSQL Hyperscale, con vantaggi quali aggiornamenti, sicurezza e monitoraggio con modalità analoghe all'esecuzione in Azure. Scalabilità elastica, applicazione degli aggiornamenti, senza tempi di inattività dell'applicazione, anche in assenza di una connessione continua ad Azure.

* Esperienza unificata che consente di visualizzare le risorse con abilitazione di Azure Arc sia che si usi il portale di Azure, l'interfaccia della riga di comando di Azure, Azure PowerShell o l'API REST di Azure.

## <a name="how-much-does-azure-arc-cost"></a>Quanto costa Azure Arc?

Ecco i dettagli relativi ai prezzi per le funzionalità attualmente disponibili con Azure Arc.

### <a name="arc-enabled-servers"></a>Server con abilitazione di Azure Arc

Nella fase di anteprima corrente i server con abilitazione di Azure Arc sono disponibili senza costi aggiuntivi.

Tutti i servizi di Azure usati nei server con abilitazione di Arc, ad esempio Centro sicurezza di Azure o Monitoraggio di Azure, verranno addebitati in base ai prezzi del relativo servizio. Per altre informazioni, vedere la [pagina relativa ai prezzi di Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes con abilitazione di Azure Arc

Nella fase di anteprima corrente Kubernetes con abilitazione di Azure Arc è disponibile senza costi aggiuntivi.

### <a name="azure-arc-enabled-data-services"></a>Servizi dati con abilitazione di Azure Arc

Nella fase di anteprima corrente i servizi dati con abilitazione di Azure Arc sono disponibili senza costi aggiuntivi.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui server con abilitazione di Arc, vedere la [panoramica](./servers/overview.md) seguente

* Per altre informazioni su Kubernetes con abilitazione di Arc, vedere la [panoramica](./kubernetes/overview.md) seguente

* Per altre informazioni sui servizi dati con abilitazione di Arc, vedere la [panoramica](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) seguente

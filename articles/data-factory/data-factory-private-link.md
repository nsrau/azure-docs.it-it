---
title: Collegamento privato di Azure per Azure Data Factory
description: Informazioni sul collegamento privato di Azure funziona in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596023"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Collegamento privato di Azure per Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un endpoint privato. Per un elenco dei servizi PaaS che supportano la funzionalità di collegamento privato, visitare la [pagina della documentazione del collegamento privato](https://docs.microsoft.com/azure/private-link/). Un endpoint privato è un indirizzo IP privato all'interno di una rete virtuale e una subnet specifiche.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>Comunicazione sicura tra la rete clienti e il servizio Azure Data Factory
Per proteggere le risorse di Azure dagli attacchi nella rete pubblica o consentire la comunicazione sicura tra loro, è possibile configurare una rete virtuale di Azure come rappresentazione logica della rete nel cloud. È anche possibile connettere una rete locale alla rete virtuale impostando la VPN IPSec (da sito a sito) o ExpressRoute (peering privato). Il Integration Runtime self-hosted può essere installato in un computer locale o in una macchina virtuale in una rete virtuale per eseguire le attività di copia tra un archivio dati cloud e un archivio dati in una rete privata o per distribuire le attività di trasformazione sulle risorse di calcolo in una rete locale o in una rete virtuale di Azure. 

Tra Data Factory e la rete virtuale cliente sono necessari diversi canali di comunicazione.


| **Dominio** | **Porta** | **Descrizione** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Piano di controllo. Obbligatorio per la creazione e il monitoraggio di Data Factory. |
| `*.{region}.datafactory.azure.net` | 443 | Richiesto dal Integration Runtime self-hosted per connettersi al servizio Data Factory. |
| `*.servicebus.windows.net` | 443 | Richiesto dal Integration Runtime self-hosted per la creazione interattiva. |
| `download.microsoft.com` | 443 | Richiesto dal Integration Runtime self-hosted per scaricare gli aggiornamenti. |


Con il supporto del collegamento privato di Azure per Azure Data Factory, è possibile creare un endpoint privato (PE) nella rete virtuale e abilitare la connessione privata a Azure Data Factory specifici. Le comunicazioni al servizio Azure Data Factory passano attraverso il collegamento privato di Azure che fornisce la connettività privata protetta. Non è quindi necessario configurare il dominio e la porta precedenti nella rete virtuale o nel firewall aziendale che fornisce un modo più sicuro per proteggere le risorse.  

![Architettura del collegamento privato Azure Data Factory](./media/data-factory-private-link/private-link-architecture.png)

Ecco i vantaggi per l'abilitazione del servizio di collegamento privato per ognuno dei canali di comunicazione descritti in precedenza:
- Supportato È possibile eseguire la creazione e il monitoraggio di Azure Data Factory nella rete virtuale, anche se si bloccano tutte le comunicazioni in uscita.
- Supportato Le comunicazioni del comando tra Integration Runtime indipendenti e il servizio Azure Data Factory possono essere eseguite in modo sicuro in un ambiente di rete privata. Il traffico tra Integration Runtime indipendenti e il servizio Azure Data Factory passa attraverso il collegamento privato. 
- (Attualmente non supportata) La creazione interattiva tramite il Integration Runtime self-hosted passa attraverso un collegamento privato, ad esempio test connessione, browse Folder List e Table list, Get schema e Preview data.
- (Attualmente non supportata) La nuova versione di Integration Runtime self-hosted può essere scaricata automaticamente dall'area download se si Abilita l'aggiornamento automatico.

> [!NOTE]
> Per le funzionalità che non sono attualmente supportate, è comunque necessario configurare il dominio e la porta precedenti nella rete virtuale o nel firewall aziendale. 

> [!WARNING]
> Quando si crea un servizio collegato, assicurarsi che le credenziali vengano archiviate in Azure Key Vault. In caso contrario, non funziona quando si Abilita il servizio di collegamento privato in Azure Data Factory.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Come configurare un collegamento privato per Azure Data Factory
È possibile creare endpoint privati usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure:

[Portale](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


È anche possibile passare all'Azure Data Factory in portale di Azure e creare un endpoint privato (PE):

![Creare un endpoint privato](./media/data-factory-private-link/create-private-endpoint.png)


Se si vuole bloccare l'accesso pubblico a questa Azure Data Factory e consentire l'accesso solo tramite collegamento privato, è possibile disabilitare l'accesso alla rete di Azure Data Factory in portale di Azure:

![Creare un endpoint privato](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> La disabilitazione dell'accesso alla rete pubblica è applicabile solo a Integration Runtime indipendenti, non Azure Integration Runtime e Integration Runtime SSIS.

> [!NOTE]
> Gli utenti possono comunque accedere a Azure Data Factory portale attraverso la rete pubblica dopo aver disabilitato l'accesso alla rete pubblica.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una data factory usando l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introduzione al servizio Azure Data Factory](introduction.md)

- [Creazione di oggetti visivi in Azure Data Factory](author-visually.md)


---
title: Collegamento privato di Azure per Azure Data Factory
description: Informazioni sul funzionamento del collegamento privato di Azure in Azure Data Factory.
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
ms.openlocfilehash: 48ab83db3dcbcf5c99b640ccab205ed1f0ee7ca1
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604377"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Collegamento privato di Azure per Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Con il collegamento privato di Azure è possibile connettersi a varie distribuzioni di piattaforma distribuita come servizio (PaaS) in Azure tramite un endpoint privato. Un endpoint privato è un indirizzo IP privato all'interno di una rete virtuale e una subnet specifiche. Per un elenco delle distribuzioni PaaS che supportano la funzionalità di collegamento privato, vedere la [documentazione del collegamento privato](https://docs.microsoft.com/azure/private-link/). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Comunicazione sicura tra le reti dei clienti e Azure Data Factory 
È possibile configurare una rete virtuale di Azure come rappresentazione logica della rete nel cloud. Questa operazione offre i vantaggi seguenti:
* Proteggi le tue risorse di Azure da attacchi nelle reti pubbliche.
* Si consente alle reti e Data Factory di comunicare in modo sicuro tra loro. 

È anche possibile connettere una rete locale alla rete virtuale configurando una connessione VPN Internet Protocol Security (IPsec) (da sito a sito) o una connessione di Azure ExpressRoute (peering privato). 

È anche possibile installare un runtime di integrazione self-hosted in un computer locale o in una macchina virtuale nella rete virtuale. In questo modo è possibile:
* Eseguire attività di copia tra un archivio dati cloud e un archivio dati in una rete privata.
* Inviare le attività di trasformazione sulle risorse di calcolo in una rete locale o in una rete virtuale di Azure. 

Tra Azure Data Factory e la rete virtuale del cliente sono necessari diversi canali di comunicazione, come illustrato nella tabella seguente:

| Dominio | Porta | Descrizione |
| ---------- | -------- | --------------- |
| `pe-adf.azure.com` | 443 | Un piano di controllo, necessario per la creazione e il monitoraggio di Data Factory. |
| `*.{region}.datafactory.azure.net` | 443 | Richiesta dal runtime di integrazione self-hosted per connettersi al servizio Data Factory. |
| `*.servicebus.windows.net` | 443 | Richiesto dal runtime di integrazione self-hosted per la creazione interattiva. |
| `download.microsoft.com` | 443 | Richiesta dal runtime di integrazione self-hosted per il download degli aggiornamenti. |

Con il supporto del collegamento privato per Azure Data Factory, è possibile:
* Creare un endpoint privato nella rete virtuale.
* Abilitare la connessione privata a un'istanza di data factory specifica. 

Le comunicazioni al servizio Azure Data Factory passano attraverso un collegamento privato e forniscono una connettività privata sicura. Non è necessario configurare il dominio e la porta precedenti in una rete virtuale o nel firewall aziendale per fornire un modo più sicuro per proteggere le risorse.  

![Diagramma del collegamento privato per l'architettura Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

L'abilitazione del servizio di collegamento privato per ognuno dei canali di comunicazione precedenti offre le funzionalità seguenti:
- **Supportato**:
   - È possibile creare e monitorare i data factory nella rete virtuale, anche se si bloccano tutte le comunicazioni in uscita.
   - Le comunicazioni del comando tra il runtime di integrazione self-hosted e il servizio Azure Data Factory possono essere eseguite in modo sicuro in un ambiente di rete privata. Il traffico tra il runtime di integrazione self-hosted e il servizio di Azure Data Factory passa attraverso il collegamento privato. 
- **Attualmente non supportata**:
   - La creazione interattiva che usa un runtime di integrazione self-hosted, ad esempio test connection, browse Folder List e Table list, Get schema e Preview data, passa attraverso un collegamento privato.
   - La nuova versione del runtime di integrazione self-hosted può essere scaricata automaticamente dall'area download Microsoft se si Abilita l'aggiornamento automatico.

   > [!NOTE]
   > Per le funzionalità che non sono attualmente supportate, è comunque necessario configurare il dominio e la porta indicati in precedenza nella rete virtuale o nel firewall aziendale. 

> [!WARNING]
> Quando si crea un servizio collegato, assicurarsi che le credenziali siano archiviate in Azure Key Vault. In caso contrario, le credenziali non funzioneranno quando si Abilita il collegamento privato in Azure Data Factory.

## <a name="set-up-private-link-for-azure-data-factory"></a>Configurare il collegamento privato per Azure Data Factory
È possibile creare endpoint privati usando [il portale di Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal), PowerShell o l'interfaccia della riga di comando di Azure.

È anche possibile passare al data factory di Azure nella portale di Azure e creare un endpoint privato, come illustrato di seguito:

![Screenshot del riquadro "connessioni a endpoint privati" per la creazione di un endpoint privato.](./media/data-factory-private-link/create-private-endpoint.png)


Se si vuole bloccare l'accesso pubblico al data factory di Azure e consentire l'accesso solo tramite collegamento privato, disabilitare l'accesso alla rete per Azure Data Factory nel portale di Azure, come illustrato di seguito:

![Screenshot del riquadro "accesso alla rete" per la creazione di un endpoint privato.](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> La disabilitazione dell'accesso alla rete pubblica è applicabile solo al runtime di integrazione self-hosted, non a Azure Integration Runtime e SQL Server Integration Services (SSIS) Integration Runtime.

> [!NOTE]
> È comunque possibile accedere al portale di Azure Data Factory tramite una rete pubblica dopo avere disabilitato l'accesso alla rete pubblica.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una data factory usando l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md)
- [Introduzione al servizio Azure Data Factory](introduction.md)
- [Creazione di oggetti visivi in Azure Data Factory](author-visually.md)


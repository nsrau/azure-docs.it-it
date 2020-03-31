---
title: Uso di endpoint privati per la configurazione delle app di AzureUsing private endpoints for Azure App Configuration
description: Proteggere l'archivio di configurazione delle app usando gli endpoint privatiSecure your App Configuration store using private endpoints
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366769"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Uso di endpoint privati per la configurazione delle app di AzureUsing private endpoints for Azure App Configuration

È possibile usare endpoint privati per Configurazione app di Azure per consentire [ai](../private-link/private-endpoint-overview.md) client in una rete virtuale (VNet) di accedere in modo sicuro ai dati tramite un [collegamento privato.](../private-link/private-link-overview.md) L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della rete virtuale per l'archivio di configurazione delle app. Il traffico di rete tra i client nella rete virtuale e l'archivio di configurazione delle app attraversa la rete virtuale utilizzando un collegamento privato nella rete backbone Microsoft, eliminando l'esposizione a Internet pubblico.

L'uso di endpoint privati per l'archivio di configurazione delle app consente di:Using private endpoints for your App Configuration store enables you to:
- Proteggere i dettagli di configurazione dell'applicazione configurando il firewall per bloccare tutte le connessioni a Configurazione app nell'endpoint pubblico.
- Aumentare la sicurezza per la rete virtuale (VNet) assicurando che i dati non fuoriescano dalla rete virtuale.
- Connettersi in modo sicuro all'archivio di configurazione app da reti locali che si connettono alla rete virtuale tramite [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoutes](../expressroute/expressroute-locations.md) con peering privato.

> [!NOTE]
> Configurazione app di Azure offre l'uso di endpoint privati come anteprima pubblica. Le offerte di anteprima pubblica consentono ai clienti di sperimentare le nuove funzionalità prima del rilascio della versione ufficiale.  I servizi e le funzionalità di anteprima pubblica non sono destinati all'uso in produzione.

## <a name="conceptual-overview"></a>Informazioni generali

Un endpoint privato è un'interfaccia di rete speciale per un servizio di Azure nella [rete virtuale](../virtual-network/virtual-networks-overview.md) (VNet). Quando si crea un endpoint privato per l'archivio di configurazione dell'app, fornisce connettività sicura tra i client nella rete virtuale e nell'archivio di configurazione. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della rete virtuale. La connessione tra l'endpoint privato e l'archivio di configurazione utilizza un collegamento privato sicuro.

Le applicazioni nella rete virtuale possono connettersi all'archivio di configurazione tramite l'endpoint privato utilizzando le stesse stringhe di connessione e meccanismi di **autorizzazione che utilizzerebbero in caso contrario.** Gli endpoint privati possono essere utilizzati con tutti i protocolli supportati dall'archivio di configurazione app.

Sebbene la configurazione delle app non supporti gli endpoint del servizio, è possibile creare endpoint privati nelle subnet che usano [gli endpoint del servizio.](../virtual-network/virtual-network-service-endpoints-overview.md) I client in una subnet possono connettersi in modo sicuro a un archivio di configurazione app usando l'endpoint privato mentre usano gli endpoint del servizio per accedere ad altri utenti.  

Quando si crea un endpoint privato per un servizio nella rete virtuale, viene inviata una richiesta di consenso per l'approvazione al proprietario dell'account del servizio. Se l'utente che richiede la creazione dell'endpoint privato è anche proprietario dell'account, questa richiesta di consenso viene approvata automaticamente.

I proprietari degli account di servizio possono `Private Endpoints` gestire le richieste di consenso e gli endpoint privati tramite la scheda dell'archivio di configurazione nel portale di [Azure.](https://portal.azure.com)

### <a name="private-endpoints-for-app-configuration"></a>Endpoint privati per la configurazione delle appPrivate endpoints for App Configuration 

Quando si crea un endpoint privato, è necessario specificare l'archivio di configurazione app a cui si connette. Se all'interno di un account sono presenti più istanze di Configurazione app, è necessario un endpoint privato separato per ogni archivio.

### <a name="connecting-to-private-endpoints"></a>Connessione agli endpoint privatiConnecting to private endpoints

Azure si basa sulla risoluzione DNS per instradare le connessioni dalla rete virtuale all'archivio di configurazione tramite un collegamento privato. È possibile trovare rapidamente le stringhe di connessione nel portale di Azure selezionando l'archivio di configurazione delle app, quindi selezionando **Impostazioni** > **chiavi di accesso**.  

> [!IMPORTANT]
> Usare la stessa stringa di connessione per connettersi all'archivio di configurazione app usando gli endpoint privati usato per un endpoint pubblico. Non connettersi all'account di `privatelink` archiviazione usando il relativo URL del sottodominio.

## <a name="dns-changes-for-private-endpoints"></a>Modifiche DNS per gli endpoint privati

Quando si crea un endpoint privato, il record di risorse CNAME DNS per l'archivio di configurazione viene aggiornato a un alias in un sottodominio con il prefisso `privatelink`. Azure crea anche una zona `privatelink` DNS [privata](../dns/private-dns-overview.md) corrispondente al sottodominio, con i record di risorse A DNS per gli endpoint privati.

Quando si risolve l'URL dell'endpoint dall'esterno della rete virtuale, questo viene risolto nell'endpoint pubblico dell'archivio. Quando viene risolto dall'interno della rete virtuale che ospita l'endpoint privato, l'URL dell'endpoint viene risolto nell'endpoint privato.

È possibile controllare l'accesso per i client esterni alla rete virtuale tramite l'endpoint pubblico usando il servizio Firewall di Azure.You can control access for clients outside the VNet through the public endpoint using the Azure Firewall service.

Questo approccio consente l'accesso all'archivio **utilizzando la stessa stringa** di connessione per i client nella rete virtuale che ospitano gli endpoint privati e i client esterni alla rete virtuale.

Se si utilizza un server DNS personalizzato nella rete, i client devono essere in grado di risolvere il nome di dominio completo (FQDN) per l'endpoint del servizio nell'indirizzo IP dell'endpoint privato. Configurare il server DNS per delegare il sottodominio del collegamento privato alla `AppConfigInstanceA.privatelink.azconfig.io` zona DNS privata per la rete virtuale oppure configurare i record A per con l'indirizzo IP dell'endpoint privato.

> [!TIP]
> Quando si utilizza un server DNS personalizzato o locale, è necessario configurare `privatelink` il server DNS per risolvere il nome dell'archivio nel sottodominio nell'indirizzo IP dell'endpoint privato. A tale scopo, delegare il `privatelink` sottodominio alla zona DNS privata della rete virtuale oppure configurare la zona DNS sul server DNS e aggiungere i record A DNS.

## <a name="pricing"></a>Prezzi

L'abilitazione degli endpoint privati richiede un archivio di configurazione app di [livello Standard.Enabling](https://azure.microsoft.com/pricing/details/app-configuration/) private endpoints requires a Standard tier App Configuration store.  Per altre informazioni sui dettagli sui prezzi dei collegamenti privati, vedere Prezzi di [Azure Private Link.](https://azure.microsoft.com/pricing/details/private-link)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla creazione di un endpoint privato per l'archivio di configurazione delle app, vedere gli articoli seguenti:Learn more about creating a private endpoint for your App Configuration store, refer to the following articles:

- [Creare un endpoint privato usando il Centro collegamenti privati nel portale di AzureCreate a private endpoint using the Private Link Center in the Azure portal](../private-link/create-private-endpoint-portal.md)
- [Creare un endpoint privato con l'interfaccia della riga di comando di Azure](../private-link/create-private-endpoint-cli.md)
- [Creare un endpoint privato usando Azure PowerShellCreate a private endpoint using Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Informazioni su come configurare il server DNS con endpoint privati:

- [Risoluzione dei nomi per le risorse in reti virtuali di Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configurazione DNS per gli endpoint privati](/azure/private-link/private-endpoint-overview#dns-configuration)

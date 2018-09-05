---
title: Applicazione connessa a Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo articolo illustra come connettere un'applicazione a Istanza gestita di database SQL di Azure.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 82e8836892b033ccbb3c3ad9806257348afe3702
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818403"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Connettere un'applicazione a Istanza gestita di database SQL di Azure

Ora sono disponibili diverse possibilità quando si decide come e dove ospitare un'applicazione. 
 
È possibile scegliere di ospitare un'applicazione nel cloud tramite il servizio app di Azure o alcune delle opzioni integrate nella rete virtuale di Azure come l'ambiente del servizio app di Azure, la macchina virtuale, il set di scalabilità di macchine virtuali. È anche possibile scegliere un approccio cloud ibrido e mantenere le applicazioni in locale. 
 
Indipendentemente dalla scelta effettuata, è possibile collegarla a un'istanza gestita (anteprima).  

![disponibilità elevata](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Connettere un'applicazione all'interno della stessa rete virtuale 

Questo è lo scenario più semplice. Le macchine virtuali all'interno della rete virtuale possono connettersi tra loro direttamente anche se si trovano in subnet diverse. Ciò significa che per la connessione dell'applicazione all'interno di un ambiente di applicazione o macchina virtuale di Azure è sufficiente impostare la stringa di connessione in modo appropriato.  
 
Nel caso in cui non sia possibile stabilire la connessione, controllare se sulla subnet dell'applicazione è impostato un gruppo di sicurezza di rete. In questo caso, è necessario aprire la connessione in uscita attraverso la porta SQL 1433 oltre all'intervallo di porte 11000 12000 per il reindirizzamento. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Connettere un'applicazione all'interno di una rete virtuale diversa 

Questo scenario è un po' più complesso perché l'istanza gestita ha un indirizzo IP privato nella propria rete virtuale. Per connettersi, un'applicazione richiede l'accesso alla rete virtuale in cui viene distribuita l'istanza gestita. Quindi è necessario innanzitutto stabilire una connessione tra l'applicazione e la rete virtuale dell'istanza gestita. Non è necessario che le reti virtuali si trovino nella stessa sottoscrizione perché questo scenario funzioni. 
 
Sono disponibili due opzioni per connettere reti virtuali: 
- [Peering di rete virtuale di Azure](../virtual-network/virtual-network-peering-overview.md) 
- Gateway da rete virtuale a rete virtuale ([portale di Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Interfaccia della riga di comando di Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
L'opzione di peering è quella preferibile perché il peering usa il backbone Microsoft e quindi, dal punto di vista della connettività non vi è alcuna differenza percepibile nella latenza tra le macchine virtuali nelle reti virtuali con peering e nella stessa rete virtuale. Il peering di reti virtuali è limitato alle reti nella stessa area.  
 
> [!IMPORTANT]
> Lo scenario di peering di reti virtuali per Istanza gestita è limitato alle reti nella stessa area a causa dei [vincoli del peering di reti virtuali globale](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). 

## <a name="connect-an-on-premises-application"></a>Connettersi a un'applicazione locale 

Istanza gestita è accessibile solo tramite un indirizzo IP privato. Per accedere all'istanza in locale, è necessario stabilire una connessione da sito a sito tra l'applicazione e la rete virtuale dell'istanza gestita. 
 
Sono disponibili due opzioni per connettersi in locale alla rete virtuale di Azure: 
- Connessione VPN da sito a sito ([portale di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Interfaccia della riga di comando di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- Connessione [ExpressRoute](../expressroute/expressroute-introduction.md)  
 
Se è stata stabilita una connessione dall'ambiente locale ad Azure e non è possibile stabilire una connessione all'istanza gestita, controllare se il firewall ha la connessione in uscita aperta sulla porta SQL 1433 e sull'intervallo di porte 11000 12000 per il reindirizzamento. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Connettere un'applicazione ospitata nel servizio app di Azure 

Istanza gestita è accessibile solo tramite un indirizzo IP privato, quindi, per accedervi dal servizio app di Azure, è necessario prima stabilire una connessione tra l'applicazione e la rete virtuale dell'istanza gestita. Vedere [Integrazione di un'app in una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md).  
 
Per risolvere il problema, vedere la [risoluzione dei problemi delle reti virtuali](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Se è possibile stabilire una connessione, provare a [sincronizzare la configurazione di rete](sql-database-managed-instance-sync-network-configuration.md). 
 
Un caso speciale di connessione del servizio app di Azure a Istanza gestita è quando il servizio app di Azure è stato integrato in una rete con il peering della rete virtuale dell'istanza gestita. Tale caso richiede la configurazione seguente: 

- La rete virtuale dell'istanza gestita NON deve avere gateway  
- La rete virtuale dell'istanza gestita deve avere l'opzione Uso gateway remoto impostata 
- La rete virtuale con peering deve avere l'opzione Consenti transito gateway impostata 
 
Questo scenario è illustrato nel diagramma seguente:

![peering app integrate](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Connettere un'applicazione nella finestra di sviluppo 

Le istanze gestite sono accessibili solo tramite un indirizzo IP privato, quindi, per accedervi dalla finestra di sviluppo, è necessario prima stabilire una connessione tra la finestra di sviluppo e la rete virtuale dell'istanza gestita.  
 
Configurare una connessione da punto a sito a una rete virtuale in base agli articoli sull'autenticazione del certificato di Azure nativo ([portale di Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [Interfaccia della riga di comando di Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) che descrivono in dettaglio come eseguire questa operazione. 

## <a name="required-versions-of-drivers-and-tools"></a>Versioni richieste di strumenti e driver

Se si desidera connettersi all'istanza gestita, è consigliabili usare le versioni minime di strumenti e driver seguenti:

| Driver/strumento | Version |
| --- | --- |
|.NET Framework | 4.6.1 (o .NET Core) | 
|Driver ODBC    | v17 |
|PHP driver | 5.2.0 |
|Driver JDBC    | 6.4.0 |
|Driver Node.js | 2.1.1 |
|Driver OLEDB   | 18.0.2.0 |
|SSMS   | 17.8.1 o [superiore](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) |

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni in proposito, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).

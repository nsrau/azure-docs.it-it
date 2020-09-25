---
title: Connettere l'applicazione a SQL Istanza gestita
titleSuffix: Azure SQL Managed Instance
description: Questo articolo illustra come connettere l'applicazione al Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 9d58a8c1dc79c10ed42fd1675115eb14f2ad4d3e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283732"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Connettere l'applicazione a un'istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ora sono disponibili diverse possibilità quando si decide come e dove ospitare un'applicazione.

È possibile scegliere di ospitare l'applicazione nel cloud usando app Azure servizio o alcune opzioni integrate della rete virtuale di Azure, ad esempio ambiente del servizio app di Azure, macchine virtuali di Azure e set di scalabilità di macchine virtuali. È anche possibile scegliere un approccio cloud ibrido e mantenere le applicazioni in locale.

Indipendentemente dalla scelta effettuata, è possibile connetterla al Istanza gestita SQL di Azure. 

![Disponibilità elevata](./media/connect-application-instance/application-deployment-topologies.png)

Questo articolo descrive come connettere un'applicazione a Istanza gestita di SQL di Azure in diversi scenari di applicazione. 

## <a name="connect-inside-the-same-vnet"></a>Connetti all'interno della stessa VNet

La connessione di un'applicazione all'interno della stessa rete virtuale di SQL Istanza gestita rappresenta lo scenario più semplice. Le macchine virtuali all'interno della rete virtuale possono connettersi tra loro direttamente anche se si trovano all'interno di subnet diverse. Ciò significa che è sufficiente connettere un'applicazione all'interno ambiente del servizio app o una macchina virtuale per impostare la stringa di connessione in modo appropriato.  

## <a name="connect-inside-a-different-vnet"></a>Connetti all'interno di una VNet diversa

La connessione di un'applicazione quando si trova in una rete virtuale diversa da SQL Istanza gestita è un po' più complessa perché SQL Istanza gestita dispone di indirizzi IP privati nella propria rete virtuale. Per connettersi, un'applicazione deve accedere alla rete virtuale in cui viene distribuito SQL Istanza gestita. È quindi necessario creare una connessione tra l'applicazione e la rete virtuale SQL Istanza gestita. Per il corretto funzionamento di questo scenario, non è necessario che le reti virtuali si trovino nella stessa sottoscrizione.

Sono disponibili due opzioni per la connessione di reti virtuali:

- [Peering di Azure VNet](../../virtual-network/virtual-network-peering-overview.md)
- Gateway da rete virtuale a rete virtuale ([portale di Azure](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Interfaccia della riga di comando di Azure](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Il peering è preferibile perché usa la rete backbone Microsoft. Pertanto, dal punto di vista della connettività, non esiste alcuna differenza evidente nella latenza tra le macchine virtuali in una rete virtuale con peering e nella stessa rete virtuale. Il peering di rete virtuale è limitato alle reti nella stessa area.  

> [!IMPORTANT]
> Lo scenario di peering di rete virtuale per SQL Istanza gestita è limitato alle reti nella stessa area a causa dei [vincoli del peering di rete virtuale globale](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Per altri dettagli, vedere anche la sezione pertinente dell'articolo [domande frequenti sulle reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . 

## <a name="connect-from-on-premises"></a>Connetti da locale 

È anche possibile connettere l'applicazione locale a SQL Istanza gestita. È possibile accedere a SQL Istanza gestita solo tramite un indirizzo IP privato. Per accedervi dall'ambiente locale, è necessario effettuare una connessione da sito a sito tra l'applicazione e la rete virtuale SQL Istanza gestita.

Per la connessione locale a una rete virtuale di Azure sono disponibili due opzioni:

- Connessione VPN da sito a sito ([portale di Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), interfaccia della riga di comando di [Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Connessione [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)  

Se è stata stabilita una connessione da locale ad Azure e non è possibile stabilire una connessione a SQL Istanza gestita, controllare se il firewall ha una connessione in uscita aperta sulla porta SQL 1433 e l'intervallo di porte 11000-11999 per il reindirizzamento.

## <a name="connect-the-developer-box"></a>Connetti la casella per sviluppatori

È anche possibile connettere la finestra per sviluppatori a SQL Istanza gestita. È possibile accedere a SQL Istanza gestita solo tramite un indirizzo IP privato, quindi, per accedervi dal box per sviluppatori, è prima necessario creare una connessione tra la propria casella di sviluppo e la rete virtuale SQL Istanza gestita. A tale scopo, configurare una connessione da punto a sito a una rete virtuale usando l'autenticazione del certificato nativa di Azure. Per altre informazioni, vedere  [configurare una connessione da punto a sito per la connessione a istanza gestita SQL di Azure da un computer locale](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>Connettersi con il peering VNet

Un altro scenario implementato dai clienti è la posizione in cui un gateway VPN viene installato in una rete virtuale separata e una sottoscrizione da quella che ospita SQL Istanza gestita. Sulle due reti virtuali viene quindi eseguito il peering. Il seguente esempio di diagramma di architettura mostra come questo può essere implementato.

![Peering di rete virtuale](./media/connect-application-instance/vnet-peering.png)

Dopo aver configurato l'infrastruttura di base, è necessario modificare alcune impostazioni in modo che il gateway VPN possa visualizzare gli indirizzi IP nella rete virtuale che ospita SQL Istanza gestita. Per fare ciò, apportare le seguenti modifiche molto specifiche in **Impostazioni di peering**.

1. Nella rete virtuale che ospita il gateway VPN passare a **peering**, passare alla connessione di rete virtuale con peering per SQL istanza gestita e quindi fare clic su **Consenti transito gateway**.
2. Nella rete virtuale che ospita Istanza gestita SQL passare a **peering**, passare alla connessione di rete virtuale con peering per il gateway VPN e quindi fare clic su **Usa gateway remoti**.

## <a name="connect-azure-app-service"></a>Connetti app Azure servizio 

È anche possibile connettere un'applicazione ospitata da app Azure servizio. È possibile accedere a SQL Istanza gestita solo tramite un indirizzo IP privato, quindi, per accedervi dal servizio app Azure, è prima di tutto necessario creare una connessione tra l'applicazione e la rete virtuale SQL Istanza gestita. Vedere [integrare l'app con una rete virtuale di Azure](../../app-service/web-sites-integrate-with-vnet.md).  

Per la risoluzione dei problemi, vedere [risoluzione dei problemi relativi alle reti virtuali e alle applicazioni](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Se non è possibile stabilire una connessione, provare a [sincronizzare la configurazione di rete](azure-app-sync-network-configuration.md).

Un caso speciale di connessione di app Azure Service a SQL Istanza gestita è quando si integra app Azure servizio in una rete con peering a una rete virtuale SQL Istanza gestita. Tale caso richiede la configurazione seguente:

- La rete virtuale SQL Istanza gestita non deve avere un gateway  
- È necessario impostare l'opzione per la rete virtuale SQL Istanza gestita `Use remote gateways`
- La rete virtuale con peering deve avere il `Allow gateway transit` set di opzioni

Questo scenario è illustrato nel diagramma seguente:

![peering app integrate](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>La funzionalità di integrazione della rete virtuale non integra un'app con una rete virtuale con un gateway ExpressRoute. Anche se il gateway ExpressRoute è configurato in modalità di coesistenza, l'integrazione della rete virtuale non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, è possibile usare ambiente del servizio app, che viene eseguito nella rete virtuale.

## <a name="troubleshooting-connectivity-issues"></a>Risoluzione dei problemi di connettività

Per la risoluzione dei problemi di connettività, verificare quanto segue:

- Se non si è in grado di connettersi a SQL Istanza gestita da una macchina virtuale di Azure all'interno della stessa rete virtuale, ma di una subnet diversa, verificare se è stato impostato un gruppo di sicurezza di rete nella subnet VM che potrebbe bloccare l'accesso. Aprire inoltre la connessione in uscita sulla porta SQL 1433 e le porte nell'intervallo 11000-11999, perché sono necessarie per la connessione tramite reindirizzamento all'interno del limite di Azure.
- Assicurarsi che la propagazione BGP sia impostata su **abilitato** per la tabella di route associata alla rete virtuale.
- Se si utilizza la VPN P2S, controllare la configurazione nel portale Azure per vedere se si vedono i numeri **Ingresso/Uscita**. I numeri diversi da zero indicano che Azure indirizza il traffico da/per un’istanza locale.

   ![Numeri in ingresso/in uscita](./media/connect-application-instance/ingress-egress-numbers.png)

- Verificare che il computer client (che esegue il client VPN) includa voci di route per tutte le reti virtuali a cui è necessario accedere. Le route vengono archiviate in `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Come illustrato in questa immagine, sono presenti due voci per ogni rete virtuale in questione e una terza voce per l'endpoint VPN configurato nel portale.

   Un altro modo per verificare le route è tramite il comando seguente. L'output mostra le route verso subnet diverse:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Se si usa il peering di rete virtuale, assicurarsi di aver seguito le istruzioni per l'impostazione [Consenti transito gateway e Usa gateway remoti](#connect-from-on-premises).

- Se si usa il peering di rete virtuale per connettere un'applicazione ospitata da un servizio app Azure e la rete virtuale SQL Istanza gestita ha un intervallo di indirizzi IP pubblici, assicurarsi che le impostazioni dell'applicazione ospitata consentano il routing del traffico in uscita alle reti IP pubbliche. Seguire le istruzioni nell' [integrazione della rete virtuale a livello](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)di area.

## <a name="required-versions-of-drivers-and-tools"></a>Versioni richieste di strumenti e driver

Le seguenti versioni minime degli strumenti e dei driver sono consigliate Se si desidera connettersi a SQL Istanza gestita:

| Driver/strumento | Versione |
| --- | --- |
|.NET Framework | 4.6.1 (o .NET Core) |
|Driver ODBC| v17 |
|PHP driver| 5.2.0 |
|Driver JDBC| 6.4.0 |
|Driver Node.js| 2.1.1 |
|Driver OLEDB| 18.0.2.0 |
|SSMS| 18.0 o [successiva](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) o successiva |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su SQL Istanza gestita, vedere [che cos'è sql istanza gestita?](sql-managed-instance-paas-overview.md).
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [creare un'istanza gestita](instance-create-quickstart.md).

---
title: Applicazione connessa a Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo articolo illustra come connettere un'applicazione a Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 11/09/2018
ms.openlocfilehash: ed9fbdd3e999cfd262ecbcf05a843c19cc969ed1
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360433"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Connettere un'applicazione a Istanza gestita di database SQL di Azure

Ora sono disponibili diverse possibilità quando si decide come e dove ospitare un'applicazione.

È possibile scegliere di ospitare un'applicazione nel cloud tramite il servizio app di Azure o alcune delle opzioni integrate nella rete virtuale di Azure come l'ambiente del servizio app di Azure, la macchina virtuale, il set di scalabilità di macchine virtuali. È anche possibile scegliere un approccio cloud ibrido e mantenere le applicazioni in locale.

Indipendentemente dalla scelta effettuata, è possibile collegarla a un'istanza gestita.  

![disponibilità elevata](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Connettere un'applicazione all'interno della stessa rete virtuale

Questo è lo scenario più semplice. Le macchine virtuali all'interno della rete virtuale possono connettersi tra loro direttamente anche se si trovano in subnet diverse. Ciò significa che per la connessione dell'applicazione all'interno di un ambiente di applicazione o macchina virtuale di Azure è sufficiente impostare la stringa di connessione in modo appropriato.  

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

## <a name="connect-an-application-on-the-developers-box"></a>Connettere un'applicazione nella finestra di sviluppo

Le istanze gestite sono accessibili solo tramite un indirizzo IP privato, quindi, per accedervi dalla finestra di sviluppo, è necessario prima stabilire una connessione tra la finestra di sviluppo e la rete virtuale dell'istanza gestita. Per eseguire questa operazione, configurare una connessione da punto a sito a una rete virtuale usando l'autenticazione del certificato di Azure nativo. Per maggiori informazioni, vedere [Configurare una connessione da punto a sito per una connessione a un'Istanza gestita di database SQL di Azure da computer locale](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Connettersi in locale con il peering reti virtuali

Un altro scenario implementato dai clienti è quello in cui il gateway VPN è installato in una rete virtuale e una sottoscrizione separata da quelle che ospitano Istanza gestita. Sulle due reti virtuali viene quindi eseguito il peering. Il seguente esempio di diagramma di architettura mostra come questo può essere implementato.

![Peering reti virtuali](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Una volta configurata l'infrastruttura di base, è necessario modificare alcune impostazioni in modo che il gateway VPN possa vedere gli indirizzi IP nella rete virtuale che ospita l'istanza gestita. Per fare ciò, apportare le seguenti modifiche molto specifiche in **Impostazioni di peering**.

1. Nella rete che ospita il gateway VPN, accedere a **Peering**, quindi alla connessione VNet con peering di Istanza gestita, quindi fare clic su **Consenti transito gateway**.
2. Nella rete che ospita Istanza gestita, accedere a **Peering**, quindi alla connessione VNet con peering del gateway VPN gestita, quindi fare clic su **Usa gateway remoti**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Connettere un'applicazione ospitata nel servizio app di Azure

Istanza gestita è accessibile solo tramite un indirizzo IP privato, quindi, per accedervi dal servizio app di Azure, è necessario prima stabilire una connessione tra l'applicazione e la rete virtuale dell'istanza gestita. Vedere [Integrazione di un'app in una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md).  

Per risolvere il problema, vedere la [risoluzione dei problemi delle reti virtuali](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Se è possibile stabilire una connessione, provare a [sincronizzare la configurazione di rete](sql-database-managed-instance-sync-network-configuration.md).

Un caso speciale di connessione del servizio app di Azure a Istanza gestita è quando il servizio app di Azure è stato integrato in una rete con il peering della rete virtuale dell'istanza gestita. Tale caso richiede la configurazione seguente:

- La rete virtuale dell'istanza gestita NON deve avere gateway  
- La rete virtuale dell'istanza gestita deve avere l'opzione Uso gateway remoto impostata
- La rete virtuale con peering deve avere l'opzione Consenti transito gateway impostata

Questo scenario è illustrato nel diagramma seguente:

![peering app integrate](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>La funzionalità Integrazione rete virtuale non consente l'integrazione di un'app con una rete virtuale dotata di un gateway ExpressRoute. Anche se il gateway ExpressRoute è configurato in modalità di coesistenza, l'integrazione rete virtuale non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, è possibile usare un ambiente del servizio app eseguito nella rete virtuale.

## <a name="troubleshooting-connectivity-issues"></a>Risoluzione dei problemi di connettività

Per la risoluzione dei problemi di connettività, verificare quanto segue:

- Se non si riesce a eseguire la connessione a Istanza gestita da una macchina virtuale Azure all'interno della stessa VNet ma con subnet diversa, verificare se si dispone di un gruppo di sicurezza di rete impostato sulla subnet VM che potrebbe bloccare l'accesso. Inoltre, è necessario aprire la connessione in uscita sulla porta SQL 1433 e sulle porte nell'intervallo 11000-12000, poiché sono necessarie per la connessione tramite reindirizzamento all'interno del confine di Azure.
- Assicurarsi che la propagazione BGP sia impostata su **Abilitata** per la tabella di route associata alla rete virtuale.
- Se si utilizza la VPN P2S, controllare la configurazione nel portale Azure per vedere se si vedono i numeri **Ingresso/Uscita**. I numeri diversi da zero indicano che Azure indirizza il traffico da/per un’istanza locale.

   ![Numeri in ingresso/in uscita](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Verificare che il computer client (che sta eseguendo il client VPN) disponga di voci di route per tutte le reti virtuali a cui è necessario accedere. Le route vengono archiviate in `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Come mostrato in questa immagine, ci sono due voci per ogni VNet coinvolta e una terza voce per l'endpoint VPN configurato nel portale.

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

- Se si usa il peering della Vnet, assicurarsi di aver seguito le istruzioni per l'impostazione di [Consenti transito Gateway e Usa gateway remoti](#connect-from-on-premises-with-vnet-peering).

## <a name="required-versions-of-drivers-and-tools"></a>Versioni richieste di strumenti e driver

Se si desidera connettersi all'istanza gestita, è consigliabili usare le versioni minime di strumenti e driver seguenti:

| Driver/strumento | Versione |
| --- | --- |
|.NET Framework | 4.6.1 (o .NET Core) |
|Driver ODBC| v17 |
|PHP driver| 5.2.0 |
|Driver JDBC| 6.4.0 |
|Driver Node.js| 2.1.1 |
|Driver OLEDB| 18.0.2.0 |
|SSMS| 17.8.1 o [superiore](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni in proposito, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).

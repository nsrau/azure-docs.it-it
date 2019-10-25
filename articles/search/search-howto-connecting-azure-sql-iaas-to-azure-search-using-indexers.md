---
title: Connessione VM macchina virtuale SQL di Azure per l'indicizzazione della ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare le connessioni crittografate e configurare il firewall per consentire le connessioni a SQL Server in una macchina virtuale (VM) di Azure da un indicizzatore in Azure ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 57bea41d95b8859af55be777b17189861a445a12
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792140"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurare una connessione da un indicizzatore di Azure ricerca cognitiva per SQL Server in una macchina virtuale di Azure

Come indicato nella pagina relativa [alla connessione del database SQL di Azure ad azure ricerca cognitiva usando gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), la creazione di indicizzatori rispetto a **SQL Server in macchine virtuali di azure** (o **SQL Azure VM** in breve) è supportata da Azure ricerca cognitiva, ma sono disponibili alcuni prerequisiti relativi alla sicurezza da gestire prima. 

Le connessioni da Azure ricerca cognitiva a SQL Server in una macchina virtuale sono una connessione Internet pubblica. Tutte le misure di sicurezza da seguire in genere per queste connessioni, sono applicabili anche per:

+ Ottenere un certificato da un [provider dell'autorità di certificazione](https://en.wikipedia.org/wiki/Certificate_authority#Providers) per il nome di dominio completo dell'istanza di SQL Server nella VM di Azure.
+ Installare il certificato nella macchina virtuale, abilitare e configurare le connessioni crittografate nella VM seguendo le istruzioni riportate in questo articolo.

## <a name="enable-encrypted-connections"></a>Abilitare le connessioni crittografate
Azure ricerca cognitiva richiede un canale crittografato per tutte le richieste di indicizzatore tramite una connessione Internet pubblica. Questa sezione elenca i passaggi necessari per eseguire questa operazione.

1. Controllare le proprietà del certificato per verificare che il nome del soggetto sia il nome di dominio completo (FQDN) della VM di Azure. È possibile usare uno strumento come CertUtils o lo snap-in Certificati per visualizzare le proprietà. È possibile ottenere il nome FQDN dalla sezione Informazioni di base del pannello dei servizi della VM nel campo **Etichetta Indirizzo IP pubblico/Nome DNS** del [portale di Azure](https://portal.azure.com/).
   
   * Per le VM create con il nuovo modello di **Gestione risorse**, il nome FQDN segue la formattazione `<your-VM-name>.<region>.cloudapp.azure.com`
   * Per le macchine virtuali meno recenti create come VM **classica**, il nome FQDN segue la formattazione `<your-cloud-service-name.cloudapp.net>`.

2. Configurare SQL Server per l'uso del certificato con l'editor del Registro di sistema (regedit). 
   
    Anche se Gestione configurazione SQL Server viene usato spesso per questa attività, non è possibile usarlo per questo scenario. Non troverà il certificato importato perché il nome FQDN della VM in Azure non corrisponde al nome FQDN determinato dalla VM. Identifica il dominio come computer locale o come dominio di rete a cui è aggiunto. Quando i nomi non corrispondono, usare regedit per specificare il certificato.
   
   * In regedit passare a questa chiave del Registro di sistema: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     La parte `[MSSQL13.MSSQLSERVER]` varia a seconda della versione e del nome dell'istanza. 
   * Impostare il valore della chiave **Certificate** sull'**identificazione personale** del certificato SSL importato nella macchina virtuale.
     
     È possibile ottenere l'identificazione personale in diversi modi, alcuni dei quali preferibili ad altri. Se la si copia dallo snap-in **Certificati** in MMC, esiste il rischio di selezionare un carattere iniziale invisibile, [come illustrato in questo articolo del supporto](https://support.microsoft.com/kb/2023869/), e di ottenere un errore quando si prova a stabilire una connessione. Esistono diverse soluzioni alternative per risolvere il problema. La più semplice consiste nel premere il tasto BACKSPACE e quindi nel digitare di nuovo il primo carattere dell'identificazione personale per rimuovere il carattere iniziale nel campo del valore della chiave in regedit. In alternativa, è possibile usare un altro strumento per copiare l'identificazione personale.

3. Concedere autorizzazioni all'account del servizio. 
   
    Assicurarsi che l'account del servizio SQL Server riceva l'autorizzazione appropriata per la chiave privata del certificato SSL. Se si ignora questo passaggio, SQL Server non verrà avviato. È possibile usare lo snap-in **Certificates** o **CertUtils** per questa attività.
    
4. Riavviare il servizio SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurare la connettività di SQL Server nella VM
Dopo aver configurato la connessione crittografata richiesta da Azure ricerca cognitiva, è necessario eseguire altri passaggi di configurazione intrinseci per SQL Server nelle VM di Azure. Se non è già stato fatto, il passaggio successivo prevede di completare la configurazione seguendo le istruzioni contenute in uno di questi articoli:

* Per una VM di **Resource Manager** , vedere [Connettersi a una macchina virtuale di SQL Server in Azure (Gestione risorse)](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Per una VM **classica** , vedere [Connettersi a una macchina virtuale di SQL Server in Azure (distribuzione classica)](../virtual-machines/windows/classic/sql-connect.md).

In particolare, vedere in ogni articolo la sezione relativa alla "connessione via Internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurare il gruppo di sicurezza di rete
Non è insolito configurare il gruppo di sicurezza di rete e il corrispondente endpoint o elenco di controllo di accesso (ACL) di Azure in modo da rendere accessibile la VM di Azure ad altri soggetti. Probabilmente questa operazione è stata eseguita prima per consentire alla logica applicativa di connettersi alla VM di SQL Azure. Non è diverso per una connessione di Azure ricerca cognitiva alla VM SQL Azure. 

I collegamenti seguenti forniscono istruzioni sulla configurazione del gruppo di sicurezza di rete per le distribuzioni VM. Usare queste istruzioni per l'ACL di un endpoint di Azure ricerca cognitiva in base al relativo indirizzo IP.

> [!NOTE]
> Per informazioni, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/security-overview.md)
> 
> 

* Per una VM di **Resource Manager** , vedere [Come creare gruppi di sicurezza di rete per le distribuzioni Azure Resource Manager](../virtual-network/tutorial-filter-network-traffic.md). 
* Per una VM **classica** , vedere [Come creare gruppi di sicurezza di rete per le distribuzioni classiche](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

L'indirizzamento IP può creare alcune difficoltà facilmente superabili se si conoscono il problema e le potenziali soluzioni alternative. Le sezioni rimanenti contengono suggerimenti per gestire i problemi correlati agli indirizzi IP nell'elenco di controllo di accesso.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Limitare l'accesso all'indirizzo IP del servizio di ricerca
Si consiglia di limitare l'accesso all'indirizzo IP del servizio di ricerca nell'elenco di controllo di accesso invece di aprire le VM di SQL Azure a tutte le richieste di connessione. È possibile trovare facilmente l'indirizzo IP eseguendo il ping del nome FQDN (ad esempio, `<your-search-service-name>.search.windows.net`) del servizio di ricerca.

#### <a name="managing-ip-address-fluctuations"></a>Gestire le fluttuazioni degli indirizzi IP
Se il servizio di ricerca ha solo un'unità di ricerca (vale a dire una replica e una partizione), l'indirizzo IP cambierà durante il riavvio del servizio di routine, invalidando un elenco di controllo di accesso esistente con l'indirizzo IP del servizio di ricerca.

Un modo per evitare l'errore di connettività successivo consiste nell'usare più di una replica e una partizione in Azure ricerca cognitiva. In questo modo, pur aumentando il costo, viene risolto il problema degli indirizzi IP. In Azure ricerca cognitiva gli indirizzi IP non cambiano quando sono presenti più unità di ricerca.

Un altro approccio è quello di consentire che la connessione non riesca e quindi di riconfigurare gli elenchi di controllo di accesso nel gruppo di sicurezza di rete. In media, gli indirizzi IP vengono modificati a distanza di alcune settimane. Per i clienti che hanno controllato l'indicizzazione raramente, questo potrebbe essere un approccio valido.

Un terzo approccio valido (ma non particolarmente sicuro) consiste nello specificare l'intervallo di indirizzi IP dell'area di Azure in cui viene effettuato il provisioning del servizio di ricerca. L'elenco degli intervalli IP da cui gli indirizzi IP pubblici vengono allocati alle risorse di Azure è pubblicato negli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Includere gli indirizzi IP del portale di Azure ricerca cognitiva
Se si usa il portale di Azure per creare un indicizzatore, la logica del portale di Azure ricerca cognitiva deve anche accedere alla VM SQL Azure durante la fase di creazione. Per trovare gli indirizzi IP del portale di Azure ricerca cognitiva è possibile effettuare il ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Passaggi successivi
Con la configurazione, è ora possibile specificare una SQL Server nella macchina virtuale di Azure come origine dati per un indicizzatore di Azure ricerca cognitiva. Per altre informazioni, vedere [connessione del database SQL di Azure ad azure ricerca cognitiva usando gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .


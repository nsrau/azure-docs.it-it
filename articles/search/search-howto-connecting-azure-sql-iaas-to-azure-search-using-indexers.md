---
title: Connessione di una macchina virtuale SQL a Ricerca di Azure | Documentazione Microsoft
description: Abilitare connessioni crittografate e configurare il firewall per consentire connessioni a SQL Server in una macchina virtuale (VM) Azure da un indicizzatore in Ricerca di Azure.
services: search
documentationcenter: 
author: HeidiSteen
manager: pablocas
editor: 
ms.assetid: 46e42e0e-c8de-4fec-b11a-ed132db7e7bc
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: be73e3d009cfcbdd585d81512afaed752ae07364
ms.lasthandoff: 03/25/2017


---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurare una connessione da un indicizzatore di Ricerca di Azure a SQL Server in una VM Azure
Come indicato in [Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#frequently-asked-questions), la creazione di indici per **SQL Server in macchine virtuali di Azure** (o in breve **VM di SQL Azure**) è supportata da Ricerca di Azure, ma prima è necessario occuparsi di alcuni prerequisiti riguardanti la sicurezza. 

**Durata attività:** circa 30 minuti, presumendo di avere già installato un certificato nella VM.

## <a name="enable-encrypted-connections"></a>Abilitare le connessioni crittografate
Ricerca di Azure richiede un canale crittografato per tutte le richieste di indicizzatori su una connessione Internet pubblica. Questa sezione elenca i passaggi necessari per eseguire questa operazione.

1. Controllare le proprietà del certificato per verificare che il nome del soggetto sia il nome di dominio completo (FQDN) della VM di Azure. È possibile usare uno strumento come CertUtils o lo snap-in Certificati per visualizzare le proprietà. È possibile ottenere il nome FQDN dalla sezione Informazioni di base del pannello dei servizi della VM nel campo **Etichetta Indirizzo IP pubblico/Nome DNS** del [portale di Azure](https://portal.azure.com/).
   
   * Per le macchine virtuali create con il nuovo modello di **Gestione risorse**, il nome FQDN segue la formattazione `<your-VM-name>.<region>.cloudapp.azure.com`. 
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
Dopo avere configurato la connessione crittografata da Ricerca di Azure, è necessario eseguire altri passaggi di configurazione intrinseci di SQL Server nelle VM di Azure. Se non è già stato fatto, il passaggio successivo prevede di completare la configurazione seguendo le istruzioni contenute in uno di questi articoli:

* Per una VM di **Resource Manager** , vedere [Connettersi a una macchina virtuale di SQL Server in Azure (Gestione risorse)](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Per una VM **classica** , vedere [Connettersi a una macchina virtuale di SQL Server in Azure (distribuzione classica)](../virtual-machines/windows/classic/sql-connect.md).

In particolare, vedere in ogni articolo la sezione relativa alla "connessione via Internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurare il gruppo di sicurezza di rete
Non è insolito configurare il gruppo di sicurezza di rete e il corrispondente endpoint o elenco di controllo di accesso (ACL) di Azure in modo da rendere accessibile la VM di Azure ad altri soggetti. Probabilmente questa operazione è stata eseguita prima per consentire alla logica applicativa di connettersi alla VM di SQL Azure. Non è diverso per una connessione di Ricerca di Azure alla VM di SQL Azure. 

I collegamenti seguenti forniscono istruzioni sulla configurazione del gruppo di sicurezza di rete per le distribuzioni VM. Usare queste istruzioni per inserire nell'elenco di controllo di accesso un endpoint di Ricerca di Azure in base all'indirizzo IP.

> [!NOTE]
> Per informazioni, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
> 
> 

* Per una VM di **Resource Manager** , vedere [Come creare gruppi di sicurezza di rete per le distribuzioni Azure Resource Manager](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 
* Per una VM **classica** , vedere [Come creare gruppi di sicurezza di rete per le distribuzioni classiche](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

L'indirizzamento IP può creare alcune difficoltà facilmente superabili se si conoscono il problema e le potenziali soluzioni alternative. Le sezioni rimanenti contengono suggerimenti per gestire i problemi correlati agli indirizzi IP nell'elenco di controllo di accesso.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Limitare l'accesso all'indirizzo IP del servizio di ricerca
Si consiglia di limitare l'accesso all'indirizzo IP del servizio di ricerca nell'elenco di controllo di accesso invece di aprire le VM di SQL Azure a tutte le richieste di connessione. È possibile trovare facilmente l'indirizzo IP eseguendo il ping del nome FQDN (ad esempio, `<your-search-service-name>.search.windows.net`) del servizio di ricerca.

#### <a name="managing-ip-address-fluctuations"></a>Gestire le fluttuazioni degli indirizzi IP
Se il servizio di ricerca ha solo un'unità di ricerca (vale a dire una replica e una partizione), l'indirizzo IP cambierà durante il riavvio del servizio di routine, invalidando un elenco di controllo di accesso esistente con l'indirizzo IP del servizio di ricerca.

Per evitare il conseguente errore di connettività, è possibile usare più di una replica e di una partizione in Ricerca di Azure. In questo modo, pur aumentando il costo, viene risolto il problema degli indirizzi IP. In Ricerca di Azure gli indirizzi IP non vengono modificati quando si ha più di un'unità di ricerca.

Un altro approccio è quello di consentire che la connessione non riesca e quindi di riconfigurare gli elenchi di controllo di accesso nel gruppo di sicurezza di rete. In media, gli indirizzi IP vengono modificati a distanza di alcune settimane. Per i clienti che hanno controllato l'indicizzazione raramente, questo potrebbe essere un approccio valido.

Un terzo approccio valido (ma non particolarmente sicuro) consiste nello specificare l'intervallo di indirizzi IP dell'area di Azure in cui viene effettuato il provisioning del servizio di ricerca. L'elenco degli intervalli IP da cui gli indirizzi IP pubblici vengono allocati alle risorse di Azure è pubblicato negli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Includere gli indirizzi IP del portale di Ricerca di Azure
Se si usa il portale di Azure per creare un indicizzatore, anche la logica del portale di Ricerca di Azure deve poter accedere alla VM di SQL Azure durante la fase di creazione. Gli indirizzi IP del portale di Ricerca di Azure sono reperibili eseguendo il ping di `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Passaggi successivi
Dopo avere eseguito la configurazione, è possibile specificare un'istanza di SQL Server nella VM di Azure come origine dati per un indicizzatore di Ricerca di Azure. Per altre informazioni, vedere [Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .



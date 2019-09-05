---
title: Endpoint e regole di rete virtuale per database singoli e in pool in Azure SQL | Microsoft Docs
description: Contrassegnare una subnet come endpoint servizio di rete virtuale. Contrassegnare quindi l'endpoint come regola di rete virtuale per ACL nel database SQL di Azure. Il database SQL accetta quindi la comunicazione da tutte le macchine virtuali e altri nodi nella subnet.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 08/27/2019
ms.openlocfilehash: d924f0eb89984fbe77f94eff553d00a25b4b7a3a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309608"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Usare endpoint servizio e regole di rete virtuale per server di database

Le *regole di rete virtuale* rappresentano una funzionalità di sicurezza firewall che consente di definire se il server di database per i database singoli e il pool elastico nel [database SQL](sql-database-technical-overview.md) di Azure o per i database in [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accetta le comunicazioni inviate da subnet specifiche nelle reti virtuali. Questo articolo spiega i motivi per cui la funzionalità relativa alle regole di rete virtuale rappresenti a volte la scelta ideale per consentire le comunicazioni con il database SQL di Azure e SQL Data Warehouse in modo sicuro.

> [!IMPORTANT]
> Questo articolo è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse. Le informazioni di questo articolo *non* sono valide per la distribuzione di un'**istanza gestita** nel database SQL di Azure perché non ha un endpoint di servizio associato.

Per creare una regola della rete virtuale, è necessario prima di tutto un [endpoint del servizio di rete virtuale][vm-virtual-network-service-endpoints-overview-649d] per la regola a cui fare riferimento.

## <a name="how-to-create-a-virtual-network-rule"></a>Come creare una regola di rete virtuale

Se si crea solo una regola di rete virtuale, è possibile procedere direttamente ai passaggi e alla spiegazione [più avanti in questo articolo](#anchor-how-to-by-using-firewall-portal-59j).

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>Dettagli sulle regole di rete virtuale

Questa sezione illustra alcuni dettagli sulle regole di rete virtuale.

### <a name="only-one-geographic-region"></a>Una sola area geografica

Ogni endpoint servizio di rete virtuale si applica a una sola area di Azure. L'endpoint non consente ad altre aree di accettare le comunicazioni dalla subnet.

Ogni regola di rete virtuale è limitata all'area a cui si applica il relativo endpoint sottostante.

### <a name="server-level-not-database-level"></a>A livello di server, non a livello di database

Ogni regola di rete virtuale si applica all'intero server di database SQL di Azure, non solo a un determinato database nel server. In altre parole, la regola di rete virtuale si applica a livello di server, non a livello di database.

- Al contrario, le regole IP possono essere applicate a qualsiasi livello.

### <a name="security-administration-roles"></a>Ruoli di amministrazione di sicurezza

I ruoli di sicurezza sono distinti nell'amministrazione degli endpoint servizio di rete virtuale. Ogni ruolo indicato di seguito deve svolgere determinate azioni:

- **Amministratore di rete:** &nbsp; attiva l'endpoint.
- **Amministratore del database:** &nbsp; aggiornare l'elenco di controllo di accesso (ACL) per aggiungere la subnet specificata al server di database SQL.

*Alternativa del controllo degli accessi in base al ruolo:*

I ruoli di amministratore di rete e amministratore di database hanno più funzionalità di quelle necessarie a gestire le regole di rete virtuale. È necessario solo un subset delle relative funzionalità.

È possibile scegliere di usare il [controllo degli accessi in base al ruolo (RBAC)][rbac-what-is-813s] in Azure per creare un singolo ruolo personalizzato con solo il subset di funzionalità necessario. È possibile usare il ruolo personalizzato anziché coinvolgere l'amministratore di rete o di database. La superficie di attacco dell'esposizione a rischi per la sicurezza è ridotta se si aggiunge un utente a un ruolo personalizzato, anziché aggiungere l'utente agli altri due ruoli di amministratore principali.

> [!NOTE]
> In alcuni casi il database SQL di Azure e la subnet della rete virtuale sono in sottoscrizioni diverse. In questi casi è necessario garantire le configurazioni seguenti:
> - Entrambe le sottoscrizioni devono essere nello stesso tenant di Azure Active Directory.
> - L'utente ha le autorizzazioni necessarie per avviare le operazioni, ad esempio abilitare gli endpoint di servizio e aggiungere una subnet della rete virtuale al server specificato.
> - Entrambe le sottoscrizioni devono avere registrato il provider di Microsoft.Sql.

## <a name="limitations"></a>Limitazioni

Per il database SQL di Azure, la funzionalità delle regole di rete virtuale presenta le limitazioni seguenti:

- Nel firewall per il database SQL ogni regola di rete virtuale fa riferimento a una subnet. Tutte queste subnet cui viene fatto riferimento devono essere ospitate nella stessa area geografica che ospita il database SQL.

- Ogni server di database SQL di Azure può includere fino a 128 voci ACL per qualsiasi rete virtuale specificata.

- Le regole della rete virtuale si applicano solo alle reti virtuali Azure Resource Manager; e non alle reti di [modelli di distribuzione classica][arm-deployment-model-568f] .

- L'attivazione degli endpoint servizio di rete virtuale nel database SQL di Azure abilita anche gli endpoint per i servizi MySQL e PostgreSQL Azure. Tuttavia, con gli endpoint attivati i tentativi di connessione dagli endpoint alle istanze di MySQL o PostgreSQL potrebbero avere esito negativo.
  - La causa principale è che MySQL e PostgreSQL probabilmente non possiedono una regola di rete virtuale configurata. Configurare una regola di rete virtuale per Database di Azure per MySQL e PostgreSQL; in questo modo la connessione avrà esito positivo.

- Nel firewall, gli intervalli di indirizzi IP si applicano ai seguenti elementi di rete, ma non le regole di rete virtuale:
  - [Rete privata virtuale (VPN) da sito a sito (S2S)][vpn-gateway-indexmd-608y]
  - Locale tramite [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Considerazioni sull'uso di endpoint del servizio

Quando si usano gli endpoint del servizio per il Database SQL di Azure, rivedere le considerazioni seguenti:

- **In uscita verso gli indirizzi IP pubblici del database SQL di Azure**: i gruppi di sicurezza di rete devono essere aperti verso gli indirizzi IP del database SQL di Azure per consentire la connettività. È possibile farlo tramite i [Tag dei servizi](../virtual-network/security-overview.md#service-tags) del Gruppo di sicurezza di rete per il Database SQL di Azure.

### <a name="expressroute"></a>ExpressRoute

Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dall'ambiente locale, per il peering pubblico o per il peering Microsoft, sarà necessario identificare gli indirizzi IP NAT usati. Per il peering pubblico, ogni circuito ExpressRoute usa per impostazione predefinita due indirizzi IP NAT applicati al traffico del servizio di Azure quando il traffico entra nel backbone della rete di Microsoft Azure. Per il peering Microsoft, gli indirizzi IP NAT usati vengono forniti dal cliente o dal provider del servizio. Per consentire l'accesso alle risorse del servizio è necessario autorizzare questi indirizzi IP pubblici nell'impostazione del firewall IP per le risorse. Per trovare gli indirizzi IP del circuito ExpressRoute per il peering pubblico, [aprire un ticket di supporto in ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Vedere altre informazioni su [NAT per il peering pubblico e il peering Microsoft ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Per consentire la comunicazione tra il circuito e il database SQL di Azure, è necessario creare regole di rete IP per gli indirizzi IP pubblici di NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->



## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impatto dell'uso degli endpoint di servizio di rete virtuale con Archiviazione di Azure

Archiviazione di Azure ha implementato la stessa funzionalità che consente di limitare la connettività all'account di archiviazione di Azure. Se si sceglie di usare questa funzionalità con un account di archiviazione di Azure usato da un server SQL Azure, possono verificarsi problemi. Di seguito sono riportati un elenco e una spiegazione delle funzionalità del database SQL di Azure e di Azure SQL Data Warehouse interessate.

### <a name="azure-sql-data-warehouse-polybase"></a>PolyBase di Azure SQL Data Warehouse

PolyBase viene in genere usato per caricare i dati in Azure SQL Data Warehouse dagli account di archiviazione di Azure. Se l'account di archiviazione di Azure da cui si caricano i dati limita l'accesso solo a un set di subnet della rete virtuale, la connettività da PolyBase all'account verrà interrotta. Per poter usare scenari sia di importazione che di esportazione di PolyBase con Azure SQL Data Warehouse che si connette ad Archiviazione di Azure protetta con la rete virtuale, seguire la procedura descritta di seguito.

#### <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

1.  Installare Azure PowerShell usando questa [guida](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.  Se si dispone di un account di archiviazione BLOB o per utilizzo generico v1, prima è necessario eseguire l'aggiornamento all'utilizzo generico v2 usando questa [guida](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  È necessario avere attivato l'opzione **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**  nel menu delle impostazioni **Firewall e reti virtuali** di tale account. Per altre informazioni, fare riferimento a [questa guida](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
 
#### <a name="steps"></a>Passaggi
1. In PowerShell **registrare il SQL Server di Azure** che ospita l'istanza di Azure SQL Data Warehouse con Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```
    
   1. Creare **un account di archiviazione per utilizzo generico v2** usando questa [guida](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Se si dispone di un account di archiviazione BLOB o per utilizzo generico v1, è necessario **prima eseguire l'aggiornamento a v2** usando questa [guida](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Per problemi noti con Azure Data Lake Storage Gen2, fare riferimento a questa [guida](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1. Quando si è posizionati nell'account di archiviazione, passare a **Controllo di accesso (IAM)** e fare clic su **Aggiungi un'assegnazione di ruolo**. Assegnare il ruolo controllo degli accessi in base al ruolo di **collaboratore dei dati BLOB di archiviazione** al SQL Server di Azure che ospita i Azure SQL data warehouse registrati con Azure Active DIRECOTORY (AAD) come nel passaggio 1.

   > [!NOTE] 
   > Solo i membri con il privilegio di proprietario possono eseguire questo passaggio. Per i vari ruoli predefiniti per le risorse di Azure, fare riferimento a questa [guida](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Connettività di Polybase all'account di archiviazione di Azure:**

   1. Creare una **[chiave master](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** del database, se non è stata creata in precedenza:
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. Creare credenziali con ambito database con **IDENTITY = 'Managed Service Identity'** :

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Non è necessario specificare SECRET con la chiave di accesso ad Archiviazione di Azure perché questo meccanismo in pratica usa l'[identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
       > - Il nome IDENTITY deve essere **'Managed Service Identity'** affinché la connettività di PolyBase funzioni con l'account di archiviazione di Azure protetto con la rete virtuale.    
    
   1. Creare un'origine dati esterna con lo schema abfss:// per la connessione all'account di archiviazione per utilizzo generico v2 con PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - Se sono già presenti tabelle esterne associate all'account di archiviazione BLOB o per utilizzo generico v1, è necessario prima rimuovere queste tabelle esterne e quindi rimuovere l'origine dati esterna corrispondente. A questo punto, creare l'origine dati esterna con lo schema abfss:// per la connessione all'account di archiviazione per utilizzo generico v2 come specificato sopra e creare di nuovo tutte le tabelle esterne usando questa nuova origine dati esterna. Per comodità, è possibile usare la procedura guidata [Genera e pubblica script](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) per generare gli script di creazione per tutte le tabelle esterne.
       > - Per altre informazioni sullo schema abfss://, fare riferimento a questa [guida](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Per altre informazioni su CREATE EXTERNAL DATA SOURCE, fare riferimento a questa [guida](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
   1. Eseguire le query come di consueto usando le [tabelle esterne](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Controllo BLOB del database SQL di Azure

Il controllo BLOB esegue il push dei log di controllo nell'account di archiviazione. Se questo account di archiviazione usa la funzionalità degli endpoint di servizio di rete virtuale, la connettività dal database SQL di Azure all'account di archiviazione verrà interrotta.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Aggiunta di una regola del firewall della rete virtuale al server senza attivare gli endpoint di servizio di rete virtuale

In passato, prima che questa funzionalità fosse migliorata, era necessario attivare gli endpoint servizio di rete virtuale prima di poter implementare una regola di rete virtuale attiva nel firewall. Gli endpoint correlavano una determinata subnet della rete virtuale a un database SQL di Azure. A partire da gennaio 2018 è invece possibile aggirare questo requisito impostando il flag **IgnoreMissingVNetServiceEndpoint**.

La semplice impostazione di una regola del firewall non consente di proteggere il server. Per garantire la sicurezza, è anche necessario attivare gli endpoint di servizio di rete virtuale. Quando si attivano gli endpoint di servizio, la subnet della rete virtuale registra un tempo di inattività fino al termine della transizione dallo stato inattivo a quello attivo. Questo vale soprattutto per le reti virtuali di grandi dimensioni. È possibile usare il flag **IgnoreMissingVNetServiceEndpoint** per ridurre o eliminare il tempo di inattività durante la transizione.

È possibile impostare il flag **IgnoreMissingVNetServiceEndpoint** usando PowerShell. Per informazioni dettagliate, vedere [PowerShell per creare un endpoint del servizio di rete virtuale e una regola per il database SQL di Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Errori 40914 e 40615

L'errore di connessione 40914 è correlato alle *regole di rete virtuale*, come specificato nel riquadro Firewall nel portale di Azure. L'errore 40615 è simile, ma è correlato alle *regole degli indirizzi IP* in Firewall.

### <a name="error-40914"></a>Errore 40914

*Testo del messaggio:* Impossibile aprire il server " *[nome-server]* " richiesto dall'account di accesso. Al client non è consentito accedere al server.

*Descrizione dell'errore:* il client si trova in una subnet che include endpoint server di rete virtuale. Tuttavia, il server di database SQL di Azure non è associato ad alcuna regola di rete virtuale che concede alla subnet il diritto di comunicare con il database SQL.

*Risoluzione dell'errore:* nel riquadro Firewall del portale di Azure usare il controllo delle regole di rete virtuale per [aggiungere una regola di rete virtuale](#anchor-how-to-by-using-firewall-portal-59j) per la subnet.

### <a name="error-40615"></a>Errore 40615

*Testo del messaggio:* Impossibile aprire il server "{0}" richiesto dall'account di accesso. Non è consentito l'accesso del client con indirizzo IP "{1}" al server.

*Descrizione dell'errore:* il client sta tentando di connettersi da un indirizzo IP che non è autorizzato a connettersi al server di database SQL di Azure. Il firewall del server non ha alcuna regola degli indirizzi IP che consente a un client di comunicare dall'indirizzo IP specifico al database SQL.

*Risoluzione dell'errore:* immettere l'indirizzo IP del client come regola IP. A questo scopo, usare il riquadro Firewall nel portale di Azure.

Un elenco di diversi messaggi di errore del database SQL è illustrato [qui][sql-database-develop-error-messages-419g].

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Il portale può creare una regola di rete virtuale

Questa sezione illustra come è possibile usare la [portale di Azure][http-azure-portal-link-ref-477t] per creare una *regola della rete virtuale* nel database SQL di Azure. La regola indica al database SQL di accettare le comunicazioni da una subnet specifica contrassegnata come *endpoint servizio di rete virtuale*.

> [!NOTE]
> Se si intende aggiungere un endpoint di servizio alle regole del firewall della rete virtuale del server di database SQL di Azure, verificare per prima cosa che gli endpoint di servizio siano attivati per la subnet.
>
> Se gli endpoint di servizio non sono attivati per la subnet, nel portale verrà richiesto di abilitarli. Fare clic sul pulsante **Abilita** nello stesso pannello in cui si aggiunge la regola.

## <a name="powershell-alternative"></a>Alternativa PowerShell

Anche uno script di PowerShell può creare regole di rete virtuale. Il cmdlet cruciale **New-AzSqlServerVirtualNetworkRule**. Se interessato, vedere [PowerShell per creare un endpoint del servizio di rete virtuale e una regola per il database SQL di Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativa API REST

Internamente, i cmdlet di PowerShell per le azioni SQL sulle reti virtuali chiamano API REST. È possibile chiamare direttamente le API REST.

- [Regole di rete virtuale: Operazioni][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Prerequisiti

È necessario avere già una subnet contrassegnata con lo specifico *nome del tipo* di endpoint servizio di rete virtuale pertinente per il database SQL di Azure.

- Il nome del tipo di endpoint pertinente è **Microsoft.Sql**.
- Se la subnet potrebbe non essere contrassegnata con il nome del tipo, vedere [verificare che la subnet sia un endpoint][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Procedure del portale di Azure

1. Accedere al [portale di Azure][http-azure-portal-link-ref-477t].

2. Nel portale passare a **SQL Server** &gt; **Firewall/Reti virtuali (anteprima)** .

3. Impostare il controllo **Consenti l'accesso a Servizi di Azure** su OFF.

    > [!IMPORTANT]
    > Se si lascia il controllo impostato su SÌ, il server di database SQL di Azure accetta la comunicazione da qualsiasi subnet. Lasciando il controllo impostato su SÌ, il numero di accessi potrebbe essere eccessivo dal punto di vista della sicurezza. La funzionalità di endpoint servizio di rete virtuale di Microsoft Azure, in sinergia con la funzionalità delle regole di rete virtuale del database SQL, consente di ridurre la superficie di attacco per la sicurezza.

4. Fare clic sul controllo **+ Aggiungi esistenti** nella sezione **Reti virtuali**.

    ![Fare clic su Aggiungi esistenti (endpoint della subnet, come regola SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. Nel nuovo riquadro **Crea/Aggiorna** compilare i controlli con i nomi delle risorse di Azure.

    > [!TIP]
    > È necessario includere il **prefisso dell'indirizzo** corretto per la subnet. Il valore è disponibile nel portale.
    > Passare a **Tutte le risorse** &gt; **Tutti i tipi** &gt; **Reti virtuali**. Il filtro visualizza le reti virtuali. Fare clic sulla rete virtuale desiderata e quindi su **Subnet**. La colonna **INTERVALLO DI INDIRIZZI** contiene il prefisso dell'indirizzo desiderato.

    ![Compilare i campi per la nuova regola.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Fare clic sul pulsante **OK** nella parte inferiore del riquadro.

7. Osservare la regola di rete virtuale risultante nel riquadro del firewall.

    ![Osservare la nuova regola nel riquadro del firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Le regole presentano gli stati seguenti:
> - **Pronta:** indica che l'operazione avviata è riuscita.
> - **Non riuscita:** indica che l'operazione avviata non è riuscita.
> - **Eliminata:** si applica solo all'operazione di eliminazione e indica che la regola è stata eliminata e non viene più applicata.
> - **In corso:** indica che l'operazione è in corso. Mentre l'operazione è in questo stato, viene applicata la regola precedente.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Articoli correlati

- [Endpoint del servizio rete virtuale di Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regole del firewall a livello di server e di database per il database SQL di Azure][sql-db-firewall-rules-config-715d]

La funzionalità delle regole di rete virtuale per il database SQL di Azure è disponibile dalla fine di settembre 2017.

## <a name="next-steps"></a>Passaggi successivi

- [Usare PowerShell per creare un endpoint del servizio di rete virtuale e quindi una regola della rete virtuale per il database SQL di Azure.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regole di rete virtuale: Operazioni][rest-api-virtual-network-rules-operations-862r] con le API REST

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->

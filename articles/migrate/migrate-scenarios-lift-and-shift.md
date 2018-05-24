---
title: Eseguire la migrazione di carichi di lavoro locali ad Azure con il Servizio Migrazione del database di Azure e Site Recovery | Microsoft Docs
description: Informazioni su come preparare Azure per la migrazione di computer locali con il Servizio Migrazione del database di Azure e il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182241"
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>Scenario 2: Migrazione ad Azure in modalità lift-and-shift

La società Contoso sta prendendo in considerazione la migrazione ad Azure. Come prova, intende eseguire la valutazione e la migrazione ad Azure di una piccola app di viaggio locale. È un'app di viaggio a due livelli, con un'app Web in esecuzione in una VM e un database di SQL Server in una seconda VM. L'applicazione viene distribuita in VMware e l'ambiente è gestito da un server vCenter. 

In [Scenario 1: Valutare i carichi di lavoro locali per la migrazione ad Azure](migrate-scenarios-assessment.md) la società ha usato Data Migration Assistant (DMA) per valutare il database di SQL Server per l'app. Ha inoltre usato il servizio Azure Migrate per valutare le VM dell'app. Ora, in questo scenario, dopo l'esito positivo della valutazione, la società intende eseguire la migrazione del database a un'istanza gestita di SQL di Azure usando il Servizio Migrazione del database di Azure, nonché dei computer locali alle macchine virtuali di Azure usando il servizio Azure Site Recovery.

Se si desidera provare lo [Scenario 1](migrate-scenarios-assessment.md) e quindi questo scenario usando l'app di viaggio illustrativa, è possibile scaricarla da [GitHub](https://github.com/Microsoft/SmartHotel360).



**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/dms-overview) | Il Servizio Migrazione del database consente di eseguire facilmente la migrazione di più origini di database nelle piattaforme dati di Azure, con tempi di inattività minimi. | Il servizio è attualmente (aprile 2018) in anteprima pubblica e in questo periodo può essere usato gratuitamente.<br/><br/> Per l'anteprima pubblica, il Servizio Migrazione del database è limitato a un [numero definito di aree](https://docs.microsoft.com/azure/dms/dms-overview).
[Istanza gestita di SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | L'istanza gestita è un servizio di database gestito che rappresenta un'istanza completamente gestita di SQL Server nel cloud di Azure. Usa lo stesso codice della versione più recente del motore di database di SQL Server e dispone delle funzionalità, dei miglioramenti delle prestazioni e delle patch di sicurezza più recenti. | L'esecuzione in Azure di istanze gestite di database SQL di Azure è soggetta a costi in base alla capacità. [Altre informazioni](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Questo servizio orchestra e gestisce la migrazione e il ripristino di emergenza per le macchine virtuali di Azure e per le macchine virtuali e i server fisici locali.  | Durante la replica in Azure vengono addebitati costi relativi all'archiviazione di Azure.  In caso di failover vengono create macchine virtuali di Azure, le quali sono soggette a costi. [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) su addebiti e prezzi.

In questo scenario si configura una VPN da sito a sito, che consente al Servizio Migrazione del database di connettersi al database locale, creare un'istanza gestita di SQL di Azure in Azure ed eseguire la migrazione del database. Per Site Recovery si prepara l'ambiente VMware in locale, si configura la replica e si esegue la migrazione delle VM ad Azure.  


> [!IMPORTANT]
> È necessario essere registrati per l'anteprima pubblica limitata dell'istanza gestita di SQL. La [registrazione](https://portal.azure.com#create/Microsoft.SQLManagedInstance) richiede una sottoscrizione di Azure. Il completamento della registrazione può richiedere qualche giorno. Accertarsi pertanto di provvedere per tempo prima di iniziare a distribuire questo scenario.

## <a name="architecture"></a>Architecture

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>Servizio Migrazione del database
![Servizio Migrazione del database](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

In questo scenario:

- Contoso è un nome fittizio che rappresenta una tipica organizzazione aziendale. Contoso desidera valutare ed eseguire la migrazione della propria app di viaggio locale su due livelli.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- L'app di viaggio interna è suddivisa in livelli in due macchine virtuali, WEBVM e SQLVM, e risiede nell'host VMware ESXi (**contosohost1.contoso.com**).
- L'ambiente VMware è gestito da un server vCenter (**vcenter.contoso.com**) in esecuzione in una VM.

## <a name="prerequisites"></a>prerequisiti

Di seguito sono elencati i requisiti per eseguire questo scenario.

Requisiti | Dettagli
--- | ---
**Sottoscrizione di Azure** | Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.<br/><br/> Se sono necessarie autorizzazioni più granulari, vedere [questo articolo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site Recovery (locale)** | Server vCenter locale che esegue la versione 5.5, 6.0 o 6.5.<br/><br/> Host ESXi che esegue la versione 5.5, 6.0 o 6.5.<br/><br/> Una o più macchine virtuali VMware in esecuzione nell'host ESXi.<br/><br/> Le macchine virtuali devono soddisfare i [requisiti di Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configurazione supportata di [rete](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [archiviazione](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage).
**Servizio Migrazione del database** | Per il Servizio Migrazione del database è necessario un [dispositivo VPN locale compatibile](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> È necessario saper configurare il dispositivo VPN locale. Il dispositivo deve avere un indirizzo IPv4 pubblicamente accessibile, che non può trovarsi dietro un dispositivo NAT.<br/><br/> Accertarsi di avere accesso al database di SQL Server locale.<br/><br/> Windows Firewall deve essere in grado di accedere al motore di database di origine. [Altre informazioni](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Se il computer dove si trova il database è protetto da un firewall, aggiungere regole per consentire l'accesso al database e ai file tramite la porta SMB 445.<br/><br/> Le credenziali usate per connettersi all'istanza di SQL Server di origine e all'istanza gestita di destinazione devono appartenere a membri del ruolo sysadmin del server.<br/><br/> Nel database locale deve essere presente una condivisione di rete che il Servizio Migrazione del database può usare per eseguire il backup del database di origine.<br/><br/> Verificare che l'account del servizio che esegue l'istanza di SQL Server di origine abbia privilegi di scrittura nella condivisione di rete.<br/><br/> Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete. Il Servizio Migrazione del database di Azure rappresenta queste credenziali utente per caricare i file di backup nel contenitore di archiviazione di Azure.<br/><br/> L'installazione di SQL Server Express imposta il protocollo TCP/IP su **Disabilitato** per impostazione predefinita. Assicurarsi che sia abilitato.


# <a name="scenario-overview"></a>Panoramica dello scenario

Verranno eseguite le operazioni seguenti.

> [!div class="checklist"]
> * **Passaggio 1: Configurare una connessione VPN da sito a sito**. Il Servizio Migrazione del database si connette al database locale tramite una connessione VPN da sito a sito. Viene creata una rete virtuale per la connessione VPN e in seguito questa rete viene riusata per Site Recovery. La rete creata deve trovarsi nell'area in cui si crea un insieme di credenziali di Servizi di ripristino.
> * **Passaggio 2: Configurare un'istanza gestita di SQL di Azure**. È necessario avere un'istanza gestita creata in precedenza dove eseguire la migrazione del database di SQL Server locale.
> * **Passaggio 3: Configurare un URI di firma di accesso condiviso per il Servizio Migrazione del database**. Un URI (Uniform Resource Identifier) di firma di accesso condiviso fornisce accesso delegato alle risorse nel proprio account di archiviazione, che consente di concedere autorizzazioni limitate per gli oggetti di archiviazione. Configurare un URI di firma di accesso condiviso per consentire al Servizio Migrazione del database di accedere al contenitore dell'account di archiviazione dove il servizio carica i file di backup di SQL Server.
> * **Passaggio 4: Preparare il Servizio Migrazione del database**. Si registra il provider di migrazione del database, si crea un'istanza e quindi si crea un progetto di Servizio Migrazione del database.
> * **Passaggio 5: Preparare Azure per Site Recovery**. Si crea un account di archiviazione di Azure per contenere i dati replicati.
> * **Passaggio 6: Preparare VMware in locale per Site Recovery**. Si preparano gli account per l'individuazione di macchine virtuali e l'installazione dell'agente, quindi si prepara la connessione alle macchine virtuali di Azure dopo il failover. 
> * **Passaggio 7: Replicare le macchine virtuali**. Si configura l'ambiente di origine e di destinazione di Site Recovery, si impostano i criteri di replica e si avvia la replica delle macchine virtuali nell'archiviazione di Azure.
> * **Passaggio 8: Eseguire la migrazione del database con il Servizio Migrazione del database**. Si esegue una migrazione del database.
> * **Passaggio 9: Eseguire la migrazione di macchine virtuali con il ripristino del sito**. Si esegue un failover per la migrazione delle macchine virtuali in Azure.


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>Passaggio 1: Configurare una connessione VPN da sito a sito.

Per preparare la connessione VPN da sito a sito, si imposta una rete virtuale e le subnet, si crea un gateway VPN per la rete virtuale e si configura un gateway di rete locale per consentire ad Azure di connettersi alla VPN locale. Quindi si crea e si verifica la connessione da sito a sito.

### <a name="set-up-a-virtual-network"></a>Configurare una rete virtuale

Creare una rete virtuale di Azure per fornire al Servizio Migrazione del database la connettività da sito a sito a SQL Server locale.


1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa**. Selezionare **Rete** > **Rete virtuale**.
2. In **Rete virtuale** > **Selezionare un modello di distribuzione** selezionare **Resource Manager** > **Crea**.
3. In **Crea rete virtuale** > **Nome** immettere un nome di rete univoco. Per questo scenario immettere **ContosoVNET**.
4. In **Spazio degli indirizzi** aggiungere l'intervallo di indirizzi IP, verificando che non si sovrapponga allo spazio degli indirizzi locale.
5. In **Gruppo di risorse** creare un nuovo gruppo o selezionarne uno esistente. In questo scenario viene usato **ContosoRG**.
6. In **Località** selezionare l'area dove creare la rete virtuale. In questo esempio viene usata l'area **Stati Uniti orientali 2**.
7. In **Subnet** aggiungere il nome e l'intervallo di indirizzi della prima subnet. In questo esempio è stata creata la subnet **Apps**.
8. Disabilitare gli endpoint di servizio.

    ![Crea rete virtuale](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. Selezionare **Aggiungi al dashboard** per ritrovare facilmente la rete in futuro, quindi fare clic su **Crea**.
10. La creazione della rete virtuale richiede qualche secondo. Al termine verificare che sia visualizzata nel dashboard del portale di Azure.
11. Assicurarsi che nella rete virtuale siano consentite queste porte di comunicazione: 443, 53, 9354, 445, 12000.


### <a name="set-up-subnets"></a>Configurare le subnet

La rete di Azure conterrà quattro subnet:

![Elenco di subnet](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

La prima subnet (Apps) è stata configurata al momento della creazione della rete. Ora si creeranno le subnet restanti. La subnet del gateway viene usata dalla connessione gateway VPN, per consentire ad Azure di inviare il traffico tramite una connessione VPN al sito locale.

1. Nella rete virtuale, in **Impostazioni**, fare clic su **Subnet** > **+Subnet**.
2. Configurare una subnet denominata **Datamigration**, con l'intervallo di indirizzi mostrato sotto, quindi fare clic su **OK**.

    ![Subnet dati](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. In **+Subnet** configurare una subnet denominata **Identity**, con l'intervallo di indirizzi mostrato sotto, quindi fare clic su **OK**.
    ![Subnet Identity](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. In **Subnet** fare clic su **Subnet gateway** e quindi fare clic su **OK**.
    - Questa subnet contiene gli indirizzi IP che saranno usati dal gateway VPN per le connessioni VPN.
    - Il nome di questa subnet viene impostato automaticamente in modo che Azure possa riconoscerla.
    - Modificare l'intervallo di indirizzi compilato automaticamente in modo che corrisponda alla subnet locale. 

    ![subnet gateway](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>Configurare un gateway di rete virtuale

1. Nel lato sinistro della pagina del portale fare clic su **+** e digitare 'Gateway di rete virtuale' nella casella di ricerca. In **Risultati** selezionare **Gateway di rete virtuale**.
2. Nella parte inferiore della pagina "Gateway di rete virtuale" fare clic su **Crea**.
3. In **Crea gateway di rete virtuale** >  **Nome** specificare un nome per l'oggetto gateway.
4. In **Tipo di gateway** selezionare **VPN**.
5. In **Tipo VPN** selezionare **Basato su route**.
6. In **SKU** selezionare lo SKU del gateway dall'elenco a discesa. Gli SKU disponibili nell'elenco a discesa dipendono dal tipo di VPN selezionato. Non abilitare la modalità attiva-attiva. [Altre informazioni](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) sugli SKU.
7. In **Rete virtuale** > **Scegli una rete virtuale** aggiungere la rete creata. Il gateway verrà aggiunto a questa rete.
8. In **Prima configurazione IP** specificare un indirizzo IP pubblico.  Un gateway VPN deve avere un indirizzo IP pubblico. Questo indirizzo viene assegnato dinamicamente alla risorsa durante la creazione del gateway VPN. Attualmente sono supportati solo indirizzi dinamici, ma l'indirizzo IP non cambia dopo l'assegnazione.
    - Fare clic su **Crea configurazione IP gateway**. In **Scegli indirizzo IP pubblico** fare clic su **+Crea nuovo**.
    - In **Crea indirizzo IP pubblico** specificare un nome per l'indirizzo IP pubblico (Contoso-Gateway). Lasciare il valore di SKU su **Basic** e fare clic su **OK** per salvare le modifiche.
        
    ![Gateway di rete virtuale](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. Fare clic su **Crea** per iniziare a creare il gateway VPN. Le impostazioni verranno convalidate e nel dashboard verrà visualizzato il riquadro relativo alla distribuzione del gateway di rete virtuale.

    ![Convalidare il gateway di rete virtuale](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
La creazione di un gateway può richiedere fino a 45 minuti. Può essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.

### <a name="set-up-a-local-network-gateway"></a>Configurare un gateway di rete locale

Configurare un gateway di rete locale in Azure per rappresentare il sito locale.

1. Nel portale fare clic su **+Crea una risorsa**.
2. Nella casella di ricerca digitare **Gateway di rete locale**, quindi premere **INVIO** per eseguire la ricerca. Nei risultati fare clic su **Gateway di rete locale** > **Crea**.
3. In **Crea un gateway di rete locale** > **Nome** specificare un nome da usare in Azure per l'oggetto gateway di rete locale.
4. In **Indirizzo IP** specificare l'indirizzo IP pubblico del dispositivo VPN locale al quale Azure dovrà connettersi. All'indirizzo IP non devono essere applicate le regole NAT e Azure deve essere in grado di raggiungerlo.
5. In **Spazio degli indirizzi** digitare gli intervalli di indirizzi di rete locale che verranno indirizzati attraverso il gateway VPN al dispositivo locale. Assicurarsi che non si sovrappongano agli intervalli nella rete virtuale di Azure.
6. L'opzione **Configura le impostazioni BGP** non è rilevante per questo scenario.
7. In **Sottoscrizione** verificare che la sottoscrizione sia corretta.
8. In **Gruppo di risorse** selezionare il gruppo di risorse usato per creare la rete (ContosoRG).
9. In **Località** selezionare l'area dove si è creata la rete virtuale.

    ![Gateway locale](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. Fare clic su **Crea** per creare il gateway locale.

### <a name="configure-your-on-premises-vpn-device"></a>Configurare il dispositivo VPN locale

Nel dispositivo VPN locale è necessario configurare:

- L'indirizzo IPv4 pubblico del gateway di rete virtuale di Azure appena creato.
- La chiave già condivisa (la chiave usata al momento della creazione della connessione VPN da sito a sito).  

Come aiuto nella configurazione del dispositivo VPN locale:

- A seconda del dispositivo VPN locale usato, potrebbe essere disponibile un apposito script di configurazione da scaricare. [Altre informazioni](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript).
- Vedere [altre risorse utili](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice).

### <a name="create-the-vpn-connection"></a>Creare la connessione VPN

A questo punto, creare la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN locale.

1. Aprire la pagina della rete virtuale in **Reti virtuali** > **Panoramica** > **Dispositivi connessi**. 
2. Fare clic su **Connessioni** > **+ Aggiungi**.
3. In **Aggiungi connessione** > **Nome** specificare un nome per la connessione.
4. In **Tipo di connessione** selezionare **Da sito a sito (IPSec)**.
5. Il valore di **Gateway di rete virtuale** è fisso perché la connessione viene eseguita da questo gateway.
6. In **Gateway di rete locale** specificare il gateway di rete locale creato.
7. In **Chiave condivisa** specificare la chiave corrispondente al valore usato per il dispositivo VPN locale. L'esempio usa "abc123", ma è possibile e consigliabile usare un elemento più complesso. È importante che il valore specificato qui sia lo stesso valore specificato durante la configurazione del dispositivo VPN.
8. I valori di **Sottoscrizione**, **Gruppo di risorse** e **Località** sono fissi.

    ![Connessione VPN](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. Fare clic su **OK** per creare la connessione. Sullo schermo lampeggerà il messaggio "Creazione connessione".
5. Dopo la creazione della connessione, è possibile visualizzarla nella pagina **Connessioni** relativa al gateway di rete virtuale. Lo stato passa da Sconosciuto a Connessione e quindi a Operazione riuscita.

### <a name="verify-the-vpn-connection"></a>Verificare la connessione VPN

Nel portale di Azure è possibile visualizzare lo stato della connessione di un gateway VPN di Resource Manager passando alla connessione. 

1. Fare clic su **Tutte le risorse** e passare al gateway di rete virtuale.
2. Nella pagina del gateway di rete virtuale fare clic su **Connessioni**. È possibile visualizzare lo stato di ogni connessione.
3. Fare clic sul nome della connessione e visualizzare ulteriori informazioni in **Essentials**. Dopo aver stabilito una connessione, lo stato visualizzato è "Operazione riuscita" e "Connesso".

A questo punto, verificare che sia possibile connettersi alla VM di SQL Server tramite VPN. Usare una connessione Desktop remoto e connettersi all'indirizzo IP della VM.



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>Passaggio 2: Preparare un'istanza gestita di SQL di Azure

### <a name="set-up-a-virtual-network"></a>Configurare una rete virtuale

Per questo scenario è necessario creare un'altra rete virtuale dedicata per l'istanza gestita di SQL di Azure.  Tenere presenti i requisiti seguenti:

- Per creare l'istanza gestita è necessario creare una subnet dedicata.
- La subnet deve essere vuota, non contenere altri servizi cloud e non essere una subnet gateway. Dopo la creazione dell'istanza gestita, non si devono aggiungere risorse alla subnet.
- Alla subnet non deve essere associato un gruppo di sicurezza di rete.
- La subnet deve avere una tabella di route definita dall'utente con hop successivo di tipo Internet 0.0.0.0/0 come unica route assegnata. 
- DNS personalizzato facoltativo: se per la rete virtuale sono specificate impostazioni DNS personalizzate, è necessario aggiungere all'elenco l'indirizzo IP dei resolver ricorsivi di Azure (ad esempio 168.63.129.16). [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Alla subnet non deve essere associato alcun endpoint di servizio (archiviazione o SQL). Gli endpoint di servizio devono essere disabilitati nella rete virtuale.
- La subnet deve avere un minimo di 16 indirizzi IP. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) sul dimensionamento della subnet dell'istanza gestita.

Configurare la rete virtuale come segue:

1.  Nel portale di Azure fare clic su **Crea una risorsa**. 
2. Selezionare **Rete** > **Rete virtuale**.
3. In **Rete virtuale** > **Selezionare un modello di distribuzione** selezionare **Resource Manager** > **Crea**.
4. In **Crea rete virtuale** > **Nome** immettere un nome di rete univoco. Per questo scenario immettere **ContosoVNETSQLMI**.
5. In **Spazio degli indirizzi** aggiungere l'intervallo di indirizzi IP mostrato sotto, verificando che non si sovrapponga allo spazio degli indirizzi locale e di ContosoVNET.
6. In **Gruppo di risorse** creare un nuovo gruppo o selezionarne uno esistente. In questo scenario viene usato **ContosoRG**.
7. In **Località** selezionare l'area dove creare la rete virtuale. In questo esempio viene usata l'area **Stati Uniti orientali 2**.
8. In **Subnet** aggiungere il nome e l'intervallo di indirizzi della prima subnet. È stata creata la subnet **ManagedInstances**.
9. Disabilitare gli endpoint di servizio.

    ![Rete dell'istanza gestita](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. Dopo la distribuzione della rete è necessario modificare le impostazioni DNS. In questo scenario il DNS punta prima a un controller di dominio nella subnet Identity mediante un indirizzo IP privato statico (172.16.3.4) e quindi al resolver DNS di Azure 168.63.129.16.

    ![Rete dell'istanza gestita](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>Configurare il routing

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure. Fare clic su **Tabella di route** > **Crea** nella pagina della tabella di route.
2. In **Crea tabella di route** > **Nome** specificare un nome per la tabella.
3. Selezionare la sottoscrizione, il gruppo di risorse e la località. Lasciare disabilitata l'opzione BGP e fare clic su **Crea**.
    ![Tabella di route](media/migrate-scenarios-lift-and-shift/route-table.png)

4. Al termine della creazione della tabella di route, aprirla e fare clic su **Route** > **+Aggiungi**.
5. In **Aggiungi route** > **Nome** specificare un nome di route.
6. In **Prefisso indirizzo** specificare 0.0.0.0/0.
7. In **Tipo hop successivo** selezionare **Internet**. Fare quindi clic su **OK**.

     ![Tabella di route](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>Applicare la route alla subnet dell'istanza gestita

1. Aprire la rete virtuale creata. Fare clic su **Subnet** > nome-subnet.
2. Fare clic su **Tabella di route** > nome-tabella. Fare quindi clic su **Salva**.

     ![Tabella di route](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>Creare un'istanza gestita

1. Fare clic su **Crea una risorsa**, individuare **Istanza gestita** e quindi selezionare **Azure SQL Database Managed Instance (preview)** (Istanza gestita di database SQL di Azure - anteprima).
2. Fare clic su **Crea**.
3. In **Istanza gestita di SQL** selezionare la propria sottoscrizione e verificare che in **Condizioni per l'anteprima** sia indicato **Accettate**.
4. In **Nome istanza gestita** specificare un nome. 
5. Specificare un nome utente e una password di amministratore per l'istanza.
6. Selezionare il gruppo di risorse, la località e la rete virtuale per l'istanza.
7. Fare clic su **Piano tariffario** per dimensionare le risorse di calcolo e di archiviazione. Per impostazione predefinita, l'istanza ottiene 32 GB di spazio di archiviazione gratuito (aprile 2018).
8. Specificare lo spazio di archiviazione e il numero di core virtuali.
9. Fare clic su **Apply** (Applica) per salvare le impostazioni e su **Crea** per distribuire l'istanza gestita. Per visualizzare lo stato di distribuzione, fare clic su **Notifiche** e **Distribuzione in corso**.

    ![Istanza gestita](media/migrate-scenarios-lift-and-shift/mi-1.png)

Al termine della distribuzione dell'istanza gestita, nel gruppo di risorse ContosoRG vengono visualizzate due nuove risorse: il cluster virtuale per le istanze gestite e l'istanza gestita di SQL. Entrambe si trovano nella località Stati Uniti orientali 2.

![Istanza gestita](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>Passaggio 3: Ottenere un URI di firma di accesso condiviso per il Servizio Migrazione del database

Ottenere un URI di firma di accesso condiviso per consentire al Servizio Migrazione del database di accedere al contenitore dell'account di archiviazione, per caricare i file di backup usati per la migrazione dei database all'Istanza gestita di database SQL di Azure. 

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere l'account di archiviazione che include il contenitore BLOB per cui si vuole ottenere una firma di accesso condiviso. Espandere **contenitori BLOB**dell'account di archiviazione.
3. Fare clic con il pulsante destro del mouse sul contenitore e scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso).

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. In **Firma di accesso condiviso** specificare i criteri, le date di inizio e di scadenza, il fuso orario e i livelli di accesso da impostare per la risorsa. Fare quindi clic su **Crea**.

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. Verrà visualizzata una seconda finestra di dialogo con il contenitore BLOB, insieme all'URL e alle stringhe di query che è possibile usare per accedere alla risorsa di archiviazione. Selezionare **Copia** per copiare i valori e conservarli in un luogo sicuro. Saranno necessari per la configurazione del Servizio Migrazione del database.

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>Passaggio 4: Preparare il Servizio Migrazione del database

Si registra il provider di migrazione del database e si crea un'istanza.

### <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Selezionare **Provider di risorse** nella propria sottoscrizione. 
2. Cercare "migration" e quindi, a destra di Microsoft.DataMigration, selezionare **Registra**. 


### <a name="create-an-instance"></a>Creare un'istanza

1. Nel portale di Azure selezionare **+ Create a resource** (+ Crea una risorsa), cercare "Servizio Migrazione del database di Azure" e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.
2. Nella schermata Servizio Migrazione del database di Azure (anteprima) selezionare **Crea**.
3. Specificare un nome per il servizio, la sottoscrizione, il gruppo di risorse, la rete virtuale e il piano tariffario.
4. Selezionare **Crea** per creare il servizio.

    ![Servizio Migrazione del database](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>Passaggio 5: Preparare Azure per il servizio Site Recovery

### <a name="set-up-a-virtual-network"></a>Configurare una rete virtuale

Sono necessari una rete di Azure dove inserire le macchine virtuali di Azure create dopo il failover e un account di archiviazione di Azure dove memorizzare i dati replicati.

- Ai fini di questo scenario, si userà la rete di Azure creata in precedenza per il Servizio Migrazione del database.
- La rete usata deve trovarsi nella stessa area dell'insieme di credenziali di Site Recovery.


### <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

Site Recovery replica i dati delle macchine virtuali nell'archiviazione di Azure. Le macchine virtuali di Azure vengono create dall'archiviazione quando si esegue il failover da locale ad Azure.

1. Nel menu [Portale di Azure](https://portal.azure.com) selezionare **Nuovo** > **Risorsa di archiviazione** > **Account di archiviazione**.
2. In **Crea account di archiviazione** immettere un nome per l'account. Per queste esercitazioni usare il nome **contosovmsacct1910171607**. Il nome deve essere univoco in Azure, avere una lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole.
3. In **Modello di distribuzione** selezionare **Resource Manager**.
4. In **Tipologia account** selezionare **Utilizzo generico**. In **Prestazioni** selezionare **Standard**. Non selezionare l'archivio BLOB.
5. In **Replica** selezionare l'opzione predefinita **Archiviazione con ridondanza geografica e accesso in lettura** per la ridondanza dell'archiviazione.
6. In **Sottoscrizione** selezionare la sottoscrizione in cui creare il nuovo account di archiviazione.
7. In **Gruppo di risorse** immettere un nuovo gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Per queste esercitazioni usare il nome **ContosoRG**.
8. In **Località** selezionare la posizione geografica dell'account di archiviazione. L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino. In questo scenario viene usata l'area **Stati Uniti orientali 2**.

   ![Creare un account di archiviazione](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. Selezionare **Crea** per creare l'account di archiviazione.

### <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Nel portale di Azure selezionare **Crea una risorsa** > **Monitoraggio e gestione** > **Backup e Site Recovery**.
2. In **Nome**immettere un nome descrittivo per identificare l'insieme di credenziali. Per questa esercitazione usare **ContosoVMVault**.
3. In **Gruppo di risorse** selezionare il gruppo di risorse esistente denominato **contosoRG**.
4. In **Località** immettere l'area di Azure **Stati Uniti orientali 2**.
5. Per accedere rapidamente all'insieme di credenziali dal dashboard, selezionare **Aggiungi al dashboard** > **Crea**.
Il nuovo insieme di credenziali viene visualizzato in **Dashboard** > **Tutte le risorse** e nella pagina principale **Insiemi di credenziali dei servizi di ripristino**.

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>Passaggio 6: Preparare l'ambiente VMware locale per Site Recovery

Per preparare VMware per Site Recovery è necessario:

- Preparare un account nel server vCenter o nell'host ESXi vSphere per rendere automatica l'individuazione delle macchine virtuali
- Preparare un account per l'installazione automatica del servizio Mobility nelle macchine virtuali VMware
- Preparare le macchine virtuali locali se si desidera connettersi alle VM di Azure dopo il failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. È necessario almeno un account di sola lettura.
- Controllare la replica, il failover e il failback. È necessario un account in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'attivazione di macchine virtuali.

Creare l'account come illustrato di seguito:

1. Per usare un account dedicato, creare un ruolo a livello vCenter. Assegnare un nome al ruolo, ad esempio **Azure_Site_Recovery**.
2. Assegnare al ruolo le autorizzazioni elencate nella tabella seguente.
3. Creare un utente nel server vCenter o nell'host vSphere. Assegnare il ruolo all'utente.

**Task** | **Ruolo/Autorizzazioni** | **Dettagli**
--- | --- | ---
**Individuazione di macchine virtuali** | Almeno un utente in sola lettura<br/><br/> Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).
**Replica completa, failover, failback** |  Creare un ruolo (Azure_Site_Recovery) con le autorizzazioni necessarie e quindi assegnare il ruolo a un utente o gruppo VMware<br/><br/> Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

È necessario installare il servizio Mobility nella macchina virtuale da replicare.

- Site Recovery è in grado di effettuare un'installazione push automatica di questo componente quando si abilita la replica per la macchina virtuale.
- Per l'installazione push automatica è necessario preparare un account che Site Recovery userà per accedere alla macchina virtuale.
- Si specifica questo account quando si configura la replica nella console di Azure.
- È necessario un account di dominio o locale con le autorizzazioni per l'installazione nella macchina virtuale.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Dopo il failover in Azure può essere necessario connettersi alle macchine virtuali replicate in Azure dalla rete locale.

Per connettersi alle macchine virtuali Windows tramite RDP dopo il failover, eseguire le operazioni seguenti:

1. Per accedere tramite Internet, abilitare RDP nella macchina virtuale locale prima del failover. Assicurarsi che siano aggiunte regole TCP e UDP per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.
2. Per accedere tramite VPN da sito a sito, abilitare RDP nel computer locale. RDP deve essere consentito in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**.
3. Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/kb/3031135).
4. Quando si attiva un failover, nella macchina virtuale non devono essere presenti aggiornamenti di Windows in sospeso. Se sono presenti aggiornamenti in sospeso, non sarà possibile accedere alla macchina virtuale fino al completamento dell'aggiornamento.
5. Dopo il failover nella macchina virtuale Windows di Azure selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale. Se non è possibile connettersi, controllare che la macchina virtuale sia in esecuzione e rivedere i [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).



## <a name="step-7-replicate-vms-with-site-recovery"></a>Passaggio 7: Replicare le macchine virtuali con Site Recovery

### <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. In **Insiemi di credenziali dei servizi di ripristino** selezionare il nome dell'insieme di credenziali **ContosoVMVault**.
2. In **Attività iniziali** selezionare su Site Recovery. Selezionare quindi **Preparare l'infrastruttura**.
3. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali** selezionare **Locale**.
4. Nella casella **In quale destinazione si vuole eseguire la replica dei computer** selezionare **In Azure**.
5. In **I computer sono virtuali** selezionare **Sì con VMware vSphere Hypervisor**. Selezionare **OK**.

    ![Modello OVF](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

In **Deployment Planning** (Pianificazione della distribuzione) fare clic su **Yes, I have done it** (Operazione completata) nell'elenco a discesa.

### <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine


Per configurare l'ambiente di origine, è necessario un singolo computer locale a disponibilità elevata per l'hosting dei componenti locali di Site Recovery. I componenti includono il server di configurazione e il server di elaborazione.

- Il server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
- Il server di elaborazione funge da gateway di replica. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia alla risorsa di archiviazione di Azure.
- Il server di elaborazione installa anche il servizio Mobility nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali VMware locali.


Per impostare il server di configurazione come una macchina virtuale VMware a disponibilità elevata:

- Scaricare un modello OVF (Open Virtualization Format) preparato.
- Importare il modello in VMware per creare la macchina virtuale.
- Impostare il server di configurazione e registrarlo nell'insieme di credenziali. 

Dopo la registrazione, Site Recovery individua le VM VMware locali.



#### <a name="download-the-vm-template"></a>Scaricare il modello di VM

1. Nell'insieme di credenziali passare a **Preparare l'infrastruttura** > **Origine**.
2. In **Prepara origine** selezionare **+Server di configurazione**.

    ![Scaricare il modello](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione per VMware**.
2. Scaricare il modello OVF per il server di configurazione.

    ![Scaricare OVF](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  È possibile scaricare l'ultima versione del modello del server di configurazione direttamente dall'[Area download Microsoft](https://aka.ms/asrconfigurationserver).

#### <a name="import-the-template-in-vmware"></a>Importare il modello in VMware

1. Accedere al server VMware vCenter usando il client VMWare vSphere.
2. Nel menu **File** scegliere **Deploy OVF Template** (Distribuire il modello OVF) per avviare la relativa procedura guidata. 

     ![Modello OVF](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. In **Select source** (Selezionare l'origine) immettere il percorso del modello OVF scaricato.
4. In **Review details** (Verifica dettagli) selezionare **Next** (Avanti).
5. In **Select name and folder** (Seleziona nome e cartella) e **Select configuration** (Seleziona configurazione) accettare le impostazioni predefinite.
6. Per prestazioni ottimali, in **Select storage** (Seleziona risorsa di archiviazione) selezionare **Thick Provision Eager Zeroed** in **Select virtual disk format** (Seleziona formato disco virtuale).
7. Nelle restanti pagine della procedura guidata accettare le impostazioni predefinite.
8. In **Ready to complete** (Completamento):

    * Per configurare la macchina virtuale con le impostazioni predefinite, selezionare **Power on after deployment** (Accendi al termine della distribuzione) > **Finish** (Fine).

    * Se si vuole aggiungere un'altra interfaccia di rete, deselezionare **Power on after deployment** (Accendi al termine della distribuzione). Selezionare quindi **Fine**. Per impostazione predefinita, il modello del server di configurazione viene distribuito con una singola scheda di interfaccia di rete. È possibile aggiungere altre schede di interfacce di rete dopo la distribuzione.



#### <a name="register-the-configuration-server"></a>Registrare il server di configurazione 

1. Accendere la macchina virtuale dalla console del client VMWare vSphere.
2. La macchina virtuale si avvia con la procedura di installazione di Windows Server 2016. Accettare il contratto di licenza e immettere una password amministratore.
3. Al termine dell'installazione, accedere alla macchina virtuale come amministratore.
4. Al primo accesso verrà avviato lo strumento di configurazione di Azure Site Recovery.
5. Immettere un nome che verrà usato per registrare il server di configurazione in Site Recovery. Quindi selezionare **Avanti**.

    ![Modello OVF](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. Lo strumento verifica che la macchina virtuale possa connettersi ad Azure. Dopo aver stabilito la connessione, selezionare **Accedi** per accedere alla sottoscrizione di Azure. Le credenziali devono avere accesso all'insieme di credenziali in cui si vuole registrare il server di configurazione.

    ![Modello OVF](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. Lo strumento esegue alcune attività di configurazione e quindi il riavvio.
8. Accedere di nuovo al computer. La procedura guidata per la gestione del server di configurazione viene avviata automaticamente.

#### <a name="configure-settings-and-add-the-vmware-server"></a>Configurare le impostazioni e aggiungere il server VMware

1. Nella procedura guidata per la gestione del server di configurazione selezionare **Configura la connettività** e quindi la scheda di interfaccia di rete che riceverà il traffico di replica. Selezionare quindi **Salva**. Questa impostazione non può essere modificata dopo che è stata configurata.
2. In **Seleziona l'insieme di credenziali di Servizi di ripristino** selezionare la sottoscrizione di Azure e quindi il gruppo di risorse e l'insieme di credenziali pertinenti.

    ![insieme di credenziali](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. In **Installa software di terze parti** accettare il contratto di licenza. Selezionare **Scarica e installa** per installare Server MySQL.
4. Selezionare **Installa VMware PowerCLI**. Prima di eseguire questa operazione, assicurarsi che tutte le finestre del browser siano chiuse. Selezionare quindi **Continua**.
5. In **Convalida la configurazione dell'appliance** i prerequisiti vengono verificati prima di continuare.
6. In **Configura vCenter Server/vSphere ESXi** immettere il nome di dominio completo o l'indirizzo IP del server vCenter o dell'host vSphere in cui si trovano le macchine virtuali da replicare. Immettere la porta su cui è in ascolto il server. Immettere un nome descrittivo da usare per il server VMware nell'insieme di credenziali.
7. Immettere le credenziali che verranno usate dal server di configurazione per connettersi al server VMware. Site Recovery usa queste credenziali per individuare automaticamente le macchine virtuali VMware disponibili per la replica. Selezionare **Aggiungi** e quindi **Continua**.

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. In **Configura le credenziali della macchina virtuale** immettere il nome utente e la password che verranno usati per installare automaticamente il servizio Mobility nei computer quando la replica è abilitata. Per i computer Windows, l'account deve avere privilegi di amministratore locale nei computer da replicare. 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. Selezionare **Finalizza configurazione** per completare la registrazione. 
8. Al termine della registrazione, verificare nel portale di Azure che il server di configurazione e il server VMware siano elencati nella pagina **Origine** dell'insieme di credenziali. Fare quindi clic su **OK** per configurare le impostazioni della destinazione.
9. Site Recovery si connette ai server VMware usando le impostazioni specificate e individua le macchine virtuali.

> [!NOTE]
> Possono trascorrere 15 minuti o più prima che il nome dell'account venga visualizzato nel portale. Per aggiornarlo immediatamente, selezionare **Server di configurazione** > ***nome del server*** > **Aggiorna server**.

### <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. In **Preparare l'infrastruttura** > **Destinazione** selezionare la sottoscrizione di Azure da usare.
2. Per il modello di distribuzione di destinazione, selezionare **Azure Resource Manager**.

3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.


### <a name="create-a-replication-policy"></a>Creare un criterio di replica

1. In **Preparare l'infrastruttura** > **Impostazioni della replica** > **Criteri di replica** fare clic su **Crea e associa**.
1. In **Creare i criteri di replica** immettere il nome dei criteri **VMwareRepPolicy**.
2. In **Soglia RPO** usare il valore predefinito di 60 minuti. Questo valore definisce la frequenza con cui vengono creati punti di ripristino. Se la replica continua supera questo limite, viene generato un avviso.
3. In **Conservazione del punto di ripristino** usare il valore predefinito di 24 ore per la durata del periodo di conservazione per ogni punto di ripristino. Per questa esercitazione usare 72 ore. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
4. In **Frequenza snapshot coerenti con l'app** usare il valore predefinito di 60 minuti per la frequenza con cui vengono creati snapshot coerenti con l'applicazione. Fare clic su **OK** per creare i criteri.

    ![Creare criteri di replica](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. I criteri vengono automaticamente associati al server di configurazione. 

    ![Associare i criteri di replica](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>Abilitare la replica

A questo punto si inizia la replica delle macchine virtuali. Site Recovery installa il servizio Mobility in ogni macchina virtuale per cui si è abilitata la replica. 


1. Selezionare **Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare il server di configurazione.
3. In **Tipo di computer** selezionare **Macchine virtuali**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host.
5. Selezionare il server di elaborazione (server di configurazione). Selezionare **OK**.

    ![Abilitare la replica](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vogliono creare le macchine virtuali di cui viene effettuato il failover. Scegliere il modello di distribuzione (classica o di Resource Manager) da usare in Azure per le VM di cui viene effettuato il failover.
2. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati.
3. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le macchine virtuali di Azure create dopo il failover.
4. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer.
5. Selezionare la subnet nella rete virtuale. Selezionare **OK**.

    ![Abilitare la replica](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. In **Macchine virtuali** > **Seleziona macchine virtuali** selezionare la macchina virtuale (WEBVM) desiderata. È possibile selezionare solo i computer per cui è possibile abilitare la replica. 

    ![Abilitare la replica](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. Per eseguire il monitoraggio delle macchine virtuali aggiunte, è possibile controllare l'ora dell'ultima individuazione di macchine virtuali in **Server di configurazione** > **Ora ultimo contatto**. Per aggiungere macchine virtuali senza attendere l'individuazione pianificata, evidenziare il server di configurazione senza selezionarlo e quindi selezionare **Aggiorna**. Le modifiche diventeranno effettive e verranno visualizzate nel portale dopo 15 minuti o più.
8. In **Proprietà** > **Configura proprietà** selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer. 

    ![Abilitare la replica](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti.
10. Selezionare **Abilita replica**.

    ![Abilitare la replica](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. Tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. 
12. Dopo l'esecuzione del processo **Finalizza protezione** il computer è pronto per il failover ed è visualizzato in **Panoramica** > **Essentials**.

    ![Informazioni di base](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>Passaggio 8: Eseguire la migrazione del database con il Servizio Migrazione del database

Si crea un progetto del Servizio Migrazione del database e si esegue la migrazione.


### <a name="create-a-database-migration-project"></a>Creare un progetto di Migrazione del database

1. Individuare la risorsa del Servizio Migrazione del database nel portale di Azure e aprirla.
2. Selezionare **+ New Migration Project** (+ Nuovo progetto di migrazione).
3. In **Nuovo progetto di migrazione** specificare un nome per il progetto.
4. In **Source server type** (Tipo server di origine) selezionare **SQL Server**. In **Tipo del server di destinazione** selezionare **Istanza gestita di database SQL di Azure**.
5. Fare clic su **Crea** per creare il progetto.
6. In **Dettagli origine** specificare i dettagli di connessione per SQL Server di origine locale. Fare clic su **Save**.
7. In **Dettagli destinazione** specificare i dettagli di connessione per la destinazione, ovvero l'Istanza gestita di database SQL di Azure di cui è già stato eseguito il provisioning e in cui verrà eseguita la migrazione del database locale. Fare quindi clic su **Salva**.
8. In **Riepilogo progetto** esaminare e verificare i dettagli associati al progetto di migrazione.

    ![Progetto del Servizio Migrazione del database](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>Eseguire la migrazione del database

1. Dopo la creazione del progetto, viene visualizzata la Migrazione guidata.
2. Specificare le credenziali per i server di origine e di destinazione e fare clic su **Salva**. La procedura guidata tenterà l'accesso a SQL Server locale.

    ![Migrazione guidata del database](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. In **Esegui il mapping nel database di destinazione** selezionare il database di origine di cui eseguire la migrazione. Fare quindi clic su **Salva**.

    ![Migrazione guidata del database](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. In **Dettagli destinazione** selezionare **Dettagli di destinazione noti**. Specificare il nome DNS delle istanze gestite di SQL, insieme alle credenziali. Fare quindi clic su **Salva**.

    ![Migrazione guidata del database](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. Nel progetto salvato selezionare **+Nuova attività**. Selezionare quindi **Esegui migrazione**.
6. Quando richiesto, immettere le credenziali per i server di origine e di destinazione. Fare quindi clic su **Salva**.
7. In **Mappa ai database di destinazione** selezionare il database di origine di cui eseguire la migrazione. Fare quindi clic su **Salva**
8. In **Configura le impostazioni di migrazione** > **Percorso di backup del server** specificare la condivisione di rete creata nel computer locale. Il Servizio Migrazione del database copierà i backup di origine in questa condivisione.  Tenere presente che l'account del servizio che esegue l'istanza di SQL Server di origine deve disporre dei privilegi di scrittura in questa condivisione.
9. Specificare il nome utente e la password che il Servizio Migrazione del database può rappresentare per il caricamento dei file di backup nel contenitore di archiviazione di Azure per il ripristino.
10. Specificare l'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di accedere al contenitore dell'account di archiviazione dell'utente per il caricamento dei file di backup e che verrà usato per la migrazione all'istanza gestita di database SQL di Azure.  Fare quindi clic su **Salva**.
11. In **Riepilogo della migrazione** specificare un nome per l'attività di migrazione > **Run the migration** (Esegui migrazione).
12. Nel progetto > **Panoramica** monitorare lo stato della migrazione. Al termine della migrazione verificare che i database di destinazione esistano nell'istanza gestita.


## <a name="step-9-migrate-vms-with-site-recovery"></a>Passaggio 9: Eseguire la migrazione delle macchine virtuali con Site Recovery

Prima di eseguire una migrazione completa, è consigliabile eseguire un failover di test per assicurarsi che tutto funzioni come previsto. Quando si esegue un failover di test, si verifica quanto segue:

1. Viene eseguito un controllo dei prerequisiti per verificare che siano presenti tutte le condizioni necessarie per la migrazione.
2. Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
3. Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

### <a name="run-a-test-failover"></a>Eseguire un failover di test

1. In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.
2. Nelle proprietà della macchina virtuale fare clic su **+ Failover di test**.

    ![Failover di test](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. Selezionare **Latest processed** (Punto di ripristino elaborato più recente) per eseguire il failover della VM al punto di ripristino più recente disponibile. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati, pertanto viene fornito un RTO (Recovery Time Objective) basso.
2. In **Failover di test** selezionare la rete di Azure di destinazione a cui vengono connesse le macchine virtuali di Azure dopo il failover.
3. Fare clic su **OK** per iniziare il failover. È possibile tenere traccia dello stato di avanzamento nell'insieme di credenziali > **Impostazioni** > **Processi** > **Failover di test**.
4. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione. Sarà ora possibile connettersi alla macchina virtuale replicata in Azure.
5. Per eliminare le macchine virtuali di Azure create durante il failover di test, fare clic su **Pulisci failover di test** nella VM. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.

### <a name="migrate-the-machines"></a>Eseguire la migrazione dei computer

Dopo aver verificato che il failover di test funzioni come previsto, creare un piano di ripristino per eseguire la migrazione in Azure.

### <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

1. Nell'insieme di credenziali selezionare **Piani di ripristino (Site Recovery)** > **+Piano di ripristino**.
2. In **Crea piano di ripristino** specificare un nome per il piano.
3. Scegliere il server di configurazione di origine e Azure come destinazione. Selezionare **Resource Manager** come modello di distribuzione. Nella posizione di origine devono essere presenti computer abilitati per il failover e il ripristino. 
4. In **Seleziona elementi** aggiungere i computer (WEBVM) al piano. Fare quindi clic su **OK**.
5. Fare clic su **OK** per creare il piano.

    ![Piano di ripristino](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>Eseguire un failover

1. In **Piani di ripristino** fare clic sul piano > **Failover**.
2. In **Failover** > **Punto di ripristino** selezionare l'ultimo punto di ripristino.
3. L'impostazione della chiave di crittografia non è rilevante per questo scenario.
4. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare le macchine virtuali di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.

    ![Failover](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. Assicurarsi che la macchina virtuale di Azure sia visualizzata in Azure come previsto.

    ![Failover](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. In **Elementi replicati** fare clic con il pulsante destro del mouse su macchina virtuale > **Completa la migrazione**. Il processo di migrazione viene completato, viene arrestata la replica per la macchina virtuale e viene arrestata la fatturazione di Site Recovery per la macchina virtuale.

    ![Failover](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>Aggiornare la stringa di connessione

Il passaggio finale del processo di migrazione consiste nell'aggiornare la stringa di connessione dell'applicazione in modo che punti al database in esecuzione sull'istanza gestita di SQL dopo la migrazione.

1. Individuare la nuova stringa di connessione nel portale di Azure facendo clic su **Impostazioni** > **Stringhe di connessione**.

    ![Failover](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. È necessario aggiornare questa stringa con il nome utente e la password per l'istanza gestita di SQL.
3. Dopo la configurazione della stringa, è necessario sostituire la stringa di connessione corrente nel file web.config dell'applicazione.
4. Dopo l'aggiornamento e il salvataggio del file, riavviare IIS sulla WEBVM. Per il riavvio è possibile usare il comando IISRESET /RESTART da un prompt dei comandi.
5. Dopo il riavvio di IIS, l'applicazione userà il database in esecuzione nell'istanza gestita di SQL.
6. A questo punto è possibile arrestare il computer SQLVM locale: la migrazione è completata.



## <a name="conclusion"></a>Conclusioni

In questo scenario:

> [!div class="checklist"]
> * Si è eseguita la migrazione del database locale a un'istanza gestita di SQL di Azure con il Servizio Migrazione del database.
> * Si è eseguita la migrazione delle macchine virtuali locali alle macchine virtuali di Azure con il servizio Azure Site Recovery.

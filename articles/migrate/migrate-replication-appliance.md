---
title: Appliance di replica Azure Migrate
description: Informazioni sull'appliance di replica di Azure Migrate per la migrazione VMWare basata su agente.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 4521fce6310b319d155a2f0c418cd934be7e2cb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245863"
---
# <a name="replication-appliance"></a>Appliance di replica

Questo articolo descrive l'appliance di replica usata dallo strumento di migrazione di [Azure: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) durante la migrazione di macchine virtuali VMware, macchine fisiche e macchine virtuali private/pubbliche cloud in Azure, usando la migrazione basata su agenti. 


## <a name="overview"></a>Panoramica

L'appliance di replica viene distribuita quando si configura la migrazione basata su agenti di macchine virtuali VMware o server fisici. Viene distribuito come singolo computer locale, come una macchina virtuale VMware o un server fisico. Funziona:

- **Appliance di replica:** l'appliance di replica coordina le comunicazioni e gestisce la replica dei dati per le macchine virtuali VMware locali e i server fisici che si replicano in Azure.Replication appliance : The replication appliance coordinates communications, and manages data replication, for on-premises VMware VMs and physical servers replicating to Azure.
- **Server di elaborazione**: il server di elaborazione, installato per impostazione predefinita nell'appliance di replica ed esegue le operazioni seguenti:
    - **Gateway di replica**: funge da gateway di replica. Riceve i dati di replica dai computer abilitati per la replica. It optimizes replication data with caching, compression, and encryption, and sends it to Azure.
    - **Installazione dell'agente**: esegue un'installazione push del servizio Mobility. Questo servizio deve essere installato e in esecuzione in ogni computer locale che si desidera replicare per la migrazione.

## <a name="appliance-deployment"></a>Distribuzione dell'appliance

**Utilizzo** | **Dettagli**
--- |  ---
Migrazione basata su agente VM VMware | Scaricare il modello OVA dall'hub di Azure Migrate e importare in vCenter Server per creare la macchina virtuale dell'appliance.
Migrazione basata su agente di computer fisico | Se non si dispone di un'infrastruttura VMware o se non è possibile creare una macchina virtuale VMware usando un modello OVA, scaricare un programma di installazione software dall'hub di Azure Migrate ed eseguirlo per configurare il computer dell'appliance.

## <a name="appliance-requirements"></a>Requisiti dell'appliance

Quando si configura l'appliance di replica utilizzando il modello OVA fornito nell'hub di Azure Migrate, l'appliance esegue Windows Server 2016 ed è conforme ai requisiti di supporto. Se si configura l'appliance di replica manualmente in un server fisico, assicurarsi che sia conforme ai requisiti.

**Componente** | **Requisito**
--- | ---
 | **Appliance VM VMware**
PowerCLI | [PowerCLI versione 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve essere installato se l'appliance di replica è in esecuzione in una macchina virtuale VMware.
Tipo di scheda di interfaccia di rete | VMXNET3 (se l'appliance è una macchina virtuale VMware)
 | **Impostazioni hardware**
Core CPU | 8
RAM | 16 GB
Numero di dischi | Tre: il disco del sistema operativo, il disco cache del server di elaborazione e l'unità di conservazione.
Spazio libero su disco (cache) | 600 GB
Spazio libero su disco (disco di conservazione) | 600 GB
**Impostazioni software** |
Sistema operativo | Windows Server 2016 o Windows Server 2012 R2
Licenza | L'appliance viene fornita con una licenza di valutazione di Windows Server 2016, valida per 180 giorni.<br/><br/> Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della macchina virtuale dell'appliance.
Impostazioni locali del sistema operativo | Inglese (en-us)
TLS | TLS 1.2 deve essere abilitato.
.NET Framework | .NET Framework 4.6 o versione successiva deve essere installato nel computer (con crittografia avanzata abilitata.
MySQL | MySQL deve essere installato sull'appliance.<br/> MySQL deve essere installato. È possibile installarlo manualmente oppure Site Recovery può installarlo durante la distribuzione dell'appliance.
Altre app | Non eseguire altre app nell'appliance di replica.
Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Criteri di gruppo | Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Scopri di più](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nessun sito Web predefinito preesistente <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443 <br>- Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Impostazioni di rete** |
Tipo di indirizzo IP | Statico
Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati)
Tipo di scheda di interfaccia di rete | VMXNET3

## <a name="mysql-installation"></a>Installazione di MySQL 

MySQL deve essere installato nel computer dell'appliance di replica. Può essere installato utilizzando uno di questi metodi.

**Metodo** | **Dettagli**
--- | ---
Scaricare e installare manualmente | Scaricare mySQL applicazione & inserirla nella cartella C: , quindi installare manualmente.<br/> Quando si imposta l'appliance MySQL verrà visualizzato come già installato.
Senza download online | Collocare l'applicazione del programma di installazione di MySQL nella cartella C: Quando si installa l'appliance e si fa clic per scaricare e installare MySQL, il programma di installazione utilizzerà il programma di installazione aggiunto.
Scaricare e installare in Azure MigrateDownload and install in Azure Migrate | Quando si installa l'appliance e viene richiesto di immettere MySQL, selezionare **Scarica e installa**.

## <a name="url-access"></a>accesso con URL

L'appliance di replica deve accedere a questi URL.

**Url** | **Dettagli**
--- | ---
\*.backup.windowsazure.com | Usato per il coordinamento e il trasferimento dei dati replicati
\*.store.core.windows.net | Usato per il coordinamento e il trasferimento dei dati replicati
\*.blob.core.windows.net | Usato per accedere all'account di archiviazione in cui sono archiviati i dati replicati
\*.hypervrecoverymanager.windowsazure.com | Usato per il coordinamento e le operazioni di gestione della replica
https:\//management.azure.com | Usato per il coordinamento e le operazioni di gestione della replica
*.services.visualstudio.com | Utilizzato per scopi di telemetria (Facoltativo)
time.nist.gov | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
time.windows.com | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | L'installazione di OVF deve accedere a questi URL. Vengono usati per la gestione di identità e controllo di accesso da Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Per completare il download di MySQL

## <a name="port-access"></a>Accesso alla porta

**Dispositivo** | **Connessione**
--- | ---
VM | Il servizio Mobility in esecuzione nelle macchine virtuali comunica con l'appliance di replica locale (server di configurazione) sulla porta HTTPS 443 in ingresso, per la gestione della replica.<br/><br/> Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
Appliance di replica | L'appliance di replica orchestra la replica con Azure sulla porta HTTPS 443 in uscita.
Server di elaborazione | Il server di elaborazione riceve i dati di replica, li ottimizza e li crittografa e li invia all'archiviazione di Azure tramite la porta 443 in uscita.<br/> Per impostazione predefinita, il server di elaborazione viene eseguito nell'appliance di replica.


## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale, viene avviata la replica iniziale nell'archiviazione di Azure con i criteri di replica specificati. 
2. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.
3. Al termine della replica iniziale, viene avviata la replica differenziale. Le revisioni per un computer vengono registrate.
4. Le comunicazioni avvengono nel modo seguente:
    - Le macchine virtuali comunicano con l'appliance di replica sulla porta HTTPS 443 in ingresso, per la gestione della replica.
    - L'appliance di replica orchestra la replica con Azure sulla porta HTTPS 443 in uscita.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione (in esecuzione sull'appliance di replica) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
    - Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.
5. I log dei dati di replica vengono prima eseguiti in un account di archiviazione della cache in Azure.The replication data logs first arate in a cache storage account in Azure. Questi log vengono elaborati e i dati vengono archiviati in un disco gestito di Azure.These logs are processed and the data is stored in an Azure managed disk.

![Architecture](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Aggiornamenti dell'appliance

L'appliance viene aggiornata manualmente dall'hub di Azure Migrate.The appliance is upgraded manually from the Azure Migrate hub. Si consiglia di eseguire sempre la versione più recente.

1. In Azure Migra > Server > Azure Migrate: Valutazione del server, Server di infrastruttura, fare clic su **Server di configurazione.**
2. Nei **server di configurazione,** viene visualizzato un collegamento nella **versione dell'agente** quando è disponibile una nuova versione dell'appliance di replica. 
3. Scaricare il programma di installazione nel computer dell'appliance di replica e installare l'aggiornamento. Il programma di installazione rileva la versione corrente in esecuzione sull'appliance.
 
## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) configurare l'appliance di replica per la migrazione delle macchine virtuali VMware basata su agente.
- [Informazioni su come](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) configurare l'appliance di replica per i server fisici.

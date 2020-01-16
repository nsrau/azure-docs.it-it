---
title: Appliance di replica Azure Migrate
description: Informazioni sull'appliance di replica di Azure Migrate per la migrazione VMWare basata su agenti.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 574877c6a0a5ade068cff08041b29d2465430ed1
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029048"
---
# <a name="replication-appliance"></a>Appliance di replica

Questo articolo descrive l'appliance di replica usato dallo strumento di [migrazione del Server Azure migrate:](migrate-services-overview.md#azure-migrate-server-migration-tool) quando si esegue la migrazione di VM VMware, computer fisici e macchine virtuali del cloud privato/pubblico in Azure, usando la migrazione basata su agenti. 


## <a name="overview"></a>Overview

L'appliance di replica viene distribuita quando si configura la migrazione basata su agente di macchine virtuali VMware o server fisici. Viene distribuito come singolo computer locale, come macchina virtuale VMware o server fisico. Esegue:

- **Appliance di replica**: l'appliance di replica coordina le comunicazioni e gestisce la replica dei dati per le macchine virtuali VMware e i server fisici locali che eseguono la replica in Azure.
- **Server di elaborazione**: il server di elaborazione, installato per impostazione predefinita nell'appliance di replica, ed esegue le operazioni seguenti:
    - **Gateway di replica**: funge da gateway di replica. Riceve i dati di replica dai computer abilitati per la replica. Ottimizza i dati di replica con la memorizzazione nella cache, la compressione e la crittografia e li invia ad Azure.
    - **Programma**di installazione dell'agente: esegue un'installazione push del servizio Mobility. Questo servizio deve essere installato e in esecuzione in ogni computer locale che si vuole replicare per la migrazione.

## <a name="appliance-deployment"></a>Distribuzione dell'appliance

**Usata per** | **Dettagli**
--- |  ---
Migrazione basata su agente di macchine virtuali VMware | Scaricare il modello OVA dall'hub Azure Migrate e importare in server vCenter per creare la macchina virtuale dell'appliance.
Migrazione basata su Agente computer fisico | Se non si dispone di un'infrastruttura VMware o se non è possibile creare una VM VMware usando un modello OVA, scaricare un programma di installazione software dall'hub Azure Migrate ed eseguirlo per configurare il computer dell'appliance.

## <a name="appliance-requirements"></a>Requisiti del dispositivo

Quando si configura l'appliance di replica usando il modello OVA fornito nell'hub Azure Migrate, l'Appliance esegue Windows Server 2016 e soddisfa i requisiti di supporto. Se si configura manualmente l'appliance di replica in un server fisico, assicurarsi che sia conforme ai requisiti.

**Componente** | **Requisito**
--- | ---
 | **Appliance VM VMware**
PowerCLI | Se l'appliance di replica è in esecuzione in una VM VMware, è necessario installare la [versione 6,0 di PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) .
Tipo di scheda di interfaccia di rete | VMXNET3 (se il dispositivo è una macchina virtuale VMware)
 | **Impostazioni hardware**
Core CPU | 8
RAM | 16 GB
Numero di dischi | Tre: il disco del sistema operativo, il disco della cache del server di elaborazione e l'unità di conservazione.
Spazio libero su disco (cache) | 600 GB
Spazio libero su disco (disco di conservazione) | 600 GB
**Impostazioni software** |
Sistema operativo | Windows Server 2016 o Windows Server 2012 R2
Licenza | L'Appliance viene fornita con una licenza di valutazione di Windows Server 2016, valida per 180 giorni.<br/><br/> Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della VM del dispositivo.
Impostazioni locali del sistema operativo | Inglese (en-us)
TLS | TLS 1,2 deve essere abilitato.
.NET Framework | Nel computer deve essere installato .NET Framework 4,6 o versione successiva (con crittografia avanzata abilitata.
MySQL | MySQL deve essere installato nell'appliance.<br/> MySQL deve essere installato. È possibile eseguire l'installazione manualmente oppure Site Recovery possibile installarla durante la distribuzione dell'appliance.
Altre app | Non eseguire altre app nell'appliance di replica.
Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Criteri di gruppo | Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Altre informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nessun sito Web predefinito preesistente <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443 <br>- Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Impostazioni di rete** |
Tipo di indirizzo IP | Statica
Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati)
Tipo di scheda di interfaccia di rete | VMXNET3

## <a name="mysql-installation"></a>Installazione di MySQL 

MySQL deve essere installato nel computer dell'appliance di replica. Può essere installato usando uno di questi metodi.

**Metodo** | **Dettagli**
--- | ---
Scaricare e installare manualmente | Scaricare l'applicazione MySQL & posizionarla nella cartella C:\Temp\ASRSetup, quindi installarla manualmente.<br/> Quando si configura l'appliance, MySQL viene visualizzato come già installato.
Senza download online | Inserire l'applicazione MySQL Installer nella cartella C:\Temp\ASRSetup. Quando si installa il dispositivo e si fa clic per scaricare e installare MySQL, il programma di installazione userà il programma di installazione aggiunto.
Scaricare e installare in Azure Migrate | Quando si installa l'appliance e viene richiesto MySQL, selezionare **Scarica e installa**.

## <a name="url-access"></a>accesso con URL

L'appliance di replica deve accedere a questi URL.

**URL** | **Dettagli**
--- | ---
\*.backup.windowsazure.com | Usato per il coordinamento e il trasferimento dei dati replicati
\*.store.core.windows.net | Usato per il coordinamento e il trasferimento dei dati replicati
\*.blob.core.windows.net | Usato per accedere all'account di archiviazione in cui sono archiviati i dati replicati
\*.hypervrecoverymanager.windowsazure.com | Usato per il coordinamento e le operazioni di gestione della replica
https:\//management.azure.com | Usato per il coordinamento e le operazioni di gestione della replica
*.services.visualstudio.com | Utilizzato per scopi di telemetria (Facoltativo)
time.nist.gov | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
time.windows.com | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | L'installazione di OVF richiede l'accesso a questi URL. Vengono usati per la gestione di identità e controllo di accesso da Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Per completare il download di MySQL

## <a name="port-access"></a>Accesso alla porta

**Dispositivo** | **Connection**
--- | ---
VM | Il servizio Mobility in esecuzione nelle VM comunica con l'appliance di replica locale (server di configurazione) sulla porta HTTPS 443 in ingresso, per la gestione della replica.<br/><br/> Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
Appliance di replica | L'appliance di replica orchestra la replica con Azure tramite la porta HTTPS 443 in uscita.
Server di elaborazione | Il server di elaborazione riceve i dati di replica, li ottimizza e li crittografa e li invia ad archiviazione di Azure tramite la porta 443 in uscita.<br/> Per impostazione predefinita, il server di elaborazione viene eseguito nell'appliance di replica.


## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale, viene avviata la replica iniziale nell'archiviazione di Azure con i criteri di replica specificati. 
2. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.
3. Al termine della replica iniziale, viene avviata la replica differenziale. Vengono registrate le modifiche rilevate per un computer.
4. Le comunicazioni avvengono nel modo seguente:
    - Le macchine virtuali comunicano con l'appliance di replica sulla porta HTTPS 443 in ingresso, per la gestione della replica.
    - L'appliance di replica orchestra la replica con Azure tramite la porta HTTPS 443 in uscita.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione (in esecuzione nell'appliance di replica) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
    - Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.
5. I dati di replica registrano il primo terreno in un account di archiviazione della cache in Azure. Questi log vengono elaborati e i dati vengono archiviati in un disco gestito di Azure.

![Architettura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Aggiornamenti Appliance

L'Appliance viene aggiornata manualmente dall'hub Azure Migrate. Si consiglia di eseguire sempre la versione più recente.

1. In Azure Migrate server > > Azure Migrate: server Assessment, server di infrastruttura, fare clic su **server di configurazione**.
2. Nei **server di configurazione**viene visualizzato un collegamento nella **versione dell'agente** quando è disponibile una nuova versione dell'appliance di replica. 
3. Scaricare il programma di installazione nel computer dell'appliance di replica e installare l'aggiornamento. Il programma di installazione rileva la versione corrente in esecuzione nell'appliance.
 
## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) configurare l'appliance di replica per la migrazione di macchine virtuali VMware basate su agenti.
- [Informazioni su come](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) configurare l'appliance di replica per i server fisici.

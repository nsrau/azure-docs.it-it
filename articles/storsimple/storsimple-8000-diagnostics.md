---
title: Strumento di diagnostica per la risoluzione dei problemi del dispositivo StorSimple 8000 | Microsoft Docs
description: "Vengono descritte le modalità del dispositivo StorSimple e viene illustrato come utilizzare Windows PowerShell per StorSimple per modificare la modalità del dispositivo."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7199009553eb7aae31db3f913fe4de87e03d74ba
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Usare lo strumento di diagnostica StorSimple per risolvere i problemi dei dispositivi della serie 8000

## <a name="overview"></a>Panoramica

Lo strumento di diagnostica StorSimple permette di eseguire la diagnosi dei problemi legati al sistema, alle prestazioni, alla rete e all'integrità dei componenti hardware per un dispositivo StorSimple. È possibile usare lo strumento di diagnostica in vari scenari, che includono la pianificazione del carico di lavoro, la distribuzione di un dispositivo StorSimple, la valutazione dell'ambiente di rete e il calcolo delle prestazioni di un dispositivo operativo. Questo articolo offre una panoramica sullo strumento di diagnostica e ne illustra l'uso con un dispositivo StorSimple.

L'uso dello strumento di diagnostica è destinato principalmente a dispositivi StorSimple locali della serie 8000 (8100 e 8600).

## <a name="run-diagnostics-tool"></a>Eseguire lo strumento di diagnostica

Per eseguire questo strumento è possibile usare l'interfaccia di Windows PowerShell del dispositivo StorSimple. Per accedere all'interfaccia locale del dispositivo è possibile procedere in due modi:

* [Usare PuTTY per connettersi alla console seriale del dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Accedere in remoto allo strumento tramite Windows PowerShell per StorSimple](storsimple-8000-remote-connect.md).

In questo articolo si presuppone che la connessione alla console seriale del dispositivo sia stata stabilita tramite PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Per eseguire lo strumento di diagnostica

Dopo aver stabilito la connessione all'interfaccia di Windows PowerShell del dispositivo, seguire questa procedura seguente per eseguire il cmdlet.
1. Accedere alla console seriale del dispositivo seguendo i passaggi riportati in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Digitare il comando seguente:

    `Invoke-HcsDiagnostics`

    Se il parametro di ambito non è specificato, il cmdlet esegue tutti i test di diagnostica. I test riguardano il sistema, l'integrità dei componenti hardware, la rete e le prestazioni. 
    
    Per eseguire solo un determinato test, è necessario specificare il parametro di ambito. Ad esempio, per eseguire solo il test della rete, digitare:

    `Invoke-HcsDiagnostics -Scope Network`

3. Selezionare e copiare l'output dalla finestra PuTTY in un file di testo per analizzarlo ulteriormente.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenari per l'uso dello strumento di diagnostica

Usare lo strumento di diagnostica per risolvere i problemi relativi alla rete, alle prestazioni, al sistema e all'integrità dei componenti hardware del dispositivo. Di seguito sono riportati alcuni scenari possibili:

* **Dispositivo offline**: il dispositivo StorSimple serie 8000 è offline. Tuttavia, dall'interfaccia di Windows PowerShell sembra che entrambi i controller siano operativi.
    * È possibile usare lo strumento di diagnostica per determinare lo stato della rete.
         
         > [!NOTE]
         > Non usare lo strumento per valutare le impostazioni delle prestazioni e della rete in un dispositivo prima della registrazione o della configurazione tramite l'installazione guidata. Durante la registrazione e l'installazione guidata, al dispositivo viene assegnato un indirizzo IP valido. In un dispositivo non registrato è possibile eseguire questo cmdlet per valutare il sistema e l'integrità dell'hardware. Usare il parametro di ambito, ad esempio:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problemi persistenti del dispositivo**: i problemi del dispositivo sembrano persistere. Ad esempio, la registrazione ha esito negativo. I problemi possono verificarsi anche dopo che il dispositivo è stato registrato e ha funzionato per un periodo di tempo.
    * In tal caso, è possibile usare lo strumento di diagnostica per risolvere i problemi prima di inviare una richiesta di servizio al supporto tecnico Microsoft. È consigliabile eseguire lo strumento e acquisirne l'output, per inviarlo al supporto tecnico e accelerare la risoluzione dei problemi.
    * In caso di problemi relativi ai componenti hardware o ai cluster, è necessario inviare una richiesta di supporto.

* **Prestazioni ridotte del dispositivo**: il dispositivo StorSimple è lento.
    * In tal caso, eseguire il cmdlet con il parametro di ambito impostato sulle prestazioni e analizzare l'output. Vengono restituite le latenze di lettura/scrittura nel cloud. Usare le latenze segnalate come obiettivo massimo raggiungibile, tenere conto dell'overhead per l'elaborazione dei dati interna e quindi distribuire i carichi di lavoro nel sistema. Per altre informazioni, vedere la sezione [Test della rete](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Test di diagnostica e output di esempio

### <a name="hardware-test"></a>Test dell'hardware

Questo test determina lo stato dei componenti hardware, del firmware USM e del firmware del disco in esecuzione nel sistema.

* I componenti hardware segnalati sono quelli che non hanno superato il test o non sono presenti nel sistema.
* Le versioni del firmware USM e del firmware del disco vengono segnalate per il Controller 0, il Controller 1 e i componenti condivisi nel sistema. Per un elenco completo dei componenti hardware, vedere:

    * [Componenti nello chassis principale](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Componenti nello chassis EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Se il test dell'hardware segnala componenti con errori, [inviare una richiesta di servizio al supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Output di esempio del test dell'hardware per un dispositivo 8100

Di seguito è riportato un output di esempio per un dispositivo StorSimple 8100. Nel dispositivo modello 8100, lo chassis EBOD non è presente. Di conseguenza, i componenti del controller EBOD non vengono segnalati.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Test del sistema

Questo test restituisce informazioni sul sistema, sul cluster e sul servizio per il dispositivo, nonché gli aggiornamenti disponibili.

* Le informazioni sul sistema includono il modello, il numero di serie del dispositivo, il fuso orario, lo stato del controller e i dettagli sulla versione del software in esecuzione nel sistema. Per comprendere i vari parametri di sistema segnalati come output, vedere l'[Appendice: interpretazione delle informazioni sul sistema](#appendix-interpreting-system-information).

* La disponibilità di aggiornamenti segnala se la modalità normale e la modalità di manutenzione sono disponibili e i relativi nomi di pacchetto associati. Se `RegularUpdates` e `MaintenanceModeUpdates` sono `false`, non sono disponibili aggiornamenti e il dispositivo è già aggiornato.
* Le informazioni sul cluster contengono informazioni su vari componenti logici di tutti i gruppi di cluster HCS e i rispettivi stati. Se in questa sezione del report è presente un gruppo di cluster offline, [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).
* Le informazioni sul servizio includono i nomi e gli stati di tutti i servizi CiS e HCS in esecuzione nel dispositivo. Queste informazioni sono utili per il supporto tecnico Microsoft nella risoluzione dei problemi del dispositivo.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Output di esempio del test del sistema per un dispositivo 8100

Di seguito è riportato un output di esempio del test del sistema per un dispositivo 8100.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_servic         Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Test della rete

Questo test verifica lo stato di interfacce di rete, porte, connettività del server NTP e DNS, certificato SSL, credenziali dell'account di archiviazione, connettività ai server di aggiornamento e connettività del proxy Web nel dispositivo StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Esempio di output del test della rete quando è abilitata solo l'interfaccia di rete DATA0

Di seguito è riportato un output di esempio per il dispositivo 8100. L'output mostra quanto segue:
* Solo le interfacce di rete DATA0 e DATA1 sono abilitate e configurate.
* Le interfacce da DATA2 a DATA5 non sono abilitate nel portale.
* La configurazione del server DNS è valida e il dispositivo può connettersi tramite il server DNS.
* La connettività del server NTP è valida.
* Le porte 80 e 443 sono aperte, ma la porta 9354 è bloccata. In base ai [requisiti di rete del sistema](storsimple-system-requirements.md), questa porta deve essere aperta per la comunicazione del bus di servizio.
* Il certificato SSL è valido.
* Il dispositivo può connettersi all'account di archiviazione _myss8000storageacct_.
* La connettività ai server di aggiornamento è valida.
* Il proxy Web non è configurato in questo dispositivo.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Esempio di output del test della rete quando sono abilitate le interfacce di rete DATA0 e DATA1

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Test delle prestazioni

Questo test restituisce le prestazioni del cloud tramite le latenze di lettura/scrittura nel cloud per il dispositivo. È possibile usare lo strumento di diagnostica per stabilire una baseline per le prestazioni del cloud raggiungibili con StorSimple. Lo strumento indica le prestazioni massime, ovvero lo scenario migliore per le latenze di lettura/scrittura, che è possibile ottenere per la connessione.

Dal momento che lo strumento segnala le prestazioni massime raggiungibili, è possibile usare le latenze di lettura/scrittura indicate come obiettivo nella distribuzione dei carichi di lavoro.

Il test simula le dimensioni dei BLOB associati ai vari tipi di volume nel dispositivo. I normali volumi a livelli e i backup dei volumi aggiunti in locale usano dimensioni del BLOB pari a 64 KB. I volumi a livelli con l'opzione di archiviazione selezionata usano dimensioni del BLOB pari a 512 KB. Se il dispositivo contiene volumi a livelli e volumi aggiunti in locale configurati, viene eseguito solo il test corrispondente alle dimensioni del BLOB di 64 KB.

Per usare lo strumento di diagnostica, seguire questa procedura:

1.  Creare prima di tutto una combinazione di volumi a livelli e volumi a livelli con l'opzione di archiviazione selezionata. Questa azione assicura l'esecuzione dei test per entrambe le dimensioni del BLOB, 64 KB e 512 KB.

2. Eseguire il cmdlet dopo aver creato e configurato i volumi. Digitare: 

    `Invoke-HcsDiagnostics -Scope Performance`

3. Annotare le latenze di lettura/scrittura segnalate dallo strumento. Con questo test possono essere necessari alcuni minuti prima che vengano restituiti i risultati.

4. Se le latenze di connessione sono tutte comprese nell'intervallo previsto, è possibile usare le latenze segnalate dallo strumento come obiettivo massimo raggiungibile nella distribuzione dei carichi di lavoro. Tenere conto dell'overhead per l'elaborazione dei dati interna.

    Se le latenze di lettura/scrittura segnalate dallo strumento di diagnostica sono elevate:

    1. Configurare Analisi archiviazione per i servizi BLOB e analizzare l'output per ottenere informazioni sulle latenze per l'account di archiviazione di Azure. Per istruzioni dettagliate, vedere come [abilitare e configurare Analisi archiviazione](../storage/common/storage-enable-and-view-metrics.md). Se anche tali latenze sono elevate, con valori analoghi a quelli restituiti dallo strumento di diagnostica StorSimple, è necessario inviare una richiesta di servizio tramite Archiviazione di Azure.

    2. Se le latenze dell'account di archiviazione sono basse, contattare l'amministratore di rete per indagare su eventuali problemi di latenza della rete.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Output di esempio del test delle prestazioni per un dispositivo 8100

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Appendice: interpretazione delle informazioni sul sistema

La tabella riportata di seguito illustra il mapping dei vari parametri di Windows PowerShell alle informazioni sul sistema. 

| Parametro di PowerShell    | DESCRIZIONE  |
|-------------------------|------------------|
| ID istanza             | Ogni controller è associato a un identificatore univoco o un GUID.|
| NOME                    | Nome descrittivo del dispositivo configurato tramite il portale di Azure durante la distribuzione del dispositivo. Il nome descrittivo predefinito è il numero di serie del dispositivo. |
| Modello                   | Modello del dispositivo StorSimple serie 8000. Il modello può essere 8100 o 8600.|
| SerialNumber            | Numero di serie di 15 caratteri assegnato in fase di produzione. Ad esempio, 8600-SHX0991003G44HT indica quanto segue:<br> 8600: modello del dispositivo.<br>SHX: sito di produzione.<br> 0991003: prodotto specifico. <br> G44HT: ultime cinque cifre incrementate per creare numeri di serie univoci. Questo potrebbe non essere un insieme sequenziale.|
| TimeZone                | Fuso orario del dispositivo configurato nel portale di Azure durante la distribuzione del dispositivo.|
| CurrentController       | Controller a cui si è connessi tramite l'interfaccia di Windows PowerShell del dispositivo StorSimple.|
| ActiveController        | Controller attivo nel dispositivo che controlla tutte le operazioni di rete e sul disco. Può trattarsi del Controller 0 o del Controller 1.  |
| Controller0Status       | Stato del Controller 0 nel dispositivo. Lo stato del controller può essere normale, in modalità di ripristino o non raggiungibile.|
| Controller1Status       | Stato del Controller 1 nel dispositivo.  Lo stato del controller può essere normale, in modalità di ripristino o non raggiungibile.|
| SystemMode              | Stato generale del dispositivo StorSimple. Lo stato del dispositivo può essere normale, manutenzione, o autorizzazioni rimosse, che corrisponde a disattivato nel portale di Azure.|
| FriendlySoftwareVersion | Stringa descrittiva che corrisponde alla versione del software del dispositivo. Per un sistema che esegue l'aggiornamento 4, la stringa descrittiva del software sarà Aggiornamento 4.0 di StorSimple serie 8000.|
| HcsSoftwareVersion      | Versione del software HCS in esecuzione nel dispositivo. Ad esempio, la versione del software HCS corrispondente all'aggiornamento 4.0 di StorSimple serie 8000 è 6.3.9600.17820. |
| ApiVersion              | Versione del software dell'API di Windows PowerShell nel dispositivo HCS.|
| VhdVersion              | Versione del software dell'immagine produttore computer fornita con il dispositivo. Se si ripristinano le impostazioni predefinite del dispositivo, viene eseguita questa versione del software.|
| OSVersion               | Versione del software del sistema operativo Windows Server in esecuzione nel dispositivo. Il dispositivo StorSimple si basa su Windows Server 2012 R2 che corrisponde a 6.3.9600.|
| CisAgentVersion         | Versione dell'agente CiS in esecuzione nel dispositivo StorSimple. Questo agente consente di comunicare con il servizio StorSimple Manager in esecuzione in Azure.|
| MdsAgentVersion         | Versione corrispondente all'agente MDS in esecuzione nel dispositivo StorSimple. Questo agente sposta i dati verso il servizio di monitoraggio e diagnostica (MDS).|
| Lsisas2Version          | Versione corrispondente ai driver LSI nel dispositivo StorSimple.|
| Capacity                | Capacità totale del dispositivo espressa in byte.|
| RemoteManagementMode    | Indica se il dispositivo può essere gestito in remoto tramite la relativa interfaccia di Windows PowerShell. |
| FipsMode                | Indica se la modalità FIPS (Federal Information Processing Standard per gli Stati Uniti) è abilitata nel dispositivo. Lo standard FIPS 140 definisce gli algoritmi di crittografia approvati per l'uso da parte dei sistemi del governo federale degli Stati Uniti per la protezione dei dati sensibili. Per i dispositivi che eseguono l'aggiornamento 4 o versione successiva, la modalità FIPS è abilitata per impostazione predefinita. |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [sintassi del cmdlet Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Altre informazioni sulla [risoluzione dei problemi di distribuzione](storsimple-troubleshoot-deployment.md) nel dispositivo StorSimple.

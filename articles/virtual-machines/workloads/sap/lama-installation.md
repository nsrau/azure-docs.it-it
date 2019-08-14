---
title: Connettore SAP LaMa per Azure | Microsoft Docs
description: Gestione dei sistemi SAP in Azure tramite SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: 4a4421b87aa094306a42212f76f7590d4f139047
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68607968"
---
# <a name="sap-lama-connector-for-azure"></a>Connettore SAP LaMa per Azure

[1877727]: https://launchpad.support.sap.com/#/notes/1877727
[2343511]: https://launchpad.support.sap.com/#/notes/2343511
[2350235]: https://launchpad.support.sap.com/#/notes/2350235
[2562184]: https://launchpad.support.sap.com/#/notes/2562184
[2628497]: https://launchpad.support.sap.com/#/notes/2628497
[2445033]: https://launchpad.support.sap.com/#/notes/2445033
[2815988]: https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Informativa generale sul supporto: se è necessario il supporto per SAP LaMa o il connettore di Azure, aprire sempre con SAP un evento imprevisto relativo al componente BC-VCM-LVM-HYPERV.

SAP LaMa viene usato da molti clienti per gestire e monitorare il proprio panorama applicativo SAP. A partire da SAP LaMa 3.0 SP05, viene fornito per impostazione predefinita anche un connettore di Azure. È possibile usare questo connettore per deallocare e avviare le macchine virtuali e anche per copiare, rilocare ed eliminare i dischi gestiti. Con queste operazioni di base, è possibile rilocare, copiare, clonare e aggiornare i sistemi SAP usando SAP LaMa.

Questa guida descrive come configurare il connettore di Azure per SAP LaMa, come creare macchine virtuali da usare per installare sistemi SAP adattivi e come configurarle.

> [!NOTE]
> Il connettore è disponibile solo in SAP LaMa Enterprise Edition.

## <a name="resources"></a>Risorse

Le note SAP seguenti sono correlate all'argomento di SAP LaMa in Azure:

| Numero della nota | Titolo |
| --- | --- |
| [2343511] |Microsoft Azure connector for SAP Landscape Management (LaMa) (Connettore di Microsoft Azure per SAP LaMa) |
| [2350235] |SAP Landscape Management 3.0 - Enterprise Edition |

Leggere anche le informazioni disponibili nel [portale del supporto SAP per SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Osservazioni generali

* Assicurarsi di abilitare *Automatic Mountpoint Creation* (Creazione automatica punto di montaggio) in Setup -> Settings -> Engine (Configurazione -> Impostazioni -> Motore).  
  Se SAP LaMa monta i volumi usando SAP Adaptive Extensions in una macchina virtuale, il punto di montaggio deve essere già definito se questa impostazione non è abilitata.

* Usare una subnet separata e non usare indirizzi IP dinamici per impedire l'acquisizione inappropriata di indirizzi IP quando si distribuiscono nuove macchine virtuali e le istanze SAP non sono preparate.  
  Se nella subnet si usa l'allocazione dinamica di indirizzi IP, che viene usata anche da SAP LaMa, è possibile che la preparazione di un sistema SAP con SAP LaMa abbia esito negativo. Se un sistema SAP non è preparato, gli indirizzi IP non sono riservati e possono essere allocati ad altre macchine virtuali.

* Se si accede a host gestiti, assicurarsi di non bloccare la disinstallazione dei file System  
  Se si accede a una macchina virtuale Linux e si modifica la directory di lavoro in una directory in un punto di montaggio, ad esempio/usr/sap/AH1/ASCS00/exe, non è possibile smontare il volume e un rilocare o non preparare ha esito negativo.

## <a name="set-up-azure-connector-for-sap-lama"></a>Configurare il connettore di Azure per SAP LaMa

Il connettore di Azure viene fornito a partire da SAP LaMa 3.0 SP05. È consigliabile installare sempre il pacchetto e la patch di supporto più recenti per SAP LaMa 3.0. Il connettore di Azure usa un'entità servizio per l'autorizzazione in Microsoft Azure. Per creare un'entità servizio per SAP Landscape Management (LaMa), seguire questi passaggi.

1. Passare a https://portal.azure.com.
1. Aprire il pannello Azure Active Directory
1. Fare clic su Registrazioni per l'app
1. Fare clic su Aggiungi
1. Immettere un nome, selezionare il tipo di applicazione "app Web/API", immettere un URL di accesso (ad esempio http\/:/localhost) e fare clic su Crea.
1. L'URL di accesso non viene usato e può essere qualsiasi URL valido
1. Selezionare la nuova app e fare clic su Chiavi nella scheda Impostazioni
1. Immettere una descrizione per una nuova chiave, selezionare "Non scade mai" e fare clic su Salva
1. Annotare il valore. che viene usato come password per l'entità servizio
1. Annotare l'ID applicazione. che viene usato come nome utente per l'entità servizio

L'entità servizio non ha le autorizzazioni per accedere alle risorse di Azure per impostazione predefinita. È quindi necessario concedere all'entità servizio le autorizzazioni di accesso.

1. Passare a https://portal.azure.com.
1. Aprire il pannello Gruppi di risorse
1. Selezionare il gruppo di risorse da usare
1. Fare clic su Controllo di accesso (IAM)
1. Fare clic su Aggiungi assegnazione di ruolo
1. Selezionare il ruolo Collaboratore
1. Immettere il nome dell'applicazione creata in precedenza
1. Fare clic su Salva.
1. Ripetere i passaggi da 3 a 8 per tutti i gruppi di risorse da usare in SAP LaMa

Aprire il sito Web di SAP LaMa e passare a Infrastructure (Infrastruttura). Scegliere la scheda Cloud Managers (Gestori cloud) e fare clic su Add (Aggiungi). Selezionare Microsoft Azure Cloud Adapter e fare clic su Next (Avanti). Immettere le seguenti informazioni:

* (Label) Etichetta: scegliere un nome per l'istanza del connettore
* User Name (Nome utente): ID applicazione dell'entità servizio
* Password: chiave/password dell'entità servizio
* URL: mantenere il valore predefinito https://management.azure.com/
* Monitoring Interval (Seconds) (Intervallo di monitoraggio (secondi)): deve essere almeno 300
* Subscription ID (ID sottoscrizione): ID sottoscrizione di Azure
* Azure Active Directory Tenant ID (ID tenant di Azure Active Directory): ID del tenant di Active Directory
* Proxy host (Host proxy): nome host del proxy se SAP LaMa richiede un proxy per connettersi a Internet
* Proxy port (Porta proxy): porta TCP del proxy

Fare clic su Test Configuration (Test configurazione) per convalidare l'input. Dovrebbe essere visualizzato quanto segue:

Connection successful (Connessione riuscita): la connessione a Microsoft Cloud è stata eseguita correttamente. 7 resource groups found (only 10 groups requested) (La connessione al cloud Microsoft è stata stabilita. Sono stati trovati 7 gruppi di risorse. Sono richiesti solo 10 gruppi.)

nella parte inferiore del sito Web.

## <a name="provision-a-new-adaptive-sap-system"></a>Effettuare il provisioning di un nuovo sistema SAP adattivo

È possibile distribuire manualmente una nuova macchina virtuale o usare uno dei modelli di Azure nel [repository di avvio rapido](https://github.com/Azure/azure-quickstart-templates) che contiene modelli per [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), i [server applicazioni SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps) e il [database](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). È anche possibile usare questi modelli per effettuare il provisioning di nuovi host come parte di un'operazione di copia/clonazione di sistema e così via.

È consigliabile usare una subnet separata per tutte le macchine virtuali da gestire con SAP LaMa e non usare indirizzi IP dinamici per impedire l'acquisizione inappropriata di indirizzi IP quando si distribuiscono nuove macchine virtuali e le istanze SAP non sono preparate.

> [!NOTE]
> Se possibile, rimuovere tutte le estensioni di macchina virtuale perché possono causare runtime prolungati per lo scollegamento dei dischi da una macchina virtuale.

Assicurarsi che l'utente \<hanasid>adm, \<sapsid>adm e il gruppo sapsys siano definiti nel computer di destinazione con gli stessi ID e GID oppure usare LDAP. Abilitare e avviare il server NFS sulle macchine virtuali da usare per l'esecuzione di SAP NetWeaver (A)SCS.

### <a name="manual-deployment"></a>Distribuzione manuale

SAP LaMa comunica con la macchina virtuale tramite SAP Host Agent. Se si distribuiscono le macchine virtuali manualmente o senza usare il modello di Azure Resource Manager dal repository di avvio rapido, assicurarsi di installare la versione più recente di SAP Host Agent e SAP Adaptive Extensions. Per altre informazioni sui livelli di patch richiesti per Azure, vedere la nota SAP [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Distribuzione manuale di una macchina virtuale Linux

Creare una nuova macchina virtuale con uno dei sistemi operativi supportati elencati nella nota SAP [2343511]. Aggiungere altre configurazioni IP per le istanze SAP. Ogni istanza richiede almeno un indirizzo IP e deve essere installata usando un nome host virtuale.

L'istanza di SAP NetWeaver ASCS richiede dischi per /sapmnt/\<SAPSID>, /usr/sap/\<SAPSID>, /usr/sap/trans e /usr/sap/\<sapsid>adm. I server applicazioni SAP NetWeaver non richiedono dischi aggiuntivi. Tutti gli elementi correlati all'istanza SAP devono essere archiviati nell'istanza di ASCS ed esportati tramite NFS, altrimenti non è attualmente possibile aggiungere altri server applicazioni usando SAP LaMa.

![SAP NetWeaver ASCS su Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Distribuzione manuale per SAP HANA

Creare una nuova macchina virtuale con uno dei sistemi operativi supportati per SAP HANA elencati nella nota SAP [2343511]. Definire una configurazione IP aggiuntiva per SAP HANA e una per ogni tenant HANA.

SAP HANA richiede dischi per /hana/shared, /hana/backup, /hana/data e /hana/log.

![SAP HANA su Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Distribuzione manuale per database Oracle su Linux

Creare una nuova macchina virtuale con uno dei sistemi operativi supportati per i database Oracle elencati nella nota SAP [2343511]. Definire una configurazione IP aggiuntiva per il database Oracle.

Il database Oracle richiede dischi per /oracle, /home/oraod1 e /home/oracle.

![Database Oracle su Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Distribuzione manuale per Microsoft SQL Server

Creare una nuova macchina virtuale con uno dei sistemi operativi supportati per Microsoft SQL Server elencati nella nota SAP [2343511]. Definire una configurazione IP aggiuntiva per l'istanza di SQL Server.

Il server di database di SQL Server richiede dischi per i file di dati e log del database e per c:\usr\sap.

![Database Oracle su Linux](media/lama/sap-lama-db-sql.png)

Assicurarsi di installare un'istanza di Microsoft ODBC Driver for SQL Server supportata in una macchina virtuale che si vuole usare per rilocare un server applicazioni SAP NetWeaver o come destinazione di una copia/clonazione di sistema.

SAP LaMa non è in grado di rilocare SQL Server e pertanto nella macchina virtuale da usare per rilocare un'istanza di database o come destinazione di una copia/clonazione di sistema deve essere preinstallato SQL Server.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Distribuzione di macchine virtuali con un modello di Azure

Da [SAP Software Marketplace](https://support.sap.com/swdc) scaricare i seguenti archivi disponibili più recenti per il sistema operativo delle macchine virtuali:

1. SAPCAR 7.21
1. SAP HOST AGENT 7.21
1. SAP ADAPTIVE EXTENSION 1.0 EXT

Scaricare inoltre i seguenti componenti dall'[Area Download Microsoft](https://www.microsoft.com/download)

1. Microsoft Visual C++ 2010 Redistributable Package (x64) (solo Windows)
1. Microsoft ODBC Driver for SQL Server (solo SQL Server)

I componenti sono necessari per distribuire il modello. Il modo più semplice per renderli disponibili per il modello consiste nel caricarli in un account di archiviazione di Azure e nel creare una firma di accesso condiviso (SAS).

I modelli sono associati ai parametri seguenti:

* sapSystemId: ID del sistema SAP. Viene usato per creare il layout del disco, ad esempio /usr/SAP/\<sapsid>.

* computerName: nome computer della nuova macchina virtuale. Questo parametro viene usato anche da SAP LaMa. Quando si usa questo modello per effettuare il provisioning di una nuova macchina virtuale come parte di una copia di sistema, SAP LaMa attende finché l'host con questo nome computer non è raggiungibile.

* osType: tipo del sistema operativo da distribuire.

* dbtype: tipo del database. Questo parametro viene usato per determinare quante altre configurazioni IP devono essere aggiunte e l'aspetto che dovrebbe avere il layout dei dischi.

* sapSystemSize: dimensione del sistema SAP da distribuire. Viene usato per determinare il tipo e la dimensione dell'istanza di macchina virtuale.

* adminUsername: nome utente per la macchina virtuale.

* adminPassword: password per la macchina virtuale. È anche possibile specificare una chiave pubblica per SSH.

* sshKeyData: chiave SSH pubblica per le macchine virtuali. Supportato solo per sistemi operativi Linux.

* subnetId: ID della subnet che si vuole usare.

* deployEmptyTarget: è possibile distribuire una destinazione vuota se si vuole usare la macchina virtuale come destinazione per rilocare un'istanza o eseguire un'operazione simile. In questo caso non vengono collegati altri dischi o configurazioni IP.

* sapcarLocation: percorso dell'applicazione sapcar corrispondente al sistema operativo distribuito. sapcar viene usata per estrarre gli archivi specificati in altri parametri.

* sapHostAgentArchiveLocation: percorso dell'archivio di SAP Host Agent. SAP Host Agent viene distribuito come parte di questa distribuzione di modello.

* sapacExtLocation: percorso di SAP Adaptive Extensions. La nota SAP [2343511] elenca il livello minimo di patch richiesto per Azure.

* vcRedistLocation: percorso del runtime di VC necessario per installare SAP Adaptive Extensions. Questo parametro è necessario solo per Windows.

* odbcDriverLocation: percorso del driver ODBC che si vuole installare. È supportato solo Microsoft ODBC Driver for SQL Server.

* sapadmPassword: password per l'utente sapadm.

* sapadmId: ID utente Linux dell'utente sapadm. Non è necessario per Windows.

* sapsysGid: ID gruppo Linux del gruppo sapsys. Non è necessario per Windows.

* _artifactsLocation: URI di base in cui si trovano gli elementi richiesti da questo modello. Quando il modello viene distribuito usando gli script correlati, viene usato un percorso privato nella sottoscrizione e questo valore viene generato automaticamente. È necessario solo se non si distribuisce il modello da GitHub.

* _artifactsLocationSasToken: token SAS necessario per accedere a _artifactsLocation. Quando il modello viene distribuito usando gli script correlati, viene generato automaticamente un token SAS. È necessario solo se non si distribuisce il modello da GitHub.

### <a name="sap-hana"></a>SAP HANA

Negli esempi seguenti si presuppone di aver installato SAP HANA con l'ID sistema HN1 e il sistema SAP NetWeaver con l'ID sistema AH1. I nomi host virtuali sono hn1-db per l'istanza di HANA, ah1-db per il tenant di HANA usato dal sistema SAP NetWeaver, ah1-ascs per l'istanza di SAP NetWeaver ASCS e ah1-di-0 per il primo server applicazioni SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Installare SAP NetWeaver ASC per SAP HANA usando Azure Managed Disks

Prima di avviare SAP Software Provisioning Manager (SWPM), è necessario montare l'indirizzo IP del nome host virtuale di ASCS. A tale scopo è consigliabile usare sapacext. Se si monta l'indirizzo IP usando sapacext, assicurarsi di rimontare l'indirizzo IP dopo un riavvio.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Eseguire SWPM e usare *ah1-ascs* per *ASCS Instance Host Name* (Nome host istanza ASCS).

![Linux][Logo_Linux] Linux  
Aggiungere il parametro seguente al profilo di SAP Host Agent che si trova in /usr/sap/hostctrl/exe/host_profile. Per altre informazioni, vedere la nota SAP [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Installare SAP NetWeaver ASC per SAP HANA in Azure NetAppFiles (e) BETA

> [!NOTE]
> Questa funzionalità non è ancora disponibile. Per altre informazioni, vedere la nota SAP [2815988] (visibile solo per i clienti in anteprima).
Aprire un evento imprevisto SAP nel componente BC-VCM-LVM-HYPERV e richiedere l'aggiunta dell'adattatore di archiviazione LaMa per Azure NetApp Files anteprima

E fornisce NFS per Azure. Nel contesto di SAP LaMa questo semplifica la creazione delle istanze di ABAP Central Services (ASC) e la successiva installazione dei server applicazioni. In precedenza, l'istanza di ASC doveva fungere anche da server NFS ed è stato necessario aggiungere il parametro acosprep/nfs_paths al host_profile di SAP Hostagent.

#### <a name="anf-is-currently-available-in-these-regions"></a>E è attualmente disponibile nelle aree geografiche seguenti:

Australia orientale, Stati Uniti centrali, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti centro-meridionali, Europa occidentale e Stati Uniti occidentali 2.

#### <a name="network-requirements"></a>Requisiti di rete

E richiede una subnet delegata che deve far parte della stessa VNET dei server SAP. Di seguito è riportato un esempio per una configurazione di questo tipo.
Questa schermata mostra la creazione di VNET e della prima subnet:

![SAP LaMa creare una rete virtuale per Azure e ](media/lama/sap-lama-createvn-50.png)

Il passaggio successivo crea la subnet delegata per Microsoft. NetApp/volumes.

![SAP LaMa aggiungere una subnet delegata ](media/lama/sap-lama-addsubnet-50.png)

![Elenco di subnet di SAP LaMa ](media/lama/sap-lama-subnets.png)

A questo punto è necessario creare un account NetApp nell'portale di Azure:

![SAP LaMa-Crea account NetApp ](media/lama/sap-lama-create-netappaccount-50.png)

![Account di SAP LaMa NetApp creato ](media/lama/sap-lama-netappaccount.png)

Nell'account NetApp il pool di capacità specifica le dimensioni e il tipo di dischi per ogni pool:

![SAP LaMa creare il pool di capacità NetApp ](media/lama/sap-lama-capacitypool-50.png)

![Pool di capacità NetApp di SAP LaMa creato ](media/lama/sap-lama-capacitypool-list.png)

È ora possibile definire i volumi NFS. Poiché saranno presenti volumi per più sistemi in un pool, è necessario scegliere uno schema di denominazione autoesplicativo. L'aggiunta del SID consente di raggruppare insieme i volumi correlati. Per ASC e l'istanza As sono necessari i seguenti montaggi:/sapmnt/\<SID\>,/usr/SAP/\<SID\> e/Home/\<SID\>ADM. /Usr/sap/trans facoltativo per la directory di trasporto centrale che è almeno usata da tutti i sistemi di un panorama.

> [!NOTE]
> Durante la fase BETA il nome dei volumi deve essere univoco all'interno della sottoscrizione.

![SAP LaMa-creazione di un volume 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa-creazione di un volume 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa-creazione di un volume 3 ](media/lama/sap-lama-createvolume3-80.png)

Questa procedura deve essere ripetuta anche per gli altri volumi.

![Elenco SAP LaMa dei volumi creati ](media/lama/sap-lama-volumes.png)

Ora questi volumi devono essere montati nei sistemi in cui verrà eseguita l'installazione iniziale di SAP SWPM.

Prima di tutto è necessario creare i punti di montaggio. In questo caso il SID è AN1, pertanto è necessario eseguire i comandi seguenti:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
I volumi e verranno quindi montati con i comandi seguenti:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
I comandi di montaggio possono anche essere derivati dal portale. È necessario modificare i punti di montaggio locali.

Usare il comando df-h per verificare.

![Livello sistema operativo punti di montaggio SAP LaMa ](media/lama/sap-lama-mounts.png)

È ora necessario eseguire l'installazione con SWPM.

È necessario eseguire gli stessi passaggi per almeno un'istanza di.

Una volta completata l'installazione, il sistema deve essere individuato all'interno di SAP LaMa.

I punti di montaggio dovrebbero avere un aspetto simile al seguente per ASC e l'istanza AS:

![Punti di montaggio di SAP lama ](media/lama/sap-lama-ascs.png) in lama (questo è un esempio. Gli indirizzi IP e il percorso di esportazione sono diversi da quelli usati in precedenza.


#### <a name="install-sap-hana"></a>Installare SAP HANA

Se si installa SAP HANA tramite lo strumento da riga di comando hdblcm, usare il parametro --hostname per specificare un nome host virtuale. È necessario aggiungere l'indirizzo IP del nome host virtuale del database a un'interfaccia di rete. A tale scopo è consigliabile usare sapacext. Se si monta l'indirizzo IP usando sapacext, assicurarsi di rimontare l'indirizzo IP dopo un riavvio.

Aggiungere un altro nome host virtuale e l'indirizzo IP relativo al nome usato dai server applicazioni per connettersi al tenant di HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Eseguire l'installazione dell'istanza del database di SWPM nella macchina virtuale del server applicazioni, non nella macchina virtuale HANA. Usare *ah1-db* per *Database Host* (Host del database) nella finestra di dialogo *Database for SAP System* (Database per il sistema SAP).

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Installare il server applicazioni SAP NetWeaver per SAP HANA

Prima di avviare SAP Software Provisioning Manager (SWPM), è necessario montare l'indirizzo IP del nome host virtuale del server applicazioni. A tale scopo è consigliabile usare sapacext. Se si monta l'indirizzo IP usando sapacext, assicurarsi di rimontare l'indirizzo IP dopo un riavvio.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

È consigliabile usare il parametro dbs/hdb/hdb_use_ident del profilo di SAP NetWeaver per impostare l'identità usata per trovare la chiave nello userstore HDB. È possibile aggiungere questo parametro manualmente dopo l'installazione dell'istanza del database con SWPM oppure eseguire SWPM con

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Se si imposta il parametro manualmente, è necessario anche creare nuove voci nello userstore HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Usare *ah1-di-0* per *PAS Instance Host Name* (Nome host istanza PAS) nella finestra di dialogo *Primary Application Server Instance* (Istanza primaria server applicazioni).

#### <a name="post-installation-steps-for-sap-hana"></a>Passaggi successivi all'installazione per SAP HANA

Assicurarsi di eseguire il backup di SYSTEMDB e di tutti i database tenant prima di provare a eseguire un'operazione di copia o spostamento di tenant o di creazione di una replica di sistema.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Negli esempi seguenti si presuppone di aver installato il sistema SAP NetWeaver con l'ID sistema AS1. I nomi host virtuali sono as1-db per l'istanza di SQL Server usata dal sistema SAP NetWeaver, as1-ascs per l'istanza di SAP NetWeaver ASCS e as1-di-0 per il primo server applicazioni SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Installare SAP NetWeaver ASCS per SQL Server

Prima di avviare SAP Software Provisioning Manager (SWPM), è necessario montare l'indirizzo IP del nome host virtuale di ASCS. A tale scopo è consigliabile usare sapacext. Se si monta l'indirizzo IP usando sapacext, assicurarsi di rimontare l'indirizzo IP dopo un riavvio.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Eseguire SWPM e usare *as1-ascs* per *ASCS Instance Host Name* (Nome host istanza ASCS).

#### <a name="install-sql-server"></a>Installare SQL Server

È necessario aggiungere l'indirizzo IP del nome host virtuale del database a un'interfaccia di rete. A tale scopo è consigliabile usare sapacext. Se si monta l'indirizzo IP usando sapacext, assicurarsi di rimontare l'indirizzo IP dopo un riavvio.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Eseguire l'installazione dell'istanza di database di SWPM nella macchina virtuale di SQL Server. Usare SAPINST_USE_HOSTNAME=*as1-db* per ignorare il nome host usato per connettersi a SQL Server. Se si è distribuita la macchina virtuale usando il modello di Azure Resource Manager, assicurarsi di impostare la directory usata per i file di dati del database su *C:\sql\data* e quella per il file di log del database su *C:\sql\log*.

Verificare che l'utente *NT AUTHORITY\SYSTEM* disponga dell'accesso a SQL Server e del ruolo server *sysadmin*. Per altre informazioni, vedere le note SAP [1877727] e [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Installare il server applicazioni SAP NetWeaver

Prima di avviare SAP Software Provisioning Manager (SWPM), è necessario montare l'indirizzo IP del nome host virtuale del server applicazioni. A tale scopo è consigliabile usare sapacext. Se si monta l'indirizzo IP usando sapacext, assicurarsi di rimontare l'indirizzo IP dopo un riavvio.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Usare *as1-di-0* per *PAS Instance Host Name* (Nome host istanza PAS) nella finestra di dialogo *Primary Application Server Instance* (Istanza primaria server applicazioni).

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="errors-and-warnings-during-discover"></a>Errori e avvisi durante l'individuazione

* Autorizzazione SELECT negata
  * [Microsoft][ODBC SQL Server Driver][SQL Server]The SELECT permission was denied on the object 'log_shipping_primary_databases', database 'msdb', schema 'dbo'. [SOAPFaultException]  
  The SELECT permission was denied on the object 'log_shipping_primary_databases', database 'msdb', schema 'dbo'. (È stata negata l'autorizzazione SELECT sull'oggetto 'log_shipping_primary_databases', database 'msdb', schema 'dbo'.)
  * Soluzione  
    Verificare che *NT AUTHORITY\SYSTEM* sia in grado di accedere a SQL Server. Vedere la nota SAP [2562184].


### <a name="errors-and-warnings-for-instance-validation"></a>Errori e avvisi relativi alla convalida delle istanze

* Eccezione durante la convalida dello userstore HDB  
  * Vedere il visualizzatore log  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Eccezione nel validator con ID 'RuntimeHDBConnectionValidator' (convalida: 'VALIDATION_HDB_USERSTORE'): Impossibile recuperare l'oggetto hdbuserstore  
    HANA userstore is not in the correct location (Lo userstore HANA non si trova nella posizione corretta)
  * Soluzione  
    Verificare che il percorso /usr/sap/AH1/hdbclient/install/installation.ini sia corretto.

### <a name="errors-and-warnings-during-a-system-copy"></a>Errori e avvisi durante una copia di sistema

* Errore durante la convalida del passaggio di provisioning del sistema
  * Causa: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException durante la chiamata a '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Soluzione  
    Eseguire il backup di tutti i database nel sistema HANA di origine.

* *Avvio* del passaggio di copia del sistema dell'istanza del database
  * L'operazione dell'agente host '000D3A282BC91EE8A1D76CF1F92E2944' non è riuscita (OperationException. FaultCode: '127', Messaggio: 'Command execution failed (Esecuzione comando non riuscita). : [Microsoft][ODBC SQL Server Driver][SQL Server]User does not have permission to alter database 'AS2', the database does not exist, or the database is not in a state that allows access checks.') (Esecuzione del comando non riuscita. [Microsoft][ODBC SQL Server Driver][SQL Server]L'utente non è autorizzato a modificare il database 'AS2', il database non esiste o non è in uno stato che consente verifiche di accesso.)
  * Soluzione  
    Verificare che *NT AUTHORITY\SYSTEM* sia in grado di accedere a SQL Server. Vedere la nota SAP [2562184].

### <a name="errors-and-warnings-during-a-system-clone"></a>Errori e avvisi durante una clonazione di sistema

* Errore durante il tentativo di registrare l'agente dell'istanza nel passaggio di *registrazione e avvio forzati dell'agente dell'istanza* del server applicazioni o ASCS
  * Si è verificato un errore durante il tentativo di registrare l'agente dell'istanza. (RemoteException: 'Failed to load instance data from profile (Impossibile caricare i dati dell'istanza dal profilo) '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0':  Cannot access profile (Impossibile accedere al profilo) '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': No such file or directory.') (Il file o la directory non esiste)
  * Soluzione  
   Verificare che la condivisione sapmnt in ASCS/SCS disponga dell'accesso completo a SAP_AS1_GlobalAdmin.

* Errore durante il passaggio di *abilitazione della protezione di avvio per la clonazione*
  * Failed to open file (Impossibile aprire il file) '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Cause: No such file or directory (Causa: il file o la directory non esiste)
  * Soluzione  
    L'account computer del server applicazioni deve disporre dell'accesso in scrittura al profilo.

### <a name="errors-and-warnings-during-create-system-replication"></a>Errori e avvisi durante la creazione di una replica di sistema

* Eccezione quando si fa clic sull'opzione per creare la replica di sistema
  * Causa: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException durante la chiamata a '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Soluzione  
    Verificare se sapacext può essere eseguito come `<hanasid`>adm

* Errore quando la copia completa non è abilitata durante il passaggio di archiviazione
  * An error occurred when reporting a context attribute message for path IStorageCopyData.storageVolumeCopyList:1 and field targetStorageSystemId (Si è verificato un errore durante la segnalazione di un messaggio di attributo di contesto per il percorso IStorageCopyData.storageVolumeCopyList:1 e il campo targetStorageSystemId)
  * Soluzione  
    Ignorare gli avvisi nel passaggio e riprovare. Questo problema verrà risolto in un nuovo pacchetto/patch di supporto di SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Errori e avvisi durante la rilocazione

* Percorso '/usr/sap/AH1' non consentito per le re-esportazioni nfs
  * Per informazioni dettagliate, verificare la nota SAP [2628497].
  * Soluzione  
    Aggiungere le esportazioni ASCS nel profilo dell'agente host ASCS. Vedere la nota SAP [2628497].

* Funzione non implementata durante la rilocazione di ASCS
  * Output del comando: exportfs: host:/usr/sap/AX1: Funzione non implementata
  * Soluzione  
    Assicurarsi che il servizio del server NFS sia abilitato nella macchina virtuale di destinazione della rilocazione.

### <a name="errors-and-warnings-during-application-server-installation"></a>Errori e avvisi durante l'installazione del server applicazioni

* Errore durante l'esecuzione del passaggio SAPinst: getProfileDir
  * ERRORE: Ultimo errore segnalato dal passaggio: Caught ESAPinstException in module call: Validator of step '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' reported an error: Node \\\as1-ascs\sapmnt\AS1\SYS\profile does not exist. Start SAPinst in interactive mode to solve this problem) (ESAPinstException rilevata nella chiamata al modulo: il validator del passaggio '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' ha segnalato un errore: il nodo \as1-ascs\sapmnt\AS1\SYS\profile non esiste. Avviare SAPinst in modalità interattiva per risolvere questo problema.)
  * Soluzione  
    Verificare che SWPM sia in esecuzione con un utente che ha accesso al profilo. L'utente può essere configurato nella procedura guidata di installazione del server applicazioni.

* Errore durante l'esecuzione del passaggio SAPinst: askUnicode
  * ERRORE: Ultimo errore segnalato dal passaggio: Caught ESAPinstException in module call: Validator of step '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode' reported an error: Start SAPinst in interactive mode to solve this problem) (ESAPinstException rilevata nella chiamata al modulo: il validator del passaggio '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' ha segnalato un errore: il nodo \as1-ascs\sapmnt\AS1\SYS\profile non esiste. Avviare SAPinst in modalità interattiva per risolvere questo problema.)
  * Soluzione  
    Se si usa un kernel SAP recente, SWPM non può determinare se il sistema non è più un sistema Unicode con il server di messaggistica di ASCS. Per altri dettagli, vedere la nota SAP [2445033].  
    Questo problema verrà risolto in un nuovo pacchetto/patch di supporto di SAP LaMa.  
    Per risolvere questo problema, impostare il parametro OS_UNICODE=uc nel profilo predefinito del sistema SAP.

* Errore durante l'esecuzione del passaggio SAPinst: dCheckGivenServer
  * Errore durante l'esecuzione del passaggio SAPinst: dCheckGivenServer" version="1.0" ERRORE: (Ultimo errore segnalato dal passaggio: \<p> Installation was canceled by user. \</p> (Installazione annullata dall'utente.)
  * Soluzione  
    Verificare che SWPM sia in esecuzione con un utente che ha accesso al profilo. L'utente può essere configurato nella procedura guidata di installazione del server applicazioni.

* Errore durante l'esecuzione del passaggio SAPinst: checkClient
  * Errore durante l'esecuzione del passaggio SAPinst: checkClient" version="1.0" ERRORE: (Ultimo errore segnalato dal passaggio: \<p> Installation was canceled by user. \</p>) (Installazione annullata dall'utente.)
  * Soluzione  
    Verificare che Microsoft ODBC Driver for SQL Server sia installato nella macchina virtuale in cui si vuole installare il server applicazioni.

* Errore durante l'esecuzione del passaggio SAPinst: copyScripts
  * Ultimo errore segnalato dal passaggio: System call failed (La chiamata al metodo non è riuscita). DETAILS: Error 13 (0x0000000d) (Permission denied) in execution of system call 'fopenU' with parameter (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), line (494) in file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), stack trace: (Chiamata di sistema non riuscita. DETTAGLI: Errore 13 (0x0000000d) (Autorizzazione negata) durante l'esecuzione della chiamata di sistema 'fopenU' con il parametro (\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), riga (494), nel file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), analisi dello stack:)  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Soluzione  
    Verificare che SWPM sia in esecuzione con un utente che ha accesso al profilo. L'utente può essere configurato nella procedura guidata di installazione del server applicazioni.

* Errore durante l'esecuzione del passaggio SAPinst: askPasswords
  * Ultimo errore segnalato dal passaggio: System call failed (La chiamata al metodo non è riuscita). DETAILS: Error 5 (0x00000005) (Access is denied.) in execution of system call 'NetValidatePasswordPolicy' with parameter (...), line (359) in file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), stack trace: (Chiamata di sistema non riuscita. DETTAGLI: Errore 5 (0x00000005) (Accesso negato) durante l'esecuzione della chiamata di sistema 'NetValidatePasswordPolicy' con il parametro (...), riga (359), nel file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), analisi dello stack:)  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Soluzione  
    Assicurarsi di aggiungere una regola Host nel passaggio di *isolamento* per consentire le comunicazioni dalla macchina virtuale al controller di dominio.

## <a name="next-steps"></a>Passaggi successivi
* [Guida operativa a SAP HANA in Azure][hana-ops-guide]
* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di macchine virtuali di Azure per SAP][dbms-guide]

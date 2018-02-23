---
title: "Configurazione della disponibilità elevata con STONITH per SAP HANA in Azure (istanze di grandi dimensioni) | Microsoft Docs"
description: "Stabilire la disponibilità elevata per SAP HANA in Azure (istanze Large) in SUSE con STONITH"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d710fe24673c6ddc581d36e4f0cacdb750ff74f9
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Configurazione della disponibilità elevata in SUSE con STONITH
Questo documento contiene le istruzioni dettagliate per configurare la disponibilità elevata nel sistema operativo SUSE usando il dispositivo STONITH.

**Dichiarazione di non responsabilità:** *questa guida è stata realizzata testando la configurazione dell'ambiente delle istanze di grandi dimensioni di HANA per Microsoft, che ha avuto esito positivo. Poiché il team di gestione dei servizi Microsoft per le istanze Large di HANA non supporta il sistema operativo, potrebbe essere necessario contattare SUSE per ulteriori procedure di risoluzione dei problemi o chiarimenti in merito al sistema operativo. Il team di gestione dei servizi Microsoft configura il dispositivo STONITH e offre assistenza completa, anche per la risoluzione dei problemi relativi al dispositivo STONITH.*
## <a name="overview"></a>Panoramica
Per configurare la disponibilità elevata usando il clustering SUSE, è necessario soddisfare i prerequisiti seguenti.
### <a name="pre-requisites"></a>Prerequisiti
- Provisioning delle istanze Large di HANA effettuato
- Sistema operativo registrato
- Server di istanze Large di HANA connessi al server SMT per ottenere patch/pacchetti
- Patch più recenti per il sistema operativo installate
- NTP (server di riferimento ora) configurato
- Conoscenza e comprensione della versione più recente della documentazione di SUSE sulla configurazione della disponibilità elevata

### <a name="set-up-details"></a>Dettagli di configurazione
- In questa guida è stata usata la configurazione seguente:
- Sistema operativo: SLES 12 SP1 per SAP
- Istanze di grandi dimensioni di HANA: 2xS192 (4 socket, 2 TB)
- Versione di HANA: HANA 2.0 SP1
- Nomi dei server: sapprdhdb95 (node1) e sapprdhdb96 (node2)
- Dispositivo STONITH: dispositivo STONITH basato su iSCSI
- NTP configurato in uno dei nodi di istanze di grandi dimensioni di HANA

Quando si configurano istanze di grandi dimensioni di HANA con HSR, è possibile richiedere al team di gestione dei servizi Microsoft di configurare STONITH. Se si è già un cliente per cui è stato eseguito il provisioning delle istanze di grandi dimensioni di HANA e si ha bisogno della configurazione del dispositivo STONITH per i pannelli esistenti, è necessario fornire le informazioni seguenti al team di gestione dei servizi Microsoft nel modulo della richiesta di servizio. È possibile richiedere il modulo della richiesta di servizio tramite il technical account manager o il contatto Microsoft per l'onboarding di istanze di grandi dimensioni di HANA. Il nuovi clienti possono richiedere il dispositivo STONITH in fase di provisioning. Gli input sono disponibili nel modulo della richiesta di provisioning.

- Nome del server e indirizzo IP del server (ad esempio, myhanaserver1, 10.35.0.1)
- Posizione (ad esempio, Stati Uniti orientali)
- Nome del cliente (ad esempio, Microsoft)
- SID - Identificatore del sistema HANA (ad esempio, H11)

Dopo la configurazione del dispositivo STONITH, il team di gestione dei servizi Microsoft fornisce il nome e l'indirizzo IP del dispositivo SBD della risorsa di archiviazione iSCSI che è possibile usare per la configurazione di STONITH. 

Per configurare la disponibilità elevata end-to-end usando STONITH, è necessario seguire questa procedura:

1.  Identificare il dispositivo SBD
2.  Inizializzare il dispositivo SBD
3.  Configurazione del cluster
4.  Configurazione del watchdog softdog
5.  Aggiungere il nodo al cluster
6.  Convalidare il cluster
7.  Configurare le risorse per il cluster
8.  Testare il processo di failover

## <a name="1---identify-the-sbd-device"></a>1.   Identificare il dispositivo SBD
Questa sezione descrive come determinare il dispositivo SBD per la configurazione dopo che il team di gestione dei servizi Microsoft ha configurato STONITH. **Questa sezione si applica solo ai clienti esistenti**. Il team di gestione dei servizi Microsoft fornisce il nome del dispositivo SBD ai nuovi clienti che possono ignorare questa sezione.

1.1 Sostituire */etc/iscsi/initiatorname.isci* con 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Il team di gestione dei servizi Microsoft fornisce questa stringa. Modificare il file su **entrambi** i nodi, nonostante il numero sia diverso in ogni nodo.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Modificare */etc/iscsi/iscsid.conf*: impostare *node.session.timeo.replacement_timeout=5* e *node.startup = automatic*. Modificare il file su **entrambi** i nodi.

1.3 Eseguire il comando discovery che visualizza quattro sessioni. Eseguirlo su entrambi i nodi.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Eseguire il comando per accedere al dispositivo iSCSI e visualizzare quattro sessioni. Eseguirlo su **entrambi** i nodi.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Eseguire lo script rescan: *rescan-scsi-bus.sh*.  Vengono visualizzati i nuovi script creati automaticamente.  Eseguirlo su entrambi i nodi. Verrà visualizzato un numero LUN maggiore di zero (ad esempio: 1, 2 e così via)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Per ottenere il nome del dispositivo, eseguire il comando *fdisk –l*. Eseguirlo su entrambi i nodi. Selezionare il dispositivo con dimensioni pari a **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inizializzare il dispositivo SBD

2.1 Inizializzare il dispositivo SBD su **entrambi** i nodi

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Controllare che cosa è stato scritto nel dispositivo. Eseguire questa operazione su **entrambi** i nodi

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Configurazione del cluster
Questa sezione descrive i passaggi per configurare il cluster SUSE a disponibilità elevata.
### <a name="31-package-installation"></a>3.1 Installazione del pacchetto
3.1.1 Controllare che i modelli ha_sles e SAPHanaSR-doc siano installati. Se non sono installati, è necessario installarli. Installarli su **entrambi** i nodi.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Configurazione del cluster
3.2.1 È possibile usare il comando *ha-cluster-init* o la procedura guidata yast2 per configurare il cluster. In questo caso, è stata usata la procedura guidata yast2. Questo passaggio si esegue **solo sul nodo primario**.

Seguire yast2> High Availability (Disponibilità elevata) > Cluster ![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Fare clic su **cancel** (Annulla) perché il pacchetto halk2 è già installato.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Fare clic su **Continue** (Continua)

Valore previsto=numero di nodi distribuiti (in questo caso, 2) ![yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) Fare clic su **Next** (Avanti)
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Aggiungere i nomi dei nodi e quindi fare clic su "Add suggested files" (Aggiungi file suggeriti)

Fare clic su "Turn csync2 ON" (Attiva csync2)

Fare clic su "Generate Pre-Shared-Keys" (Genera chiavi precondivise) che apre il popup seguente

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Fare clic su **OK**.

L'autenticazione viene eseguita usando gli indirizzi IP e le chiavi precondivise in Csync2. Il file della chiave viene generato con csync2 -k /etc/csync2/key_hagroup. Il file key_hagroup, dopo essere stato creato, deve essere copiato manualmente in tutti i membri del cluster. **Assicurarsi di copiare il file da node1 a node2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Fare clic su **Next** (Avanti)
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

L'impostazione predefinita di Booting (Avvio) è "Off" (No). Impostarlo su "On" (Sì) per avviare Pacemaker durante il bootstrap. È possibile scegliere in base ai requisiti di configurazione.
Fare clic su **Next** (Avanti) per completare la configurazione del cluster.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Configurazione del watchdog softdog
Questa sezione descrive la configurazione del watchdog (softdog).

4.1 Aggiungere la riga seguente a */etc/init.d/boot.local* in **entrambi** i nodi.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Aggiornare il file */etc/sysconfig/sbd* su **entrambi** i nodi, come segue:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Caricare il modulo del kernel in **entrambi** i nodi usando il comando seguente
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Verificare che il softdog sia in esecuzione in **entrambi** i nodi, come segue:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Avviare il dispositivo SBD su **entrambi** i nodi
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Testare il daemon SBD su **entrambi** i nodi. Dopo averlo configurato in **entrambi** i nodi, vengono visualizzate due voci
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Inviare un messaggio di test a **uno** dei nodi
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 Nel **secondo** nodo (node2) è possibile controllare lo stato del messaggio
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Per adottare la configurazione SBD, aggiornare il file */etc/sysconfig/sbd*, come segue. Aggiornare il file su **entrambi** i nodi
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Avviare il servizio Pacemaker nel **nodo primario** (node1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Se il servizio Pacemaker *ha esito negativo*, vedere *Scenario 5: Il servizio Pacemaker ha esito negativo*

## <a name="5---joining-the-cluster"></a>5.   Aggiunta del cluster
Questa sezione descrive come aggiungere il nodo al cluster.

### <a name="51-add-the-node"></a>5.1 Aggiungere il nodo
Usare il comando seguente in **node2** per consentire a node2 di creare un join al cluster.
```
ha-cluster-join
```
Se viene visualizzato un *errore* durante l'aggiunta al cluster, vedere *Scenario 6: Non è possibile aggiungere node2 al cluster*.

## <a name="6---validating-the-cluster"></a>6.   Convalida del cluster

### <a name="61-start-the-cluster-service"></a>6.1 Avviare il servizio cluster
Per controllare e, facoltativamente, avviare il cluster per la prima volta in **entrambi** i nodi.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Monitorare lo stato
Eseguire il comando *crm_mon* per assicurarsi che **entrambi** i nodi siano online. È possibile eseguirlo in **qualsiasi nodo** del cluster
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) È anche possibile accedere a hawk per controllare lo stato del cluster *https://<node IP>:7630*. L'utente predefinito è hacluster e la password è linux. Se necessario, è possibile modificare la password usando il comando *passwd*.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Configurare le proprietà e le risorse del cluster 
Questa sezione descrive i passaggi per configurare le risorse del cluster.
In questo esempio è stata configurata la risorsa seguente. Per configurare le altre, se necessario, vedere la guida alla disponibilità elevata di SUSE. È necessario eseguire questa configurazione solo su **uno dei nodi**. Eseguire l'operazione nel nodo primario.

- Bootstrap del cluster
- Dispositivo STONITH
- Indirizzo IP virtuale


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Bootstrap del cluster e altre operazioni
Aggiungere il bootstrap del cluster. Creare il file e aggiungere il testo seguente:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Aggiungere la configurazione al cluster.
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 Dispositivo STONITH
Aggiungere la risorsa STONITH. Creare il file e aggiungere il testo seguente.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15" \
op monitor interval="15" timeout="15"
```
Aggiungere la configurazione al cluster.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 Indirizzo IP virtuale
Aggiungere l'IP virtuale della risorsa. Creare il file e aggiungere il testo seguente.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Aggiungere la configurazione al cluster.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 Convalidare le risorse

Quando si esegue il comando *crm_mon*, è possibile visualizzare le due risorse.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

È anche possibile visualizzare lo stato all'indirizzo *https://<node IP address>:7630/cib/live/state*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Test del processo di failover
Per testare il processo di failover, arrestare il servizio Pacemaker in node1. Viene effettuato il failover delle risorse in node2.
```
Service pacemaker stop
```
Arrestare ora il servizio Pacemaker in **node2**. È stato effettuato il failover delle risorse in **node1**

**Prima del failover**
![Before-failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**Dopo il failover**
![after-failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. risoluzione dei problemi
Questa sezione descrive alcuni scenari di errore che possono verificarsi durante la configurazione. anche se non necessariamente.

### <a name="scenario-1-cluster-node-not-online"></a>Scenario 1: Nodo del cluster non online
Se uno dei nodi non risulta online in Cluster Manager, seguire questa procedura per portarlo online.

Avviare il servizio iSCSI
```
service iscsid start
```

Ora dovrebbe essere possibile accedere a tale nodo iSCSI
```
iscsiadm -m node -l
```
L'output previsto è simile al seguente
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenario 2: yast2 non mostra la visualizzazione grafica
In questo documento è stata usata la schermata grafica di yast2 per configurare il cluster a disponibilità elevata. Se yast2 non si apre con la finestra grafica illustrata e genera l'errore Qt, seguire questa procedura. Se si apre con la finestra grafica, è possibile ignorare la procedura.

**Error (Errore) (Error (Errore)e)**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Output previsto**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Se yast2 non si apre con la visualizzazione grafica, seguire questa procedura.

Installare i pacchetti necessari. È necessario essere connessi come utente "ROOT" e SMT deve essere configurato per scaricare/installare i pacchetti.

Per installare i pacchetti, usare yast>Software>Software Management (Gestione software)>Dependencies (Dipendenze)> opzione "Install recommended packages…" (Installa pacchetti consigliati). Lo screenshot seguente illustra le schermate previste.
>[!NOTE]
>È necessario eseguire i passaggi in entrambi i nodi, per poter accedere alla visualizzazione grafica di yast2 da entrambi i nodi.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

In Dependencies (Dipendenze) selezionare "Install Recommended Packages" (Installa pacchetti consigliati) ![yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Rivedere le modifiche e fare clic su OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

L'installazione dei pacchetti prosegue ![yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Fare clic su Next (Avanti)

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Fare clic su Finish (Fine)

È anche necessario installare i pacchetti libqt4 e libyui-qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) In Yast2 sarà ora possibile aprire la visualizzazione grafica, come illustrato qui.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenario 3: yast2 non visualizza l'opzione per la disponibilità elevata
Perché l'opzione per la disponibilità elevata sia visibile nel centro di controllo di yast2, è necessario installare i pacchetti aggiuntivi.

Usando Yast2>Software>Software management (Gestione software)>selezionare i modelli seguenti

- SAP HANA server base (Base server SAP HANA)
- C/C++ Compiler and tools (Strumenti e compilatore C/C++)
- Disponibilità elevata
- SAP Application server base (Base server applicazioni SAP)

La schermata seguente illustra i passaggi per installare i modelli.

Usando yast2 > Software > Software Management (Gestione software)

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Selezionare i modelli

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Fare clic **Accept** (Accetta)

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Fare clic su **Continue** (Continua)

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Al termine dell'installazione, fare clic su **Next** (Avanti)

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenario 4: L'installazione di HANA ha esito negativo con errore relativo agli assembly gcc
L'installazione di HANA ha esito negativo con l'errore seguente.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Per risolvere il problema, è necessario installare le librerie (libgcc_sl e libstdc++6), come segue.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenario 5: Il servizio Pacemaker ha esito negativo

Durante l'avvio del servizio Pacemaker si è verificato il problema seguente.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Per risolverlo, eliminare la riga seguente dal file */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenario 6: Non è possibile aggiungere node2 al cluster

Durante l'aggiunta di node2 al cluster esistente tramite il comando *ha-cluster-join*, si è verificato l'errore seguente.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Per risolvere il problema, usare il comando seguente in entrambi i nodi

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Dopo la correzione precedente, node2 verrà aggiunto al cluster

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Documentazione generale
Per altre informazioni sulla configurazione della disponibilità elevata di SUSE, vedere gli articoli seguenti: 

- [SAP HANA SR Performance Optimized Scenario](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf ) (Scenario di ottimizzazione delle prestazioni di SAP HANA SR)
- [Storage based fencing](https://www.suse.com/documentation/sle-ha-2/book_sleha/data/sec_ha_storage_protect_fencing.html) (Isolamento basato sull'archiviazione)
- [Blog - Uso del cluster Pacemaker per SAP HANA - parte 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - Uso del cluster Pacemaker per SAP HANA - parte 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
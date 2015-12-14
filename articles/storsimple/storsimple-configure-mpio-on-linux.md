<properties 
   pageTitle="Configurare MPIO sull'host Linux StorSimple | Microsoft Azure"
   description="Configurare MPIO in dispositivi StorSimple connessi all'host Linux che esegue CentOS 6.6"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# Configurare MPIO in un host di StorSimple che esegue CentOS

Questo articolo illustra i passaggi necessari a configurare l'I/O a percorsi multipli (MPIO) nel server host CentOS 6.6. Il server host è connesso al dispositivo Microsoft Azure StorSimple per la disponibilità elevata attraverso gli iniziatori iSCSI. L'articolo descrive in dettaglio il rilevamento automatico dei dispositivi con percorsi multipli e la configurazione specifica solo per i volumi StorSimple.

Questa procedura è applicabile a tutti i modelli di dispositivi della serie StorSimple 8000.

>[AZURE.NOTE]Questa procedura non può essere usata per un dispositivo virtuale StorSimple. Per altre informazioni, vedere Come configurare i server host per il dispositivo virtuale.

## Informazioni sui percorsi multipli 

La funzionalità di percorsi multipli consente di configurare più percorsi I/O tra un server host e un dispositivo di archiviazione. I percorsi I/O sono connessioni SAN fisiche che possono includere cavi, switch, interfacce di rete e controller separati. I percorsi multipli aggregano i percorsi I/O per configurare un nuovo dispositivo associato a tutti i percorsi aggregati.

Lo scopo dei percorsi multipli è duplice:

- **Disponibilità elevata**: fornisce un percorso alternativo se qualsiasi elemento del percorso I/O (ad esempio un cavo, uno switch, l'interfaccia di rete o il controller) non riesce.

- **Bilanciamento del carico**: a seconda della configurazione del dispositivo di archiviazione, è possibile migliorare le prestazioni rilevando carichi sui percorsi I/O e in modo dinamico con il ribilanciamento di tali carichi.


### Informazioni sui componenti dei percorsi multipli 

I percorsi multipli in Linux sono costituiti da componenti kernel e spazio utente come elencato di seguito.

- **Kernel**: il componente principale è il *mapper dei dispositivi* che reindirizza l'I/O e supporta il failover per i percorsi e i gruppi di percorsi.

1. **Spazio utente**: si tratta di *strumenti per percorsi multipli* che gestiscono i dispositivi a percorsi multipli, indicando il modulo a percorsi multipli del mapper dei dispositivi le operazioni da eseguire. Questi strumenti sono:

	- **Multipath**: elenca e configura i dispositivi a percorsi multipli.
		
	- **Multipathd**: daemon che esegue i percorsi multipli e monitora i percorsi.
	
	- **Devmap-name**: fornisce un nome significativo del dispositivo a udev per devmap.
 
	- **Kpartx**: mappa devmap lineari alle partizioni del dispositivo per rendere partizionabili le mappe a percorsi multipli.
	
	- **Multipath.conf**: file di configurazione per daemon a percorsi multipli usato per sovrascrivere la tabella di configurazione predefinita.

### Informazioni sul file di configurazione multipath.conf

Il file di configurazione `/etc/multipath.conf` rende configurabili dall'utente molte delle funzionalità dei percorsi multipli. Il comando `multipath` e il daemon kernel `multipathd` usano le informazioni ricavate da questo file. Il file viene consultato solo durante la configurazione dei dispositivi a percorsi multipli. Assicurarsi che tutte le modifiche vengano apportate prima di eseguire il comando `multipath`. Se successivamente si modifica il file, è necessario arrestare e avviare multipathd nuovamente per rendere effettive le modifiche.

Il file multipath.conf è composto da cinque sezioni:

- **System level defaults** *(defaults)*: è possibile ignorare i valori predefiniti a livello di sistema.

1. **Blacklisted devices** *(blacklist)*: è possibile specificare l'elenco dei dispositivi che il mapper dei dispositivi non deve controllare.

1. **Blacklist exceptions** *(blacklist\_exceptions)*: è possibile identificare i dispositivi specifici da considerare come dispositivi a percorsi multipli anche se elencati nella blacklist.

1. **Storage controller specific settings** *(devices)*: è possibile specificare impostazioni di configurazione che verranno applicate ai dispositivi con informazioni sul fornitore e sul prodotto.

1. **Device specific settings** *(multipaths)*: è possibile usare questa sezione per ottimizzare le impostazioni di configurazione per le singole unità logiche.

## Configurare percorsi multipli in dispositivi StorSimple connessi all'host Linux

Un dispositivo StorSimple connesso a un host Linux può essere configurato per l'elevata disponibilità e il bilanciamento del carico. Ad esempio, se l'host Linux ha due interfacce connesse alla SAN e il dispositivo ha due interfacce connesse alla SAN in modo che queste interfacce siano tutte nella stessa subnet, saranno disponibili quattro percorsi. Tuttavia, se ogni interfaccia DATA sull'interfaccia del dispositivo e dell'host si trova in una diversa subnet IP (non instradabile), saranno disponibili solo due percorsi. È possibile configurare percorsi multipli per trovare automaticamente tutti i percorsi disponibili, scegliere un algoritmo di bilanciamento del carico per tali percorsi, applicare impostazioni di configurazione specifiche per i volumi solo StorSimple, quindi abilitare e verificare i percorsi multipli.

La procedura seguente descrive come configurare i percorsi multipli quando un dispositivo StorSimple con due interfacce di rete viene connesso a un host con due interfacce di rete.

## Prerequisiti

Questa sezione illustra nel dettaglio i prerequisiti di configurazione per il server CentOS e il dispositivo StorSimple.

### Sull'host CentOS



1. Assicurarsi che l'host CentOS abbia due interfacce di rete abilitate. Digitare:

	`ifconfig`

	L'esempio seguente mostra l'output che si ottiene quando sull'host sono presenti due interfacce di rete (`eth0` e `eth1`).

    	[root@centosSS ~]# ifconfig
    	eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
      	inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
     	RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
    
    	eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
      	inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
      	RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
    
    	loLink encap:Local Loopback  
      	inet addr:127.0.0.1  Mask:255.0.0.0
      	inet6 addr: ::1/128 Scope:Host
      	UP LOOPBACK RUNNING  MTU:65536  Metric:1
      	RX packets:12 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:0 
      	RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)


1. Installare *iSCSI-initiator-utils* sul server CentOS. Seguire questa procedura per installare *iSCSI-initiator-utils*.

	1. Accedere come `root` all'host CentOS.

	1. Installare *iSCSI-initiator-utils*. Digitare:
		
		`yum install iscsi-initiator-utils`


	1. Dopo aver correttamente installato *iSCSI-initiator-utils*, avviare il servizio iSCSI. Digitare:

		`service iscsid start`

		A volte, `iscsid` potrebbe non avviarsi e può rendersi necessaria l'opzione `--force`

	1. Per assicurarsi che l'iniziatore iSCSI sia abilitato durante la fase di avvio, usare il comando `chkconfig` per abilitare il servizio.

		`chkconfig iscsi on`


	1. Per verificare che sia stato correttamente configurato, eseguire il comando:
	
		`chkconfig --list | grep iscsi`
	
		Di seguito è riportato un output di esempio.

			iscsi   0:off   1:off   2:on3:on4:on5:on6:off
			iscsid  0:off   1:off   2:on3:on4:on5:on6:off

		Nell'esempio precedente, è possibile vedere che l'ambiente iSCSI verrà eseguito in fase di avvio su livelli di esecuzione 2, 3, 4 e 5.


1. Installare *device-mapper-multipath*. Digitare:

	`yum install device-mapper-multipath`

	Viene avviata l'installazione. Digitare **Y** per continuare quando viene richiesto di confermare.



### Sul dispositivo StorSimple

Il dispositivo StorSimple deve avere:

- Almeno due interfacce abilitate per iSCSI. Per verificare che le due interfacce siano abilitate per iSCSI sul dispositivo StorSimple, seguire questa procedura nel portale di Azure classico del dispositivo StorSimple:

	1. Accedere al portale classico del dispositivo StorSimple.

	1. Selezionare il servizio StorSimple Manager, fare clic su **Dispositivi** e scegliere lo specifico dispositivo StorSimple. Fare clic su **Configura** e verificare le impostazioni di interfaccia di rete. Di seguito è riportata una schermata con due interfacce di rete abilitate per iSCSI. Entrambe le interfacce di rete da 10 GbE, DATA 2 e DATA 3, sono abilitate per iSCSI.
	
		![Configurazione DATA 2 StorSimple MPIO](./media/storsimple-configure-mpio-on-linux/IC761347.png)
	
		![Configurazione DATA 3 StorSimple MPIO](./media/storsimple-configure-mpio-on-linux/IC761348.png)

		Nella pagina **Configura**

		1. Assicurarsi che entrambe le interfacce di rete siano abilitate per iSCSI. Impostare il campo **Abilitato per iSCSI** su **Sì**.
		2. Assicurarsi che le interfacce di rete abbiano la stessa velocità, cioè 1 GbE o 10 GbE.
		3. Prendere nota degli indirizzi IPv4 delle interfacce abilitate per iSCSI e salvarli per un uso successivo nell'host.


- Le interfacce iSCSI sul dispositivo StorSimple devono essere raggiungibili dal server CentOS.

	Per verificarlo, è necessario fornire gli indirizzi IP delle interfacce di rete abilitate per iSCSI StorSimple nel server host. I comandi usati e l'output corrispondente con DATA2 (10.126.162.25) e DATA3 (10.126.162.26) sono illustrati di seguito:

    	[root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    	10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    	10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target


### Configurazione hardware

Si consiglia di connettere le due interfacce di rete iSCSI in percorsi distinti per la ridondanza. La figura seguente illustra la configurazione hardware consigliata per la disponibilità elevata e il bilanciamento del carico dei percorsi multipli per il server CentOS e il dispositivo StorSimple.

![Configurazione hardware MPIO per StorSimple all'host Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Come illustrato nella figura precedente:

- Il dispositivo StorSimple ha una configurazione attiva-passiva con due controller.

- Due commutatori SAN sono connesse ai controller dei dispositivi.
 
- Due iniziatori iSCSI sono abilitati nel dispositivo StorSimple.
 
- Due interfacce di rete sono abilitate sull'host CentOS.

La configurazione sopra indicata restituirà 4 percorsi separati tra il dispositivo e l'host se l'host e le interfacce di dati sono instradabili.

>[AZURE.IMPORTANT]
>
>- È consigliabile non combinare interfacce di rete da 1 GbE e da 10 GbE per i percorsi multipli. Quando si usano due interfacce di rete, devono essere di tipo identico.
>- Sul dispositivo StorSimple, DATA0, DATA1, DATA4 e DATA5 sono interfacce da 1 GbE mentre DATA2 e DATA3 sono interfacce di rete da 10 GbE.|

## Procedura di configurazione

La procedura di configurazione per i percorsi multipli implica la configurazione dei percorsi disponibili per il rilevamento automatico, specificando l'algoritmo di bilanciamento del carico da usare, abilitando i percorsi multipli e infine verificando la configurazione. Ognuno dei passaggi precedenti viene illustrato nel dettaglio nelle sezioni seguenti.

### Passaggio 1: Configurare percorsi multipli per il rilevamento automatico

I dispositivi supportati da percorsi multipli possono essere individuati e configurati automaticamente.

1. Inizializzare il file `/etc/multipath.conf`. Digitare:

	 `Copy mpathconf --enable`
	
	Il comando precedente creerà un file `sample/etc/multipath.conf`.


1. Avviare il servizio a percorsi multipli. Digitare:

    ``Copy service multipathd start``
	
	Viene visualizzato l'output seguente:

	`Starting multipathd daemon:`

1. Abilitare il rilevamento automatico dei percorsi multipli. Digitare:

	`mpathconf --find_multipaths y`

	Verrà modificata la sezione delle impostazioni predefinite del file `multipath.conf`, come illustrato di seguito:

		defaults {
		find_multipaths yes
		user_friendly_names yes
		path_grouping_policy multibus
		}

### Passaggio 2: Configurare i percorsi multipli per volumi StorSimple

Per impostazione predefinita, tutti i dispositivi sono elencati nella blacklist del file multipath.conf neri e verranno ignorati. È necessario creare eccezioni di blacklist per consentire percorsi multipli per volumi dai dispositivi StorSimple.

1. Modificare il file `/etc/mulitpath.conf`. Digitare:

	`vi /etc/multipath.conf`

1. Trovare la sezione blacklist\_exceptions nel file multipath.conf. Il dispositivo StorSimple deve essere elencato come eccezione di blacklist in questa sezione. È possibile rimuovere il commento dalle righe pertinenti in questo file per modificarlo, come mostrato di seguito (usare solo il modello specifico del dispositivo in uso):

    	blacklist_exceptions {
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8100*"
    	    }
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8600*"
    	    }
    	   }

### Passaggio 3: Configurare percorsi multipli round robin

Questo algoritmo di bilanciamento del carico usa tutti i percorsi multipli disponibili per il controller attivo in modo bilanciato e round robin.

1. Modificare il file `/etc/multipath.conf`. Digitare:

	`vi /etc/multipath.conf`

1. Nella sezione `defaults` impostare `path_grouping_policy` su `multibus`. `path_grouping_policy` specifica il criterio di raggruppamento dei percorsi predefinito da applicare ai percorsi multipli non specificati. La sezione delle impostazioni predefinite avrà l'aspetto seguente.

	    defaults {
	            user_friendly_names yes
	            path_grouping_policy multibus
	    }



> [AZURE.NOTE]I valori più comuni di `path_grouping_policy` includono:
	
> - failover = 1 percorso per ogni gruppo prioritario
> - multibus = tutti i percorsi validi in 1 gruppo prioritario

### Passaggio 4: Abilitare i percorsi multipli

1. Riavviare il daemon `multipathd`. Digitare:

    `service multipathd restart`

1. Si otterrà un output come quello illustrato di seguito:

    	[root@centosSS ~]# service multipathd start
    	Starting multipathd daemon:  [OK]




### Passaggio 5: Verificare i percorsi multipli

1. Assicurarsi prima di tutto che venga stabilita la connessione iSCSI con il dispositivo StorSimple come indicato di seguito:


	1. Trovare il dispositivo StorSimple. Digitare:
		
		`iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`

		Quando l'indirizzo IP per DATA0 è 10.126.162.25 e viene aperta la porta 3260 sul dispositivo StorSimple per il traffico iSCSI in uscita, l'output è il seguente:

		    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
		    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target


		Copiare il nome qualificato iSCSI del dispositivo StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, dall'output precedente.



	1. Connettersi al dispositivo usando il nome qualificato iSCSI di destinazione. In questo caso, la destinazione iSCSI è il dispositivo StorSimple. Digitare:

		`iscsiadm -m node --login -T <IQN of iSCSI target>`

		La figura seguente mostra l'output con un nome qualificato iSCSI `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. L'output indica che è stata effettuata la connessione alle due interfacce di rete abilitate per iSCSI sul dispositivo.

		    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
	    		Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.


		Se in questo caso vengono visualizzati due percorsi e una sola interfaccia host, sarà necessario abilitare entrambe le interfacce sull'host per iSCSI. Consultare le [istruzioni dettagliate nella documentazione Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

	
	1. Un volume viene esposto al server CentOS dal dispositivo StorSimple. Per altre informazioni, vedere la procedura [Passaggio 6: Creare un volume](storsimple-deployment-walkthrough.md#step-6-create-a-volume) nel portale di Azure classico dal dispositivo StorSimple.

	1. Verificare i percorsi disponibili. Digitare:

		`multipath –l`

		L'esempio seguente illustra l'output di due interfacce di rete su un dispositivo StorSimple connesso a una singola interfaccia di rete host con due percorsi disponibili.

		    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 7:0:0:1 sdc 8:32 active undef running
    		  `- 6:0:0:1 sdd 8:48 active undef running

		L'esempio seguente illustra l'output di due interfacce di rete su un dispositivo StorSimple connesso a due interfacce di rete host con quattro percorsi disponibili.
		
		    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 17:0:0:0 sdb 8:16 active undef running
    		  |- 15:0:0:0 sdd 8:48 active undef running
    		  |- 14:0:0:0 sdc 8:32 active undef running
    		  `- 16:0:0:0 sde 8:64 active undef running

		Dopo aver configurati i percorsi, vedere le istruzioni specifiche sul sistema operativo dell'host (CentOS 6.6) per montare e formattare il volume.


## Risoluzione dei problemi relativi ai percorsi multipli

Questa sezione contiene alcuni suggerimenti utili in caso di problemi durante la configurazione dei percorsi multipli.

D: Le modifiche apportate al file `multipath.conf` non hanno effetto.

R. Se sono state apportate modifiche al file `multipath.conf`, è necessario riavviare il servizio di percorsi multipli. Digitare il seguente comando:
    
    service multipathd restart

D: Sono state abilitate due interfacce di rete sul dispositivo StorSimple e due interfacce di rete sull'host. Nell'elenco dei percorsi disponibili sono visibili solo due percorsi, mentre dovrebbero essercene quattro.

R. Assicurarsi che i due percorsi siano nella stessa subnet e instradabili. Se le interfacce di rete si trovano su VLAN diverse e non sono instradabili, verranno visualizzati solo due percorsi. Un modo per verificare questa condizione è assicurarsi che sia possibile raggiungere entrambe le interfacce host da un'interfaccia di rete nel dispositivo StorSimple. Sarà necessario [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) perché questa verifica può essere eseguita solo in una sessione di supporto.

D: Nell'elenco dei percorsi disponibili non è visualizzato alcun output.

R. In genere, la mancata visualizzazione di percorsi multipli suggerisce un problema con il daemon di percorsi multipli ed è probabile che qualsiasi problema riguardi il file `multipath.conf`.

Potrebbe anche essere opportuno verificare che si possano visualizzare alcuni dischi dopo aver eseguito la connessione alla destinazione, perché se non si riceve alcuna risposta dagli elenchi dei percorsi multipli è probabile che non sia presente alcun disco.

- Per ripetere la scansione del bus iSCSI, usare il comando seguente: 
 
	`$ rescan-scsi-bus.sh `(parte del pacchetto sg3\_utils)
 
- Digitare i comandi seguenti:

	`$ dmesg | grep sd*`
 
- Oppure

	`$ fdisk –l`
 
	Verranno restituite informazioni dettagliate sui dischi aggiunti di recente.
  
- Per determinare se si tratta di un disco StorSimple, usare i comandi seguenti:
 
	`cat /sys/block/<DISK>/device/model`
 
	Verrà restituita una stringa, che determinerà se si tratta di un disco StorSimple.

Una causa meno probabile, ma possibile, potrebbe anche essere un PID iSCSI non aggiornato. Per disconnettersi dalle sessioni iSCSI usare il comando seguente:

    iscsiadm -m node --logout -p <Target_IP>

Ripetere questo comando per tutte le interfacce di rete connesse nella destinazione iSCSI, ovvero il dispositivo StorSimple. Dopo aver effettuato la disconnessione da tutte le sessioni iSCSI, usare il nome qualificato iSCSI di destinazione per ristabilire la sessione iSCSI. Digitare il seguente comando:

    iscsiadm -m node --login -T <TARGET_IQN>


D: Come è possibile verificare che il dispositivo sia incluso nell'elenco dei dispositivi consentiti?

R. Per verificare che il dispositivo sia incluso nell'elenco dei dispositivi consentiti, usare il comando interattivo di risoluzione dei problemi seguente:

	multipathd –k
	multipathd> show devices
	available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Per altre informazioni, vedere come [usare il comando interattivo di risoluzione dei problemi per i percorsi multipli](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## Elenco di comandi utili

|Tipo|Comando|Descrizione|
|---|---|---|
|**iSCSI**|`service iscsid start`|Avviare il servizio iSCSI|
||`service iscsid stop`|Arrestare il servizio iSCSI| ||`service iscsid restart`|Riavviare il servizio iSCSI| ||`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>`|Trovare destinazioni disponibili all'indirizzo specificato| ||`iscsiadm -m node --login -T <TARGET_IQN>`|Eseguire l'accesso alla destinazione iSCSI| ||`iscsiadm -m node --logout -p <Target_IP>`|Disconnettersi dalla destinazione iSCSI| ||`cat /etc/iscsi/initiatorname.iscsi`|Stampare il nome dell'iniziatore iSCSI| ||`iscsiadm –m session –s <sessionid> -P 3`|Verificare lo stato della sessione iSCSI e il volume rilevato sull'host| ||`iscsi –m session`|Mostra tutte le sessioni iSCSI stabilite tra l'host e il dispositivo StorSimple| | | | | |**Percorsi multipli**|`service multipathd start`|Avviare daemon a percorsi multipli| ||`service multipathd stop`|Arrestare daemon a percorsi multipli| ||`service multipathd restart`|Riavviare daemon a percorsi multipli| ||`chkconfig multipathd on` </br> O </br> `mpathconf –with_chkconfig y`|Abilitare l'esecuzione del daemon a percorsi multipli in fase di avvio| ||`multipathd –k`|Avviare la console interattiva per la risoluzione dei problemi| ||`multipath –l`|Elencare connessioni e dispositivi a percorsi multipli| ||`mpathconf --enable`|Creare un file multipath.conf di esempio in `/etc/mulitpath.conf`| ||||

## Passaggi successivi

Nella configurazione di MPIO sull'host Linux può anche essere necessario consultare i seguenti documenti relativi a CentOS 6.6:

- [Configurazione di MPIO su CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
- [Guida alla formazione Linux](http://linux-training.be/files/books/LinuxAdm.pdf)

<!---HONumber=AcomDC_1203_2015-->
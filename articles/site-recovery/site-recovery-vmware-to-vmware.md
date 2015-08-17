<properties 
	pageTitle="Configurare la protezione tra siti VMware locali" 
	description="Utilizzare questo articolo per configurare la protezione tra due siti VMware usando Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="raynew"/>


# Configurare la protezione tra siti VMware locali


## Panoramica

In Azure Site Recovery, InMage Scout fornisce la replica in tempo reale tra più siti VMware locali. InMage Scout è incluso nelle sottoscrizioni del servizio Azure Site Recovery.


## Prerequisiti

- **Account Azure**: sarà necessario disporre di un account [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](pricing/free-trial/).


## Passaggio 1: Creare un insieme di credenziali

1. Accedere al [portale di gestione](https://portal.azure.com).
2. Espandere **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali di Site Recovery**.
3. Fare clic su **Crea nuovo** > **Creazione rapida**.
4. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali.
5. In **Area** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate, vedere la sezione Disponibilità a livello geografico in [Prezzi di Azure Site Recovery](pricing/details/site-recovery/).

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di Servizi di ripristino.

## Passaggio 2: Configurare l'insieme di credenziali e scaricare i componenti InMage Scout

1. Fare clic su **Create vault**.
2. Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido.
3. Dall'elenco a discesa selezionare **Tra due siti VMware locali**.
4. Scaricare InMage Scout. I file di installazione per tutti i componenti necessari sono nel file zip scaricato.


## Passaggio 3: Installare gli aggiornamenti dei componenti

Scopri gli ultimi[aggiornamenti](#updates). È necessario installare i file di aggiornamento nell'ordine seguente:

1. Server RX se è presente
2. Server di configurazione
3. Server di elaborazione
3. Server di destinazione master.
4. Server vContinuum.

Installare come segue:

1. Scaricare il file zip [aggiornamento] (http://download.microsoft.com/download/9/F/D/9FDC6001-1DD0-4C10-BDDD-8A9EBFC57FDF/ASRUpdate1.zip Scout 8.0.1). Il file zip contiene i file seguenti:

	-  RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz
	-  CX\_Windows\_8.0.1.0\_GA\_Update\_1\_3259146\_23Jun15.exe
	-  UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe
	-  UA\_RHEL6 64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz
	-  vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe
2. Estrarre i file zip
2. **Server RX**: copia**RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz**al server RX ed estrarre i file. Nella cartella estratta eseguire**/Install**.
2. **Server di elaborazione/server di configurazione**: copia**CX\_Windows\_8.0.1.0\_GA\_Update\_1\_3259146\_23Jun15.exe**per il server di configurazione e il server di elaborazione. Fare doppio clic per eseguirlo.
3. **Server master di destinazione Windows**: per aggiornare la copia di agente unificata**UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe**per il server master di destinazione. Fare doppio clic su di esso per eseguirlo. Si noti che l'agente unificato per Windows non è applicabile nel server di origine. Deve essere installato solo nel server di destinazione master di Windows.
4. **Server master di destinazione Linux**: per aggiornare la copia di agente unificata**UA\_RHEL6 64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz**per il server master di destinazione ed estrarre i file. Nella cartella estratta eseguire**/Install**.
5. **Server vContinuum**: copia**vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe**al server vContinuum. Assicurarsi che la procedura guidata vContinuum sia stata chiusa. Fare doppio clic sul file per eseguirlo.

## Passaggio 4: Configurare la replica
5. Impostare la replica tra i siti di origine e di destinazione VMware.
6. Per istruzioni, utilizzare la documentazione InMage Scout che viene scaricata con il prodotto. In alternativa è possibile accedere alla documentazione come segue:

	- [Note sulla versione](http://download.microsoft.com/download/4/5/0/45008861-4994-4708-BFCD-867736D5621A/InMage_Scout_Standard_Release_Notes.pdf)
	- [Matrice di compatibilità](http://download.microsoft.com/download/C/D/A/CDA1221B-74E4-4CCF-8F77-F785E71423C0/InMage_Scout_Standard_Compatibility_Matrix.pdf)
	- [manuale dell'utente](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
	- [manuale dell'utente RX](http://download.microsoft.com/download/A/7/7/A77504C5-D49F-4799-BBC4-4E92158AFBA4/InMage_ScoutCloud_RX_User_Guide_8.0.1.pdf)
	- [Guida all'installazione rapida](http://download.microsoft.com/download/6/8/5/685E761C-8493-42EB-854F-FE24B5A6D74B/InMage_Scout_Standard_Quick_Install_Guide.pdf)


## Aggiornamenti

### ASR Scout 8.0.1 Update 1

L'aggiornamento più recente include correzioni di bug e nuove funzionalità:

- 31 giorni di protezione gratuita per le istanze del server. Consente di testare le funzionalità o impostare un proof-of-concept.
	- Tutte le operazioni sul server, inclusi failover e failback sono gratuite per i primi 31 giorni a partire dal momento in cui un server viene protetto utilizzando ASR Scout.
	- Dal 32esimo giorno in poi, per ogni server protetto si addebiterà al sito del cliente una tariffa standard per la protezione di Azure Site Recovery.
	- E’ possibile consultare il numero di server protetti addebitati in qualsiasi momento nella pagina Dashboard delle credenziali di Azure Site Recovery.
- Aggiunto il supporto per vCLI 5.5 Update 2.
- Supporto aggiunto per i sistemi operativi Linux nel server di origine:
	- RHEL 6 Update 6
	- RHEL 5 Update 11
	- CentOS 6 Update 6
	- CentOS 5 Update 11
- Correzioni dei bug per risolvere i problemi seguenti:
	- La registrazione delle credenziali ha esito negativo per il server di configurazione o RX.
	- I volumi del cluster non vengono visualizzati come previsto quando le macchine virtuali vengono nuovamente protette durante la ripresa.
	- Il failback ha esito negativo quando il server master di destinazione si trova in un altro server ESXi dalle macchine virtuali di produzione locali.
	- Le autorizzazioni del file di configurazione vengono modificate durante l'aggiornamento a 8.0.1, influendo sulla protezione e sulle operazioni.
	- La soglia di risincronizzazione non è applicata come previsto, causando un comportamento incoerente della replica.
	- Le impostazioni RPO non vengono visualizzate correttamente nell'interfaccia di configurazione del server. Il valore di dati non compressi mostra in modo errato il valore compresso.
	-  L'operazione di rimozione non elimina come previsto nella procedura guidata vContinuum e la replica non viene eliminata dall'interfaccia del server di configurazione.
	-  Nella procedura guidata vContinuum il disco viene deselezionato automaticamente quando si fa clic su**Dettagli**nella visualizzazione del disco durante la protezione delle macchine virtuali MSCS.
	- Durante lo scenario P2V, i servizi HP, come ad esempio CIMnotify e CqMgHost non vengono spostati sul Manuale nella macchina virtuale di ripristino causando tempo di avvio aggiuntivo.
	- La macchina virtuale Linux protetta ha esito negativo quando sono presenti più di 26 dischi nel server master di destinazione.
	
## Passaggi successivi

In caso di domande, è possibile pubblicarle nel [forum relativo ai Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).<

<!---HONumber=August15_HO6-->
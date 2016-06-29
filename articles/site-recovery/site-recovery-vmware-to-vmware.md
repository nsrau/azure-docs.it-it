<properties
	pageTitle="Eseguire la replica di macchine virtuali VMware locali o server fisici in un sito secondario | Microsoft Azure"
	description="Usare questo articolo per eseguire la replica di VM VMware o server fisici Windows/Linux in un sito secondario con Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="nsoneji"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="nisoneji"/>


# Eseguire la replica di macchine virtuali VMware locali o server fisici in un sito secondario


## Panoramica

In Azure Site Recovery, InMage Scout fornisce la replica in tempo reale tra più siti VMware locali. InMage Scout è incluso nelle sottoscrizioni del servizio Azure Site Recovery.


## Prerequisiti

- **Account Azure**: sarà necessario disporre di un account [Microsoft Azure](https://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.


## Passaggio 1: Creare un insieme di credenziali

1. Accedere al [portale di gestione](https://portal.azure.com).
2. Espandere **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali di Site Recovery**.
3. Fare clic su **Crea nuovo** > **Creazione rapida**.
4. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali.
5. In **Area** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate, vedere la sezione Disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

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
5. Server di origine (solo per Windows server)

Installare come segue:

1. Scaricare il file zip di [aggiornamento](https://aka.ms/asr-scout-update3). Il file zip contiene i file seguenti:

	-  RX\_8.0.3.0\_GA\_Update\_3\_6684045\_17Mar16.tar.gz
	-  CX\_Windows\_8.0.3.0\_GA\_Update\_3\_5048668\_16Mar16.exe
	-  UA\_Windows\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.exe
	-  UA\_RHEL6-64\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.zip
	-  vCon\_Windows\_8.0.3.0\_GA\_Update\_3\_6873369\_16Mar16.exe

2. Estrarre i file zip
3. **Server RX**: copiare **RX\_8.0.3.0\_GA\_Update\_3\_6684045\_17Mar16.tar.gz** nel server RX ed estrarre il file. Nella cartella estratta eseguire **/Install**.
4. **Server di elaborazione/server di configurazione**: copiare **CX\_Windows\_8.0.3.0\_GA\_Update\_3\_5048668\_16Mar16.exe** nel server di configurazione e nel server di elaborazione. Fare doppio clic per eseguirlo.
5. **Server master di destinazione Windows**: per aggiornare la copia dell'agente unificato copiare **UA\_Windows\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.exe** nel server master di destinazione. Fare doppio clic su di esso per eseguirlo. Si noti che l'agente unificato è applicabile anche per il server di origine. Deve essere installato anche nel server di origine, come indicato di seguito.
6. **Server master di destinazione Linux**: per aggiornare l'agente unificato, copiare **UA\_RHEL6-64\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.zip** nel server master di destinazione ed estrarre i file. Nella cartella estratta eseguire **/Install**.
7. **Server vContinuum**: copiare **vCon\_Windows\_8.0.3.0\_GA\_Update\_3\_6873369\_16Mar16.exe** nel server vContinuum. Assicurarsi che la procedura guidata vContinuum sia stata chiusa. Fare doppio clic sul file per eseguirlo.
8. **Server master di origine Windows**: per aggiornare la copia dell'agente unificato copiare **UA\_Windows\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.exe** nel server master di origine. Fare doppio clic su di esso per eseguirlo. 

## Passaggio 4: Configurare la replica
1. Impostare la replica tra i siti di origine e di destinazione VMware.
2. Per istruzioni, utilizzare la documentazione InMage Scout che viene scaricata con il prodotto. In alternativa è possibile accedere alla documentazione come segue:

	- [Note sulla versione](https://aka.ms/asr-scout-release-notes)
	- [Matrice di compatibilità](https://aka.ms/asr-scout-cm)
	- [manuale dell'utente](https://aka.ms/asr-scout-user-guide)
	- [manuale dell'utente RX](https://aka.ms/asr-scout-rx-user-guide)
	- [Guida all'installazione rapida](https://aka.ms/asr-scout-quick-install-guide)


## Aggiornamenti

### ASR Scout 8.0.1 Update 3
Update 3 include le correzioni di bug e i miglioramenti seguenti:

1. Il server di configurazione e RX non riescono a registrarsi nell'insieme di credenziali di Ripristino automatico di sistema (ASR) quando si trovano dietro il proxy.
2. Il numero di ore RPO non rispettate non viene aggiornato nel report sull'integrità.
3. Il server di configurazione non si sincronizza con RX quando i dettagli dell'hardware ESX o della rete contengono caratteri UTF-8.
4. I computer controller di dominio di Windows 2008 Server R2 non si riavviano dopo il ripristino.
5. La sincronizzazione offline non funziona come previsto. 
6. Dopo il failover della macchina virtuale, l'eliminazione della coppia di replica rimane bloccata a lungo nell'interfaccia utente di CX e l'utente non può eseguire l'operazione di ripristino da failback.
7. Ottimizzazione delle operazioni per la creazione di snapshot complessive eseguite dal processo di coerenza per ridurre le disconnessioni dell'applicazione, come i client SQL.
8. Miglioramento delle prestazioni di VACP, riducendo l'utilizzo della memoria necessaria per la creazione di snapshot in Windows.
9. Il servizio di installazione push si arresta in modo anomalo quando la password è maggiore di 16 caratteri.
10. vContinuum non verifica le nuove credenziali di vCenter e non richiede conferma quando le credenziali vengono modificate.
11. Gestione cache (cachemgr) del server di destinazione master Linux non scarica i file dal server di elaborazione con la conseguente limitazione della coppia di replica.
12. Quando l'ordine dei dischi fisici del cluster MSCS non è uguale in tutti i nodi, la replica non viene impostata per alcuni volumi del cluster. <br/>Nota: per utilizzare questa correzione, il cluster deve essere protetto di nuovo.  
13. La funzionalità SMTP non funziona come previsto dopo l'aggiornamento di RX da Scout 7.1 a Scout 8.0.1.
14. Sono state aggiunte altre statistiche nel log per l'operazione di rollback per tenere traccia del tempo impiegato per completarla.
15. Aggiunta del supporto per i sistemi operativi Linux nel server di origine 
	- RHEL 6 Update 7
	- CentOS 6 Update 7 
16. CX e l'interfaccia utente di RX ora visualizzano la notifica per la coppia che passa alla modalità bitmap.
17. Le correzioni rapide per la sicurezza seguenti sono state aggiunte a RX.

**#**|**Descrizione del problema**|**Procedure di implementazione**
---|---|---
1\. |Bypass dell'autorizzazione tramite manomissione dei parametri|Accesso limitato agli utenti non applicabili
2\. |Richiesta intersito falsa|Implementazione del concetto di token di pagina generato in modo casuale per ogni pagina. <br/>In questo modo verrà visualizzata <br/>1) solo una singola istanza di accesso per lo stesso utente., br/>2) l'aggiornamento della pagina non funzionerà, con il conseguente reindirizzamento al dashboard. <br/>
3\. |Caricamento di file dannosi|Limitazione dei file a determinate estensioni. Sono consentite: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, zip
4\. | Scripting intersito persistente | Aggiunta di convalide di input


>[AZURE.NOTE]
>
>-	Tutti gli aggiornamenti di ASR sono cumulativi. Update 3 include tutte le correzioni di Update 1 e Update 2. Update 3 può essere applicato direttamente in 8.0.1 GA.
>-	Non è possibile annullare gli aggiornamenti CS e RX dopo l'applicazione al sistema.

### ASR Scout 8.0.1 - Aggiornamento del 3 dicembre 2015 (Update 2)

Le correzioni apportate in Update 2 includono:

- **Server di configurazione**: è stato risolto un problema che impediva il corretto funzionamento della funzionalità di misurazione gratuita per 31 giorni quando il server di configurazione veniva registrato in Site Recovery.
- **Agente unificato**: è stato risolto un problema dell'aggiornamento 1 relativo alla destinazione master, che comportava la mancata installazione di un aggiornamento nel server di destinazione master quando veniva aggiornato dalla versione 8.0 alla versione 8.0.1.


### ASR Scout 8.0.1 Update 1

Update 1 include correzioni di bug e nuove funzionalità:

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
	-  Nella procedura guidata vContinuum il disco viene deselezionato automaticamente quando si fa clic su **Dettagli** nella visualizzazione del disco durante la protezione delle macchine virtuali MSCS.
	- Durante lo scenario P2V, i servizi HP, come ad esempio CIMnotify e CqMgHost non vengono spostati sul Manuale nella macchina virtuale di ripristino causando tempo di avvio aggiuntivo.
	- La macchina virtuale Linux protetta ha esito negativo quando sono presenti più di 26 dischi nel server master di destinazione.

## Passaggi successivi

In caso di domande, è possibile pubblicarle nel [forum relativo ai Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

<!---HONumber=AcomDC_0615_2016-->
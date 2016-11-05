---
title: Eseguire la replica di macchine virtuali VMware locali o server fisici in un sito secondario | Microsoft Docs
description: Usare questo articolo per eseguire la replica di VM VMware o server fisici Windows/Linux in un sito secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: jwhit
editor: ''

ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: nisoneji

---
# Eseguire la replica di macchine virtuali VMware locali o server fisici in un sito secondario
## Overview
In Azure Site Recovery, InMage Scout fornisce la replica in tempo reale tra più siti VMware locali. InMage Scout è incluso nelle sottoscrizioni del servizio Azure Site Recovery.

## Prerequisiti
**Account Azure**: sarà necessario un account [Microsoft Azure](https://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.

## Passaggio 1: Creare un insieme di credenziali
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Espandere **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali di Site Recovery**.
3. Fare clic su **Crea nuovo** > **Creazione rapida**.
4. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali.
5. In **Region** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree supportate, vedere [Prezzi di Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di **Servizi di ripristino**.

## Passaggio 2: Configurare l'insieme di credenziali e scaricare i componenti InMage Scout
1. Fare clic su **Create vault**.
2. Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina **Avvio rapido**.
3. Dall'elenco a discesa selezionare **Tra due siti VMware locali**.
4. Scaricare InMage Scout. I file di installazione per tutti i componenti necessari sono nel file ZIP scaricato.

## Passaggio 3: Installare gli aggiornamenti dei componenti
Scopri gli ultimi[aggiornamenti](#updates). È necessario installare i file di aggiornamento nei server nell'ordine seguente:

1. Server RX se è presente
2. Server di configurazione
3. Server di elaborazione
4. Server di destinazione master
5. Server vContinuum
6. Server di origine (solo per Windows server)

Installare gli aggiornamenti come segue:

1. Scaricare il file ZIP di [aggiornamento](https://aka.ms/asr-scout-update3). Il file ZIP contiene i file seguenti:
   
   * RX_8.0.3.0_GA\_Update_3_6684045\_17Mar16.tar.gz
   * CX\_Windows_8.0.3.0_GA\_Update_3_5048668\_16Mar16.exe
   * UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe
   * UA\_RHEL6-64_8.0.3.0_GA\_Update_3_7101745\_04Apr16.zip
   * vCon\_Windows_8.0.3.0_GA\_Update_3_6873369\_16Mar16.exe
2. Estrarre i file ZIP.
3. **Per il server RX**: copiare **RX_8.0.3.0_GA\_Update_3_6684045\_17Mar16.tar.gz** nel server RX e decomprimerlo. Nella cartella estratta eseguire **/Install**.
4. **Per il server di configurazione e il server di elaborazione**: copiare **CX\_Windows_8.0.3.0_GA\_Update_3_5048668\_16Mar16.exe** nel server di configurazione e nel server di elaborazione. Fare doppio clic per eseguirlo.
5. **Per il server di destinazione master Windows**: per aggiornare l'agente unificato, copiare **UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe** nel server di destinazione master. Fare doppio clic per eseguirlo. Si noti che l'agente unificato è applicabile anche al server di origine. È consigliabile installarlo anche nel server di origine, come indicato più avanti in questo elenco.
6. **Per il server di destinazione master Linux**: per aggiornare l'agente unificato, copiare **UA\_RHEL6-64_8.0.3.0_GA\_Update_3_7101745\_04Apr16.zip** nel server di destinazione master e decomprimerlo. Nella cartella estratta eseguire **/Install**.
7. **Per il server vContinuum**: copiare **vCon\_Windows_8.0.3.0_GA\_Update_3_6873369\_16Mar16.exe** nel server vContinuum. Assicurarsi che la procedura guidata vContinuum sia stata chiusa. Fare doppio clic sul file per eseguirlo.
8. **Per il server di origine Windows**: per aggiornare l'agente unificato, copiare **UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe** nel server di origine. Fare doppio clic per eseguirlo.

## Passaggio 4: Configurare la replica
1. Impostare la replica tra i siti di origine e di destinazione VMware.
2. Per istruzioni, utilizzare la documentazione InMage Scout che viene scaricata con il prodotto. In alternativa, è possibile accedere alla documentazione come segue:
   
   * [Note sulla versione](https://aka.ms/asr-scout-release-notes)
   * [Matrice di compatibilità](https://aka.ms/asr-scout-cm)
   * [manuale dell'utente](https://aka.ms/asr-scout-user-guide)
   * [manuale dell'utente RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guida all'installazione rapida](https://aka.ms/asr-scout-quick-install-guide)

## Aggiornamenti
### Azure Site Recovery Scout 8.0.1 Update 3
Update 3 include le correzioni di bug e i miglioramenti seguenti:

* Il server di configurazione e RX non riescono a registrarsi nell'insieme di credenziali di Site Recovery quando si trovano dietro il proxy.
* Il numero di ore per cui l'obiettivo del punto di ripristino (RPO) non viene rispettato non viene aggiornato nel report sull'integrità.
* Il server di configurazione non si sincronizza con RX quando i dettagli dell'hardware ESX o della rete contengono caratteri UTF-8.
* I controller di dominio Windows Server 2008 R2 non si riavviano dopo il ripristino.
* La sincronizzazione offline non funziona come previsto.
* Dopo il failover della macchina virtuale (VM), l'eliminazione della coppia di replica si blocca a lungo nell'interfaccia utente di CX e gli utenti non possono completare l'operazione di failback o ripresa.
* Le operazioni per la creazione di snapshot complessive eseguite dal processo di coerenza sono state ottimizzate per ridurre le disconnessioni delle applicazioni, come i client SQL.
* Le prestazioni dello strumento di coerenza (VACP.exe) sono state migliorate riducendo l'utilizzo di memoria necessario per creare snapshot in Windows.
* Il servizio di installazione push si arresta in modo anomalo quando la password supera 16 caratteri.
* vContinuum non verifica le nuove credenziali di vCenter e non richiede conferma quando le credenziali vengono modificate.
* In Linux, il gestore della cache di destinazione master (cachemgr) non scarica i file dal server di elaborazione, con conseguente limitazione della coppia di replica.
* Quando l'ordine dei dischi fisici del cluster di failover (MSCS) non è uguale in tutti i nodi, la replica non viene impostata per alcuni volumi del cluster. <br/>Si noti che il cluster deve essere riprotetto per usufruire di questa correzione.
* La funzionalità SMTP non funziona come previsto dopo l'aggiornamento di RX da Scout 7.1 a Scout 8.0.1.
* Sono state aggiunte altre statistiche nel log per l'operazione di rollback per tenere traccia del tempo impiegato per completarla.
* È stato aggiunto il supporto per sistemi operativi Linux nel server di origine:
  * Red Hat Enterprise Linux (RHEL) 6 Update 7
  * CentOS 6 Update 7
* L'interfaccia utente di RX e CX visualizzano ora la notifica per la coppia, che passa alla modalità bitmap.
* In RX sono state aggiunte le correzioni per la sicurezza seguenti:

| **Descrizione del problema** | **Procedure di implementazione** |
| --- | --- |
| Bypass dell'autorizzazione tramite manomissione dei parametri |Accesso limitato agli utenti non applicabili. |
| Richiesta intersito falsa |Implementazione del concetto di token di pagina, generato in modo casuale per ogni pagina. <br/>In questo modo: <li> Esiste una sola istanza di accesso per lo stesso utente.</li><li>L'aggiornamento di pagina non funziona, con conseguente reindirizzamento al dashboard.</li> |
| Caricamento di file dannosi |Limitazione dei file a determinate estensioni. Le estensioni consentite sono: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip. |
| Scripting intersito persistente |Aggiunta di convalide dell'input. |

> [!NOTE]
> * Tutti gli aggiornamenti di Site Recovery sono cumulativi. Update 3 include tutte le correzioni di Update 1 e Update 2. Update 3 può essere applicato direttamente in 8.0.1 GA.
> * Non è possibile eseguire il rollback degli aggiornamenti del server di configurazione e di RX dopo che sono stati applicati nel sistema.
> 
> 

### Azure Site Recovery Scout 8.0.1 Update 2 (aggiornamento del 3 dicembre 15)
Le correzioni apportate in Update 2 includono:

* **Server di configurazione**: risoluzione di un problema che impediva il corretto funzionamento della funzionalità di misurazione gratuita per 31 giorni quando il server di configurazione veniva registrato in Site Recovery.
* **Agente unificato**: risoluzione di un problema di Update 1 a causa del quale l'aggiornamento non veniva installato nel server di destinazione master quando veniva aggiornato dalla versione 8.0 alla versione 8.0.1.

### Azure Site Recovery Scout 8.0.1 Update 1
Update 1 include le correzioni di bug e le nuove funzionalità seguenti:

* 31 giorni di protezione gratuita per istanza del server. Consente di testare le funzionalità o impostare un proof-of-concept.
  * Tutte le operazioni sul server, inclusi failover e failback, sono gratuite per i primi 31 giorni a partire dal momento in cui un server viene protetto con Site Recovery Scout.
  * Dal 32esimo giorno in poi, per ogni server protetto verrà addebitata al sito del cliente la tariffa per istanza standard per la protezione di Azure Site Recovery.
  * E’ possibile consultare il numero di server protetti addebitati in qualsiasi momento nella pagina Dashboard delle credenziali di Azure Site Recovery.
* Aggiunta del supporto per vCLI (vSphere Command-Line Interface) 5.5 Update 2.
* Supporto aggiunto per i sistemi operativi Linux nel server di origine:
  * RHEL 6 Update 6
  * RHEL 5 Update 11
  * CentOS 6 Update 6
  * CentOS 5 Update 11
* Correzioni dei bug per risolvere i problemi seguenti:
  * La registrazione delle credenziali ha esito negativo per il server di configurazione o RX.
  * I volumi del cluster non vengono visualizzati come previsto quando le macchine virtuali vengono riprotette durante la ripresa.
  * Il failback ha esito negativo quando il server di destinazione master è ospitato in un altro server ESXi dalle macchine virtuali di produzione locali.
  * Le autorizzazioni del file di configurazione vengono modificate durante l'aggiornamento a 8.0.1, con impatto sulla protezione e sulle operazioni.
  * La soglia di risincronizzazione non è applicata come previsto, con conseguente comportamento incoerente della replica.
  * Le impostazioni RPO non vengono visualizzate correttamente nell'interfaccia del server di configurazione. Il valore di dati non compressi mostra erroneamente il valore compresso.
  * L'operazione di rimozione non elimina come previsto nella procedura guidata vContinuum e la replica non viene eliminata dall'interfaccia del server di configurazione.
  * Nella procedura guidata vContinuum il disco viene deselezionato automaticamente quando si fa clic su **Dettagli** nella visualizzazione del disco durante la protezione delle macchine virtuali MSCS.
  * Nello scenario da macchina fisica a virtuale (P2V), i servizi HP obbligatori, come CIMnotify e CqMgHost, non vengono impostati su manuale nel ripristino della macchina virtuale. Questo causa tempo di avvio aggiuntivo.
  * La protezione di macchine virtuali Linux ha esito negativo quando sono presenti più di 26 dischi nel server di destinazione master.

## Passaggi successivi
Pubblicare eventuali domande nel [forum Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

<!---HONumber=AcomDC_0921_2016-->
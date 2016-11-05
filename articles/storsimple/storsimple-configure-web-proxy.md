---
title: Impostare un proxy Web per un dispositivo StorSimple | Microsoft Docs
description: Informazioni su come configurare le impostazioni del proxy Web per il dispositivo StorSimple tramite Windows PowerShell per StorSimple
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli

---
# Configurare il proxy web per il dispositivo StorSimple
## Overview
In questa esercitazione viene illustrato come utilizzare Windows PowerShell per StorSimple per configurare e visualizzare le impostazioni proxy Web per il dispositivo StorSimple. Le impostazioni proxy Web vengono utilizzate dal dispositivo StorSimple per comunicare con il cloud. Un server proxy Web viene utilizzato per aggiungere un ulteriore livello di sicurezza, contenuto per filtro e cache per semplificare i requisiti di larghezza di banda o l'analisi dei dati.

Il proxy Web è una configurazione facoltativa per il dispositivo StorSimple. È possibile configurare il proxy Web solo tramite Windows PowerShell per StorSimple. La configurazione è un processo in due passaggi come indicato di seguito:

1. È innanzitutto necessario configurare le impostazioni proxy Web tramite l'installazione guidata o i cmdlet di Windows PowerShell per StorSimple.
2. È quindi possibile abilitare le impostazioni proxy Web configurate utilizzando i cmdlet di Windows PowerShell per StorSimple.

Una volta completata la configurazione del proxy Web, è possibile visualizzare le impostazioni proxy Web configurate nel servizio Microsoft Azure StorSimple Manager e in Windows PowerShell per StorSimple.

Dopo aver letto questa esercitazione, si sarà in grado di:

* Configurare il proxy Web utilizzando i cmdlet e l'installazione guidata
* Abilitare il proxy Web utilizzando i cmdlet
* Visualizzare le impostazioni proxy Web nel portale di Azure classico
* Risolvere gli errori durante la configurazione del proxy Web

## Configurare il proxy Web tramite Windows PowerShell per StorSimple
Utilizzare uno dei seguenti metodi per configurare le impostazioni proxy Web:

* Installazione guidata per semplificare i passaggi di configurazione.
* Cmdlet di Windows PowerShell per StorSimple.

Ognuno di questi metodi è descritto nelle sezioni seguenti.

## Configurare il proxy Web tramite l'installazione guidata
È possibile utilizzare l'installazione guidata per semplificare i passaggi per la configurazione del proxy Web. Eseguire i passaggi seguenti per configurare il proxy Web sul dispositivo.

#### Per configurare il proxy Web tramite l'installazione guidata
1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo** e fornire la **password di amministratore del dispositivo**. Digitare il comando seguente per avviare una sessione dell'installazione guidata:
   
    `Invoke-HcsSetupWizard`
2. Se questa è la prima volta che viene utilizzata l'installazione guidata per la registrazione del dispositivo, è necessario configurare tutte le impostazioni di rete necessarie fino a raggiungere la configurazione del proxy Web. Se il dispositivo è già registrato, è possibile accettare tutte le impostazioni di rete configurate fino a raggiungere la configurazione del proxy Web. Nella configurazione guidata, quando viene richiesto di configurare le impostazioni proxy Web, digitare **Sì**.
3. Per l'**URL proxy Web** specificare l'indirizzo IP o il nome di dominio completo (FQDN) del server proxy Web e il numero di porta TCP che si desidera che il dispositivo utilizzi per comunicare con il cloud. Utilizzare il seguente formato:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Per impostazione predefinita, viene specificato il numero di porta TCP 8080.
4. Scegliere il tipo di autenticazione: **NTLM**, **Di base** o **Nessuno**. L'autenticazione di base è quella meno sicura per la configurazione del server proxy. NT LAN Manager (NTLM) è un protocollo di autenticazione altamente protetta e complessa che utilizza un sistema di messaggistica a tre vie (a volte quattro, se è necessaria ulteriore integrità) per autenticare un utente. L'autenticazione predefinita è NTLM. Per ulteriori informazioni, vedere l'autenticazione [di base](http://hc.apache.org/httpclient-3.x/authentication.html) e [NTLM](http://hc.apache.org/httpclient-3.x/authentication.html).
   
   > [!IMPORTANT]
   > **Nel servizio StorSimple Manager, i grafici di monitoraggio del dispositivo non funzionano quando l'autenticazione di base o NTLM è abilitata nella configurazione del server proxy per il dispositivo. Per utilizzare i grafici di monitoraggio, è necessario assicurarsi che l'autenticazione sia impostata su NESSUNO.**
   > 
   > 
5. Se si utilizza l'autenticazione, fornire un **nome utente del proxy Web** e la **password del proxy Web**. È necessario confermare la password.
   
    ![Configurare il proxy Web nel dispositivo StorSimple 1](./media/storsimple-configure-web-proxy/IC751830.png)

Se si sta registrando il dispositivo per la prima volta, continuare con la registrazione. Se il dispositivo è già stato registrato, la procedura guidata viene chiusa. Le impostazioni configurate verranno salvate.

Verrà abilitato anche il proxy Web. È possibile ignorare il passaggio [Abilita il proxy Web](#enable-web-proxy) e passare direttamente alla [Visualizza le impostazioni proxy Web nel portale di Azure classico](#view-web-proxy-settings-in-the-azure-classic-portal).

## Configurare il proxy Web tramite i cmdlet di Windows PowerShell per StorSimple
Un modo alternativo per configurare le impostazioni proxy Web consiste nell'utilizzo dei cmdlet di Windows PowerShell per StorSimple. Eseguire i passaggi seguenti per configurare il proxy Web.

#### Per configurare il proxy Web tramite i cmdlet
1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**. Quando richiesto, fornire la **password di amministratore del dispositivo**. La password predefinita è `Password1`.
2. Al prompt dei comandi digitare:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Fornire e confermare la password quando richiesto, come illustrato di seguito.
   
    ![Configurare il proxy Web nel dispositivo StorSimple 3](./media/storsimple-configure-web-proxy/IC751831.png)

Il proxy Web è ora configurato e deve essere abilitato.

## Abilitare il proxy Web
Il proxy Web è disabilitato per impostazione predefinita. Dopo aver configurato le impostazioni proxy Web sul dispositivo StorSimple, è necessario utilizzare Windows PowerShell per StorSimple per abilitare le impostazioni proxy Web.

> [!NOTE]
> **Questo passaggio non è necessario se è stata utilizzata l'installazione guidata per configurare il proxy Web. Il proxy Web viene abilitato automaticamente per impostazione predefinita dopo una sessione dell'installazione guidata.**
> 
> 

Per abilitare il proxy Web sul dispositivo in Windows PowerShell per StorSimple, procedere come segue:

#### Per abilitare il proxy Web
1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**. Quando richiesto, fornire la **password di amministratore del dispositivo**. La password predefinita è `Password1`.
2. Al prompt dei comandi digitare:
   
    `Enable-HcsWebProxy`
   
    A questo punto, è stata abilitata la configurazione del proxy Web sul dispositivo StorSimple.
   
    ![Configurare il proxy Web nel dispositivo StorSimple 4](./media/storsimple-configure-web-proxy/IC751832.png)

## Visualizzare le impostazioni proxy Web nel portale di Azure classico
Le impostazioni proxy Web sono configurate tramite l'interfaccia di Windows PowerShell e non possono essere modificate nel portale classico. È possibile, tuttavia, visualizzare le impostazioni configurate nel portale classico. Eseguire i passaggi seguenti per visualizzare le impostazioni proxy Web.

#### Per visualizzare le impostazioni proxy Web
1. Passare al **servizio StorSimple Manager > Dispositivi**. Selezionare e fare clic su un dispositivo e quindi passare a **Configura**.
2. Scorrere verso il basso la pagina **Configura** fino alla sezione **Impostazioni proxy Web**. È possibile visualizzare le impostazioni proxy Web configurate sul dispositivo StorSimple, come illustrato di seguito.
   
    ![Visualizzare il proxy Web nel portale di gestione](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)

## Errori durante la configurazione del proxy Web
Se le impostazioni proxy Web sono state configurate in modo non corretto, verranno visualizzati messaggi di errore in Windows PowerShell per StorSimple. Nella tabella seguente sono descritti alcuni di questi messaggi di errore, le possibili cause e le azioni consigliate.

| N. di serie | Codice di errore HRESULT | Possibile causa principale | Azione consigliata |
|:--- |:--- |:--- |:--- |
| 1\. |0x80070001 |Il comando viene eseguito dal controller passivo e non è in grado di comunicare con il controller attivo. |Eseguire il comando dal controller attivo. Per eseguire il comando dal controller passivo, sarà necessario correggere la connettività da controller passivo a controller attivo. È necessario contattare il supporto tecnico Microsoft se la connettività viene interrotta. |
| 2\. |0x800710dd - L'identificatore di operazione non è valido |Le impostazioni proxy non sono supportate dal dispositivo virtuale StorSimple. |Le impostazioni proxy non sono supportate dal dispositivo virtuale StorSimple. Possono essere configurate solo in un dispositivo fisico StorSimple. |
| 3\. |0x80070057 - Parametro non valido |Uno dei parametri forniti per le impostazioni proxy non è valido. |L'URI non è fornito nel formato corretto. Utilizzare il seguente formato: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4\. |0x800706ba - Server RPC non disponibile |La causa principale è una delle seguenti:</br></br>Il cluster non è attivo.</br></br>Il servizio di percorso dati non è in esecuzione.</br></br>Il comando viene eseguito dal controller passivo e non è in grado di comunicare con il controller attivo. |Contattare il supporto tecnico Microsoft per assicurarsi che il cluster sia attivo e il servizio di percorso dati sia in esecuzione.</br></br>Eseguire il comando dal controller attivo. Se si desidera eseguire il comando dal controller passivo, è necessario verificare che il controller passivo sia in grado di comunicare con il controller attivo. È necessario contattare il supporto tecnico Microsoft se la connettività viene interrotta. |
| 5\. |0X800706be - Chiamata RPC non riuscita |Cluster non attivo. |Contattare il supporto tecnico Microsoft per assicurarsi che il cluster sia attivo. |
| 6\. |0x8007138f - Risorsa cluster non trovata |La risorsa cluster del servizio di piattaforma non è stata trovata. Questa situazione può verificarsi in caso di installazione non corretta. |Potrebbe essere necessario eseguire il ripristino delle impostazioni predefinite sul dispositivo. Potrebbe essere necessario creare una risorsa di piattaforma. Contattare il supporto tecnico Microsoft per i passaggi successivi. |
| 7\. |0x8007138c - Risorsa cluster non online |Le risorse cluster del servizio di percorso dati o piattaforma non sono online. |Contattare il supporto tecnico Microsoft per garantire che la risorsa servizio di percorso dati e piattaforma sia online. |

> [!NOTE]
> * L'elenco dei messaggi di errore riportato sopra non è esaustivo.
> * Gli errori correlati alle impostazioni del proxy Web non vengono visualizzati nel portale di Azure classico del servizio StorSimple Manager. Se si verifica un problema con il proxy Web dopo il completamento della configurazione, lo stato del dispositivo verrà modificato in **Offline** nel portale classico. |
> 
> 

## Passaggi successivi
* Se si riscontrano problemi durante la distribuzione del dispositivo o la configurazione delle impostazioni proxy Web, fare riferimento a [Risoluzione dei problemi relativi alla distribuzione del dispositivo StorSimple](storsimple-troubleshoot-deployment.md).
* Per informazioni sull’utilizzo del servizio StorSimple Manager, passare a[utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0817_2016-->
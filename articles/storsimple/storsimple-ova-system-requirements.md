<properties
   pageTitle="Requisiti di sistema StorSimple Virtual Array"
   description="Scoprire il software e i requisiti di rete per StorSimple Virtual Array"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/07/2016"
   ms.author="alkohli"/>

# Requisiti di sistema di StorSimple Virtual Array (anteprima)

## Panoramica

Questo articolo illustra importanti requisiti di sistema per Microsoft Azure StorSimple Virtual Array (anche noto come dispositivo virtuale locale StorSimple o dispositivo virtuale StorSimple) e per i client di archiviazione con accesso all'array. Prima di distribuire il sistema StorSimple è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

I requisiti di sistema includono:

-   **Requisiti software per i client di archiviazione**: descrive le piattaforme di virtualizzazione supportate, Web browser, iniziatori iSCSI, client SMB, requisiti minimi del dispositivo virtuale ed eventuali requisiti aggiuntivi per questi sistemi operativi.

-   **Requisiti di rete per il dispositivo StorSimple**: fornisce informazioni sulle porte che devono essere aperte nel firewall per consentire il traffico iSCSI, cloud o di gestione.

Le informazioni sui requisiti di sistema StorSimple pubblicate in questo articolo si applicano solo a StorSimple Virtual Array.

- Per i dispositivi serie 8000, andare all'articolo sui [requisiti di sistema per il dispositivo StorSimple serie 8000](storsimple-system-requirements.md).
 
- Per i dispositivi serie 7000, andare all'articolo sui [requisiti di sistema per il dispositivo StorSimple serie 5000-7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

> [AZURE.IMPORTANT]
> 
> - Questa anteprima pubblica è destinata esclusivamente alla valutazione. L'installazione di questa versione di anteprima in un ambiente di produzione non è supportata.
> - Se si riscontrano problemi con StorSimple Virtual Array, inserire un post al riguardo nel [forum MSDN per StorSimple](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=StorSimple).  

## Requisiti software

I requisiti software includono le informazioni su Web browser supportati, versioni SMB, piattaforme di virtualizzazione e i requisiti minimi del dispositivo virtuale.

### Piattaforme di virtualizzazione supportate


| **Hypervisor** | **Versione** |
|----------------|--------------------------------------|
| Hyper-V | Windows Server 2008 R2 SP1 e versioni successive |
| VMware ESXi | 5\.5 e versioni successive |

### Requisiti del dispositivo virtuale

| **Componente** | **Requisito** |
|----------------------------------------------|----------------------------|
| Numero minimo di processori virtuali (memorie centrali) | 4 |
| Memoria minima (RAM) | 8 GB |
| Spazio su disco<sup>1</sup> | Disco sistema operativo: 80 GB <br></br>Disco dati: da 500 GB a 8 TB|
| Numero minimo di interfaccia o interfacce di rete | 1 |
| Larghezza di banda Internet minima<sup>2</sup> | 5 Mbps |

<sup>1</sup>: thin provisioning

<sup>2</sup>: i requisiti di rete possono variare a seconda della frequenza di modifica dei dati giornaliera. Ad esempio, se un dispositivo deve eseguire il backup di 10 GB o di più modifiche durante la giornata, il backup giornaliero con una connessione da 5 Mbps potrebbe richiedere fino a 4,25 ore (se i dati non possono essere compressi o deduplicati).

### Web browser supportati

| **Componente** | **Versione** | **Requisiti aggiuntivi/note** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge | Versione più recente | |
| Internet Explorer | Versione più recente | Testato con Internet Explorer 11 |
| Google Chrome | Versione più recente | Testato con Chrome 46 |

### Versioni SMB supportate

| **Versione** |
|-------------|
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

## Requisiti di rete 

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico iSCSI, SMB, cloud o di gestione. In questa tabella, *in* *ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *In* *uscita* fa riferimento alla direzione in cui il dispositivo StorSimple invia i dati all'esterno, oltre la distribuzione: ad esempio, in uscita verso Internet.

| **Numero porta<sup>1</sup>** | **In ingresso/In uscita** | **Ambito porta** | **Obbligatorio** | **Note** |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP) | In uscita | WAN | No | La porta in uscita viene usata per consentire all'accesso Internet di recuperare gli aggiornamenti. <br></br>Il proxy Web in uscita è configurabile dall'utente. |
| TCP 443 (HTTPS) | In uscita | WAN | Sì | La porta in uscita viene usata per accedere ai dati nel cloud. <br></br>Il proxy Web in uscita è configurabile dall'utente. |
| UDP 53 (DNS) | In uscita | WAN | In alcuni casi; vedere le note. | Questa porta è obbligatoria solo se si usa un server DNS basato su Internet. <br></br> **Nota**: se si distribuisce un file server, si consiglia l'uso del server DNS locale.|
| UDP 123 (NTP) | In uscita | WAN | In alcuni casi; vedere le note. | Questa porta è obbligatoria solo se si usa un server NTP basato su Internet.<br></br> **Nota:** se si distribuisce un file server, si consiglia di sincronizzare l'ora con i controller di dominio di Active Directory. |
|TCP 9354 | In uscita | WAN | Sì | La porta in uscita viene usata dal servizio StorSimple per comunicare con il servizio StorSimple Manager.|
| TCP 80 (HTTP) | In ingresso | LAN | Sì | Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo StorSimple per la gestione locale. <br></br> **Nota**: l'accesso all'interfaccia utente locale tramite HTTP consente il reindirizzamento automatico a HTTPS.|
| TCP 443 (HTTPS) | In ingresso | LAN | Sì | Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo StorSimple per la gestione locale.|
| TCP 3260 (iSCSI) | In ingresso | LAN | No | Questa porta viene usata per accedere ai dati tramite iSCSI.|

<sup>1</sup> Nessuna porta in ingresso deve essere aperta sulla rete Internet pubblica.

## Passaggio successivo

-   [Preparare il portale per distribuire StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md)

<!---HONumber=AcomDC_0204_2016-->
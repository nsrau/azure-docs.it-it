---
title: Modificare la configurazione del dispositivo StorSimple serie 8000 | Microsoft Docs
description: "Viene descritto come usare il servizio Gestione dispositivi StorSimple per riconfigurare un dispositivo StorSimple che è già stato distribuito."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 13ff24c24a881297775fa5f65821e53ceb83c351
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Usare il servizio Gestione dispositivi StorSimple per modificare la configurazione del dispositivo StorSimple

## <a name="overview"></a>Panoramica

Il pannello **Impostazioni** nella sezione **Impostazioni del dispositivo** del portale di Azure contiene tutti i parametri del dispositivo che è possibile riconfigurare per un dispositivo StorSimple gestito da un servizio Gestione dispositivi StorSimple. In questa esercitazione viene illustrato come usare il pannello **Impostazioni** per eseguire le seguenti attività a livello di dispositivo:

* Modificare il nome descrittivo del dispositivo
* Modificare le impostazioni ora del dispositivo
* Assegnare un DNS secondario
* Modificare le interfacce di rete
* Sostituire o riassegnare indirizzi IP

## <a name="modify-device-friendly-name"></a>Modificare il nome descrittivo del dispositivo

È possibile usare il portale di Azure per modificare il nome del dispositivo e assegnare un nome descrittivo univoco di propria scelta. Usare il pannello **Impostazioni generali** del dispositivo per modificare il nome descrittivo del dispositivo. Il nome descrittivo può contenere qualsiasi carattere e può contenere un massimo di 64 caratteri.

> [!NOTE] 
> Il nome del dispositivo può essere modificato nel portale di Azure solo se la configurazione del dispositivo non è stata completata. Non è possibile modificare il nome del dispositivo se è già stata completata una configurazione minima del dispositivo.

![Nome del dispositivo nelle impostazioni generali](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

A un dispositivo StorSimple connesso al servizio Gestione dispositivi StorSimple viene assegnato un nome predefinito. Il nome predefinito riflette in genere il numero di serie del dispositivo. Ad esempio, un nome di dispositivo predefinito che è composto da 15 caratteri, ad esempio 8600-SHX0991003G44HT indica quanto segue:

* **8600** – indica il modello del dispositivo.
* **SHX** – indica il sito di produzione.
* **0991003** -indica un prodotto specifico.
* **G44HT**-le ultime 5 cifre vengono aggiunte per creare numeri di serie univoci. Questo potrebbe non essere un insieme sequenziale.

## <a name="modify-device-description"></a>Modificare la descrizione del dispositivo

Usare il pannello **Impostazioni generali** del dispositivo per modificare la descrizione del dispositivo.

![Descrizione del dispositivo nelle impostazioni generali](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Una descrizione del dispositivo in genere consente di identificare il proprietario e il percorso fisico del dispositivo. Il campo descrizione deve contenere meno di 256 caratteri.

## <a name="modify-time-settings"></a>Modificare le impostazioni di tempo

Il dispositivo deve sincronizzare l'ora per l'autenticazione con il provider di servizi di archiviazione cloud. Usare il pannello **Impostazioni generali** del dispositivo per modificare le impostazioni ora del dispositivo.

![Descrizione del dispositivo nelle impostazioni generali](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Selezionare il fuso orario dall'elenco a discesa. È possibile specificare fino a due server NTP (Network Time Protocol):

 - **Server NTP primario**: la configurazione è obbligatoria e si specifica quando si usa Windows PowerShell per StorSimple per configurare il dispositivo. È possibile specificare il valore predefinito di Windows Server **time.windows.com** come server NTP. È possibile visualizzare la configurazione del server NTP primario nel portale di Azure, ma è necessario usare l'interfaccia Windows PowerShell per modificarla. Usare il cmdlet `Set-HcsNTPClientServerAddress` per modificare il server NTP primario del dispositivo. Per altre informazioni, vedere la sintassi per il cmdlet [Set-HcsNTPClientServerAddress] (https://technet.microsoft.com/library/dn688138.aspx).

- **Server NTP secondario**: la configurazione è facoltativa. È possibile usare il portale per configurare un server NTP secondario.

Quando si configura il server NTP, assicurarsi che la rete consenta il traffico NTP dal Data Center a Internet. Quando si specifica un server NTP pubblico, è necessario assicurarsi che i firewall di rete e altri dispositivi di sicurezza siano configurati per consentire il traffico NTP da e verso la rete esterna. Se non è consentito il traffico NTP bidirezionale, è necessario utilizzare un server NTP interno (un controller di dominio di Windows fornisce questa funzione). Se il dispositivo non può sincronizzare l'ora, potrebbe non essere in grado di comunicare con il provider di archiviazione cloud.

Per visualizzare un elenco dei server NTP pubblico, visitare il [Web server NTP](http://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Cosa accade se il dispositivo viene distribuito in un fuso orario diverso?

Se il dispositivo viene distribuito in un fuso orario diverso, cambia il fuso orario del dispositivo. Dato che tutti i criteri di backup utilizzano il fuso orario del dispositivo, i criteri di backup verranno regolati automaticamente secondo il nuovo fuso orario. Non è necessario alcun intervento dell'utente.

## <a name="modify-dns-settings"></a>Modificare le impostazioni DNS

Un server DNS viene utilizzato quando il dispositivo tenta di comunicare con il provider di servizi di archiviazione cloud. Usare il pannello **Impostazioni di rete** del dispositivo per visualizzare e modificare le impostazioni DNS configurate. 

![Impostazioni DNS nelle impostazioni di rete](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Per la disponibilità elevata, è necessario configurare il database primario e i server DNS secondari durante la distribuzione iniziale del dispositivo.

**Server DNS primario**: usare Windows PowerShell per StorSimple per specificare il server DNS primario durante l'installazione iniziale. È possibile riconfigurare il server DNS primario solo usando l'interfaccia Windows PowerShell. Usare il cmdlet `Set-HcsDNSClientServerAddress` per modificare il server DNS primario del dispositivo. Per altre informazioni, vedere la sintassi per il cmdlet [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx).

**Server DNS secondario**: per modificare il server DNS secondario, usare il cmdlet `Set-HcsDNSClientServerAddress` nell'interfaccia Windows PowerShell del dispositivo o il pannello **Impostazioni di rete** del dispositivo StorSimple nel portale di Azure.

Per modificare il server DNS secondario nel portale di Azure, attenersi alla procedura seguente.

1. Passare al servizio Gestione dispositivi StorSimple. Nell'elenco dei dispositivi selezionare e fare clic sul dispositivo.

2. Nel pannello **Impostazioni** passare a **Impostazioni del dispositivo > Rete**. Viene visualizzato il pannello **Impostazioni di rete**. Fare clic sul riquadro **Impostazioni DNS**. Modificare l'indirizzo IP del server DNS secondario.

    ![Modificare l'indirizzo IP del server DNS secondario](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Sulla barra dei comandi fare clic su **Salva** e, quando viene richiesta la conferma, su **Sì**.

    ![Salvare e confermare le modifiche](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modificare le interfacce di rete

Il dispositivo dispone di sei interfacce di rete del dispositivo, di cui quattro sono da 1 GbE e due sono da 10 GbE. Queste interfacce sono contrassegnate come DATA 0 - DATA 5. DATA 0, DATA 1, DATA 4 e DATA 5 sono da 1 GbE, mentre DATA 2 e DATA 3 sono interfacce di rete da 10 GbE.

Usare il pannello **Impostazioni di rete** per configurare ognuna delle interfacce da usare.

![Configurare le interfacce di rete in Impostazioni di rete](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Per garantire la disponibilità elevata, si consiglia di disporre di almeno due interfacce di iSCSI e due interfacce abilitate per cloud sul dispositivo. È consigliabile, ma non richiesta, la disabilitazione di interfacce inutilizzate.

Per ogni interfaccia di rete, vengono visualizzati i seguenti parametri:

* **Velocità** : non è un parametro configurabile dall'utente. DATA 0, DATA 1, DATA 4 e DATA 5 sono sempre da 1 GbE, mentre DATA 2 e DATA 3 sono interfacce di rete da 10 GbE.
  
  > [!NOTE]
  > Velocità e duplex sono sempre sottoposti alla negoziazione automatica. Non sono supportati i frame Jumbo.
  
* **Stato interfaccia** – un'interfaccia può essere abilitata o disabilitata. Se abilitata, il dispositivo tenterà di utilizzare l'interfaccia. Si consiglia di abilitare solo tali interfacce che sono connesse alla rete e utilizzati. Disabilitare tutte le interfacce che non sono in uso.
* **Tipo di interfaccia** : questo parametro consente di isolare il traffico iSCSI dal traffico di archiviazione cloud. Questo parametro può essere uno dei seguenti:
  
  * **Abilitato per il cloud** – quando abilitato, il dispositivo utilizzerà questa interfaccia per comunicare con il cloud.
  * **iSCSI abilitato** – quando abilitato, il dispositivo utilizzerà questa interfaccia per comunicare con l'host iSCSI.
    
    Si consiglia di isolare il traffico iSCSI dal traffico di archiviazione cloud. Si noti inoltre che se l'host è all'interno della stessa subnet del dispositivo, non è necessario assegnare un gateway. Tuttavia, se l'host si trova in una subnet diversa del dispositivo, è necessario assegnare un gateway.
* **Indirizzo IP**: quando si configura una delle interfacce di rete, è necessario configurare un IP virtuale (VIP). L'IP virtuale può essere IPv4 o IPv6 o entrambi. Sono supportate sia le famiglie di indirizzi IPv4 e sia IPv6 per le interfacce di rete del dispositivo. Quando si utilizza IPv4, specificare un indirizzo IP a 32 bit (*xxx.xxx.xxx.xxx*) in notazione decimale. Quando si utilizza IPv6, è sufficiente fornire un prefisso a 4 cifre e un indirizzo a 128 bit verrà generato automaticamente per l'interfaccia di rete del dispositivo in base al prefisso.
* **Subnet** : si riferisce alla subnet mask e viene configurata tramite l'interfaccia Windows PowerShell.
* **Gateway** : si tratta del gateway predefinito che deve essere utilizzato da tale interfaccia durante il tentativo di comunicare con i nodi che non sono nello stesso spazio di indirizzi IP (subnet). Il gateway predefinito deve essere nello stesso spazio degli indirizzi (subnet) dell'interfaccia indirizzo IP, come determinato dalla subnet mask.
* **Indirizzo IP fisso** : questo campo è disponibile solo quando si configura l’interfaccia di DATA 0. Per operazioni come aggiornamenti o risoluzione dei problemi relativi al dispositivo, potrebbe essere necessario connettersi direttamente al controller del dispositivo. L'indirizzo IP fisso può essere utilizzato per accedere al controller attivo e passivo sul dispositivo.

> [!NOTE]
> * Per garantire il corretto funzionamento, verificare la velocità dell'interfaccia e duplex del commutatore a cui ogni interfaccia del dispositivo è connessa. Le interfacce del commutatore devono essere negoziate oppure configurate per Gigabit Ethernet (1000 Mbps) e devono essere full duplex. Le interfacce funzionanti a velocità più basse o half duplex comportano problemi di prestazioni.
> * Per ridurre al minimo le interruzioni e tempi di inattività, è consigliabile abilitare portfast su ciascuna delle porte del commutatore a cui l'interfaccia di rete iSCSI del dispositivo sarà connessa. Ciò garantisce che la connettività di rete può essere stabilita rapidamente in caso di failover.

### <a name="configure-data-0"></a>Configurare DATA 0

DATA 0 è abilitata per il cloud per impostazione predefinita. Durante la configurazione di DATA 0, è inoltre necessario configurare due indirizzi IP fissi, uno per ogni controller. Questi indirizzi IP fissi possono essere usati per accedere direttamente ai controller dei dispositivi e sono utili quando si installano gli aggiornamenti del dispositivo, per il corretto funzionamento di Garbage Collection o quando si accede ai controller per la risoluzione dei problemi.

È possibile riconfigurare i controller IP fissi usando il pannello delle impostazioni di DATA 0.

![Configurare l'interfaccia di rete - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Gli indirizzi IP fissi per il controller vengono usati per gestire gli aggiornamenti al dispositivo e per il corretto funzionamento degli algoritmi di recupero dello spazio (Garbage Collection). Di conseguenza, gli indirizzi IP fissi devono essere instradabili e in grado di connettersi a Internet.

### <a name="configure-data-1---data-5"></a>Configurare DATA 1 - DATA 5

Per le interfacce di rete DATA 1 - DATA 5 è possibile configurare tutte le impostazioni di rete come indicato nella schermata seguente:

![Configurare le interfacce di rete DATA 1 - DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Sostituire o riassegnare indirizzi IP

Attualmente, se a qualsiasi interfaccia di rete nel controller viene assegnato un VIP è in uso (per lo stesso dispositivo o un altro dispositivo di rete), il controller avrà esito negativo. Se si scambiano o si riassegnano i VIP per un'interfaccia di rete del dispositivo, è necessario seguire una procedura appropriata poiché si potrebbe creare una situazione con IP duplicati.

Per sostituire o riassegnare i VIP per qualsiasi interfaccia di rete, procedere come segue:

#### <a name="to-reassign-ips"></a>Per riassegnare indirizzi IP

1. Deselezionare l'indirizzo IP per entrambe le interfacce.
2. Dopo che gli indirizzi IP sono deselezionati, assegnare nuovi indirizzi IP alle rispettive interfacce.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [Configurare MPIO per il dispositivo StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Informazioni su come [usare il servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


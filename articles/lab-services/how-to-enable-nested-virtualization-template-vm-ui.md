---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services (UI) | Microsoft Docs
description: Informazioni su come creare una macchina virtuale modello contenente più macchine virtuali.  In altre parole, abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f8135e11fb7b7ddb588ab3a8ed01227712072fd2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647920"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services manualmente

La virtualizzazione annidata consente di creare un ambiente con più macchine virtuali all'interno della macchina virtuale modello di un lab. La pubblicazione del modello fornirà a ogni utente del lab una macchina virtuale configurata con più macchine virtuali al suo interno.  Per ulteriori informazioni sulla virtualizzazione annidata e Azure Lab Services, vedere [abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Questo articolo illustra come configurare la virtualizzazione annidata in un computer modello in Azure Lab Services usando direttamente i ruoli e gli strumenti di Windows.  Per consentire a una classe di utilizzare la virtualizzazione nidificata, è necessario eseguire alcune operazioni.  Nei passaggi seguenti viene descritto come configurare manualmente un modello di computer di Lab Services con Hyper-V.  I passaggi sono destinati a Windows Server 2016 o Windows Server 2019.  

>[!IMPORTANT]
>Per la creazione del lab, selezionare le dimensioni **Medium (virtualizzazione annidata)** o **Large (virtualizzazione annidata)** per la macchina virtuale.  In caso contrario, la virtualizzazione annidata non funzionerà.  

## <a name="enable-hyper-v-role"></a>Abilita ruolo Hyper-V

Nei passaggi seguenti vengono descritte le azioni necessarie per abilitare Hyper-V in Windows Server utilizzando uno dei Server Manager.  Una volta completata l'installazione, la console di gestione di Hyper-V sarà disponibile per aggiungere, modificare ed eliminare le macchine virtuali client.

1. Nella pagina dashboard di **Server Manager** fare clic su **Aggiungi ruoli e funzionalità**.
2. Nella pagina **Prima di iniziare**, fare clic su **Avanti**.
3. Nella pagina **Selezione tipo di installazione** Mantieni la selezione predefinita di installazione basata su ruoli o basata su funzionalità e quindi fai clic su **Avanti**.
4. Nella pagina **Selezione server di destinazione** selezionare selezionare un server dal pool di server.   Il server corrente sarà già selezionato.  Scegliere Avanti.
5. Nella pagina **Selezione ruoli server** selezionare **Hyper-V**.  
6. Verrà visualizzato il popup **Aggiunta guidata ruoli e funzionalità** .  Selezionare **Includi strumenti di gestione (se applicabile)**.  Fare clic sul pulsante **Aggiungi funzionalità** .
7. Nella pagina **Selezione ruoli server** fare clic su **Avanti**.
8. Nella **pagina Selezione funzionalità** fare clic su **Avanti**.
9. Nella pagina **Hyper-V** fare clic su **Avanti**.
10. Nella pagina **Crea commutatori virtuali** accettare le impostazioni predefinite e fare clic su **Avanti**.
11. Nella pagina **migrazione macchina virtuale** accettare le impostazioni predefinite e fare clic su **Avanti**.
12. Nella pagina **archivi predefiniti** accettare le impostazioni predefinite e fare clic su **Avanti**.
13. Nella pagina **Conferma selezioni** per l'installazione selezionare **Riavvia automaticamente il server di destinazione, se necessario**.
14. Quando viene visualizzato il popup **Aggiunta guidata ruoli e funzionalità** , fare clic su **Sì**.
15. Fare clic su **Installa**.
16. Attendere che la pagina **stato installazione** indichi che il ruolo Hyper-V è completo.  Il computer può essere riavviato al centro dell'installazione.
17. Fare clic su **Close**.

## <a name="enable-dhcp-role"></a>Abilita ruolo DHCP

Per tutte le macchine virtuali Client Hyper-V create, è necessario un indirizzo IP nella rete NAT.  La rete NAT verrà creata in un secondo momento.  Un modo per assegnare gli indirizzi IP consiste nel configurare l'host, in questo caso il modello di macchina virtuale Lab come server DHCP.  Di seguito sono riportati i passaggi necessari per abilitare il ruolo DHCP.

1. Nella pagina **Dashboard** di **Server Manager** fare clic su **Aggiungi ruoli e funzionalità**.
2. Nella pagina **Prima di iniziare**, fare clic su **Avanti**.
3. Nella pagina **Selezione tipo di installazione** selezionare **Installazione basata su ruoli o basata su funzionalità** e quindi fare clic su **Avanti**.
4. Nella pagina **Selezione server di destinazione** selezionare il server corrente dal pool di server e quindi fare clic su **Avanti**.
5. Nella pagina **Selezione ruoli server** selezionare **server DHCP**.  
6. Verrà visualizzato il popup **Aggiunta guidata ruoli e funzionalità** .  Selezionare **Includi strumenti di gestione (se applicabile)**.  Fare clic su **Aggiungi funzionalità**.

    >[!NOTE]
    >Potrebbe essere visualizzato un errore di convalida che informa che non sono stati trovati indirizzi IP statici.  Questo avviso può essere ignorato per questo scenario.

7. Nella pagina **Selezione ruoli server** fare clic su **Avanti**.
8. Nella pagina **Selezione funzionalità** fare clic su **Avanti**.
9. Nella pagina **Server DHCP** fare clic su **Avanti**.
10. Nella pagina **Conferma selezioni per l'installazione** fare clic su **Installa**.
11. Attendere che la **pagina stato installazione** indichi che il ruolo DHCP è completo.
12. Fare clic su Chiudi.

## <a name="enable-routing-and-remote-access-role"></a>Abilitare il ruolo Routing e accesso remoto

1. Nella pagina **Dashboard** di **Server Manager** fare clic su **Aggiungi ruoli e funzionalità**.
2. Nella pagina **Prima di iniziare**, fare clic su **Avanti**.
3. Nella pagina **Selezione tipo di installazione** selezionare **Installazione basata su ruoli o basata su funzionalità** e quindi fare clic su **Avanti**.
4. Nella pagina **Selezione server di destinazione** selezionare il server corrente dal pool di server e quindi fare clic su **Avanti**.
5. Nella pagina **Selezione ruoli server** selezionare **accesso remoto**. Fare clic su **OK**.
6. Nella pagina **Selezione funzionalità** fare clic su **Avanti**.
7. Nella pagina **accesso remoto** fare clic su **Avanti**.
8. Nella pagina **servizi ruolo** selezionare **routing**.
9. Verrà visualizzato il popup **Aggiunta guidata ruoli e funzionalità** .  Selezionare **Includi strumenti di gestione (se applicabile)**.  Fare clic su **Aggiungi funzionalità**.
10. Fare clic su **Avanti**.
11. Nella pagina **Ruolo server Web (IIS)** fare clic su **Avanti**.
12. Nella pagina **Selezione servizi ruolo** fare clic su **Avanti**.
13. Nella pagina **Conferma selezioni per l'installazione** fare clic su **Installa**.
14. Attendere la pagina **stato dell'installazione** per indicare che il ruolo accesso remoto è stato completato.  
15. Fare clic su **Close**.

## <a name="create-virtual-nat-network"></a>Creare una rete NAT virtuale

Ora che sono stati installati tutti i ruoli necessari, è il momento di creare la rete NAT.  Il processo di creazione comporterà la creazione di un'opzione e della rete NAT.  Una rete NAT (Network Address Translation) assegna un indirizzo IP pubblico a un gruppo di macchine virtuali in una rete privata per consentire la connettività a Internet.  In questo caso, il gruppo di macchine virtuali private sarà costituito dalle macchine virtuali nidificate.  La rete NAT consentirà alle VM annidate di comunicare tra loro. Un'opzione è un dispositivo di rete che gestisce la ricezione e il routing del traffico in una rete.

### <a name="create-a-new-virtual-switch"></a>Crea un nuovo commutire virtuale

1. Aprire la console di **gestione di Hyper-V** da strumenti di amministrazione di Windows.
2. Selezionare il server corrente nel menu di spostamento a sinistra.
3. Fare clic su **gestione Commuter virtuale...** dal menu **azioni** sul lato destro della console di **gestione di Hyper-V**.
4. Nella finestra popup **gestione Commuter virtuale** selezionare **interno** per il tipo di opzione da creare.  Fare clic su **Crea commutatore virtuale**.
5. Per il Commuter virtuale appena creato, impostare il nome su un elemento memorabile.  Per questo esempio verrà usato ' LabServicesSwitch '.  Fare clic su **OK**.
6. Verrà creata una nuova scheda di rete.  Il nome sarà simile a' vEthernet (LabServicesSwitch)'.  Per verificare aprire il **Pannello di controllo**, fare clic su **rete e Internet**, quindi su **Visualizza stato della rete e attività**.  A sinistra fare clic su **Modifica impostazioni scheda**.

### <a name="create-a-nat-network"></a>Creare una rete NAT

1. Aprire lo strumento **routing e accesso remoto** da strumenti di amministrazione di Windows.
2. Selezionare il server locale nella pagina di spostamento a sinistra.
3. Scegliere **azione**  ->  **Configura e Abilita routing e accesso remoto**.
4. Quando viene visualizzata l' **installazione guidata del server di routing e accesso remoto** , fare clic su **Avanti**.
5. Nella pagina **configurazione** selezionare configurazione **NAT (Network Address Translation)** .  Fare clic su **Avanti**.

    >[!WARNING]
    >Non scegliere l'opzione "accesso alla rete privata virtuale (VPN) e NAT".

6. Nella pagina **connessione Internet NAT** scegliere "Ethernet".  Non scegliere la connessione ' vEthernet (LabServicesSwitch)' creata nella console di gestione di Hyper-V. Fare clic su **Avanti**.
7. Fare clic su **fine** nell'ultima pagina della procedura guidata.
8. Quando viene visualizzata la finestra di dialogo **Avvia servizio** , fare clic su **Avvia servizio**.
9. Attendere che il servizio venga avviato.

## <a name="update-network-adapter-settings"></a>Aggiornare le impostazioni della scheda di rete

La scheda di rete verrà associata all'indirizzo IP usato per l'indirizzo IP del gateway predefinito per la rete NAT creata in precedenza.  In questo esempio viene creato un indirizzo IP di 192.168.0.1 con un subnet mask 255.255.255.0.  Si userà il Commuter virtuale creato in precedenza.

1. Aprire il **Pannello di controllo**, fare clic su **rete e Internet**, fare clic su **Visualizza stato della rete e attività**.
2. A sinistra fare clic su **Modifica impostazioni scheda**.  
3. Nella finestra **connessioni di rete** fare doppio clic su "VEthernet (LabServicesSwitch)" per visualizzare la finestra di dialogo Dettagli **stato vEthernet (LabServicesSwitch)** .
4. Fare clic sul pulsante **Proprietà** .
5. Selezionare l'elemento **protocollo Internet versione 4 (TCP/IPv4)** e fare clic sul pulsante **Proprietà** .
6. Nella finestra di dialogo **Proprietà protocollo Internet versione 4 (TCP/IPv4)** selezionare **utilizza il seguente indirizzo IP**.  Per l'indirizzo IP immettere 192.168.0.1. Per il subnet mask, immettere 255.255.255.0.  Lasciare vuoto il gateway predefinito.  Lasciare vuoti anche i server DNS.

    >[!NOTE]
    > L'intervallo per la rete NAT sarà, in notazione CIDR, 192.168.0.0/24.  In questo modo viene creato un intervallo di indirizzi IP utilizzabili da 192.168.0.1 a 192.168.0.254.  Per convenzione, i gateway hanno il primo indirizzo IP in un intervallo di subnet.

7. Fare clic su OK.

## <a name="create-dhcp-scope"></a>Crea ambito DHCP

I passaggi seguenti sono istruzioni per l'aggiunta di un ambito DHCP.  In questo articolo la rete NAT è 192.168.0.0/24 in notazione CIDR.  In questo modo viene creato un intervallo di indirizzi IP utilizzabili da 192.168.0.1 a 192.168.0.254.  L'ambito creato deve trovarsi in un intervallo di indirizzi utilizzabile, escluso l'indirizzo IP già creato in precedenza.

1. Aprire **strumenti di amministrazione** e aprire lo strumento di amministrazione **DHCP** .
2. Nello strumento **DHCP** espandere il nodo del server corrente e selezionare **IPv4**.
3. Dal menu azione scegliere **nuovo ambito...**
4. Quando viene visualizzata la **procedura guidata nuovo ambito** , fare clic su **Avanti** nella pagina **iniziale** .
5. Nella pagina **nome ambito** immettere "LabServicesDhcpScope" o un altro elemento memorabile per il nome.  Fare clic su **Avanti**.
6. Nella pagina **intervallo indirizzi IP** immettere i valori seguenti.

    - 192.168.0.100 per l'indirizzo IP iniziale
    - 192.168.0.200 per l'indirizzo IP finale
    - 24 per la lunghezza
    - 255.255.255.0 per la subnet mask

7. Fare clic su **Avanti**.
8. Nella pagina **Aggiungi esclusioni e ritardo** fare clic su **Avanti**.
9. Nella pagina **durata lease** fare clic su **Avanti**.
10. Nella pagina **Configura opzioni DHCP** , selezionare **Sì, ora si desidera configurare le opzioni**. Fare clic su **Avanti**.
11. Sul **router (gateway predefinito)**
12. Aggiungere 192.168.0.1, se non è già stato fatto. Fare clic su **Avanti**.
13. Nella pagina **nome di dominio e server DNS** aggiungere 168.63.129.16 come indirizzo IP del server DNS, se non è già stato fatto.  168.63.129.16 è l'indirizzo IP di un server DNS statico di Azure. Fare clic su **Avanti**.
14. Nella pagina **server WINS** fare clic su **Avanti**.
15. Nella pagina **Attiva ambito** selezionare **Sì, attiva ora questo ambito**.  Fare clic su **Avanti**.
16. Nella pagina **Completamento procedura guidata nuovo ambito** fare clic su **fine**.

## <a name="conclusion"></a>Conclusione

A questo punto il computer modello è pronto per la creazione di macchine virtuali Hyper-V.   Per istruzioni su come creare macchine virtuali Hyper-V, vedere [creare una macchina virtuale in Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) .  Vedere anche [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) per consultare i sistemi operativi e il software disponibili.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
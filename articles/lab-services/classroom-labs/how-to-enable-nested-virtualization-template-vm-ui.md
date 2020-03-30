---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services (UI)Enable nested virtualization on a template VM in Azure Lab Services (UI) Documenti Microsoft
description: Informazioni su come creare una macchina virtuale modello con più macchine virtuali all'interno.  In other words, enable nested virtualization on a template VM in Azure Lab Services.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 10f8d2760474631fbcabac4d66139aedf4c7560c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503075"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Abilitare manualmente la virtualizzazione annidata in una macchina virtuale modello in Azure Lab ServicesEnable nested virtualization on a template virtual machine in Azure Lab Services manually

La virtualizzazione annidata consente di creare un ambiente con più macchine virtuali all'interno della macchina virtuale modello di un lab. La pubblicazione del modello fornirà a ogni utente del lab una macchina virtuale configurata con più macchine virtuali al suo interno.  Per altre informazioni sulla virtualizzazione annidata e sui servizi lab di Azure, vedere [Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services.For](how-to-enable-nested-virtualization-template-vm.md)more information about nested virtualization and Azure Lab Services, see Enable nested virtualization on a template virtual machine in Azure Lab Services.

Questo articolo illustra come configurare la virtualizzazione annidata in un computer modello in Azure Lab Services usando direttamente i ruoli e gli strumenti di Windows.This article covers how to set up nested virtualization on a template machine in Azure Lab Services using Windows roles and tools directly.  Ci sono alcuni elementi necessari per consentire a una classe di utilizzare la virtualizzazione annidata.  La procedura seguente descriverà come configurare manualmente un modello di computer Di OLab Services con Hyper-V.  I passaggi sono destinati a Windows Server 2016 o Windows Server 2019.Steps are intended for Windows Server 2016 or Windows Server 2019.  

>[!IMPORTANT]
>Selezionare **Grande (virtualizzazione annidata)** o **Medio (virtualizzazione annidata)** per le dimensioni della macchina virtuale durante la creazione del lab.  La virtualizzazione annidata non funzionerà in caso contrario.  

## <a name="enable-hyper-v-role"></a>Abilitare il ruolo Hyper-VEnable Hyper-V role

Nei passaggi seguenti vengono descritte le azioni necessarie per abilitare Hyper-V in Windows Server utilizzando Server Manager.  Una volta completata l'installazione, la gestione Hyper-V sarà disponibile per aggiungere, modificare ed eliminare macchine virtuali client.

1. In **Server Manager**, nella pagina Dashboard fare clic su Aggiungi ruoli e **funzionalità**.
2. Nella pagina **Prima di iniziare** fare clic su **Avanti**.
3. Nella pagina **Selezione tipo di installazione** mantenere la selezione predefinita di Installazione basata su ruoli o basata su funzionalità e quindi fare clic su **Avanti**.
4. Nella pagina **Seleziona server** di destinazione selezionare Selezionare un server dal pool di server.   Il server corrente sarà già selezionato.  Scegliere Avanti.
5. Nella pagina **Selezione ruoli server ** selezionare **Hyper-V**.  
6. Verrà visualizzato il popup **Aggiunta guidata ruoli e funzionalità.**  Selezionare **Includi strumenti di gestione (se applicabile).**  Fare clic sul pulsante **Aggiungi funzionalità.**
7. Nella pagina **Selezione ruoli server** fare clic su **Avanti**.
8. Nella **pagina Selezione funzionalità**fare clic su **Avanti**.
9. Nella pagina **Hyper-V** fare clic su **Avanti**.
10. Nella pagina **Crea commutatori virtuali** accettare le impostazioni predefinite e fare clic su **Avanti**.
11. Nella pagina **Migrazione macchina virtuale** accettare le impostazioni predefinite e fare clic su **Avanti**.
12. Nella pagina **Archivi predefiniti** accettare le impostazioni predefinite e fare clic su **Avanti**.
13. Nella pagina **Conferma selezioni per l'installazione** selezionare Riavvia automaticamente il server di **destinazione se necessario.**
14. Quando viene visualizzato il popup **Aggiunta guidata ruoli e funzionalità,** fare clic su **Sì**.
15. Fare clic su **Installa**.
16. Attendere che la pagina **Stato installazione** indichi che il ruolo Hyper-V è stato completato.  Il computer potrebbe riavviarsi durante l'installazione.
17. Fare clic su **Chiudi**.

## <a name="enable-dhcp-role"></a>Abilita ruolo DHCP

Tutte le macchine virtuali del client Hyper-V create, richiede un indirizzo IP nella rete NAT.  Creeremo la rete NAT in un secondo momento.  Un modo per assegnare gli indirizzi IP consiste nel configurare l'host, in questo caso il modello di macchina virtuale lab, come server DHCP.  Di seguito sono riportati i passaggi necessari per abilitare il ruolo DHCP.

1. In **Server Manager**, nella pagina **Dashboard** fare clic su Aggiungi ruoli **e funzionalità**.
2. Nella pagina **Prima di iniziare** fare clic su **Avanti**.
3. Nella pagina **Selezione tipo di installazione** selezionare **Installazione basata su ruoli o basata su funzionalità** e quindi fare clic su **Avanti**.
4. Nella pagina **Seleziona server** di destinazione selezionare il server corrente dal pool di server e quindi fare clic su **Avanti**.
5. Nella pagina **Selezione ruoli server** selezionare Server **DHCP**.  
6. Verrà visualizzato il popup **Aggiunta guidata ruoli e funzionalità.**  Selezionare **Includi strumenti di gestione (se applicabile).**  Fare clic su **Aggiungi funzionalità**.

    >[!NOTE]
    >È possibile che venga visualizzato un errore di convalida che indica che non sono stati trovati indirizzi IP statici.  Questo avviso può essere ignorato per lo scenario.

7. Nella pagina **Selezione ruoli server** fare clic su **Avanti**.
8. Nella pagina **Selezione funzionalità** fare clic su **Avanti**.
9. Nella pagina **Server DHCP** fare clic su **Avanti**.
10. Nella pagina **Conferma selezioni per l'installazione** fare clic su **Installa**.
11. Attendere che la **pagina Stato installazione** indichi che il ruolo DHCP è stato completato.
12. Fare clic su Chiudi.

## <a name="enable-routing-and-remote-access-role"></a>Abilitare il ruolo Routing e Accesso remoto

1. In **Server Manager**, nella pagina **Dashboard** fare clic su Aggiungi ruoli **e funzionalità**.
2. Nella pagina **Prima di iniziare** fare clic su **Avanti**.
3. Nella pagina **Selezione tipo di installazione** selezionare **Installazione basata su ruoli o basata su funzionalità** e quindi fare clic su **Avanti**.
4. Nella pagina **Seleziona server** di destinazione selezionare il server corrente dal pool di server e quindi fare clic su **Avanti**.
5. Nella pagina **Selezione ruoli server** selezionare Accesso **remoto**. Fare clic su **OK**.
6. Nella pagina **Selezione funzionalità** fare clic su **Avanti**.
7. Nella pagina **Accesso remoto** fare clic su **Avanti**.
8. Nella pagina **Servizi ruolo** selezionare **Routing**.
9. Verrà visualizzato il popup **Aggiunta guidata ruoli e funzionalità.**  Selezionare **Includi strumenti di gestione (se applicabile).**  Fare clic su **Aggiungi funzionalità**.
10. Fare clic su **Avanti**.
11. Nella pagina **Ruolo server Web (IIS)** fare clic su **Avanti**.
12. Nella pagina **Selezione servizi ruolo** fare clic su **Avanti**.
13. Nella pagina **Conferma selezioni per l'installazione** fare clic su **Installa**.
14. Attendere che la pagina **Stato installazione** indichi che il ruolo Accesso remoto è stato completato.  
15. Fare clic su **Chiudi**.

## <a name="create-virtual-nat-network"></a>Creare una rete NAT virtuale

Ora che tutti i ruoli necessari sono stati installati, è il momento di creare la rete NAT.  Il processo di creazione prevede la creazione di uno switch e la rete NAT, se stessa.  Una rete NAT (network address translation) assegna un indirizzo IP pubblico a un gruppo di macchine virtuali in una rete privata per consentire la connettività a Internet.  Nel nostro caso, il gruppo di macchine virtuali private sarà le macchine virtuali annidate.  La rete NAT consentirà alle macchine virtuali annidate di comunicare tra loro. Uno switch è un dispositivo di rete che gestisce la ricezione e il routing del traffico in una rete.

### <a name="create-a-new-virtual-switch"></a>Creare un nuovo commutatore virtualeCreate a new virtual switch

1. Aprire la console di gestione di **Hyper-V** da Strumenti di amministrazione di Windows.
2. Selezionare il server corrente nel menu di navigazione a sinistra.
3. Fare clic su **Gestione commutatori virtuali...** dal menu **Azioni** sul lato destro della console di gestione di **Hyper-V**.
4. Nel popup **Virtual Switch Manager** selezionare Internal **(Interno)** per il tipo di opzione da creare.  Fare clic su **Crea commutatore virtuale**.
5. Per il nuovo interruttore virtuale creato, impostare il nome su qualcosa di memorabile.  Per questo esempio verrà usato 'LabServicesSwitch'.  Fare clic su **OK**.
6. Verrà creata una nuova scheda di rete.  Il nome sarà simile a 'vEthernet (LabServicesSwitch)'.  Per verificare aprire il **Pannello di controllo**, fare clic su rete e **Internet**, fare clic su Visualizza stato di rete **e attività**.  A sinistra, fai clic su **Modifica impostazioni scheda.**

### <a name="create-a-nat-network"></a>Creare una rete NAT

1. Aprire lo strumento **Routing e Accesso remoto** da Strumenti di amministrazione di Windows.
2. Selezionare il server locale nella pagina di spostamento a sinistra.
3. Scegliere **Attiva** -> **configurazione e Abilita routing e Accesso remoto**.
4. Quando viene visualizzata **l'Installazione guidata server di Routing e Accesso remoto,** fare clic su **Avanti**.
5. Nella pagina **Configurazione** selezionare **Configurazione NAT (Network address Translation).**  Fare clic su **Avanti**.

    >[!WARNING]
    >Non scegliere l'opzione 'Accesso e NAT della rete privata virtuale (VPN).

6. Nella pagina **Connessione Internet NAT** scegliere 'Ethernet'.  Non scegliere la connessione 'vEthernet (LabServicesSwitch)' creata nella console di gestione di Hyper-V. Fare clic su **Avanti**.
7. Fare clic su **Fine** nell'ultima pagina della procedura guidata.
8. Quando viene visualizzata la finestra di dialogo **Avvia servizio,** fare clic su **Avvia servizio**.
9. Attendere l'avvio del servizio.

## <a name="update-network-adapter-settings"></a>Aggiornare le impostazioni della scheda di rete

La scheda di rete verrà associata all'indirizzo IP utilizzato per l'IP del gateway predefinito per la rete NAT creata in precedenza.  In questo esempio viene creato un indirizzo IP 192.168.0.1 con una subnet mask di 255.255.255.0.In this example, we create an IP address of 192.168.0.1 with a subnet mask of 255.255.255.0.  Useremo il commutatore virtuale creato in precedenza.

1. Aprire il **Pannello di controllo**, fare clic su Rete e **Internet**, fare clic su Visualizza stato della rete **e attività**.
2. A sinistra, fai clic su **Modifica impostazioni scheda.**  
3. Nella finestra Connessioni di **rete** fare doppio clic su 'vEthernet (LabServicesSwitch)' per visualizzare la finestra di dialogo dei dettagli **dello stato di vEthernet (LabServicesSwitch).**
4. Fare clic sul pulsante **Proprietà.**
5. Selezionare l'elemento **Protocollo Internet versione 4 (TCP/IPv4)** e fare clic sul pulsante **Proprietà.**
6. Nella finestra di dialogo **Proprietà - Protocollo Internet versione 4 (TCP/IPv4)** selezionare Utilizza il seguente indirizzo **IP**.  Per l'indirizzo IP, immettere 192.168.0.1. Per la subnet mask, immettere 255.255.255.0.For the subnet mask, enter 255.255.255.0.  Lasciare vuoto il gateway predefinito.  Lasciare vuoti anche i server DNS.

    >[!NOTE]
    > La nostra gamma per la nostra rete NAT sarà, in notazione CIDR, 192.168.0.0/24.  In questo modo viene creato un intervallo di indirizzi IP utilizzabili da 192.168.0.1 a 192.168.0.254.  Per convenzione, i gateway hanno il primo indirizzo IP in un intervallo di subnet.

7. Fare clic su OK.

## <a name="create-dhcp-scope"></a>Crea ambito DHCP

I passaggi seguenti sono istruzioni per aggiungere l'ambito DHCP.  In questo articolo, la nostra rete NAT è 192.168.0.0/24 in notazione CIDR.  In questo modo viene creato un intervallo di indirizzi IP utilizzabili da 192.168.0.1 a 192.168.0.254.  L'ambito creato deve essere compreso in tale intervallo di indirizzi utilizzabili, escluso l'indirizzo IP già creato in precedenza.

1. Aprire **Strumenti di amministrazione** e aprire lo strumento di amministrazione **DHCP.**
2. Nello strumento **DHCP** espandere il nodo relativo al server corrente e selezionare **IPv4**.
3. Dal menu Azione, scegliere **Nuovo ambito...**
4. Quando viene visualizzata la **Creazione guidata nuovo ambito,** fare clic su **Avanti** nella pagina **iniziale.**
5. Nella pagina **Nome ambito** immettere 'LabServicesDhcpScope' o un'altra operazione che può essere memorabile per il nome.  Fare clic su **Avanti**.
6. Nella pagina **Intervallo** di indirizzi IP immettere i valori seguenti.

    - 192.168.0.100 per l'indirizzo IP iniziale
    - 192.168.0.200 per l'indirizzo IP finale
    - 24 per la lunghezza
    - 255.255.255.0 per la subnet mask

7. Fare clic su **Avanti**.
8. Nella pagina **Aggiungi esclusioni e ritardo** fare clic su **Avanti**.
9. Nella pagina **Durata lease** fare clic su **Avanti**.
10. Nella pagina **Configura opzioni DHCP** selezionare **Sì, desidero configurare queste opzioni ora.** Fare clic su **Avanti**.
11. Sul **Router (gateway predefinito)**
12. Aggiungere 192.168.0.1, se non è già stato fatto. Fare clic su **Avanti**.
13. Nella pagina **Domain Name and DNS Servers** aggiungere 168.63.129.16 come indirizzo IP del server DNS, se non è già stato fatto.  168.63.129.16 è l'indirizzo IP di un server DNS statico di Azure.168.63.129.16 is the IP address for an Azure static DNS server. Fare clic su **Avanti**.
14. Nella pagina **Server WINS** fare clic su **Avanti**.
15. Nella pagina **Attiva ambito** selezionare **Sì, desidero attivare l'ambito.**  Fare clic su **Avanti**.
16. Nella pagina **Completamento della Creazione guidata nuovo ambito** fare clic su **Fine**.

## <a name="conclusion"></a>Conclusioni

A questo punto il computer modello è pronto per creare macchine virtuali Hyper-V.   Per istruzioni su come creare macchine virtuali Hyper-V, vedere [Creare una macchina virtuale in Hyper-V.See Create a Virtual Machine in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) for instructions about how to create Hyper-V virtual machines.  Vedere anche [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) per estrarre i sistemi operativi e il software disponibili.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni alla configurazione di qualsiasi lab.

- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Link di registrazione via e-mail agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
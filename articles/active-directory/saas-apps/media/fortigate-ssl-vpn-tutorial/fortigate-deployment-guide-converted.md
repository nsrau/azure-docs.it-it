---
title: Guida alla distribuzione di FortiGate | Microsoft Docs
description: Informazioni su come configurare e usare il prodotto firewall di nuova generazione Fortinet FortiGate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025464"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>Guida alla distribuzione di FortiGate come macchina virtuale di Azure

Questa guida alla distribuzione descrive come configurare e usare il prodotto firewall di nuova generazione Fortinet FortiGate distribuito come macchina virtuale di Azure. Verrà inoltre configurata l'app della raccolta di Azure AD per FortiGate SSL VPN per fornire l'autenticazione della VPN tramite Azure Active Directory.

## <a name="redeem-the-fortigate-license"></a>Riscattare la licenza FortiGate

Il prodotto firewall di nuova generazione Fortinet FortiGate è disponibile come macchina virtuale in un'infrastruttura distribuita come servizio (IaaS) di Azure. Per questa macchina virtuale esistono due modalità di gestione delle licenze, ovvero con pagamento in base al consumo e BYOL (Bring Your Own License).

Se è stata acquistata una licenza di FortiGate da Fortinet da usare con l'opzione BYOL per la distribuzione della macchina virtuale, è possibile riscattarla dalla pagina di attivazione del prodotto di Fortinet, https://support.fortinet.com. Il file di licenza risultante avrà un'estensione file LIC.

## <a name="download-firmware"></a>Scaricare il firmware

Al momento della stesura di questo documento, la VM di Azure FortiGate Fortinet non viene fornita con la versione del firmware necessaria per l'autenticazione SAML. È necessario ottenere la versione più recente da Fortinet.

1. Eseguire l'accesso all'indirizzo https://support.fortinet.com/.
2. Passare a **Download** > **Firmware Images** (Scarica > Immagini firmware).
3. A destra di **Release Notes** (Note sulla versione) selezionare **Download** (Scarica).
4. Selezionare **v6.00** > **6.4** > **6.4.2**.
5. Per scaricare **FGT_VM64_AZURE-v6-build1723-FORTINET.out**, selezionare il collegamento **HTTPS** sulla stessa riga.
6. Salvare il file per un momento successivo.

## <a name="deploy-the-fortigate-vm"></a>Distribuire la VM FortiGate

1. Passare al portale di Azure e accedere alla sottoscrizione in cui verrà distribuita la macchina virtuale FortiGate.
2. Creare un nuovo gruppo di risorse o aprire quello in cui verrà distribuita la macchina virtuale FortiGate.
3. Selezionare **Aggiungi**.
4. In **Cerca nel Marketplace** immettere *Forti*. Selezionare **Fortinet FortiGate Next-Generation Firewall** (Firewall di nuova generazione Fortinet FortiGate).
5. Selezionare il piano software, ovvero BYOL se si ha una licenza oppure con pagamento in base al consumo in caso contrario. Selezionare **Create** (Crea).
6. Popolare la configurazione della macchina virtuale.

    ![Screenshot di Crea macchina virtuale.](virtual-machine.png)

7. Impostare **Tipo di autenticazione** su **Password** e fornire le credenziali amministrative per la macchina virtuale.
8. Selezionare **Rivedi e crea** > **Crea**.
9. Attendere il completamento della distribuzione della macchina virtuale.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Impostare un indirizzo IP pubblico statico e assegnare un nome di dominio completo

Per un'esperienza utente coerente, configurare l'assegnazione statica dell'indirizzo IP pubblico alla macchina virtuale FortiGate. Eseguirne inoltre il mapping a un nome di dominio completo (FQDN).

1. Passare al portale di Azure e aprire le impostazioni per la macchina virtuale FortiGate.
2. Nella schermata **Panoramica** selezionare l'indirizzo IP pubblico.

    ![Screenshot della rete privata virtuale SSL di FortiGate.](public-ip-address.png)

3. Selezionare **Statico** > **Salva**.

Se si è proprietari di un nome di dominio instradabile pubblicamente per l'ambiente in cui viene distribuita la macchina virtuale FortiGate, creare un record host (A) per la macchina virtuale. Questo record è associato all'indirizzo IP pubblico assegnato staticamente in precedenza.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Creare una nuova regola del gruppo di sicurezza di rete in ingresso per la porta TCP 8443

1. Passare al portale di Azure e aprire le impostazioni per la macchina virtuale FortiGate.
2. Scegliere **Rete** dal menu a sinistra. Verrà elencata l'interfaccia di rete e verranno visualizzate le regole della porta in ingresso.
3. Selezionare **Aggiungi regola porta in ingresso**.
4. Creare una nuova regola della porta in ingresso per TCP 8443.

    ![Screenshot di Aggiungi regola di sicurezza in ingresso.](port-rule.png)

5. Selezionare **Aggiungi**.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Creare una seconda scheda di interfaccia di rete virtuale per la macchina virtuale

Per rendere disponibili agli utenti le risorse interne, è necessario aggiungere una seconda scheda di interfaccia di rete virtuale alla VM FortiGate. La rete virtuale in Azure in cui si trova la scheda di interfaccia di rete virtuale deve avere una connessione instradabile a tali risorse interne.

1. Passare al portale di Azure e aprire le impostazioni per la macchina virtuale FortiGate.
2. Se la VM FortiGate non è già stata arrestata, selezionare **Arresta** e attendere l'arresto della VM.
3. Scegliere **Rete** dal menu a sinistra.
4. Selezionare **Collega interfaccia di rete**.
5. Selezionare **Crea e collega interfaccia di rete**.
6. Configurare le proprietà per la nuova interfaccia di rete e quindi selezionare **Crea**.

    ![Screenshot di Crea interfaccia di rete.](new-network-interface.png)

7. Avviare la VM FortiGate.


## <a name="configure-the-fortigate-vm"></a>Configurare la VM FortiGate

Le sezioni seguenti illustrano come configurare la macchina virtuale FortiGate.

### <a name="install-the-license"></a>Installare la licenza

1. Passare a `https://<address>`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Continuare ignorando eventuali errori dei certificati.
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
4. Se la distribuzione usa il modello BYOL, verrà visualizzata la richiesta di caricare una licenza. Selezionare il file della licenza creato in precedenza e caricarla. Selezionare **OK** e riavviare la macchina virtuale FortiGate.

    ![Screenshot della licenza della macchina virtuale FortiGate.](license.png)

5. Dopo il riavvio, eseguire di nuovo l'accesso con le credenziali di amministratore per convalidare la licenza.

### <a name="update-firmware"></a>Aggiornare il firmware

1. Passare a `https://<address>`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Continuare ignorando eventuali errori dei certificati.
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
4. Nel menu a sinistra selezionare **System** > **Firmware** (Sistema > Firmware).
5. In **Firmware Management** (Gestione firmware) selezionare **Browse** (Sfoglia) e selezionare il file del firmware scaricato in precedenza.
6. Ignorare l'avviso e selezionare **Backup config and upgrade** (Configurazione backup e aggiornamento).

    ![Screenshot di Firmware Management.](backup-configure-upgrade.png)

7. Selezionare **Continua**.
8. Quando viene chiesto di salvare la configurazione di FortiGate (come file con estensione conf), selezionare **Save** (Salva).
9. Attendere il caricamento e l'applicazione del firmware. Attendere il riavvio della macchina virtuale FortiGate.
10. Dopo il riavvio della macchina virtuale FortiGate, eseguire di nuovo l'accesso con le credenziali di amministratore.
11. Quando viene richiesto di configurare il dashboard, selezionare **Later** (In seguito).
12. Quando inizia il video dell'esercitazione, selezionare **OK**.

### <a name="change-the-management-port-to-tcp-8443"></a>Impostare la porta di gestione su TCP 8443

1. Passare a `https://<address>`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Continuare ignorando eventuali errori dei certificati.
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
4. Nel menu a sinistra selezionare **System** (Sistema).
5. In **Administration Settings** (Impostazioni di amministrazione) impostare la porta HTTPS su **8443** e selezionare **Apply** (Applica).
6. Dopo l'applicazione della modifica, il browser prova a ricaricare la pagina di amministrazione, ma il tentativo non riusce. Da questo punto in poi, l'indirizzo della pagina di amministrazione sarà `https://<address>:8443`.

    ![Screenshot di Upload Remote Certificate.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Caricare il certificato di firma SAML di Azure AD

1. Passare a `https://<address>:8443`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Continuare ignorando eventuali errori dei certificati.
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
4. Nel menu a sinistra selezionare **System** > **Certificates** (Sistema > Certificati).
5. Selezionare **Import** > **Remote Certificate** (Importa > Certificato remoto).
6. Passare al certificato scaricato dalla distribuzione dell'app personalizzata FortiGate nel tenant di Azure. Selezionarlo e scegliere **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Caricare e configurare un certificato SSL personalizzato

Se si preferisce, è possibile configurare la macchina virtuale FortiGate con un certificato SSL personalizzato che supporta il nome di dominio completo usato. Se si ha accesso a un certificato SSL fornito con la chiave privata in formato PFX, è possibile usarlo a questo scopo.

1. Passare a `https://<address>:8443`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Continuare ignorando eventuali errori dei certificati.
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
4. Nel menu a sinistra selezionare **System** > **Certificates** (Sistema > Certificati).
5. Selezionare **Import** > **Local Certificate** > **PKCS #12 Certificate** (Importa > Certificato locale > Certificato PKCS 12).
6. Passare al file PFX contenente il certificato SSL e la chiave privata.
7. Immettere la password per PFX e un nome significativo per il certificato. Quindi scegliere **OK**.
8. Nel menu a sinistra selezionare **System** > **Settings** (Sistema > Impostazioni).
9. In **Administration Settings** (Impostazioni di amministrazione) espandere l'elenco accanto a **HTTPS server certificate** (Certificato del server HTTPS) e selezionare il certificato SSL importato in precedenza.
10. Selezionare **Applica**.
11. Chiudere la finestra del browser e passare a `https://<address>:8443`.
12. Accedere con le credenziali di amministratore di FortiGate. A questo punto dovrebbe essere visualizzato il certificato SSL corretto usato.

### <a name="configure-authentication-timeout"></a>Configurare il timeout di autenticazione

1. Passare al portale di Azure e aprire le impostazioni per la macchina virtuale FortiGate.
2. Nel menu a sinistra selezionare **Serial Console** (Console seriale).
3. Accedere alla console seriale con le credenziali di amministratore della macchina virtuale FortiGate.
4. Nella console seriale eseguire i comandi seguenti:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Verificare che le interfacce di rete ottengano indirizzi IP

1. Passare a `https://<address>:8443`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
3. Scegliere **Rete** dal menu a sinistra.
4. In Rete selezionare **Interfacce**.
5. Esaminare port1 (interfaccia esterna) e port2 (interfaccia interna) per assicurare che stiano ottenendo un indirizzo IP dalla subnet di Azure corretta.
    a. Se una delle porte non ottiene un indirizzo IP dalla subnet (tramite DHCP), fare clic con il pulsante destro del mouse e scegliere **Modifica**.
    b. Accanto alla modalità di indirizzamento assicurarsi che sia selezionata l'opzione **DHCP**.
    c. Selezionare **OK**.

    ![Screenshot di indirizzamento interfaccia di rete.](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>Verificare che la VM FortiGate abbia la route corretta per le risorse aziendali locali

Le VM di Azure multihomed hanno tutte le interfacce di rete nella stessa rete virtuale, ma possono avere subnet separate. Questo significa spesso che entrambe le interfacce di rete hanno una connessione alle risorse aziendali locali pubblicate tramite FortiGate. Per questo motivo è necessario creare voci di route personalizzate che assicurano che il traffico esca dall'interfaccia corretta quando vengono effettuate richieste per risorse aziendali locali.

1. Passare a `https://<address>:8443`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Accedere con le credenziali di amministratore fornite durante la distribuzione della VM FortiGate.
3. Scegliere **Rete** dal menu a sinistra.
4. In Rete selezionare **Route statiche**.
5. Selezionare **Crea nuovo**.
6. Accanto a Destinazione selezionare **Subnet**.
7. In Subnet specificare le informazioni sulla subnet in cui si trovano le risorse aziendali locali, ad esempio 10.1.0.0/255.255.255.0
8. Accanto a Indirizzo gateway specificare il gateway nella subnet di Azure a cui port2 è connessa, ad esempio la subnet che termina in genere con 1, come 10.6.1.1
9. Accanto a Interfaccia selezionare l'interfaccia di rete interna, port2
10. Selezionare **OK**.

    ![Screenshot odi una route di configurazione.](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>Configurare FortiGate SSL VPN

Seguire la procedura illustrata in https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial

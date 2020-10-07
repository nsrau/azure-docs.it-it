---
title: Guida alla distribuzione di FortiGate | Microsoft Docs
description: Informazioni su come configurare e usare il prodotto firewall di nuova generazione Fortinet FortiGate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273331"
---
# <a name="fortigate-deployment-guide"></a>Guida alla distribuzione di FortiGate

Questa guida alla distribuzione descrive come configurare e usare il prodotto firewall di nuova generazione Fortinet FortiGate.

## <a name="redeem-the-fortigate-license"></a>Riscattare la licenza FortiGate

Il prodotto firewall di nuova generazione Fortinet FortiGate è disponibile come macchina virtuale in un'infrastruttura distribuita come servizio (IaaS) di Azure. Per questa macchina virtuale esistono due modalità di gestione delle licenze, ovvero con pagamento in base al consumo e BYOL (Bring Your Own License).

Fortinet può fornire le licenze ai membri del team "Get to Production Secure Hybrid Access (SHA)" di Azure Active Directory (Azure AD). Nei casi in cui non è stata fornita alcuna licenza, sarà valida anche la distribuzione con pagamento in base al consumo.

Nei casi in cui è stata rilasciata una licenza, Fortinet fornisce un codice di registrazione che deve essere riscattato online.

![Screenshot del codice di registrazione della rete privata virtuale SSL di FortiGate.](registration-code.png)

1. Eseguire la registrazione all'indirizzo https://support.fortinet.com/.
2. Dopo la registrazione, eseguire l'accesso all'indirizzo https://support.fortinet.com/.
3. Passare a **Asset** > **Register/Activate** (Asset > Registra/Attiva).
4. Immettere il codice di registrazione fornito da Fortinet.
5. Specificare il codice di registrazione, selezionare **The product will be used by a non-government user** (Il prodotto verrà usato da utenti non governativi) e selezionare **Next** (Avanti).
6. Immettere una descrizione del prodotto, ad esempio FortiGate, impostare il partner Fortinet come **Other** > **Microsoft** (Altro > Microsoft) e selezionare **Next** (Avanti).
7. Accettare il **contratto di registrazione del prodotto Fortinet** e selezionare **Next** (Avanti).
8. Accettare le **condizioni** e selezionare **Confirm** (Conferma).
9. Selezionare l'opzione **License File Download** (Download del file di licenza) e salvare la licenza per un momento successivo.


## <a name="download-firmware"></a>Scaricare il firmware

La macchina virtuale di Azure FortiGate Fortinet non viene fornita con la versione del firmware necessaria per l'autenticazione SAML. È necessario ottenere la versione più recente da Fortinet.

1. Eseguire l'accesso all'indirizzo https://support.fortinet.com/.
2. Passare a **Download** > **Firmware Images** (Scarica > Immagini firmware).
3. A destra di **Release Notes** (Note sulla versione) selezionare **Download** (Scarica).
4. Selezionare **v6.** > **6.** > **6.4.** .
5. Per scaricare **FGT_VM64_AZURE-v6-build1637-FORTINET.out**, selezionare il collegamento **HTTPS** sulla stessa riga.
6. Salvare il file per un momento successivo.


## <a name="deploy-the-fortigate-vm"></a>Distribuire la VM FortiGate

1. Passare a https://portal.azure.com e accedere alla sottoscrizione in cui si vuole distribuire la macchina virtuale FortiGate.
2. Creare un nuovo gruppo di risorse o aprire quello in cui si vuole distribuire la macchina virtuale FortiGate.
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

1. Passare a https://portal.azure.com e aprire le impostazioni per la macchina virtuale FortiGate.
2. Nella schermata **Panoramica** selezionare l'indirizzo IP pubblico.

    ![Screenshot della rete privata virtuale SSL di FortiGate.](public-ip-address.png)

3. Selezionare **Statico** > **Salva**.

Se si è proprietari di un nome di dominio instradabile pubblicamente per l'ambiente in cui viene distribuita la macchina virtuale FortiGate, creare un record host (A) per la macchina virtuale. Questo record è associato all'indirizzo IP pubblico assegnato staticamente in precedenza.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Creare una nuova regola del gruppo di sicurezza di rete in ingresso per la porta TCP

1. Passare a https://portal.azure.com e aprire le impostazioni per la macchina virtuale FortiGate.
2. Scegliere **Rete** dal menu a sinistra. Verrà elencata l'interfaccia di rete e verranno visualizzate le regole della porta in ingresso.
3. Selezionare **Aggiungi regola porta in ingresso**.
4. Creare una nuova regola della porta in ingresso per TCP 8443.

    ![Screenshot di Aggiungi regola di sicurezza in ingresso.](port-rule.png)

5. Selezionare **Aggiungi**.


## <a name="create-a-custom-azure-app-for-fortigate"></a>Creare un'app Azure personalizzata per FortiGate

1. Passare a https://portal.azure.com e aprire il riquadro di Azure AD relativo al tenant che fornirà l'identità per gli accessi a FortiGate.
2. Nel menu di sinistra selezionare **Applicazioni aziendali**.
3. Selezionare **Nuova applicazione** > **Applicazione non nella raccolta**.
4. Immettere un nome, ad esempio FortiGate, e selezionare **Aggiungi**.
5. Scegliere **Utenti e gruppi** dal menu a sinistra.
6. Aggiungere gli utenti che potranno eseguire l'accesso e selezionare **Assegna**.
7. Nel menu a sinistra selezionare **Single Sign-On**.
8. Selezionare **SAML**.
9. In **Configurazione SAML di base** selezionare l'icona della matita per modificare la configurazione.
10. Configurare quanto segue:
    - **Identificatore (ID entità)** su `https://<address>/remote/saml/metadata`.
    - **URL di risposta (URL del servizio consumer di asserzione)** su `https://<address>/remote/saml/login`.
    - **URL disconnessione** su `https://<address>/remote/saml/logout`.

    `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

11. Prendere nota di ognuno di questi URL per usarli in un secondo momento: ID entità, URL di risposta e URL disconnessione.
12. Selezionare **Salva** e chiudere **Configurazione SAML di base**.
13. In **3. Certificato di firma SAML** scaricare il **certificato (Base64)** e salvarlo per un momento successivo.
14. In **4. Configura (nome app)** copiare l'URL di accesso ad Azure, l'identificatore di Azure AD e l'URL di disconnessione da Azure e salvarli per un momento successivo.
15. In **2. Attributi e attestazioni utente** selezionare l'icona della matita per modificare la configurazione.
16. Selezionare **Aggiungi nuova attestazione** e impostare il nome su **username**.
17. Impostare l'attributo di origine su **user.userprincipalname**.
18. Selezionare **Salva** > **Aggiungi un'attestazione basata su gruppo** > **Tutti i gruppi**.
19. Selezionare **Personalizza il nome dell'attestazione basata su gruppo** e impostare il nome su **group**.
20. Selezionare **Salva**.


## <a name="prepare-for-group-matching"></a>Preparare la corrispondenza dei gruppi

FortiGate offre diverse esperienze utente nel portale dopo l'accesso, in base all'appartenenza al gruppo. Può ad esempio essere disponibile un'esperienza per il gruppo marketing e un'altra per quello finanziario. Ecco come creare gruppi per gli utenti:

1. Passare a https://portal.azure.com e aprire il riquadro di Azure AD relativo al tenant che fornirà l'identità per gli accessi a FortiGate.
2. Selezionare **Gruppi** > **Nuovo gruppo**.
3. Creare un gruppo con i dettagli seguenti:
    - Tipo di gruppo: sicurezza
    - Nome del gruppo: `a meaningful name`
    - Descrizione del gruppo: `a meaningful description for the group`
    - Tipo di appartenenza: Assegnato
    - Membri: `users for the user experience that will map to this group`
4. Ripetere i passaggi 3 e 4 per altre esperienze utente.
5. Dopo aver creato i gruppi, selezionare ognuno di essi e prendere nota del valore di **ID oggetto**.
6. Salvare questi ID oggetto e nomi di gruppo per un secondo momento.


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

### <a name="change-the-management-port-to-tcp"></a>Impostare la porta di gestione su TCP

1. Passare a `https://<address>`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Continuare ignorando eventuali errori dei certificati.
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
4. Nel menu a sinistra selezionare **System** (Sistema).
5. In **Administration Settings** (Impostazioni di amministrazione) impostare la porta HTTPS su **8443** e selezionare **Apply** (Applica).
6. Dopo l'applicazione della modifica, il browser prova a ricaricare la pagina di amministrazione, ma il tentativo non riusce. Da questo punto in poi, l'indirizzo della pagina di amministrazione sarà `https://<address>`.

    ![Screenshot di Upload Remote Certificate.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Caricare il certificato di firma SAML di Azure AD

1. Passare a `https://<address>`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Continuare ignorando eventuali errori dei certificati.
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
4. Nel menu a sinistra selezionare **System** > **Certificates** (Sistema > Certificati).
5. Selezionare **Import** > **Remote Certificate** (Importa > Certificato remoto).
6. Passare al certificato scaricato dalla distribuzione dell'app personalizzata FortiGate nel tenant di Azure. Selezionarlo e scegliere **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Caricare e configurare un certificato SSL personalizzato

Se si preferisce, è possibile configurare la macchina virtuale FortiGate con un certificato SSL personalizzato che supporta il nome di dominio completo usato. Se si ha accesso a un certificato SSL fornito con la chiave privata in formato PFX, è possibile usarlo a questo scopo.

1. Passare a `https://<address>`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Continuare ignorando eventuali errori dei certificati.
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
4. Nel menu a sinistra selezionare **System** > **Certificates** (Sistema > Certificati).
5. Selezionare **Import** > **Local Certificate** > **PKCS #12 Certificate** (Importa > Certificato locale > Certificato PKCS 12).
6. Passare al file PFX contenente il certificato SSL e la chiave privata.
7. Immettere la password per PFX e un nome significativo per il certificato. Quindi scegliere **OK**.
8. Nel menu a sinistra selezionare **System** > **Settings** (Sistema > Impostazioni).
9. In **Administration Settings** (Impostazioni di amministrazione) espandere l'elenco accanto a **HTTPS server certificate** (Certificato del server HTTPS) e selezionare il certificato SSL importato in precedenza.
10. Selezionare **Applica**.
11. Chiudere la finestra del browser e passare a `https://<address>`.
12. Accedere con le credenziali di amministratore di FortiGate. A questo punto dovrebbe essere visualizzato il certificato SSL corretto usato.


### <a name="perform-command-line-configuration"></a>Eseguire la configurazione dalla riga di comando

Le sezioni seguenti illustrano le procedure relative a diverse configurazioni eseguibili dalla riga di comando.

#### <a name="for-saml-authentication"></a>Per l'autenticazione SAML

1. Passare a https://portal.azure.com e aprire le impostazioni per la macchina virtuale FortiGate.
2. Nel menu a sinistra selezionare **Serial Console** (Console seriale).
3. Accedere alla console seriale con le credenziali di amministratore della macchina virtuale FortiGate. Per il passaggio successivo vengono richiesti gli URL annotati in precedenza:
    - ID entità
    - URL di risposta
    - URL di chiusura sessione
    - URL di accesso ad Azure
    - Identificatore di Azure AD
    - URL di disconnessione da Azure
4. Nella console seriale eseguire i comandi seguenti:

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > L'URL di disconnessione di Azure contiene un carattere `?`. Per specificarlo correttamente nella console seriale di FortiGate, è necessario immettere una sequenza di tasti speciale. L'URL è in genere `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`. Per immetterlo nella console seriale, digitare:
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    Premere quindi CTRL+V e incollare il resto dell'URL per completare la riga: 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. Per confermare la configurazione, eseguire il comando seguente:

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>Per la corrispondenza dei gruppi

1. Passare a https://portal.azure.com e aprire le impostazioni per la macchina virtuale FortiGate.
2. Nel menu a sinistra selezionare **Serial Console** (Console seriale).
3. Accedere alla console seriale con le credenziali di amministratore della macchina virtuale FortiGate.
4. Nella console seriale eseguire i comandi seguenti:

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Ripetere questi comandi a partire dalla seconda riga del codice per ogni gruppo aggiuntivo che avrà un'esperienza diversa del portale in FortiGate.

#### <a name="for-authentication-timeout"></a>Per il timeout di autenticazione

1. Passare a https://portal.azure.com e aprire le impostazioni per la macchina virtuale FortiGate.
2. Nel menu a sinistra selezionare **Serial Console** (Console seriale).
3. Accedere alla console seriale con le credenziali di amministratore della macchina virtuale FortiGate.
4. Nella console seriale eseguire i comandi seguenti:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Creare portali VPN e criteri firewall

1. Passare a `https://<address>`. Dove `<address>` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla macchina virtuale FortiGate.

2. Accedere con le credenziali di amministratore fornite durante la distribuzione della macchina virtuale FortiGate.
3. Nel menu a sinistra selezionare **VPN** > **SSL-VPN Portals** > **Create New** (VPN > Portali VPN SSL > Crea nuovo).
6. Specificare un nome (in genere corrispondente al gruppo di Azure usato per fornire l'esperienza personalizzata del portale).
7. Selezionare il segno più ( **+** ) accanto a **Source IP Pools** (Pool di indirizzi IP di origine), selezionare il pool predefinito e quindi **Close** (Chiudi).
8. Personalizzare l'esperienza per questo gruppo. A scopo di test, è possibile personalizzare il messaggio e il tema del portale. È anche possibile creare segnalibri personalizzati che indirizzano gli utenti alle risorse interne.
9. Selezionare **OK**.
10. Ripetere i passaggi da 5 a 9 per ogni gruppo di Azure che avrà un'esperienza personalizzata del portale.
11. In VPN selezionare **SSL-VPN Settings** (Impostazioni VPN SSL).
12. Selezionare il segno più ( **+** ) accanto a **Listen on Interfaces** (Rimani in ascolto sulle interfacce), selezionare **Port1** (Porta1) e quindi **Close** (Chiudi).
14. Se in precedenza è stato installato un certificato SSL personalizzato, modificare il valore di **Server Certificate** (Certificato del server) per usare il certificato SSL personalizzato nel menu a discesa.
15. In **Authentication/Portal Mapping** (Autenticazione/Mapping del portale) selezionare **Create New** (Crea nuovo). Scegliere il primo gruppo di Azure e abbinarlo al portale con lo stesso nome. Quindi scegliere **OK**.
18. Ripetere i passaggi da 15 a 17 per ogni coppia composta da un gruppo di Azure e da un portale.
19. In **Authentication/Portal Mapping** (Autenticazione/Mapping del portale) modificare **All Other Users/Groups** (Tutti gli altri utenti/gruppi).
20. Impostare il portale su **full-access** (accesso completo) e selezionare **OK** > **Apply** (OK > Applica).
23. Scorrere all'inizio della pagina **SSL-VPN Setting** (Impostazione VPN SSL) e selezionare **No SSL-VPN policies exist. Click here to create a new SSL-VPN policy using these settings** (Nessuna policy VPN SSL disponibile. Fare clic qui per crearne una nuova con queste impostazioni).
24. Specificare un nome, ad esempio **VPN Grp**. Impostare quindi **Outgoing Interface** (Interfaccia in uscita) su **port** (porta) e selezionare **Source** (Origine).
27. In **Address** (Indirizzo) selezionare **all** (tutti).
28. In **User** (Utente) selezionare il primo gruppo di Azure.
29. Selezionare **Close** > **Destination** (Chiudi > Destinazione). In **Address** (Indirizzo) è in genere specificata la rete interna. Selezionare **login.microsoft.com** per il test.
32. Selezionare **Close** > **Service** > **All** (Chiudi > Servizio > Tutti). Selezionare quindi **Close** > **OK** (Chiudi > OK).
37. Nel menu a sinistra selezionare **Policy & Objects** > **Firewall Policy** (Criteri e oggetti > Criteri firewall).
39. Espandere **SSL-VPN tunnel interface (ssl.root)**  > **port** (Interfaccia tunnel VPN SSL -> porta).
40. Fare clic con il pulsante destro del mouse sulla policy VPN creata in precedenza (**VPN Grp 1**) e selezionare **Copy** (Copia).
41. Fare clic con il pulsante destro del mouse sotto la policy VPN e selezionare **Paste** > **Below** (Incolla > Sotto).
42. Modificare la nuova policy, specificando un nome diverso, ad esempio **VPN Grp2**. Modificare anche il gruppo a cui si applica, ovvero a un altro gruppo di Azure.
43. Fare clic con il pulsante destro del mouse sul nuovo criterio e impostarne lo stato su **Enabled** (Abilitato).


## <a name="test-sign-in-by-using-azure"></a>Testare l'accesso con Azure

1. Usando una sessione privata del browser, passare a `https://<address>`.  
2. Per l'accesso dovrebbe essere eseguito il reindirizzamento ad Azure AD.
3. Dopo aver fornito le credenziali per un utente che è stato assegnato all'app FortiGate nel tenant di Azure, dovrebbe essere visualizzato il portale utente appropriato.

    ![Screenshot della rete privata virtuale SSL di FortiGate](test-sign-in.png)

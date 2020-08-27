---
title: Guida alla distribuzione di FortiGate | Microsoft Docs
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e la VPN SSL FortiGate.
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
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657104"
---
# <a name="fortigate-deployment-guide"></a>Guida alla distribuzione di FortiGate

## <a name="contents"></a>Contenuto

- Riscattare la licenza FortiGate
- Scaricare il firmware
- Distribuire la VM FortiGate
   - Impostare un indirizzo IP pubblico statico e assegnare un nome di dominio completo
   - Creare una nuova regola del gruppo di sicurezza di rete in ingresso per la porta TCP
- Creare un'app Azure personalizzata per FortiGate
- Preparare la corrispondenza dei gruppi
   - Creare gruppi per gli utenti
- Configurare la VM FortiGate
   - Installare la licenza
   - Aggiornare il firmware
   - Impostare la porta di gestione su TCP
   - Caricare il certificato di firma SAML di Azure Active Directory
   - Caricare e configurare un certificato SSL personalizzato
   - Eseguire la configurazione della riga di comando
   - Creare portali VPN e criteri firewall
- Testare l'accesso con Azure

## <a name="redeeming-the-fortigate-license"></a>Riscattare la licenza FortiGate

Il prodotto firewall di nuova generazione Fortinet FortiGate è disponibile come macchina virtuale in Azure IaaS. Per questa macchina virtuale esistono due modalità di gestione delle licenze:

- Pagamento in base al consumo
- Bring Your Own License (BYOL)

Quando si collabora con Fortinet per fornire linee guida per l'accesso ibrido sicuro (SHA, Secure Hybrid Access), Fortinet può offrire licenze ai membri del team Azure AD Get to Production SHA. Nei casi in cui non viene fornita una licenza, sarà valida anche la distribuzione con pagamento in base al consumo.

Nei casi in cui viene rilasciata una licenza, Fortinet fornisce un codice di registrazione che deve essere riscattato online

![VPN SSL FortiGate](registration-code.png)

1. Eseguire la registrazione all'indirizzo https://support.fortinet.com/
2. Dopo la registrazione, accedere all'indirizzo https://support.fortinet.com/
3. Passare a **Asset** - > **Register/Activate** (Registrazione/attivazione)
4. Immettere il codice di registrazione fornito da Fortinet
5. Specificare il codice di registrazione, selezionare **The product will be used by a non-government user** (Il prodotto verrà usato da utenti non governativi) e fare clic su **Avanti**
6. Immettere una descrizione del prodotto (ad esempio FortiGate), impostare il partner Fortinet come **Other** (Altro) - > **Microsoft** e fare clic su **Avanti**
7. Accettare il **contratto di registrazione del prodotto Fortinet** e fare clic su **Avanti**
8. Accettare le **condizioni** e fare clic su **Confirm** (Conferma)
9. Fare clic sul **download del file della licenza** e salvare la licenza per dopo


## <a name="download-firmware"></a>Scaricare il firmware

Al momento della stesura di questo documento, la VM di Azure FortiGate Fortinet non viene fornita con la versione del firmware necessaria per l'autenticazione SAML. È necessario ottenere la versione più recente da Fortinet.

1. Eseguire l'accesso all'indirizzo https://support.fortinet.com/
2. Passare a **Download** - > **Firmware Images** (Immagini firmware)
3. Fare clic su **Download** a destra di **Release Notes** (Note sulla versione)
4. Fare clic su **v6.**
5. Fare clic su **6.**
6. Fare clic su **6.4.**
7. Scaricare **FGT_VM64_AZURE-v6-build1637-FORTINET.out** facendo clic sul collegamento **HTTPS** sulla stessa riga
8. Salvare il file per dopo


## <a name="deploy-the-fortigate-vm"></a>Distribuire la VM FortiGate

1. Passare a https://portal.azure.com e accedere alla sottoscrizione in cui distribuire la macchina virtuale FortiGate
2. Creare un nuovo gruppo di risorse o aprire quello in cui distribuire la macchina virtuale FortiGate
3. Fare clic su **Aggiungi**
4. Immettere "Forti" nella finestra di dialogo **Cerca nel Marketplace** e selezionare **Fortinet FortiGate Next-** **Generation Firewall**
5. Selezionare il piano software (BYOL se si ha una licenza o con pagamento in base al consumo in caso contrario) e fare clic su **Crea**
6. Creare la configurazione della macchina virtuale

    ![VPN SSL FortiGate](virtual-machine.png)

7. Impostare il tipo di autenticazione su **Password** e fornire le credenziali amministrative per la macchina virtuale
8. Fare clic su **Rivedi e crea**
9. Fare clic su **Crea**
10. Attendere il completamento della distribuzione della VM


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Impostare un indirizzo IP pubblico statico e assegnare un nome di dominio completo

Per un'esperienza utente coerente, è consigliabile configurare l'assegnazione statica dell'indirizzo IP pubblico alla VM FortiGate. Inoltre, per gli stessi motivi, è anche utile eseguire il mapping a un nome di dominio completo.

_Impostare un indirizzo IP pubblico statico_

1. Passare a https://portal.azure.com e aprire le impostazioni per la VM FortiGate
2. Nella schermata **Panoramica** fare clic sull'indirizzo IP pubblico

    ![VPN SSL FortiGate](public-ip-address.png)

3. Fare clic su **Statico** e quindi su **Salva**

_Assegnare un nome di dominio completo_

Se si è proprietari di un nome di dominio instradabile pubblicamente per l'ambiente in cui viene distribuita la VM FortiGate, creare un record host (A) per la macchina virtuale mappato all'indirizzo IP pubblico assegnato staticamente in precedenza.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Creare una nuova regola del gruppo di sicurezza di rete in ingresso per la porta TCP

1. Passare a https://portal.azure.com e aprire le impostazioni per la VM FortiGate
2. Scegliere **Rete** dal menu a sinistra. Verranno visualizzate l'interfaccia di rete e le regole della porta in ingresso
3. Fare clic su **Aggiungi regola porta in ingresso**
4. Creare una nuova regola della porta in ingresso per TCP 8443

    ![VPN SSL FortiGate](port-rule.png)

5. Fare clic su **Aggiungi**


## <a name="create-a-custom-azure-app-for-fortigate"></a>Creare un'app Azure personalizzata per FortiGate

1. Passare a https://portal.azure.com e aprire il pannello Azure Active Directory per il tenant che fornirà l'identità per gli accessi a FortiGate
2. Scegliere **Applicazioni aziendali** dal menu a sinistra
3. Fare clic su **Nuova applicazione**
4. Fare clic su **Applicazione non nella raccolta**
5. Specificare un nome (ad esempio FortiGate) e fare clic su **Aggiungi**
6. Scegliere **Utenti e gruppi** dal menu a sinistra
7. Aggiungere gli utenti che potranno eseguire l'accesso e fare clic su **Assegna**
8. Scegliere **Single Sign-On** dal menu a sinistra
9. Fare clic su **SAML**
10. In **Configurazione SAML di base** fare clic sulla matita per modificare la configurazione
11. Configura
    - Identificatore (ID entità) su `https://<address>/remote/saml/metadata`
    - URL di risposta (URL del servizio consumer di asserzione) su `https://<address>/remote/saml/login`
    - URL di disconnessione su`https://<address>/remote/saml/logout`

    Dove `address` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla VM FortiGate

    Registrare ognuno di questi URL per un uso successivo:

    - ID entità
    - URL di risposta
    - URL di chiusura sessione
12. Fare clic su **Save** (Salva).
13. Chiudere la finestra di configurazione SAML di base
14. In **3. Certificato di firma SAML** scaricare il **certificato (Base64)** e salvarlo per dopo
15. In **4. Configura (nome app)** copiare l'URL di accesso ad Azure, l'identificatore di Azure AD e l'URL di disconnessione da Azure e salvarli per dopo
    - URL di accesso ad Azure
    - Identificatore di Azure AD
    - URL di disconnessione da Azure
16. In **2. Attributi e attestazioni utente** fare clic sulla matita per modificare la configurazione
17. Fare clic su **Aggiungi nuova attestazione**
18. Impostare Nome su **nome utente**
19. Impostare Attributo di origine su **user.userprincipalname**
20. Fare clic su **Save** (Salva).
21. Fare clic su **Aggiungi un'attestazione basata su gruppo**
22. Selezionare **Tutti i gruppi**
23. Selezionare **Personalizza il nome dell'attestazione basata su gruppo**
24. Impostare Nome su **gruppo**
25. Fare clic su **Save** (Salva).


## <a name="prepare-for-group-matching"></a>Preparare la corrispondenza dei gruppi

FortiGate offre diverse esperienze utente nel portale dopo l'accesso, in base all'appartenenza al gruppo. Può ad esempio essere disponibile un'esperienza per il gruppo di marketing e un'altra per quello delle finanze.

Configurare queste esperienze come segue:

### <a name="create-groups-for-users"></a>Creare gruppi per gli utenti

1. Passare a https://portal.azure.com e aprire il pannello Azure Active Directory per il tenant che fornirà l'identità per gli accessi a FortiGate
2. Fare clic su **Gruppi**
3. Fare clic su **Nuovo gruppo**
4. Creare un gruppo con
    - Tipo di gruppo: sicurezza
    - Nome del gruppo: `a meaningful name`
    - Descrizione del gruppo: `a meaningful description for the group`
    - Tipo di appartenenza: Assegnato
    - Membri: `users for the user experience that will map to this group`
5. Ripetere i passaggi 3 e 4 per altre esperienze utente
6. Una volta creati i gruppi, selezionarli uno alla volta e registrare l'ID oggetto per ognuno
7. Salvare questi ID oggetto e nomi di gruppo per dopo


## <a name="configure-the-fortigate-vm"></a>Configurare la VM FortiGate

### <a name="install-the-license"></a>Installare la licenza

1. Passare a `https://<address>`

    Dove `address` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla VM FortiGate

2. Continuare ignorando eventuali errori dei certificati
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della VM FortiGate
4. Se la distribuzione usa il modello BYOL, verrà visualizzata la richiesta di scaricare una licenza. Selezionare il file della licenza creato in precedenza e caricarlo, fare clic su **OK** e riavviare la VM FortiGate:

    ![VPN SSL FortiGate](license.png)

5. Dopo il riavvio, eseguire di nuovo l'accesso con le credenziali di amministratore per convalidare la licenza

### <a name="update-firmware"></a>Aggiornare il firmware

1. Passare a `https://<address>`

    Dove `address` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla VM FortiGate

2. Continuare ignorando eventuali errori dei certificati
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della VM FortiGate
4. Scegliere **System** (Sistema) dal menu a sinistra
5. Scegliere **Firewall** dal menu a sinistra in System
6. Nella pagina Firmware Management (Gestione firmware) fare clic su **Browse** (Sfoglia) e selezionare il file del firmware scaricato in precedenza
7. Ignorare l'avviso e fare clic su **Backup config and upgrade** (Configurazione backup e aggiornamento)

    ![VPN SSL FortiGate](backup-configure-upgrade.png)

8. Fare clic su **Continue** (Continua)
9. Quando viene chiesto di salvare la configurazione di FortiGate (come file con estensione conf), fare clic su **Save** (Salva)
10. Attendere il caricamento e l'applicazione del firmware, quindi il riavvio della VM FortiGate
11. Dopo il riavvio della VM FortiGate, eseguire di nuovo l'accesso con le credenziali di amministratore
12. Quando viene chiesto di eseguire la configurazione del dashboard, fare clic su **Later** (Più tardi)
13. Quando inizia il video dell'esercitazione, fare clic su **OK**

### <a name="change-the-management-port-to-tcp"></a>Impostare la porta di gestione su TCP

1. Passare a `https://<address>`

    Dove `address` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla VM FortiGate

2. Continuare ignorando eventuali errori dei certificati
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della VM FortiGate
4. Scegliere **System** (Sistema) dal menu a sinistra
5. Nelle impostazioni dai amministrazione impostare la porta HTTPS su **8443**
6. Fare clic su **Applica**.
7. Una volta applicata la modifica, il browser proverà a ricaricare la pagina di amministrazione, ma il tentativo non riuscirà. Da questo punto in poi, l'indirizzo della pagina di amministrazione sarà `https://<address>`

    ![VPN SSL FortiGate](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Caricare il certificato di firma SAML di Azure Active Directory

1. Passare a `https://<address>`

    Dove `address` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla VM FortiGate

2. Continuare ignorando eventuali errori dei certificati
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della VM FortiGate
4. Scegliere **System** (Sistema) dal menu a sinistra
5. In System fare clic su **Certificates** (Certificati)
6. Fare clic su **Import** (Importa)- > **Remote Certificate** (Certificato remoto)
7. Passare al certificato scaricato dalla distribuzione dell'app personalizzata FortiGate nel tenant di Azure, selezionarlo e fare clic su **OK**

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Caricare e configurare un certificato SSL personalizzato

È possibile configurare la VM FortiGate con un proprio certificato SSL che supporta il nome di dominio completo in uso. Se si ha accesso a un certificato SSL fornito con la chiave privata in formato PFX, è possibile usarlo a questo scopo

1. Passare a `https://<address>`

    Dove `address` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla VM FortiGate

2. Continuare ignorando eventuali errori dei certificati
3. Accedere con le credenziali di amministratore fornite durante la distribuzione della VM FortiGate
4. Scegliere **System** (Sistema) dal menu a sinistra
5. In System fare clic su **Certificates** (Certificati)
6. Fare clic su **Import** (Importa) - > **Local Certificate** (Certificato locale)
7. Fare clic su **PKCS #12 Certificate** (Certificato PKCS #12)
8. Passare al file PFX contenente il certificato SSL e la chiave privata
9. Specificare la password per PFX
10. Immettere un nome significativo per il certificato
11. Fare clic su **OK**.
12. Scegliere **System** (Sistema) dal menu a sinistra
13. In System fare clic su **Settings** (Impostazioni)
14. Nelle impostazioni di amministrazione espandere il menu a discesa accanto al certificato server HTTP e selezionare il certificato SSL importato in precedenza
15. Fare clic su **Applica**.
16. Chiudere la finestra del browser e tornare all'indirizzo `https://<address>`
17. Accedere con le credenziali di amministratore di FortiGate e osservare il certificato SSL corretto in uso


### <a name="perform-command-line-configuration"></a>Eseguire la configurazione della riga di comando

_Eseguire la configurazione della riga di comando per l'autenticazione SAML_

1. Passare a https://portal.azure.com e aprire le impostazioni per la VM FortiGate
2. Scegliere **Console seriale** dal menu a sinistra
3. Accedere alla console seriale con le credenziali di amministratore della VM FortiGate

    Per il passaggio successivo, verranno richiesti gli URL registrati in precedenza, vale a dire:

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
    > L'URL di disconnessione da Azure contiene un carattere ?. Per specificarlo correttamente nella console seriale di FortiGate, è necessario immettere una sequenza di tasti speciale. L'URL è in genere:

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    Per immetterlo nella console seriale, procedere digitando

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    Quindi premere CTRL+V

    Incollare quindi il resto dell'URL per completare la riga

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. Per confermare la configurazione, eseguire:

    ```
    show user saml
    ```

_Eseguire la configurazione della riga di comando per l'associazione dei gruppi_

1. Passare a https://portal.azure.com e aprire le impostazioni per la VM FortiGate
2. Scegliere **Console seriale** dal menu a sinistra
3. Accedere alla console seriale con le credenziali di amministratore della VM FortiGate
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

    Ripetere questi comandi a partire da edit `group 1 name` per ogni gruppo aggiuntivo che avrà un'esperienza diversa del portale in FortiGate

_Eseguire la configurazione della riga di comando per il timeout dell'autenticazione_

1. Passare a https://portal.azure.com e aprire le impostazioni per la VM FortiGate
2. Scegliere **Console seriale** dal menu a sinistra
3. Accedere alla console seriale con le credenziali di amministratore della VM FortiGate
4. Nella console seriale eseguire i comandi seguenti:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Creare portali VPN e criteri firewall

1. Passare a `https://<address>`

    Dove `address` è il nome di dominio completo o l'indirizzo IP pubblico assegnato alla VM FortiGate

2. Accedere con le credenziali di amministratore fornite durante la distribuzione della VM FortiGate
3. Scegliere **VPN** dal menu a sinistra
4. In VPN fare clic su **SSL-VPN Portals** (Portali VPN SSL)
5. Fare clic su **Create New** (Crea nuovo)
6. Specificare un nome (in genere corrispondente al gruppo di Azure usato per fornire l'esperienza personalizzata del portale)
7. Fare clic sul segno più ( **+** ) accanto a Source IP Pools (Pool di indirizzi IP di origine), selezionare il pool predefinito e fare clic su **Chiudi**
8. Personalizzare l'esperienza per questo gruppo. A scopo di test, è possibile personalizzare il messaggio e il tema del portale. È anche possibile creare segnalibri personalizzati che indirizzano gli utenti alle risorse interne
9. Fare clic su **OK**.
10. Ripetere i passaggi da 5 a 9 per ogni gruppo di Azure che avrà un'esperienza personalizzata del portale
11. In VPN fare clic su **SSL-VPN Settings** (Impostazioni VPN SSL)
12. Fai clic sul segno più ( **+** ) accanto a Listen on Interfaces (Resta in ascolto sulle interfacce)
13. Selezionare **Port1** e fare clic su **Chiudi**


14. Se in precedenza è stato installato un certificato SSL personalizzato, impostarlo al posto del certificato del server nel menu a discesa
15. In Authentication/Portal Mapping (Autenticazione/Mapping del portale) fare clic su **Create New** (Crea nuovo)
16. Scegliere il primo gruppo di Azure e associarlo al portale con lo stesso nome
17. Fare clic su **OK**.
18. Ripetere i passaggi da 15 a 17 per ogni coppia gruppo di Azure/portale
19. In Authentication/Portal Mapping (Autenticazione/Mapping del portale) modificare **All Other Users/Groups** (Tutti gli altri utenti/gruppi)
20. Impostare il portale su **full-access** (accesso completo)
21. Fare clic su **OK**.
22. Fare clic su **Applica**.
23. Scorrere all'inizio della pagina di impostazioni della VPN SSL e fare clic sull'avviso **No SSL-VPN policies**
    **exist. Click here to create a new SSL-VPN policy using these settings. (Nessuna policy VPN SSL disponibile. Fare clic qui per crearne una nuova con queste impostazioni)**
24. Specificare un nome, ad esempio **VPN Grp**
25. Impostare l'interfaccia in uscita su **port** (porta)
26. Fare clic su **Source** (origine)
27. In Address (Indirizzo) selezionare **all** (tutti)
28. In User (Utente) selezionare il primo gruppo di Azure
29. Fare clic su **Chiudi**.
30. Fare clic su **Destination** (Destinazione)
31. In Address (Indirizzo) è in genere specificata la rete interna. Selezionare login.microsoft.com per il test
32. Fare clic su **Chiudi**.
33. Fare clic su **Service** (Servizio)
34. Fare clic su **All** (Tutti)
35. Fare clic su **Chiudi**.
36. Fare clic su **OK**.
37. Scegliere **Policy & Objects** (Criteri e oggetti) dal menu a sinistra
38. In Policy & Objects fare clic su **Firewall Policy** (Criterio firewall)
39. Espandere **SSL-VPN tunnel interface (ssl.root) -> port** (Interfaccia tunnel VPN SSL -> porta)
40. Fare clic con il pulsante destro del mouse sulla policy VPN creata in precedenza (**VPN GRP 1**) e scegliere **Copy** (Copia)
41. Fare clic con il pulsante destro del mouse sotto la policy VPN e scegliere **Paste** (Incolla) -> **Below** (Sotto)
42. Modificare la nuova policy, specificando un nome diverso, ad esempio **VPN grp2**, e cambiando il gruppo a cui si applica, ossia un altro gruppo di Azure
43. Fare clic con il pulsante destro del mouse sulla nuova policy e scegliere **Enabled** (Abilitata)


## <a name="test-sign-in-using-azure"></a>Testare l'accesso con Azure

1. Usando una sessione privata del browser, passare a `https://<address>` 
2. L'accesso dovrebbe reindirizzare ad Azure Active Directory
3. Dopo aver fornito le credenziali per un utente che è stato assegnato all'app FortiGate nel tenant di Azure, verrà visualizzato il portale utente appropriato

    ![VPN SSL FortiGate](test-sign-in.png)

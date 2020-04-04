---
title: Usare Azure MFA Server con AD FS 2.0 - Azure Active DirectoryUse Azure MFA Server with AD FS 2.0 - Azure Active Directory
description: Questa è la pagina di autenticazione a più fattori di Azure che descrive come iniziare a utilizzare l'autenticazione a più fattori Azure e AD FS 2.0.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c79a42bbd60d7a1857649cffc97ed7f0103fa16
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653505"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Configurare il server Azure Multi-Factor Authentication per lavorare con AD FS 2.0

Questo articolo è rivolto alle organizzazioni che vengono federate con Azure Active Directory e intendono proteggere le risorse che risiedono in locale o nel cloud. È possibile proteggere le risorse usando il server Azure Multi-Factor Authentication e configurandolo per il funzionamento con AD FS in modo da attivare la verifica in due passaggi per gli endpoint di alto valore.

In questa documentazione viene illustrato l'utilizzo di Server Azure multi-Factor Authentication con ADFS 2.0. Per altre informazioni su AD FS, vedere [Protezione delle risorse cloud e locali tramite il server Azure Multi-Factor Authentication con Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> A partire dal 1 luglio 2019, Microsoft non offrirà più MFA Server per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori dai propri utenti devono usare L'autenticazione a più fattori di Azure basata su cloud. I clienti esistenti che hanno attivato MFA Server prima del 1 luglio potranno scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Proteggere ADFS 2.0 con un proxy

Per proteggere AD FS 2.0 con un proxy, installare il server Azure Multi-Factor Authentication nel server proxy di AD FS.

### <a name="configure-iis-authentication"></a>Configurare l'autenticazione IIS

1. Nel server Azure Multi-Factor Authentication fare clic sull'icona **Autenticazione IIS** nel menu a sinistra.
2. Fare clic sulla scheda **Basato su modulo.**
3. Fare clic su **Aggiungi**.

   ![Finestra Autenticazione IIS di MFA Server](./media/howto-mfaserver-adfs-2/setup1.png)

4. Per rilevare automaticamente le variabili di nome utente, `https://sso.contoso.com/adfs/ls`password e dominio, immettere l'URL di accesso (ad esempio ) nella finestra di dialogo Configura automaticamente sito Web basato su moduli e fare clic su **OK**.
5. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti alla verifica in due passaggi, selezionare la casella di controllo **Richiedi corrispondenza utente di Multi-Factor Authentication**. Se un numero significativo di utenti non è ancora stato importato nel server e/o non sarà soggetto alla verifica in due passaggi, lasciare deselezionata la casella.
6. Se le variabili di pagina non possono essere rilevate automaticamente, fare clic su **Specifica manualmente...** pulsante nella finestra di dialogo Configura automaticamente sito Web.
7. Nella finestra di dialogo Aggiungi sito Web basato su moduli immettere l'URL della pagina di accesso di ADFS nel campo Invia URL (ad esempio `https://sso.contoso.com/adfs/ls`) e immettere un Nome applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.
8. Impostare il formato della richiesta su **POST o GET**.
9. Immettere la variabile di nome utente (ctl00$ ContentPlaceHolder1$ UsernameTextBox) e la variabile Password (ctl00$ ContentPlaceHolder1$ PasswordTextBox). Se la pagina di accesso basata su form viene visualizzata una casella di testo di dominio, immettere anche la variabile di dominio. Per individuare i nomi delle caselle di input nella pagina di accesso, passare alla pagina di accesso in un Web browser, fare clic con il pulsante destro del mouse nella pagina e quindi scegliere **Visualizza origine**.
10. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti alla verifica in due passaggi, selezionare la casella di controllo **Richiedi corrispondenza utente di Multi-Factor Authentication**. Se un numero significativo di utenti non è ancora stato importato nel server e/o non sarà soggetto alla verifica in due passaggi, lasciare deselezionata la casella.

    ![Aggiungere un sito Web basato su moduli a MFA Server](./media/howto-mfaserver-adfs-2/manual.png)

11. Fare clic su **Avanzate...** per verificare le impostazioni avanzate. Ecco le impostazioni che possono essere configurate:

    - Selezionare un file di paging di rifiuto personalizzato
    - Memorizzare nella cache le autenticazioni riuscite per il sito Web tramite cookie
    - Selezionare la modalità di autenticazione delle credenziali primarie

12. Poiché il server proxy AD FS non è destinato a essere aggiunto al dominio, è possibile usare LDAP per connettersi al controller di dominio per l'importazione utente e la preautenticazione. Nella finestra di dialogo Sito Web avanzato basato su form, fare clic sulla scheda **Autenticazione primaria** e selezionare **Binding LDAP** per il tipo di autenticazione di preautenticazione.
13. Al termine, fare clic su **OK** per tornare alla finestra di dialogo Aggiungi sito Web basato su form.
14. Fare clic su **OK** per chiudere la finestra di dialogo.
15. Dopo aver rilevato o immesso le variabili di pagina e URL, i dati del sito Web vengono visualizzati nel pannello Basata su form.
16. Fare clic sulla scheda **Modulo nativo** e selezionare il server, il sito Web in cui è in esecuzione il proxy ADFS (ad esempio "Sito Web predefinito") o l'applicazione proxy ADFS (ad esempio "ls" in "adfs") per abilitare il plug-in IIS al livello desiderato.
17. Scegliere la casella **Abilita autenticazione IIS** nella parte superiore della schermata.

L'autenticazione di IIS è abilitato.

### <a name="configure-directory-integration"></a>Configurare l'integrazione di directory

L'autenticazione di IIS è stata abilitata, tuttavia per eseguire la preautenticazione per Active Directory (AD) tramite LDAP è necessario configurare la connessione LDAP al controller di dominio.

1. Fare clic sull'icona **Integrazione directory**.
2. Nella scheda Impostazioni selezionare il pulsante di opzione **Usa configurazione LDAP specifica**.

   ![Configurare le impostazioni LDAP per impostazioni LDAP specifiche](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Fare clic su **Edit**.
4. Nella finestra di dialogo Modifica configurazione LDAP, compilare i campi con le informazioni necessarie per connettersi al controller di dominio Active Directory. Le descrizioni dei campi sono incluse nel file della Guida del server Azure Multi-Factor Authentication.
5. Verificare la connessione LDAP facendo clic sul pulsante **Test**.

   ![Testare la configurazione LDAP nel server MFATest LDAP Configuration in MFA Server](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Se il test della connessione LDAP ha esito positivo, fare clic su **OK**.

### <a name="configure-company-settings"></a>Configurare le impostazioni aziendali

1. Quindi, fare clic sull'icona **Impostazioni società** e selezionare la scheda **Risoluzione nome utente.**
2. Selezionare il pulsante di opzione **Usa attributo dell'identificatore univoco LDAP per la corrispondenza dei nomi utente**.
3. Se gli utenti immettono il proprio nome utente nel formato "dominio/nomeutente", il server deve essere in grado di rimuovere il dominio dal nome utente quando crea la query LDAP. Che può essere eseguita tramite un'impostazione del Registro di sistema.
4. Aprire l'editor del Registro di sistema e passare a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/positivo Networks/PhoneFactor su un server a 64 bit. Se su un server a 32 bit, estrarre il "Wow6432Node" dal percorso. Creare una chiave del Registro di sistema DWORD denominata "UsernameCxz_stripPrefixDomain" e impostare il valore su 1. Il proxy AD FS è ora protetta da Azure Multi-Factor Authentication.

Assicurarsi che gli utenti siano stati importati da Active Directory nel Server. Vedere la [sezione IP attendibili](#trusted-ips) se si desidera consentire gli indirizzi IP interni in modo che la verifica in due passaggi non sia necessaria quando si accede al sito Web da tali posizioni.

![Editor del Registro di sistema per configurare le impostazioni aziendali](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 diretto senza un proxy

È possibile proteggere ADFS quando il proxy di ADFS non viene usato. Installare il server Azure Multi-Factor Authentication nel server ADFS e configurare il server seguendo questa procedura:

1. All'interno del server Azure Multi-Factor Authentication, fare clic sull'icona **IIS Authentication** nel menu a sinistra.
2. Fare clic sulla scheda **HTTP.**
3. Fare clic su **Aggiungi**.
4. Nella finestra di dialogo Aggiungi URL di base immettere l'URL `https://sso.domain.com/adfs/ls/auth/integrated`del sito Web ADFS in cui viene eseguita l'autenticazione HTTP (ad esempio ) nel campo URL di base. e immettere un nome di applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.
5. Se si desidera, modificare il timeout di inattività e massimo per i tempi di sessione.
6. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti alla verifica in due passaggi, selezionare la casella di controllo **Richiedi corrispondenza utente di Multi-Factor Authentication**. Se un numero significativo di utenti non è ancora stato importato nel server e/o non sarà soggetto alla verifica in due passaggi, lasciare deselezionata la casella.
7. Se lo si desidera, selezionare la casella di cache di cookie.

   ![AD FS 2.0 diretto senza un proxy](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Fare clic su **OK**.
9. Fare clic sulla scheda **Modulo nativo** e selezionare il server, il sito Web (ad esempio "Sito Web predefinito") o l'applicazione ADFS (ad esempio "ls" in "adfs") per abilitare il plug-in IIS al livello desiderato.
10. Scegliere la casella **Abilita autenticazione IIS** nella parte superiore della schermata.

AD FS è protetto da Azure Multi-Factor Authentication.

Assicurarsi che gli utenti siano stati importati da Active Directory nel Server. Vedere la sezione IP attendibili se si desidera consentire gli indirizzi IP interni in modo che la verifica in due passaggi non sia necessaria quando si accede al sito Web da tali posizioni.

## <a name="trusted-ips"></a>Indirizzi IP attendibili

Gli IP attendibili consentono agli utenti di ignorare Azure Multi-Factor Authentication per le richieste del sito Web provenienti da specifici indirizzi IP o subnet. Ad esempio, è consigliabile fare in modo che gli utenti non debbano eseguire la verifica in due passaggi quando accedono dall'ufficio. A tale scopo, specificare la subnet dell'ufficio come voce di Indirizzi IP attendibili.

### <a name="to-configure-trusted-ips"></a>Per configurare indirizzi IP attendibili

1. Fare clic sulla scheda **IP attendibili** nella sezione Autenticazione IIS.
2. Fare clic sul pulsante **Aggiungi...** .
3. Quando viene visualizzata la finestra di dialogo Aggiungi indirizzo IP attendibile, selezionare uno dei pulsanti di opzione **IP singolo**, **Intervallo IP** o **Subnet**.
4. Immettere l'indirizzo IP, l'intervallo di indirizzi IP o la subnet che devono essere consentiti. Se si immette una subnet, selezionare la subnet mask appropriata e fare clic su **OK**.

![Configurare gli indirizzi IP attendibili nel server MFAConfigure trusted IPs to MFA Server](./media/howto-mfaserver-adfs-2/trusted.png)

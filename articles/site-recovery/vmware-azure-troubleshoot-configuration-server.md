---
title: Risolvere i problemi relativi al server di configurazione durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di distribuzione del server di configurazione per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: ab72091c58420459620352c8169773111149316d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245729"
---
# <a name="troubleshoot-configuration-server-issues"></a>Risolvere i problemi del server di configurazione

Questo articolo offre informazioni sulla risoluzione dei problemi di distribuzione e gestione del server di configurazione di [Azure Site Recovery](site-recovery-overview.md). Il server di configurazione agisce come server di gestione. Usare il server di configurazione per configurare il ripristino di emergenza per i server fisici e le macchine virtuali VMware locali in Azure mediante Site Recovery. Le sezioni seguenti illustrano gli errori più comuni che si verificano quando si aggiunge un nuovo server di configurazione e quando si gestisce un server di configurazione.

## <a name="registration-failures"></a>Errori di registrazione

Il computer di origine esegue la registrazione con il server di configurazione quando si installa l'agente di mobilità. Eseguire il debug di eventuali errori durante questo passaggio attenendosi alle linee guida:

1. Aprire il file C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (La cartella ProgramData potrebbe essere una cartella nascosta. Se la cartella ProgramData in Esplora File non viene visualizzata, nella scheda **Visualizza** all'interno della sezione **Mostra/Nascondi** selezionare la casella di controllo **Elementi nascosti**). Le cause degli errori posso essere molteplici.

2. Cercare la stringa **Nessun indirizzo IP valido trovato**. Se si trova la stringa:
    1. Verificare che l'ID host richiesto sia quello usato per l'ID host della macchina di origine.
    2. Verificare che la macchina di origine disponga di almeno un indirizzo IP assegnato alla scheda di interfaccia di rete fisica. Per la registrazione dell'agente con il server di configurazione, il computer di origine deve avere almeno un indirizzo IP v4 assegnato alla scheda di interfaccia di rete fisica.
    3. Eseguire uno dei seguenti comandi nella macchina di origine per ottenere tutti gli indirizzi IP della stessa:
      - Per Windows: `> ipconfig /all`
      - Per Linux: `# ifconfig -a`

3. Se non si trova la stringa **Nessun indirizzo IP valido trovato**, cercare la stringa **Motivo=>NULL**. Questo errore si verifica quando il computer di origine usa un host vuoto per effettuare la registrazione con il server di configurazione. Se si trova la stringa:
    - Dopo aver risolto i problemi, attenersi alle linee guida contenute in: [Registrare il computer di origine con il server di configurazione](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) per ripetere la registrazione manuale.

4. Se non si trova la stringa **Motivo=>NULL**, aprire il file C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log nella macchina di origine. (La cartella ProgramData potrebbe essere una cartella nascosta. Se la cartella ProgramData in Esplora File non viene visualizzata, nella scheda **Visualizza** all'interno della sezione **Mostra/Nascondi** selezionare la casella di controllo **Elementi nascosti**). Le cause degli errori posso essere molteplici. 

5. Cercare la stringa **richiesta post: (7): Impossibile connettersi al server**. Se si trova la stringa:
    1. Risolvere i problemi di rete tra il computer di origine e il server di configurazione. Verificare che il server di configurazione sia raggiungibile dalla macchina di origine con gli strumenti di rete, ad esempio un Web browser, ping o traceroute. Assicurarsi che tale macchina di origine sia in grado di raggiungere il server di configurazione tramite la porta 443.
    2. Verificare se siano presenti regole del firewall nel computer di origine che bloccano la connessione tra il computer di origine e il server di configurazione. Rivolgersi all'amministratore di rete per sbloccare i problemi di connessione.
    3. Assicurarsi che le cartelle elencate in [Esclusioni della cartella Azure Site Recovery dal programma antivirus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) vengano escluse dal software antivirus.
    4. Dopo aver risolto i problemi di rete, ripetere la registrazione attenendosi alle linee guida contenute in: [Registrare il computer di origine con il server di configurazione](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Se non si trova la stringa **richiesta post: (7) - Impossibile connettersi al server** nello stesso file di log, cercare la stringa **richiesta: (60) - Impossibile autenticare il certificato peer con i certificati CA assegnati**. Questo errore può verificarsi perché è scaduto il certificato del server di configurazione oppure perché il computer di origine non supporta il protocollo TLS 1.0 o le versioni successive del protocollo SSL. Si potrebbe verificare anche se un firewall blocca le comunicazioni SSL tra il computer di origine e il server di configurazione. Se si trova la stringa: 
    1. Per risolvere il problema, connettersi all'indirizzo IP del server di configurazione usando un Web browser sul computer di origine. Usare il protocollo https URI:\/\/<indirizzo IP del server di configurazione\>: 443 /. Assicurarsi che tale macchina di origine sia in grado di raggiungere il server di configurazione tramite la porta 443.
    2. Verificare se siano presenti regole del firewall nel computer di origine che devono essere aggiunte o rimosse dal computer di origine per far sì che comunichi con il server di configurazione. A causa della varietà di software firewall che potrebbero essere in uso, non è possibile fornire un elenco di tutte le configurazioni firewall necessarie. Rivolgersi all'amministratore di rete per sbloccare i problemi di connessione.
    3. Assicurarsi che le cartelle elencate in [Esclusioni della cartella Azure Site Recovery dal programma antivirus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) vengano escluse dal software antivirus.  
    4. Dopo aver risolto i problemi, ritentare la registrazione attenendosi alle linee guida contenute in [Registrare il computer di origine con il server di configurazione](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. In Linux, se il valore della piattaforma in <INSTALLATION_DIR>\>/etc/drscout.conf è danneggiato, la registrazione non avviene correttamente. Per identificare questo problema, aprire il file /var/log/ua_install.log. Cercare la stringa: **L'interruzione della configurazione come valore VM_PLATFORM è null o non è VmWare/Azure**. La piattaforma deve essere impostata su **VmWare** o **Azure**. Se il file drscout.conf è danneggiato, è consigliabile [disinstallare l'agente di mobilità](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) e quindi installarlo nuovamente. Se la disinstallazione non è riuscita, completare questi passaggi:
    1. Aprire il file Installation_Directory/uninstall.sh e commentare la chiamata alla funzione **StopServices**.
    2. Aprire il file Installation_Directory/Vx/bin/uninstall.sh e commentare la chiamata alla funzione **stop_services**.
    3. Aprire il file Installation_Directory/Fx/uninstall e commentare la sezione completa che prova a fermare il servizio Fx.
    4. [Disinstallare](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) l'agente di mobilità. Al termine della disinstallazione, riavviare il sistema e provare nuovamente a installare l'agente.

## <a name="installation-failure-failed-to-load-accounts"></a>Errore di installazione: Impossibile caricare gli account

Questo errore si verifica quando il servizio non riesce a leggere i dati dalla connessione di trasporto durante l'installazione dell'agente di mobilità e la registrazione del server di configurazione. Per risolvere il problema, assicurarsi che nel computer di origine sia abilitato TLS 1.0.

## <a name="vcenter-discovery-failures"></a>Errori di individuazione di vCenter

Per risolvere gli errori di individuazione di vCenter, aggiungere il server vCenter alle impostazioni proxy dell'elenco di esclusione. 

- scaricare lo strumento PsExec da [qui](https://aka.ms/PsExec) per accedere al contenuto utente del sistema.
- Aprire Internet Explorer nel contenuto utente del sistema eseguendo la riga di comando seguente: psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Aggiungere le impostazioni del proxy in Internet Explorer e riavviare il servizio tmanssvc.
- Per configurare le impostazioni del proxy DRA, eseguire cd C:\Programmi\Microsoft Azure Site Recovery Provider
- Eseguire quindi DRCONFIGURATOR.EXE /configure /AddBypassUrls [aggiungere l'indirizzo IP/il nome di dominio completo del server vCenter ottenuto nel passaggio **Configurare il server vCenter/vSphere ESXi** della procedura [Distribuzione del server di configurazione](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Cambiare l'indirizzo IP del server di configurazione

È consigliabile non modificare l'indirizzo IP di un server di configurazione. Assicurarsi che tutti gli indirizzi IP assegnati al server di configurazione siano indirizzi IP statici. Non usare indirizzi IP DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: Token SAML non valido

Per evitare che si verifichi questo errore assicurarsi che l'ora del clock di sistema non differisca di oltre 15 minuti rispetto all'ora locale. Eseguire di nuovo il programma di installazione per completare la registrazione.

## <a name="failed-to-create-a-certificate"></a>Creazione del certificato non riuscita

Impossibile creare un certificato richiesto per l'autenticazione di Site Recovery. Eseguire di nuovo l'installazione dopo essersi assicurati di eseguire il programma di installazione come amministratore locale.

## <a name="failure-to-activate-windows-licence-from-server-standard-evaluation-to-server-standard"></a>Errore durante l'attivazione della licenza di Windows dalla versione Server Standard Evaluation a Server Standard

1. Nell'ambito della distribuzione di server di configurazione tramite OVF viene usata una licenza di valutazione, valida per 180 giorni. È necessario attivare la licenza prima che scada. In caso contrario, questo può comportare l'arresto frequente del server di configurazione, pregiudicando le attività di replica.
2. Se non è possibile attivare la licenza di Windows, rivolgersi al [team di supporto di Windows](https://aka.ms/Windows_Support) per risolvere il problema.

## <a name="register-source-machine-with-configuration-server"></a>Registrare il computer di origine con il server di configurazione

### <a name="if-the-source-machine-runs-windows"></a>Se la macchina di origine esegue Windows

Eseguire il comando seguente sul computer di origine:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Impostazione | Dettagli
--- | ---
Uso | UnifiedAgentConfigurator.exe  /CSEndPoint <configurazione indirizzo IP del server\> /PassphraseFilePath <passphrase file path\>
Log di configurazione dell'agente | In %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parametro obbligatorio. Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
/PassphraseFilePath |  Obbligatorio. Percorso della passphrase. Usare qualsiasi percorso file locale o UNC valido.

### <a name="if-the-source-machine-runs-linux"></a>Se la macchina di origine esegue Linux

Eseguire il comando seguente sul computer di origine:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Impostazione | Dettagli
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <configurazione dell'indirizzo IP del server\> -P <passphrase file path\>
-i | Parametro obbligatorio. Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
-P |  Obbligatorio. Percorso completo del file in cui è stata salvata la passphrase. Usare qualsiasi cartella valida.

## <a name="unable-to-configure-the-configuration-server"></a>Non è possibile configurare il server di configurazione

Se si installano applicazioni diverse dal server di configurazione nella macchina virtuale, potrebbe essere impossibile configurare la destinazione master. 

Il server di configurazione deve essere un server dedicato e il suo utilizzo come server condiviso non è supportato. 

Per altre informazioni, vedere le domande frequenti in [Distribuire un server di configurazione](vmware-azure-deploy-configuration-server.md#faq). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Rimuovere le voci non aggiornate per gli elementi protetti dal database del server di configurazione 

Per rimuovere un computer protetto non aggiornato nel server di configurazione, usare la procedura seguente. 
 
1. Per determinare il computer di origine e l'indirizzo IP della voce non aggiornata: 

    1. Aprire la riga di comando MYSQL in modalità amministratore. 
    2. Eseguire i comandi seguenti. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Restituisce l'elenco di computer registrati con i relativi indirizzi IP e l'ultimo heartbeat. Trovare l'host che ha coppie di replica non aggiornate.

2. Aprire un prompt dei comandi con privilegi elevati e passare a C:\ProgramData\ASR\home\svsystems\bin. 
4. Per rimuovere i dettagli degli host registrati e le informazioni relative alla voce non aggiornata dal server di configurazione, eseguire il comando seguente usando il computer di origine e l'indirizzo IP della voce non aggiornata. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Se è presente una voce server di origine "OnPrem-VM01" con indirizzo IP 10.0.0.4, usare invece il comando seguente.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Riavviare i servizi seguenti nel computer di origine per ripetere la registrazione con il server di configurazione. 
 
    - Servizio dell'applicazione InMage Scout
    - InMage Scout VX Agent - Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>L'aggiornamento ha esito negativo quando i servizi non si arrestano correttamente

L'aggiornamento del server di configurazione non riesce quando determinati servizi non si arrestano. 

Per identificare il problema, passare a C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile nel server di configurazione. Se si trovano gli errori seguenti, seguire la procedura illustrata per risolvere il problema: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

Per risolvere il problema:

Arrestare manualmente i servizi seguenti:

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Outpost 
- Agente di Servizi di ripristino di Microsoft Azure 
- Servizio Microsoft Azure Site Recovery 
- tmansvc
  
Per aggiornare il server di configurazione, eseguire nuovamente l'[installazione unificata](service-updates-how-to.md#links-to-currently-supported-update-rollups).

## <a name="azure-active-directory-application-creation-failure"></a>Errore di creazione dell'applicazione Azure Active Directory

Usando il modello [OVA (Open Virtualization Application)](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
), non si hanno autorizzazioni sufficienti per creare un'applicazione in Azure Active Directory (AAD).

Per risolvere il problema, accedere al portale di Azure ed eseguire una delle operazioni seguenti:

- Richiedere il ruolo Sviluppatore applicazioni in AAD. Per altre informazioni sul ruolo Sviluppatore applicazioni, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Verificare che il flag **Gli utenti possono registrare applicazioni** sia impostato su *Sì* in AAD. Per ulteriori informazioni, consultare [Come Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>I server di elaborazione/destinazione master non riescono a comunicare con il server di configurazione 

I moduli Server di elaborazione (PS) e Destinazione master (MT) non sono in grado di comunicare con il server di configurazione (CS) e il relativo stato viene visualizzato come non connesso nel portale di Azure.

In genere questo è dovuto a un errore con la porta 443. Usare la procedura seguente per sbloccare la porta e riabilitare la comunicazione con il server di configurazione.

**Verificare che l'agente MARS venga richiamato dall'agente del server di destinazione master**

Per verificare che l'agente del server di destinazione master possa creare una sessione TCP per l'IP del server di configurazione, cercare una traccia simile alla seguente nei log dell'agente del server di destinazione master:

TCP <Replace IP with CS IP here>:52739 <Replace IP with CS IP here>:443 SYN_SENT 

TCP    192.168.1.40:52739     192.168.1.40:443      SYN_SENT  // Qui sostituire IP con l'IP del server di configurazione

Se si trovano tracce simile alle seguenti nei log dell'agente del server di destinazione master, l'agente sta segnalando errori sulla porta 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Questo errore può essere rilevato quando anche altre applicazioni stanno usando la porta 443 o a causa di un'impostazione del firewall che blocca la porta.

Per risolvere il problema:

- Verificare che la porta 443 non sia bloccata dal firewall.
- Se la porta non è raggiungibile perché la sta usando un'altra applicazione, arrestare e disinstallare l'app.
  - Se l'arresto dell'app non è fattibile, configurare un nuovo server di configurazione pulito.
- Riavviare il server di configurazione.
- Riavviare il servizio IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Il server di configurazione non è connesso per la presenza di voci UUID non corrette

Questo errore può verificarsi quando nel database sono presenti più voci UUID di istanza del server di configurazione (CS). Il problema si verifica spesso quando si clona la macchina virtuale del server di configurazione.

Per risolvere il problema:

1. Rimuovere le macchine virtuali dei server di configurazione non aggiornati o precedenti da vCenter. Per altre informazioni, vedere [Rimuovere server e disabilitare la protezione](site-recovery-manage-registration-and-protection.md).
2. Accedere alla macchina virtuale del server di configurazione e connettersi al database MySQL svsdb1. 
3. Eseguire la query seguente:

    > [!IMPORTANT]
    >
    > Verificare di aver immesso i dettagli UUID del server di configurazione clonato o la voce non aggiornata del server di configurazione che non viene più usato per proteggere le macchine virtuali. L'inserimento di un UUID errato comporta la perdita delle informazioni relative a tutti gli elementi protetti esistenti.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Aggiornare la pagina del portale.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Quando si immettono le credenziali si verifica un ciclo di accesso infinito

Dopo aver immesso il nome utente e la password corretti nell'OVF del server di configurazione, la finestra di accesso di Azure continua a chiedere le credenziali corrette.

Questo problema può verificarsi quando l'ora di sistema è errata.

Per risolvere il problema:

Impostare l'ora corretta nel computer e ripetere l'accesso. 
 
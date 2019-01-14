---
title: Risolvere i problemi relativi al server di configurazione durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di distribuzione del server di configurazione per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 597b8f59ef6991f7868d3de481e98ed9a459077b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050796"
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

## <a name="change-the-ip-address-of-the-configuration-server"></a>Cambiare l'indirizzo IP del server di configurazione

È consigliabile non modificare l'indirizzo IP di un server di configurazione. Assicurarsi che tutti gli indirizzi IP assegnati al server di configurazione siano indirizzi IP statici. Non usare indirizzi IP DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: Token SAML non valido

Per evitare che si verifichi questo errore assicurarsi che l'ora del clock di sistema non differisca di oltre 15 minuti rispetto all'ora locale. Eseguire di nuovo il programma di installazione per completare la registrazione.

## <a name="failed-to-create-a-certificate"></a>Creazione del certificato non riuscita

Impossibile creare un certificato richiesto per l'autenticazione di Site Recovery. Eseguire di nuovo l'installazione dopo essersi assicurati di eseguire il programma di installazione come amministratore locale.

## <a name="register-the-source-machine-with-the-configuration-server"></a>Registrare il computer di origine con il server di configurazione

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


---
title: Risolvere i problemi relativi agli errori di backup dei database SAP HANA
description: Viene descritto come risolvere i problemi relativi agli errori comuni che possono verificarsi quando si usa Backup di Azure per eseguire il backup di database SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 5cdad55ef849b9ced31646466e2c2c170ebf0827
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89377685"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Risolvere i problemi relativi al backup di database SAP HANA in Azure

Questo articolo fornisce informazioni per la risoluzione dei problemi relativi al backup di database SAP HANA in macchine virtuali di Azure. Per altre informazioni sugli scenari di backup SAP HANA attualmente supportati, vedere [Supporto degli scenari](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Prerequisiti e autorizzazioni

Fare riferimento alle sezioni [Prerequisiti](tutorial-backup-sap-hana-db.md#prerequisites) e [Funzionalità dello script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) prima di configurare i backup.

## <a name="common-user-errors"></a>Errori utente comuni

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Messaggio di errore**      | <span style="font-weight:normal">Backup di Azure non dispone dei privilegi di ruolo necessari per eseguire il backup</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Possibili cause**    | È possibile che il ruolo sia stato sovrascritto.                          |
| **Azione consigliata** | Per risolvere il problema, eseguire lo script dal riquadro **Individua database** oppure scaricarlo [qui](https://aka.ms/scriptforpermsonhana). In alternativa, aggiungere il ruolo 'SAP_INTERNAL_HANA_SUPPORT' all'utente di Backup del carico di lavoro (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Messaggio di errore      | <span style="font-weight:normal">Non è stato possibile connettersi al sistema HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | È possibile che l'istanza di SAP HANA sia inattiva.<br/>Le autorizzazioni necessarie per il backup di Azure per interagire con il database HANA non sono impostate. |
| **Azione consigliata** | Verificare che il database SAP HANA sia attivo. Se il database è attivo e in esecuzione, verificare che tutte le autorizzazioni necessarie siano impostate. Se manca qualche autorizzazione, eseguire lo [script di pre-registrazione](https://aka.ms/scriptforpermsonhana) per aggiungere le autorizzazioni mancanti. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Messaggio di errore      | <span style="font-weight:normal">L'istanza di SAP HANA specificata non è valida o non è stata trovata</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Non è possibile eseguire il backup di più istanze di SAP HANA in una singola macchina virtuale di Azure. |
| **Azione consigliata** | Eseguire lo [script di pre-registrazione](https://aka.ms/scriptforpermsonhana) nell'istanza di SAP HANA di cui si vuole eseguire il backup. Se il problema persiste, contattare il supporto tecnico Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Messaggio di errore      | <span style="font-weight:normal">L'operazione SAP HANA specificata non è supportata</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Backup di Azure per SAP HANA non supporta il backup incrementale e le azioni eseguite sui client SAP HANA nativi (studio/pozzetto/DBA) |
| **Azione consigliata** | Per altre informazioni, vedere [qui](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Messaggio di errore      | <span style="font-weight:normal">Questo database SAP HANA non supporta il tipo di backup richiesto</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Backup di Azure non supporta il backup incrementale e il backup tramite snapshot |
| **Azione consigliata** | Per altre informazioni, vedere [qui](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Messaggio di errore      | <span style="font-weight:normal">La catena di log di backup è interrotta</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | È possibile che la destinazione di backup del log sia stata aggiornata da backint a file system oppure che il file eseguibile di backint sia stato modificato |
| **Azione consigliata** | Attivare un backup completo per risolvere il problema                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Messaggio di errore      | <span style="font-weight:normal">È stato rilevato un aggiornamento dal sistema SDC al sistema MDC</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | L'istanza di SAP HANA è stata aggiornata da SDC a MDC. Dopo l'aggiornamento i backup non riusciranno. |
| **Azione consigliata** | Per risolvere il problema, seguire i passaggi descritti in [Aggiornamento da SDC a MDC](#sdc-to-mdc-upgrade-with-a-change-in-sid) |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Messaggio di errore      | <span style="font-weight:normal">La configurazione di Backint rilevata non è valida</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | I parametri di supporto non sono specificati correttamente per backup di Azure |
| **Azione consigliata** | Verificare che siano impostati i parametri seguenti (backint):<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>Se in HOST sono presenti parametri basati su backint, rimuoverli. Se i parametri non sono presenti a livello di HOST ma sono stati modificati manualmente a livello di database, ripristinare i valori appropriati come descritto in precedenza. In alternativa, eseguire la procedura per [arrestare la protezione e conservare i dati di backup](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) dal portale di Azure e quindi selezionare **Riprendi backup**. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Messaggio di errore  |I sistemi di origine e destinazione per il ripristino sono incompatibili  |
|---------|---------|
|Possibili cause   | I sistemi di origine e destinazione selezionati per il ripristino sono incompatibili        |
|Azione consigliata   |   Assicurarsi che lo scenario di ripristino non sia incluso nell'elenco seguente di possibili ripristini incompatibili: <br><br>   **Caso 1:** non è possibile rinominare SYSTEMDB durante il ripristino.  <br><br> **Caso 2:** con origine SDC e destinazione MDC: non è possibile ripristinare il database di origine come SYSTEMDB o database tenant nella destinazione. <br><br> **Caso 3:** con origine MDC e destinazione SDC: non è possibile ripristinare il database di origine (SYSTEMDB o database tenant) nella destinazione. <br><br>  Per altre informazioni, vedere la nota **1642148** nel [launchpad del supporto SAP](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Controlli di ripristino

### <a name="single-container-database-sdc-restore"></a>Ripristino di un contenitore di database singolo (SDC)

Prestare attenzione agli input durante il ripristino di un contenitore di database singolo (SDC) per HANA in un altro computer con SDC. Il nome del database deve essere specificato in lettere minuscole e includere il suffisso "sdc" racchiuso tra parentesi quadre. L'istanza di HANA verrà visualizzata in lettere maiuscole.

Si supponga di eseguire il backup di un'istanza SDC HANA denominata "H21". Il nome dell'elemento di backup visualizzato nella pagina degli elementi di backup sarà **"h21(sdc)"** . Se si prova a ripristinare questo database in un altro SDC di destinazione, ad esempio H11, è necessario specificare gli input seguenti.

![Nome del database SDC ripristinato](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenere presente quanto segue:

- Per impostazione predefinita, il nome del database ripristinato verrà popolato con il nome dell'elemento di backup, in questo caso h21(sdc).
- Se si seleziona la destinazione come H11, il nome del database ripristinato non verrà modificato automaticamente. **Deve essere modificato in h11(sdc)** . Per quanto riguarda SDC, il nome del database ripristinato corrisponderà all'ID istanza di destinazione scritto in lettere minuscole e con il suffisso 'sdc' racchiuso tra parentesi quadre.
- Poiché la DSC può avere un solo database singolo, è necessario selezionare la casella di controllo per consentire l'override dei dati del database esistenti con i dati del punto di ripristino.
- Linux fa distinzione tra maiuscole e minuscole, di conseguenza prestare attenzione a rispettare tale distinzione.

### <a name="multiple-container-database-mdc-restore"></a>Ripristino di un contenitore di più database (MDC)

Nei contenitori di più database per HANA la configurazione standard prevede SYSTEMDB e uno o più database tenant. Il ripristino di un'intera istanza di SAP HANA implica il ripristino sia di SYSTEMDB che dei database tenant. Si inizia con il ripristino di SYSTEMDB e quindi si procede con quello del database tenant. Con il ripristino di SYSTEMDB viene in pratica eseguito l'override delle informazioni di sistema nella destinazione selezionata, nonché delle informazioni correlate a BackInt nell'istanza di destinazione. Di conseguenza, dopo il ripristino di SYSTEMDB in un'istanza di destinazione, eseguire di nuovo lo script di pre-registrazione. Solo allora sarà possibile eseguire il ripristino dei database tenant successivi.

## <a name="back-up-a-replicated-vm"></a>Eseguire il backup di una macchina virtuale replicata

### <a name="scenario-1"></a>Scenario 1

La macchina virtuale originale è stata replicata con Azure Site Recovery o il backup della macchina virtuale di Azure. La nuova macchina virtuale è stata creata per simulare quella precedente, di conseguenza le impostazioni sono identiche. Questo perché la macchina virtuale originale è stata eliminata e il ripristino è stato eseguito dal backup della macchina virtuale o da Azure Site Recovery.

Questo scenario può includere due casi possibili. Ecco come eseguire il backup della macchina virtuale replicata in entrambi i casi:

1. La nuova macchina virtuale creata ha lo stesso nome e si trova nello stesso gruppo di risorse e nella stessa sottoscrizione della macchina virtuale eliminata.

    - L'estensione è già presente nella macchina virtuale, ma non è visibile ad alcun servizio
    - Eseguire lo script di pre-registrazione
    - Ripetere la registrazione dell'estensione per lo stesso computer nel portale di Azure (**Backup** -> **Visualizza dettagli** -> Selezionare la macchina virtuale di Azure pertinente -> Ripeti registrazione)
    - Il backup dei database già esistenti (dalla macchina virtuale eliminata) dovrebbe avviarsi correttamente

2. La nuova macchina virtuale creata presenta:

    - un nome diverso rispetto a quello della macchina virtuale eliminata
    - lo stesso nome della macchina virtuale eliminata ma si trova in un gruppo di risorse diverso o in una sottoscrizione diversa (rispetto alla macchina virtuale eliminata)

    In questo caso seguire questa procedura:

    - L'estensione è già presente nella macchina virtuale, ma non è visibile ad alcun servizio
    - Eseguire lo script di pre-registrazione
    - Se si individuano e proteggono i nuovi database, nel portale si inizieranno a vedere database attivi duplicati. Per evitare questo problema, [arrestare la protezione conservando i dati](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) per i database obsoleti. Continuare quindi con i passaggi rimanenti.
    - Individuare i database per abilitare il backup
    - Abilitare i backup in questi database
    - I database già esistenti di cui è stato eseguito il backup (dalla macchina virtuale eliminata) continueranno a essere archiviati nell'insieme di credenziali e i relativi backup verranno conservati in base ai criteri

### <a name="scenario-2"></a>Scenario 2

La macchina virtuale originale è stata replicata con Azure Site Recovery o il backup della macchina virtuale di Azure. La nuova macchina virtuale creata non include contenuto in modo da poterla usare come modello. Si tratta di una nuova macchina virtuale con un nuovo SID.

Per abilitare i backup nella nuova macchina virtuale, seguire questa procedura:

- L'estensione è già presente nella macchina virtuale, ma non è visibile ad alcun servizio
- Eseguire lo script di pre-registrazione. In base al SID della nuova macchina virtuale, possono presentarsi due scenari:
  - La macchina virtuale originale e la nuova macchina virtuale hanno lo stesso SID. Lo script di pre-registrazione viene eseguito correttamente.
  - La macchina virtuale originale e la nuova macchina virtuale hanno SID diversi. Lo script di pre-registrazione non viene eseguito correttamente. Contattare il supporto tecnico per ottenere assistenza per questo scenario.
- Individuare i database di cui eseguire il backup
- Abilitare i backup in questi database

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Aggiornamento della versione di SDC o aggiornamento della versione di MDC nella stessa macchina virtuale

Gli aggiornamenti al sistema operativo, la modifica della versione di SDC o la modifica della versione di MDC che non comportano una modifica del SID possono essere gestiti come descritto di seguito:

- Assicurarsi che la nuova versione del sistema operativo, la versione di SDC o quella di MDC siano attualmente [supportate da Backup di Azure](sap-hana-backup-support-matrix.md#scenario-support)
- [Arrestare la protezione conservando i dati](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) per il database
- Eseguire l'aggiornamento
- Eseguire di nuovo lo script di pre-registrazione. In genere, con il processo di aggiornamento vengono rimossi i ruoli necessari. L'esecuzione dello script di pre-registrazione consente di verificare la presenza di tutti i ruoli necessari
- Riprendere di nuovo la protezione per il database

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>Aggiornamento da SDC a MDC senza modifica del SID

Gli aggiornamenti da SDC a MDC che non implicano una modifica del SID possono essere gestiti come illustrato di seguito:

- Assicurarsi che la nuova versione di MDC sia attualmente [supportata da Backup di Azure](sap-hana-backup-support-matrix.md#scenario-support)
- [Arrestare la protezione conservando i dati](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) per il database meno recente
- Eseguire l'aggiornamento. Al termine, il sistema HANA risulta di tipo MDC e include un database di sistema e database tenant
- Eseguire di nuovo lo [script di pre-registrazione](https://aka.ms/scriptforpermsonhana)
- Ripetere la registrazione dell'estensione per lo stesso computer nel portale di Azure (**Backup** -> **Visualizza dettagli** -> Selezionare la macchina virtuale di Azure pertinente -> Ripeti registrazione)
- Selezionare **riindividuare** i database per la stessa macchina virtuale. Con questa azione i nuovi database del passaggio 3 dovrebbero essere SYSTEMDB e tenant DB, non SDC
- Il database SDC meno recente continuerà a esistere nell'insieme di credenziali e i dati di backup obsoleti verranno conservati in base ai criteri
- Configurare il backup per questi database

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>Aggiornamento da SDC a MDC con modifica del SID

Gli aggiornamenti da SDC a MDC che implicano una modifica del SID possono essere gestiti come illustrato di seguito:

- Assicurarsi che la nuova versione di MDC sia attualmente [supportata da Backup di Azure](sap-hana-backup-support-matrix.md#scenario-support)
- **Arrestare la protezione conservando i dati** per il database meno recente
- Eseguire l'aggiornamento. Al termine, il sistema HANA risulta di tipo MDC e include un database di sistema e database tenant
- Eseguire di nuovo lo [script di pre-registrazione](https://aka.ms/scriptforpermsonhana) con i dettagli corretti (nuovo SID e MDC). In seguito a una modifica del SID, potrebbero verificarsi problemi durante l'esecuzione dello script. Se si verificano problemi, contattare il supporto di Backup di Azure.
- Ripetere la registrazione dell'estensione per lo stesso computer nel portale di Azure (**Backup** -> **Visualizza dettagli** -> Selezionare la macchina virtuale di Azure pertinente -> Ripeti registrazione)
- Selezionare **riindividuare** i database per la stessa macchina virtuale. Con questa azione i nuovi database del passaggio 3 dovrebbero essere SYSTEMDB e tenant DB, non SDC
- Il database SDC meno recente continuerà a esistere nell'insieme di credenziali e i dati di backup obsoleti verranno conservati in base ai criteri
- Configurare il backup per questi database

## <a name="re-registration-failures"></a>Errori di ripetizione della registrazione

Prima di attivare l'operazione di ripetizione della registrazione, verificare la presenza di uno o più dei sintomi seguenti:

- Tutte le operazioni, ad esempio il backup, il ripristino e la configurazione del backup, non riescono nella macchina virtuale e viene restituito uno dei codici di errore seguenti: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Se nell'area **Stato backup** relativa all'elemento di backup è visualizzato il messaggio **Non raggiungibile**, escludere tutte le altre cause che potrebbero comportare lo stesso stato:

  - Mancanza di autorizzazioni per eseguire operazioni correlate al backup nella macchina virtuale
  - La macchina virtuale è stata arrestata e quindi non è possibile eseguire i backup
  - Problemi di rete

Questi sintomi possono comparire per uno o più dei motivi seguenti:

- Un'estensione è stata eliminata o disinstallata dal portale.
- La macchina virtuale è stata ripristinata a un punto precedente tramite il ripristino del disco sul posto.
- La macchina virtuale si trova in uno stato di arresto da molto tempo e quindi la configurazione dell'estensione è scaduta.
- La macchina virtuale è stata eliminata e ne è stata creata un'altra con lo stesso nome e nello stesso gruppo di risorse di quella eliminata.

Negli scenari precedenti è consigliabile attivare un'operazione di ripetizione della registrazione nella macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [domande frequenti](./sap-hana-faq-backup-azure-vm.md) sul backup di database SAP HANA nelle macchine virtuali di Azure.

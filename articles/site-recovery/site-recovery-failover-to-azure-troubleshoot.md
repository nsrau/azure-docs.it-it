---
title: Risoluzione dei problemi di failover di Azure | Microsoft Docs
description: Questo articolo descrive come risolvere gli errori comuni durante l'esecuzione del failover in Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/04/2019
ms.author: mayg
ms.openlocfilehash: 2156ee6cf27ecfa32b19ad5bbef7549e99c3f7ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61280627"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Risolvere gli errori durante il failover di un computer fisico o di una macchina virtuale VMware in Azure

Durante il failover di una macchina virtuale in Azure è possibile che l'utente riceva uno dei seguenti errori. Per risolvere i problemi, usare i passaggi descritti per ogni condizione di errore.

## <a name="failover-failed-with-error-id-28031"></a>Il failover non è riuscito con errore ID 28031

Site Recovery non è riuscito a creare un'operazione di failover sulla macchina virtuale in Azure. Questo può verificarsi a causa di uno dei motivi seguenti:

* Non è disponibile una quota sufficiente per creare la macchina virtuale: È possibile controllare la quota disponibile in Sottoscrizione -> Utilizzo e quote. È possibile aprire una [nuova richiesta di supporto](https://aka.ms/getazuresupport) per aumentare la quota.

* Si sta tentando di effettuare il failover delle macchine virtuali delle famiglie di dimensioni diverse nello stesso set di disponibilità. Assicurarsi di aver scelto la stessa famiglia di dimensioni per tutte le macchine virtuali nello stesso set di disponibilità. È possibile modificare le dimensioni passando alle impostazioni Calcolo e rete della macchina virtuale e ritentando il failover.

* Nella sottoscrizione esiste un criterio che impedisce la creazione di una macchina virtuale. Modificare i criteri per consentire la creazione di una macchina virtuale, quindi ripetere il failover.

## <a name="failover-failed-with-error-id-28092"></a>Il failover non è riuscito con errore ID 28092

Site Recovery non è riuscito a creare un'interfaccia di rete per il failover della macchina virtuale. Assicurarsi di disporre di una quota sufficiente per creare interfacce di rete nella sottoscrizione. È possibile controllare la quota disponibile in Sottoscrizione -> Utilizzo e quote. È possibile aprire una [nuova richiesta di supporto](https://aka.ms/getazuresupport) per aumentare la quota. Se si dispone di una quota sufficiente, potrebbe trattarsi di un problema intermittente, riprovare. Se il problema persiste anche dopo vari tentativi, lasciare un commento alla fine di questo documento.  

## <a name="failover-failed-with-error-id-70038"></a>Il failover non è riuscito con errore ID 70038

Site Recovery non è riuscito a creare un'operazione di failover sulla macchina virtuale classica in Azure. Questo può verificarsi perché:

* Non esiste una delle risorse, ad esempio una rete virtuale necessaria per la macchina virtuale da creare. Creare la rete virtuale come specificato nelle impostazioni di Calcolo e rete della macchina virtuale o modificare l'impostazione per una rete virtuale che esiste già, quindi riprovare a eseguire il failover.

## <a name="failover-failed-with-error-id-170010"></a>Il failover non è riuscito con errore ID 170010

Site Recovery non è riuscito a creare un'operazione di failover sulla macchina virtuale in Azure. Questa condizione potrebbe essersi verificata perché un'attività di idratazione interna non è riuscita per la macchina virtuale locale.

Per visualizzare tutte le macchine in Azure, l'ambiente Azure richiede che alcuni driver siano in stato di esecuzione avvio e che servizi come DHCP siano nello stato di avvio automatico. Di conseguenza, l'attività di idratazione, al momento del failover, converte il tipo di **driver atapi, intelide, storflt, vmbus, e storvsc** in esecuzione avvio. Converte anche il tipo di avvio di alcuni servizi, ad esempio DHCP in avvio automatico. Questa attività può non essere eseguita correttamente a causa di problemi specifici nell'ambiente. 

Per modificare manualmente il tipo di avvio dei driver per il **sistema operativo guest Windows**, seguire la procedura seguente:

1. [Scaricare](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) lo script no-hydration ed eseguirlo nel modo seguente. Questo script verifica se è necessario eseguire l'idratazione della macchina virtuale.

    `.\Script-no-hydration.ps1`

    Se è necessaria, genera il risultato seguente:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    Nel caso in cui la macchina virtuale soddisfi il requisito di non idratazione, lo script genererà il risultato indicante che questo sistema soddisfa il requisito di non attivazione. In questo caso, tutti i driver e i servizi hanno lo stato richiesto da Azure e l'idratazione della macchina virtuale non è necessaria.

2. Eseguire lo script no-hydration-set come indicato di seguito se la macchina virtuale non soddisfa il requisito di non idratazione.

    `.\Script-no-hydration.ps1 -set`
    
    In questo modo si convertirà il tipo di avvio automatico dei driver e si otterrà un risultato simile al seguente:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Impossibile connettersi o stabilire una connessione RDP/SSH alla macchina virtuale con failover perché il pulsante di connessione è disabilitato nella macchina virtuale

Se su una macchina virtuale in cui è stato eseguito il failover, il pulsante **Connetti** risulta disabilitato e non si è connessi ad Azure tramite una connessione di Ciclo di lavorazione espresso o VPN da sito a sito, allora:

1. Andare a **Macchina virtuale** > **Rete** e fare clic sul nome dell'interfaccia di rete richiesta.  ![interfaccia di rete](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Passare a **Configurazioni IP** e quindi fare clic sul campo del nome della configurazione IP richiesta. ![Configurazioni IP](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Per abilitare l'indirizzo IP pubblico, fare clic su **Abilita**. ![Abilita IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Fare clic su **Configurare le impostazioni necessarie** > **Crea nuovo**. ![Crea nuovo](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Immettere il nome dell'indirizzo pubblico, scegliere le opzioni predefinite per **SKU** e **assegnazione** e quindi fare clic su **OK**.
6. Per salvare le modifiche apportate, fare clic su **Salva**.
7. Chiudere i pannelli e passare alla sezione **Panoramica** della macchina virtuale per connettersi o stabilire la connessione RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>La connessione a RDP/SSH non è riuscita - Pulsante di connessione alla macchina virtuale disponibile

Se su una macchina virtuale in cui è stato eseguito il failover, il pulsante **Connetti** risulta disponibile (non disabilitato) controllare la **Diagnostica di avvio** nella macchina virtuale e verificare la presenza di errori come indicato in [questo articolo](../virtual-machines/windows/boot-diagnostics.md).

1. Se la macchina virtuale non si è avviata, provare a effettuare il failover a un punto di ripristino meno recente.
2. Se l'applicazione all'interno della macchina virtuale non si avvia, provare a effettuare il failover a un punto di ripristino coerente con l'app.
3. Se la macchina virtuale è aggiunta a un dominio, verificare che il controller di dominio funzioni accuratamente. Questa operazione può essere eseguita seguendo la procedura indicata di seguito:

    a. Creare una nuova macchina virtuale nella stessa rete.

    b.  Assicurarsi che possa essere aggiunta allo stesso dominio in cui è previsto che la macchina virtuale con failover venga avviata.

    c. Se il controller di dominio **non** funziona accuratamente, provare ad accedere alla macchina virtuale con failover usando un account amministratore locale.
4. Se si usa un server DNS personalizzato, assicurarsi che sia raggiungibile. Questa operazione può essere eseguita seguendo la procedura indicata di seguito:

    a. Creare una nuova macchina virtuale nella stessa rete e

    b. Verificare se la macchina virtuale è in grado di procedere alla risoluzione dei nomi tramite il server DNS personalizzato

>[!Note]
>Per l'abilitazione di qualsiasi altra impostazione che non sia Diagnostica di avvio è necessario installare l'agente di macchine virtuali di Azure nella macchina virtuale prima del failover.

## <a name="unexpected-shutdown-message-event-id-6008"></a>Messaggio di arresto imprevisto (ID evento 6008)

Quando si esegue l'avvio di una macchina virtuale Windows dopo il failover e si riceve un messaggio di arresto imprevisto nella macchina virtuale ripristinata, significa che non è stato acquisito uno stato di arresto della macchina virtuale nel punto di ripristino usato per il failover. Questo accade quando si effettua il ripristino ad un punto in cui la macchina virtuale non è stata completamente arrestata.

In genere, non è motivo di preoccupazione e può essere ignorato per i failover non pianificati. Se è previsto il failover, assicurarsi che la macchina virtuale viene arrestata in modo corretto prima del failover e fornire il tempo necessario per in attesa di replica dei dati in locale da inviare ad Azure. Usare l'opzione **Più recente** nella [schermata Failover](site-recovery-failover.md#run-a-failover) in modo che tutti i dati in sospeso in Azure siano elaborati in un punto di recupero, che viene quindi usato per il failover della macchina virtuale.

## <a name="unable-to-select-the-datastore"></a>Non è possibile selezionare l'archivio dati

Questo problema è indicato quando è in grado di visualizzare il portale l'archivio dati in Azure durante il tentativo di abilitare la riprotezione la macchina virtuale che ha riscontrato un failover. Infatti, il Master target non è riconosciuto come una macchina virtuale in vCenter aggiunto ad Azure Site Recovery.

Per altre informazioni su come eseguire la riprotezione di una macchina virtuale, vedere [ma la Riprotezione ed eseguire indietro macchine a un sito locale dopo il failover in Azure](vmware-azure-reprotect.md).

Per risolvere il problema:

Creare manualmente il Master target in vCenter che gestisce la macchina di origine. L'archivio dati saranno disponibile dopo il successivo vCenter individuazione e l'aggiornamento operazioni sull'infrastruttura.

> [!Note]
> 
> Le operazioni di individuazione e l'aggiornamento dell'infrastruttura possono richiedere fino a 30 minuti. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>Registrazione di destinazione Master Linux con CS ha esito negativo con un errore SSL 35 

La registrazione di destinazione Master di Site Recovery di Azure con il server di configurazione non riesce a causa di un Proxy autenticato abilitato nella destinazione Master. 
 
Questo errore è indicato da stringhe seguenti nel log di installazione: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Per risolvere il problema:
 
1. Nel server di configurazione della macchina virtuale, aprire un prompt dei comandi e verificare le impostazioni del proxy usando i comandi seguenti:

    Cat /etc/environment echo $http_proxy echo https_proxy $ 

2. Se l'output del comando precedente mostra che vengono definite le impostazioni di http_proxy o https_proxy, usare uno dei metodi seguenti per sbloccare le comunicazioni del server di destinazione Master con server di configurazione:
   
   - Scaricare il [lo strumento PsExec](https://aka.ms/PsExec).
   - Utilizzare lo strumento per accedere al contesto utente di sistema e determinare se l'indirizzo del proxy è configurato. 
   - Se il proxy è configurato, aprire Internet Explorer in un contesto utente di sistema utilizzando lo strumento PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Per assicurarsi che il server di destinazione master può comunicare con il server di configurazione:
  
     - Modificare le impostazioni di proxy in Internet Explorer per ignorare l'indirizzo IP del server di destinazione Master tramite il proxy.   
     Oppure
     - Disabilitare il proxy nel server di destinazione Master. 


## <a name="next-steps"></a>Passaggi successivi
- Risoluzione problemi [di connessione RDP a una macchina virtuale Windows](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Risoluzione problemi [di connessione SSH a una macchina virtuale Linux](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Se l'utente richiede ancora assistenza, registrare la query nel [forum di Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) o lasciare un commento alla fine di questo documento. È disponibile una community attiva in grado di offrire supporto.
